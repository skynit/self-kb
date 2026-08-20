---
title: SMB 会话建立流程
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB
  - 会话建立
---

# SMB 会话建立流程

> 属于 [[Samba学习索引]] | 相关：[[SMB报文格式]]、[[SMB认证机制]]

---

## 全流程

```
Client                                         Server
  │                                               │
  │─── TCP 三次握手 (端口 445) ──────────────────▶│
  │                                               │
  │─── NEGOTIATE ────────────────────────────────▶│  ① 方言协商
  │◀── NEGOTIATE_RESPONSE ───────────────────────│     选定方言 + 交换能力
  │                                               │
  │─── SESSION_SETUP ────────────────────────────▶│  ② 认证（可能多轮）
  │◀── SESSION_SETUP_RESPONSE ───────────────────│     返回 SessionId
  │    [NTLMSSP 需 3 轮 / Kerberos 需 1-2 轮]   │
  │                                               │
  │─── TREE_CONNECT ─────────────────────────────▶│  ③ 连接共享
  │◀── TREE_CONNECT_RESPONSE ────────────────────│     返回 TreeId
  │    \\server\share                            │
  │                                               │
  │─── CREATE ───────────────────────────────────▶│  ④ 打开文件
  │◀── CREATE_RESPONSE ──────────────────────────│     返回 FileId
  │                                               │
  │─── READ / WRITE / QUERY_DIRECTORY ──────────▶│  ⑤ 文件操作
  │◀── responses ────────────────────────────────│
  │                                               │
  │─── CLOSE ────────────────────────────────────▶│  ⑥ 关闭文件
  │─── TREE_DISCONNECT ─────────────────────────▶│  ⑦ 断开共享
  │─── LOGOFF ───────────────────────────────────▶│  ⑧ 断开会话
  │─── TCP 四次挥手 ────────────────────────────▶│
```

---

## ① NEGOTIATE — 方言协商

**客户端发送**：支持的方言列表 + 能力标志

```
请求体关键字段：
- DialectCount: 支持的方言数量
- Dialects[]:  {0x0202, 0x0210, 0x0300, 0x0302, 0x0311}
- SecurityMode: 签名相关
- Capabilities: 加密、多通道等能力
- ClientGuid: 客户端唯一标识
```

**服务端响应**：选择最高共同方言

```
响应体关键字段：
- DialectRevision: 选定的方言（如 0x0311）
- SecurityMode: 服务端签名要求
- Capabilities: 服务端能力
- ServerGuid: 服务端标识
- SecurityBuffer: SPNEGO 初始 token（可选）
```

> **SMB 3.1.1 特殊**：NEGOTIATE 中需计算所有协商报文的 SHA-512 哈希，用于预认证完整性验证，防降级攻击。

---

## ② SESSION_SETUP — 认证

认证通过 SPNEGO 包装，内层为 NTLMSSP 或 Kerberos。

### NTLMSSP 三轮流程

```
Client                                         Server
  │                                               │
  │─── SESSION_SETUP (Type 1: Negociate) ───────▶│
  │◀── SESSION_SETUP (Type 2: Challenge) ────────│  ← 含 Server Challenge
  │                                               │
  │─── SESSION_SETUP (Type 3: Auth) ────────────▶│  ← 用 Challenge 计算响应
  │◀── SESSION_SETUP (STATUS_SUCCESS) ───────────│  ← 返回 SessionId
```

### Kerberos 流程

```
Client                                         Server
  │                                               │
  │─── SESSION_SETUP (Kerberos AP-REQ) ─────────▶│
  │◀── SESSION_SETUP (STATUS_SUCCESS) ───────────│  ← 通常一轮成功
```

**响应中的关键字段**：
- `SessionId`（8 字节）：后续所有请求必须携带此 ID
- `SecurityBuffer`：SPNEGO token
- 状态码 `STATUS_MORE_PROCESSING_REQUIRED`（0xC0000016）= 认证未完成，继续

---

## ③ TREE_CONNECT — 连接共享

```
请求体：
- Buffer: "\\server\share"（共享路径，UTF-16LE）
- PathLength: 路径字节长度

响应体：
- TreeId: 写入 SMB2 头部的 TID 字段
- ShareType: 磁盘共享(0x01) / 打印机共享(0x02) / IPC(0x03)
- ShareFlags: 共享能力
```

> IPC 共享（`\\server\IPC$`）用于 DCE/RPC 命名管道通信。

---

## ④ CREATE — 打开/创建文件

```
请求体关键字段：
- DesiredAccess: 访问权限（GENERIC_READ、GENERIC_WRITE 等）
- FileAttributes: 文件属性
- ShareAccess: 共享模式（读/写/删除共享）
- CreateDisposition: FILE_OPEN / FILE_CREATE / FILE_OVERWRITE 等
- CreateOptions: FILE_DIRECTORY_FILE 等
- Buffer: 文件路径（UTF-16LE，相对于共享根）

响应体关键字段：
- FileId: 文件标识符（Persistent + Volatile 各 8 字节）
- CreateAction: 实际执行的操作（打开/创建/覆盖）
- AllocationSize / EndOfFile: 文件大小信息
```

---

## ⑤ 文件操作

| 命令 | 请求关键字段 | 响应关键字段 |
|------|------------|------------|
| **READ** | FileId, Offset, Length | Data(变长) |
| **WRITE** | FileId, Offset, Data | Count(写入字节数) |
| **QUERY_DIRECTORY** | FileId(目录句柄), FileName(通配符) | FileInfos[] |
| **QUERY_INFO** | FileId, InfoType, FileInfoClass | 信息缓冲区 |
| **SET_INFO** | FileId, InfoType, FileInfoClass, Buffer | — |
| **IOCTL** | CtlCode, FileId, InputBuffer | OutputBuffer |

---

## ⑥⑦⑧ 关闭流程

```
CLOSE       → 释放 FileId
TREE_DISCONNECT → 释放 TreeId
LOGOFF      → 释放 SessionId
TCP 关闭    → 释放连接
```

---

## Compound 请求示例

CREATE + WRITE + CLOSE 一条消息完成：

```
┌────────────────────┬────────────────────┬────────────────────┐
│ SMB2 Header        │ SMB2 Header        │ SMB2 Header        │
│ Cmd=CREATE         │ Cmd=WRITE          │ Cmd=CLOSE          │
│ NextCommand=offset │ NextCommand=offset │ NextCommand=0      │
│ Flags=0            │ Flags=RELATED      │ Flags=RELATED      │
│ SessionId=S1       │ SessionId=S1(复用) │ SessionId=S1(复用) │
│ TreeId=T1          │ TreeId=T1(复用)    │ TreeId=T1(复用)    │
├────────────────────┼────────────────────┼────────────────────┤
│ CREATE Request     │ WRITE Request      │ CLOSE Request      │
│ (打开文件)         │ (FileId 来自       │ (FileId 来自       │
│                    │  CREATE 响应)      │  CREATE 响应)      │
└────────────────────┴────────────────────┴────────────────────┘
```

> Compound 请求是**原子操作**：要么全部成功，要么全部回滚。

---

> 回到 [[Samba学习索引]]
