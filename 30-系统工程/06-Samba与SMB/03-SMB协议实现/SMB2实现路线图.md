---
title: SMB2 实现路线图
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议实现
tags:
  - OS
  - Samba
  - SMB2
  - 实现
  - 手写协议
---

# SMB2 实现路线图

> 属于 [[Samba学习索引]] | 相关：[[SMB2报文序列化]]、[[NTLMSSP认证实现]]

---

## 核心原则

1. **从 SMB2 开始**，不要实现 SMB1（已弃用，100+ 命令，报文格式复杂）
2. **先做客户端**，用 Samba 作服务端验证
3. **逐层递进**，每步可测试

---

## 推荐实现顺序

```
Phase 1: 能连 ──────────────────────────────────────────
  ① TCP 传输层（端口 445 直连）
  ② SMB2 报文头序列化/反序列化
  ③ NEGOTIATE 请求/响应
  ④ SESSION_SETUP + NTLMSSP（3 轮）
  ⑤ TREE_CONNECT / TREE_DISCONNECT
  ⑥ LOGOFF

Phase 2: 能用 ──────────────────────────────────────────
  ⑦ CREATE / CLOSE（打开关闭文件）
  ⑧ READ / WRITE（读写文件）
  ⑨ QUERY_DIRECTORY（列目录）
  ⑩ QUERY_INFO / SET_INFO（查询设置属性）
  ⑪ ECHO（心跳）

Phase 3: 能用好 ─────────────────────────────────────────
  ⑫ Compound 请求支持（NextCommand 链式）
  ⑬ Credit 流控（正确请求/授予信用）
  ⑭ Oplock/Lease 支持
  ⑮ SMB3 加密（AES-CCM）
  ⑯ 多通道支持

Phase 4: 能管 ──────────────────────────────────────────
  ⑰ DCE/RPC over Named Pipes
  ⑱ Kerberos 认证（GSSAPI）
  ⑲ 持久句柄（Durable Handle v2）
  ⑳ 服务端实现（可选）
```

---

## 每个阶段的关键挑战

### Phase 1

| 步骤 | 挑战 | 解决思路 |
|------|------|---------|
| TCP 传输 | 4 字节大端长度前缀 + 报文边界 | 用 ring buffer 处理粘包/拆包 |
| 报文序列化 | 小端序、对齐、变长字段 Offset 从头部算起 | 写单元测试，对照 Wireshark 抓包 |
| NEGOTIATE | SMB 3.1.1 需预认证完整性（SHA-512） | 先实现 SMB 2.1 或 3.0 跳过此要求 |
| NTLMSSP | 3 轮状态机 | 画状态图，STATUS_MORE_PROCESSING_REQUIRED = 继续 |
| TREE_CONNECT | 路径格式 `\\server\share` UTF-16LE | 注意反斜杠转义 |

### Phase 2

| 步骤 | 挑战 | 解决思路 |
|------|------|---------|
| CREATE | 大量可选字段、Impersonation Level | 先只支持 FILE_OPEN 和 FILE_CREATE |
| READ/WRITE | 大文件分片、Offset 对齐 | 先支持小文件（<64KB），再处理分片 |
| QUERY_DIRECTORY | 多种信息类、分页查询 | 先实现 FileIdBothDirectoryInformation |
| Compound | 链式中后续请求依赖前一个的响应 | CREATE+WRITE+CLOSE 需处理 FileId 传递 |

### Phase 3

| 步骤 | 挑战 | 解决思路 |
|------|------|---------|
| Credit 流控 | 死锁（信用耗尽无法发请求） | 确保服务端至少授予 1 信用 |
| 加密 | 密钥派生、Nonce 管理 | 参考MS-SMB2 Section 3.2.5.2 |
| Oplock | 服务端主动推送 Break | 需要异步 I/O 模型 |

---

## 测试策略

### 单元测试

```
每个命令的序列化/反序列化：
  - 构造请求 → 序列化 → 对比预期字节
  - 从字节反序列化 → 对比预期字段值
  - 对照 MS-SMB2 规范中的报文示例
```

### 集成测试

```
1. 用 Samba 作为参考服务端
2. 启动 Samba，配置一个简单共享
3. 用自实现的客户端连接，逐步验证：
   - NEGOTIATE 成功
   - SESSION_SETUP 成功
   - TREE_CONNECT 成功
   - CREATE + READ 能读文件
   - CREATE + WRITE 能写文件
4. 同时用 Wireshark 抓包对比
```

### Wireshark 验证

```bash
# 抓包
tcpdump -i lo -w test.pcap 'port 445'

# 用 Wireshark 打开
# 过滤器：smb2
# 对比每个请求/响应的字段值
```

---

## 推荐语言/框架

| 语言 | 优势 | 参考实现 |
|------|------|---------|
| **Go** | 网络库成熟、跨平台、协程模型适合异步 | [go-smb2](https://github.com/hirochachacha/go-smb2) |
| **Rust** | 内存安全、零成本抽象、async/await | 适合高性能实现 |
| **C** | 最接近 Samba 源码，直接参考 | Samba 源码 |
| **Python** | 快速原型、impacket 可参考 | [impacket](https://github.com/fortra/impacket) |

---

## 必读规范章节

实现每个阶段前，阅读 MS-SMB2 的对应章节：

| 阶段 | MS-SMB2 章节 |
|------|-------------|
| 报文格式 | §2 Message Syntax |
| NEGOTIATE | §2.2.3 + §3.3.5.3 |
| SESSION_SETUP | §2.2.5 + §3.3.5.5 |
| TREE_CONNECT | §2.2.6 + §3.3.5.7 |
| CREATE | §2.2.13 + §3.3.5.9 |
| READ/WRITE | §2.2.14-15 + §3.3.5.11-12 |
| Compound | §2.2.1.2 + §3.2.4.1.3 |
| Credit | §3.3.1.2 |

---

> 回到 [[Samba学习索引]]
