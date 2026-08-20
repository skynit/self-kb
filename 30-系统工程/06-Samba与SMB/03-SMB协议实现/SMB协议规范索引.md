---
title: SMB 协议规范索引
created: 2026-06-23
updated: 2026-06-23
type: reference
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB
  - 规范
  - MS-SMB2
  - MS-CIFS
---

# SMB 协议规范索引

> 属于 [[Samba学习索引]]

---

## 核心协议规范

### MS-SMB2 — SMB2/3 协议规范

| 项 | 值 |
|---|---|
| 全称 | `[MS-SMB2]: Server Message Block (SMB) Protocol Versions 2 and 3` |
| 最新版本 | Rev 86.0 (2026-04) |
| 在线 | [learn.microsoft.com](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/5606ad47-5ee0-437a-817e-70c366052962) |
| PDF | [下载](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/MS-SMB2/%5bMS-SMB2%5d.pdf) |

**阅读顺序**：

```
§1 Introduction          → 全局了解
§2 Message Syntax        → 报文格式（实现必读）
§3 Protocol Details      → 状态机与处理规则
  §3.1 Common            → 通用处理
  §3.2 Client            → 客户端实现细节
  §3.3 Server            → 服务端实现细节
§4 Protocol Examples     → 报文示例（调试对照）
```

### MS-CIFS — SMB1/CIFS 协议规范

| 项 | 值 |
|---|---|
| 全称 | `[MS-CIFS]: Common Internet File System (CIFS) Protocol` |
| 最新版本 | Rev 31.0 (2025-11) |
| 在线 | [learn.microsoft.com](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/) |
| PDF | [下载](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/MS-CIFS/%5bMS-CIFS%5d.pdf) |

> **仅了解历史**，不要基于此实现新协议栈。SMB1 已弃用。

### MS-SMB — Microsoft CIFS 扩展

| 项 | 值 |
|---|---|
| 全称 | `[MS-SMB]: Server Message Block (SMB) Protocol` |
| 最新版本 | Rev 55.0 (2026-01) |
| PDF | [下载](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/MS-SMB/%5bMS-SMB%5d.pdf) |

---

## 认证规范

| 规范 ID | 全称 | 用途 |
|---------|------|------|
| **MS-NLMP** | NT LAN Manager (NTLM) Authentication Protocol | NTLMSSP 实现参考 |
| **MS-KILE** | Kerberos Protocol Extensions | Kerberos 实现参考 |
| **MS-SPNG** | SPNEGO Protocol | SPNEGO token 格式 |

---

## DCE/RPC 规范

| 规范 ID | 全称 | 用途 |
|---------|------|------|
| **MS-RPCE** | Remote Procedure Call Protocol Extensions | DCE/RPC PDU 格式 |
| **MS-SRVS** | Server Service Remote Protocol | 共享枚举 |
| **MS-SAMR** | Security Account Manager Remote Protocol | 用户管理 |
| **MS-LSAT/MS-LSAD** | Local Security Authority | 安全策略 |
| **MS-SVCCTL** | Service Control Manager Remote Protocol | 服务控制 |

---

## 传输层规范

| 规范 | 说明 |
|------|------|
| **RFC 1001** | NetBIOS over TCP — Concepts and Methods |
| **RFC 1002** | NetBIOS over TCP — Detailed Specifications |

---

## 全量下载

Microsoft Open Specifications 完整协议包（ZIP）：

[Windows_Protocols.zip](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/Windows_Protocols.zip)

> 包含所有 Windows 协议规范的 PDF 版本，约 200+ 文档。

---

## 第三方参考

| 资源 | 说明 | 链接 |
|------|------|------|
| *Implementing CIFS* (Hertel) | SMB1 协议内部实现指南，Samba 团队成员所著 | [ubiqx.org/cifs](https://www.ubiqx.org/cifs/) |
| SNIA CIFS Technical Reference | 2002 年 CIFS 标准化尝试，有历史价值 | [snia.org](https://www.samba.org/cifs/docs/what-is-smb.html) |
| X/Open CAE C209 | 1992 年 SMB 标准化文档 | 历史参考 |
| Samba 源码 | C 语言参考实现 | [git.samba.org](https://git.samba.org/) |
| impacket | Python SMB 库，适合学习协议流程 | [GitHub](https://github.com/fortra/impacket) |
| go-smb2 | Go SMB2/3 客户端 | [GitHub](https://github.com/hirochachacha/go-smb2) |

---

## 许可证

Microsoft Open Specifications 受 **Microsoft Open Specifications Promise** 保护，授予实现文档中描述协议的专利权利。可自由实现，无需额外许可。

---

> 回到 [[Samba学习索引]]
