---
title: Samba 与 SMB 协议学习索引
created: 2026-06-23
updated: 2026-06-23
type: moc
tags:
  - OS
  - Samba
  - SMB
  - MOC
---

# Samba 与 SMB 协议 — 学习索引

> SMB/CIFS 是 Windows 文件共享的核心协议，Samba 是其开源实现。
> 本目录覆盖**使用 → 配置 → 手写协议实现**全链路。

---

## 一、SMB 协议原理

> 理解协议才能写出协议

- [[SMB协议总览]] — 协议历史、SMB1→SMB2→SMB3 演进、CIFS vs SMB 区别
- [[SMB报文格式]] — SMB1/SMB2 报文头结构、字段含义、Magic Bytes
- [[SMB会话建立流程]] — NEGOTIATE → SESSION_SETUP → TREE_CONNECT → CREATE 全链路
- [[SMB认证机制]] — NTLM、Kerberos、SPNEGO 原理与交互流程
- [[SMB2特性详解]] — Compound 请求、Credit 流控、Oplock/Lease、加密
- [[POSIX文件系统]] — POSIX 文件接口标准、权限模型、与 Windows ACL 差异
- [[DCE-RPC-over-SMB]] — 命名管道上的远程过程调用

## 二、Samba 部署与配置

> 从用开始，再学原理

- [[Samba概念讲解]] — Samba 是什么、解决什么问题、核心概念速查
- [[Samba架构与组件]] — smbd / nmbd / winbindd 三大守护进程
- [[smb-conf配置详解]] — 全局段、共享段、关键指令速查
- [[Samba部署场景]] — 独立服务器 / AD 域成员 / AD 域控制器
- [[Samba调试与排错]] — testparm、smbstatus、日志级别、Wireshark 抓包

## 三、手写 SMB 协议实现

> 从零实现 SMB2/3 协议栈

- [[SMB2实现路线图]] — 推荐实现顺序、关键挑战、测试方法
- [[SMB2报文序列化]] — 头部编解码、变长字段处理、字节序
- [[NTLMSSP认证实现]] — Type1/Type2/Type3 三轮握手的状态机
- [[SMB3加密实现]] — Transform Header、AES-CCM/GCM、密钥协商
- [[SMB协议规范索引]] — MS-SMB2、MS-CIFS 等规范下载与阅读指南

---

## 学习路径

```
阶段一：会用 ─────────────────────────────────────────────
  Samba架构与组件 → smb-conf配置详解 → Samba部署场景
  （目标：搭一个可用的 Samba 文件共享）

阶段二：懂原理 ───────────────────────────────────────────
  SMB协议总览 → SMB报文格式 → SMB会话建立流程
  → SMB认证机制 → SMB2特性详解 → DCE-RPC-over-SMB
  （目标：用 Wireshark 能看懂 SMB 报文交互）

阶段三：能写 ─────────────────────────────────────────────
  SMB2实现路线图 → SMB2报文序列化 → NTLMSSP认证实现
  → SMB3加密实现
  （目标：手写一个能与 Samba 通信的 SMB2 客户端）
```

---

## 核心规范下载

| 规范 | 版本 | 内容 | 下载 |
|------|------|------|------|
| **MS-SMB2** | Rev 86.0 | SMB2/3 协议权威规范 | [PDF](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/MS-SMB2/%5bMS-SMB2%5d.pdf) |
| **MS-CIFS** | Rev 31.0 | SMB1/CIFS 协议规范 | [PDF](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/MS-CIFS/%5bMS-CIFS%5d.pdf) |
| **MS-SMB** | Rev 55.0 | Microsoft 对 CIFS 的扩展 | [PDF](https://winprotocoldocs-bhdugrdyduf5h2e4.b02.azurefd.net/MS-SMB/%5bMS-SMB%5d.pdf) |
| **MS-NLMP** | — | NTLM 认证协议 | [在线](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/) |
| **MS-RPCE** | — | DCE/RPC 扩展 | [在线](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rpce/) |

---

## 开源参考实现

| 项目 | 语言 | 说明 |
|------|------|------|
| [Samba](https://git.samba.org/) | C | 权威开源实现，source3/smbd/ 和 source4/smbd/ |
| [impacket](https://github.com/fortra/impacket) | Python | Python SMB 库，适合学习协议流程 |
| [go-smb2](https://github.com/hirochachacha/go-smb2) | Go | Go 语言 SMB2/3 客户端 |
| [pysmb](https://github.com/samba/pysmb) | Python | Python SMB1/SMB2 客户端 |

---

> 回到 [[OS理论MOC]] | [[Linux实操MOC]]
