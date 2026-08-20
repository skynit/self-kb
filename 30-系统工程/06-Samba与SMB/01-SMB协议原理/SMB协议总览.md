---
title: SMB 协议总览
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB
  - CIFS
  - 协议
---

# SMB 协议总览

> 属于 [[Samba学习索引]] | 相关：[[SMB报文格式]]、[[SMB会话建立流程]]

## 一句话定义

SMB（Server Message Block）是 Windows 文件与打印共享的核心协议，运行在 TCP 445（直连）或 TCP 139（NetBIOS）上。

---

## 协议演进时间线

```
1984  IBM PC Network Technical Reference     NetBIOS 诞生
 │
1986  Intel/Microsoft SMB v1.9               早期 SMB 扩展
 │
1987  RFC 1001/1002                          NetBIOS over TCP 标准化
 │
1992  X/Open CAE C209                        SMB 第一次正式标准化
 │
1996  NT LM 0.12 (SMB1 终极形态)            SMB1 最后的方言
 │     CIFS 1.0 (SNIA/Microsoft)            本质 = NT LM 0.12 + Internet 友好改造
 │
2006  SMB2 (Windows Vista)                   ★ 完全重写，100+ 命令 → 19 命令
 │
2012  SMB3 (Windows 8)                       加密、多通道、持久句柄
 │
2015  SMB 3.1.1 (Windows 10)                预认证完整性、AES-128-GCM
```

---

## CIFS vs SMB 区别

| 术语 | 实质 | 说明 |
|------|------|------|
| **SMB1** | 原始协议族（1984-1996） | 方言协商字符串 `NT LM 0.12` |
| **CIFS** | SMB1 的标准化尝试（1996） | 本质 = NT LM 0.12，做了少量 Internet 友好改造 |
| **SMB2** | 完全重写（2006） | **不是** SMB1/CIFS 的扩展，报文格式完全不同 |
| **SMB3** | SMB2 的扩展（2012） | 同一报文格式，增加加密/多通道等能力 |

> **关键**：SMB1/CIFS 已被弃用。Windows 10 1709+ 默认禁用，Samba 4.11+ 默认禁用。
> 新实现**必须从 SMB2 起步**，不要实现 SMB1。

---

## 传输层

```
方式一：NetBIOS over TCP (NBT) — 端口 139
  需先建立 NetBIOS 会话，再传 SMB 报文
  ┌─────────┬────────┬──────────┬───────────────┐
  │ Type(1) │Flags(1)│Length(2) │  SMB payload  │
  └─────────┴────────┴──────────┴───────────────┘

方式二：Direct TCP — 端口 445（现代、推荐）
  直接 TCP 流，无 NetBIOS 开销
  ┌──────────┬───────────────┐
  │ Length(4)│  SMB payload  │
  └──────────┴───────────────┘
```

---

## SMB1 vs SMB2 核心差异

| 维度 | SMB1 | SMB2/3 |
|------|------|--------|
| 命令数 | 100+ | 19（可扩展） |
| Magic Bytes | `\xFFSMB` | `\xFESMB` |
| 头部大小 | 32 字节 + 变长参数 | 64 字节固定（SYNC）/ 72 字节（ASYNC） |
| 请求链 | AndX chaining（有限） | Compound 请求（任意命令可组合） |
| 流控 | MID 多路复用 | **Credit 流控**：服务端授予信用量 |
| 签名 | HMAC-MD5 | HMAC-SHA256 |
| 加密 | 无 | AES-CCM (3.0)、AES-GCM (3.1.1) |
| 持久句柄 | 无 | Durable Handle v1 (2.1)、v2 (3.0+) |
| 多通道 | 无 | SMB 3.0+ 多 TCP 连接聚合带宽 |
| 预认证完整性 | 无 | SMB 3.1.1 SHA-512 哈希防降级 |

---

## SMB2 方言协商值

| 值 | 方言 |
|---|---|
| `0x0202` | SMB 2.002 |
| `0x0210` | SMB 2.1 |
| `0x0300` | SMB 3.0 |
| `0x0302` | SMB 3.0.2 |
| `0x0311` | SMB 3.1.1 |

客户端在 NEGOTIATE 中发送支持的方言列表，服务端选择最高的共同支持版本。

---

## 相关规范

| 规范 | 内容 |
|------|------|
| [[SMB协议规范索引#MS-SMB2\|MS-SMB2]] | SMB2/3 权威规范（必读） |
| [[SMB协议规范索引#MS-CIFS\|MS-CIFS]] | SMB1/CIFS 规范（了解历史） |
| RFC 1001/1002 | NetBIOS over TCP |
| *Implementing CIFS* (Hertel) | SMB1 内部实现指南，[在线阅读](https://www.ubiqx.org/cifs/) |

---

> 回到 [[Samba学习索引]]
