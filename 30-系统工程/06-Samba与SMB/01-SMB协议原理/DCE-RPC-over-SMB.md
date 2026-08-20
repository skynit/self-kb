---
title: DCE/RPC over SMB
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB
  - DCE/RPC
  - 命名管道
---

# DCE/RPC over SMB

> 属于 [[Samba学习索引]] | 相关：[[SMB会话建立流程]]

---

## 为什么需要 DCE/RPC

SMB 的 CREATE/READ/WRITE 只能做文件 I/O。但管理操作（用户管理、共享枚举、服务控制、事件日志）需要 **远程过程调用**——这就是 DCE/RPC。

---

## 传输方式

DCE/RPC 通过 **SMB 命名管道** 传输。

```
┌───────────────────────────────────────────┐
│  DCE/RPC PDU                              │
│  ┌─────────────────────────────────────┐  │
│  │ Header (调用号、OP号、长度)         │  │
│  │ Stub (参数序列化，NDR 格式)         │  │
│  └─────────────────────────────────────┘  │
└───────────────────────────────────────────┘
           ↕ 通过命名管道传输
┌───────────────────────────────────────────┐
│  SMB 层                                   │
│  SMB1: SMB_COM_TRANSACTION → \pipe\xxx    │
│  SMB2: CREATE(\pipe\xxx) → WRITE → READ  │ 
└───────────────────────────────────────────┘
```

### SMB1 方式

用 `SMB_COM_TRANSACTION` 命令，管道名写在请求中，DCE/RPC PDU 放在 Data 块。

### SMB2 方式

```
1. CREATE → 打开命名管道 "\\server\IPC$\pipe\lsass"
2. WRITE  → 发送 DCE/RPC 请求 PDU
3. READ   → 接收 DCE/RPC 响应 PDU
4. CLOSE  → 关闭管道
```

> 命名管道句柄和文件句柄使用方式完全相同。

---

## 关键命名管道

| 管道路径 | 服务 | 规范 |
|---------|------|------|
| `\pipe\lsass` | LSA（本地安全授权） | MS-LSAD / MS-LSAT |
| `\pipe\samr` | SAM（安全账户管理） | MS-SAMR |
| `\pipe\svcctl` | 服务控制管理器 | MS-SVCCTL |
| `\pipe\srvsrv` | 服务器服务（共享枚举） | MS-SRVS |
| `\pipe\winreg` | 远程注册表 | MS-RRP |
| `\pipe\eventlog` | 事件日志 | MS-EVEN |
| `\pipe\wkssvc` | 工作站服务 | MS-WKST |
| `\pipe\netlogon` | Netlogon | MS-NRPC |

---

## DCE/RPC PDU 结构

```
偏移   大小   字段
0      8      Magic: 0x05 0x00（版本5.0）
2      1      PDU Type: 0=REQUEST, 2=RESPONSE, ...
3      1      Flags
4      1      Data Representation (字节序、对齐)
5      1      Float Representation
6      2      Fragment Length（整个 PDU 长度）
8      2      Auth Length
10     4      Call ID（匹配请求和响应）
14+    变长   Stub Data（NDR 编码的参数）
```

### PDU Type

| 值 | 类型 | 说明 |
|---|------|------|
| 0 | REQUEST | 客户端请求 |
| 2 | RESPONSE | 服务端响应 |
| 3 | FAULT | 服务端错误 |
| 11 | BIND | 绑定到接口（握手） |
| 12 | BIND_ACK | 绑定确认 |
| 13 | BIND_NAK | 绑定拒绝 |
| 14 | ALTER_CONTEXT | 更改上下文 |
| 15 | ALTER_CONTEXT_RESP | 更改响应 |

---

## 交互流程

```
Client                                         Server
  │─── CREATE(\pipe\srvsrv) ──────────────────▶│  打开管道
  │◀── CREATE_RESPONSE (FileId) ──────────────│
  │                                               │
  │─── WRITE (BIND PDU) ─────────────────────▶│  绑定接口
  │    Interface: MS-SRVS (4B324FC8...)         │
  │◀── READ (BIND_ACK PDU) ──────────────────│  绑定确认
  │                                               │
  │─── WRITE (REQUEST PDU) ──────────────────▶│  调用方法
  │    OpNum: 21 (NetShareEnum)                  │
  │◀── READ (RESPONSE PDU) ──────────────────│  返回结果
  │    Stub: 共享列表 NDR 数据                    │
  │                                               │
  │─── CLOSE ────────────────────────────────▶│
```

---

## 大数据传输：分片

DCE/RPC PDU 有最大长度限制（通常 4280 字节）。大数据需要**分片传输**：

```
请求方：
  BIND → BIND_ACK（协商最大传输长度）
  REQUEST(first) → RESPONSE(pending)
  REQUEST(frag2) → RESPONSE(pending)
  REQUEST(frag3, last) → RESPONSE(complete)

每个分片的 PDU Flags 中：
  PFC_FIRST_FRAG = 首片
  PFC_LAST_FRAG  = 末片
  非首非末 = 中间片
```

---

## 实现建议

1. **先跳过 DCE/RPC**——实现 SMB2 文件 I/O 后再考虑
2. 如需实现，先做 BIND + NetShareEnum（最简单的管理操作）
3. NDR 编解码可用现成库（如 Samba 的 ndr 库、impacket 的 ndr）
4. 分片逻辑容易出错，建议参考 impacket 的实现

---

> 回到 [[Samba学习索引]]
