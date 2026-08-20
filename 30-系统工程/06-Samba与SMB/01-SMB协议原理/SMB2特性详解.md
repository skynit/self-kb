---
title: SMB2 特性详解
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB2
  - Compound
  - Credit
  - Oplock
---

# SMB2 特性详解

> 属于 [[Samba学习索引]] | 相关：[[SMB报文格式]]、[[SMB会话建立流程]]

---

## 一、Compound 请求（链式请求）

SMB1 的 AndX chaining 只能链接特定命令组合。SMB2 的 Compound **任意命令可组合**。

### 原理

```
单条 TCP 消息中打包多个 SMB2 请求：

┌──────────┬──────────┬──────────┐
│ Request1 │ Request2 │ Request3 │
│ NextCmd→ │ NextCmd→ │ NextCmd=0│
└──────────┴──────────┴──────────┘
```

- Header.NextCommand = 下一个请求在本消息中的偏移（字节）
- 最后一个请求的 NextCommand = 0
- 设置 `SMB2_FLAGS_RELATED_OPERATIONS` → 自动复用前一个请求的 SessionId、TreeId

### 原子性

Compound 请求是**原子操作**：
- 全部成功 → 全部生效
- 任一失败 → 全部回滚

### 常见组合

| 组合 | 场景 |
|------|------|
| CREATE + WRITE + CLOSE | 创建并写入文件，1 RTT |
| CREATE + QUERY_INFO | 打开文件并查属性 |
| NEGOTIATE + SESSION_SETUP | 协商+认证一次完成 |

---

## 二、Credit 流控

SMB1 用 MID 做多路复用，无服务端流控。SMB2 引入 Credit 机制。

### 原理

```
1. 服务端在响应中授予信用量：CreditResponse = N
2. 客户端累计持有可用信用
3. 每发一个请求消耗 CreditCharge 个信用（通常 1）
4. 信用不足时不能发新请求，必须等响应
```

```
Client                     Server
  │─── Req1 (CreditCharge=1, CreditRequest=3) ──▶│
  │◀── Resp1 (CreditResponse=3) ────────────────│  授予 3 信用
  │                                               │
  │─── Req2 (CreditCharge=1) ──▶                  │  剩余 2
  │─── Req3 (CreditCharge=1) ──▶                  │  剩余 1
  │─── Req4 (CreditCharge=1) ──▶                  │  剩余 0
  │    （不能发更多，等响应）                      │
  │◀── Resp2 (CreditResponse=2) ────────────────│  获得 2 信用
  │─── Req5 ...                                   │
```

### 实现要点

- 大型 READ/WRITE 可能 CreditCharge > 1（按传输大小计算）
- NEGOTIATE 和 ECHO 不消耗信用
- 初始信用量 = 服务端在 NEGOTIATE 响应中授予
- **死锁防范**：服务端必须保证至少授予 1 个信用，否则客户端永远无法发送

---

## 三、Oplock / Lease

Oplock（机会锁）= 客户端缓存授权，减少网络往返。

### Oplock 级别

| 级别 | 缓存 | 说明 |
|------|------|------|
| None | 无 | 不缓存 |
| Level II | 读缓存 | 多客户端只读共享 |
| Exclusive | 读写缓存 | 独占，可本地读写在客户端 |
| Batch | 读写 + 打开缓存 | 最强，可延迟关闭 |

### Lease（SMB 2.1+）

Lease 是 Oplock 的增强版，用 **Lease Key**（16 字节客户端生成的 GUID）标识缓存对象。

```
优势：
- Lease Key 绑定到文件而非句柄（同一客户端多个句柄共享缓存）
- 支持目录 Lease（缓存目录内容）
- 支持 Lease Break 精确通知（告知哪些范围失效）
```

### Oplock Break 流程

```
ClientA(持Exclusive)                Server                 ClientB
  │                                   │                      │
  │                                   │◀── CREATE(同一文件) ─│
  │◀── OPLOCK_BREAK ────────────────│                      │
  │    (降级为LevelII)               │                      │
  │─── OPLOCK_BREAK_ACK ───────────▶│                      │
  │                                   │─── CREATE响应 ──────▶│
```

---

## 四、持久句柄（Durable Handle）

网络中断后文件句柄不丢失，重连后可继续操作。

| 版本 | 特性 |
|------|------|
| SMB 2.1 | Durable Handle v1 — 单通道，短时中断 |
| SMB 3.0 | Durable Handle v2 — 多通道，持久化到存储 |
| SMB 3.0 | Persistent Handle — 集群故障转移后仍有效 |

```
正常流程：
  CREATE (DurableHandleRequest=1) → 获得 FileId + durable_id
  ... 读写 ...
  网络断开

重连流程：
  NEGOTIATE → SESSION_SETUP → TREE_CONNECT
  CREATE (DurableHandleReconnect, durable_id=之前保存的)
  → 服务端恢复原 FileId，继续操作
```

---

## 五、多通道（Multichannel）

SMB 3.0+ 允许一个会话跨越多条 TCP 连接。

```
              ┌── TCP 连接1 (网卡1) ──┐
Client ───────┤                       ├────── Server
              ├── TCP 连接2 (网卡2) ──┘
              │
              同一个 SessionId
              带宽聚合 + 故障切换
```

**实现要求**：
- 客户端和服务端都在 NEGOTIATE 中声明 `SMB2_GLOBAL_CAP_MULTI_CHANNEL`
- 每条连接独立管理 Credit
- FileId 跨连接有效

---

## 六、SMB3 加密

| 版本 | 加密算法 | 完整性 |
|------|---------|--------|
| SMB 3.0 | AES-128-CCM | CCM MAC |
| SMB 3.0.2 | AES-128-CCM | CCM MAC |
| SMB 3.1.1 | AES-128-CCM / AES-128-GCM | GCM MAC 或 CCM MAC |

加密启用后，报文用 Transform Header 替换标准 SMB2 头：

```
0xFD 0x53 0x4D 0x42  ← 不同 Magic（\xFDSMB）
OriginalMessageSize
EncryptionNonce(16)
OriginalMessageSignature(16)
EncryptedPayload(变长)
```

加密密钥由 SessionBaseKey 派生，每个会话独立。

---

> 回到 [[Samba学习索引]]
