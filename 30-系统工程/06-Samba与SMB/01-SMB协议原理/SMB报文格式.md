---
title: SMB 报文格式
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB
  - 报文格式
---

# SMB 报文格式

> 属于 [[Samba学习索引]] | 相关：[[SMB协议总览]]、[[SMB会话建立流程]]

---

## 传输层封装

### Direct TCP（端口 445，推荐）

```
┌─────────────┬────────────────────┐
│  Length(4)  │   SMB payload      │
│  大端序      │                    │
│  0在最高位   │                    │
└─────────────┴────────────────────┘
```

- 前 4 字节 = 后续 SMB 报文的字节长度（大端序）
- 最高位必须为 0（否则是 NetBIOS 会话消息）

### NetBIOS over TCP（端口 139，旧式）

```
┌──────┬──────┬──────────┬────────────────────┐
│Type  │Flags │ Length(2)│   SMB payload      │
│0x81  │0x00  │ 大端序17位 │                    │
│会话请求│     │          │                    │
└──────┴──────┴──────────┴────────────────────┘
```

---

## SMB1 报文头（已弃用，了解即可）

```
偏移   大小   字段
0      4      Magic: 0xFF 0x53 0x4D 0x42 ("\xFFSMB")
4      1      Command（命令码，见下表）
5      4      Status（NT 状态码）
9      1      Flags
10     2      Flags2
12     2      PID High
14     8      Security Signature（签名开启时）
22     2      Reserved
24     2      TID（Tree ID）
26     2      PID（Process ID，低 16 位）
28     2      UID（User Id / Session Id）
30     2      MID（Multiplex Id）
── 变长部分 ──
32     2      WordCount（参数字个数）
34     n      ParameterWords（n × 2 字节）
34+n   2      ByteCount（数据字节数）
36+n   m      DataBytes
```

### SMB1 关键命令码

| 码 | 名称 | 用途 |
|---|------|------|
| `0x72` | NEGOTIATE | 方言协商 |
| `0x73` | SESSION_SETUP_ANDX | 认证建会话 |
| `0x75` | TREE_CONNECT_ANDX | 连接共享 |
| `0xA2` | NT_CREATE | 打开/创建文件 |
| `0x2B` | IOCTL | I/O 控制 |

---

## SMB2 报文头（手写协议的核心）

### SYNC 头（64 字节，用于同步请求/响应）

```
偏移   大小   字段                      说明
0      4      ProtocolId                0xFE 0x53 0x4D 0x42 ("\xFESMB")
4      2      StructureSize             64（SYNC）/ 72（ASYNC）
6      2      CreditCharge              本请求消耗的信用量
8      4      Status                    响应中为 NT 状态码；请求中为 0
12     2      Command                   SMB2 命令码
14     2      CreditRequest/Response     请求/授予的信用量
16     4      Flags                     标志位
20     4      NextCommand               下一个链式请求偏移（0 = 最后）
24     8      MessageId                 请求序列号
32     4      Reserved                  保留（早期实现中为 ProcessId）
36     8      SessionId                 会话标识符
44     16     Signature                  HMAC-SHA256 签名
```

### ASYNC 头（72 字节，用于异步响应）

```
偏移   大小   字段                      说明
0-31   32     同 SYNC 头前 32 字节
32     8      AsyncId                  异步操作标识符
40     16     Signature                签名
```

### Flags 关键位

| 位          | 名称                            | 说明                                  |
| ---------- | ----------------------------- | ----------------------------------- |
| 0x00000001 | SMB2_FLAGS_SERVER_TO_REDIR    | 响应报文置此位                             |
| 0x00000002 | SMB2_FLAGS_ASYNC_COMMAND      | 异步响应                                |
| 0x00000004 | SMB2_FLAGS_RELATED_OPERATIONS | 链式请求中复用前一个的 SessionId/TreeId/FileId |
| 0x00000008 | SMB2_FLAGS_SIGNED             | 已签名                                 |
| 0x00000010 | SMB2_FLAGS_DFS_OPERATIONS     | DFS 路径解析                            |

---

## SMB2 命令码

| 码        | 名称              | 用途      |
| -------- | --------------- | ------- |
| `0x0000` | NEGOTIATE       | 方言协商    |
| `0x0001` | SESSION_SETUP   | 认证      |
| `0x0002` | LOGOFF          | 断开会话    |
| `0x0003` | TREE_CONNECT    | 连接共享    |
| `0x0004` | TREE_DISCONNECT | 断开共享    |
| `0x0005` | CREATE          | 打开/创建文件 |
| `0x0006` | CLOSE           | 关闭文件    |
| `0x0007` | FLUSH           | 刷盘      |
| `0x0008` | READ            | 读文件     |
| `0x0009` | WRITE           | 写文件     |
| `0x000A` | IOCTL           | I/O 控制  |
| `0x000B` | CANCEL          | 取消请求    |
| `0x000C` | ECHO            | 心跳      |
| `0x000D` | QUERY_DIRECTORY | 列目录     |
| `0x000E` | CHANGE_NOTIFY   | 目录变更通知  |
| `0x000F` | QUERY_INFO      | 查询信息    |
| `0x0010` | SET_INFO        | 设置信息    |
| `0x0011` | OPLOCK_BREAK    | 机会锁中断   |

---

## Compound 请求（链式）

多个 SMB2 请求打包在一条 TCP 消息中：

```
┌──────────────┬──────────────┬──────────────┐
│ SMB2 Header1 │ SMB2 Header2 │ SMB2 Header3 │
│ + Req Body1  │ + Req Body2  │ + Req Body3  │
└──────────────┴──────────────┴──────────────┘
     │              │              │
     │ NextCommand  │ NextCommand  │ NextCommand=0
     └──────────────┴──────────────┘
```

- Header1.NextCommand = Header2 在本消息中的偏移
- Header2 设置 `SMB2_FLAGS_RELATED_OPERATIONS` → 复用 Header1 的 SessionId、TreeId
- 减少 RTT：如 CREATE + WRITE + CLOSE 一条消息完成

---

## SMB3 加密报文（Transform Header）

协商加密后，整条消息被替换为：

```
偏移   大小   字段
0      4      ProtocolId: 0xFD 0x53 0x4D 0x42 ("\xFDSMB")
4      2      OriginalMessageSize
6      2      Reserved
8      16     EncryptionNonce（随机数）
24     16     OriginalMessageSignature
40     n      EncryptedPayload（AES-CCM 或 AES-GCM）
```

---

## 实现要点

1. **字节序**：SMB2 头部所有多字节字段均为**小端序**（little-endian），与网络字节序相反
2. **对齐**：请求/响应体中的字段通常有 4 字节或 8 字节对齐要求，需注意填充
3. **变长字段**：SMB2 请求/响应体中变长字段用 `Offset` + `Length` 对引用，Offset 从 SMB2 头部起始计算（不是从请求体起始）

---

> 回到 [[Samba学习索引]]
