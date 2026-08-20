---
title: Samba 架构与组件
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: Samba
tags:
  - OS
  - Samba
  - smbd
  - nmbd
  - winbindd
---

# Samba 架构与组件

> 属于 [[Samba学习索引]] | 相关：[[smb-conf配置详解]]

---

## 三大守护进程

```
┌─────────────────────────────────────────────────────┐
│                    Samba 整体架构                     │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────────┐      │
│  │  smbd    │  │  nmbd    │  │  winbindd    │      │
│  │ 文件/打印│  │ 名称解析 │  │  AD 集成     │      │
│  │ 认证     │  │ 浏览服务 │  │  身份映射    │      │
│  │ 端口445  │  │ 端口137/138│ │  域成员/控制器│     │
│  └────┬─────┘  └────┬─────┘  └──────┬───────┘      │
│       │              │               │              │
│       ▼              ▼               ▼              │
│  ┌──────────────────────────────────────────────┐   │
│  │              smb.conf (配置文件)              │   │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

---

## smbd — 核心服务

**职责**：文件共享、打印共享、认证、锁管理

```
客户端连接流程：
  TCP:445 → smbd fork 子进程 → 认证 → 文件操作

关键功能：
  - SMB1/SMB2/SMB3 协议处理
  - 用户认证（本地 pdb 或 AD）
  - 文件锁与 Oplock 管理
  - POSIX ACL → Windows ACL 映射
  - 共享权限控制
```

**进程模型**：每个客户端连接 fork 一个 smbd 子进程（Samba 4 支持 prefork 模式）。

```bash
# 查看当前连接
smbstatus

# 输出示例：
# PID     Username     Machine              Protocol
# 12345   alice        192.168.1.100        SMB3_11
```

---

## nmbd — NetBIOS 名称服务

**职责**：NetBIOS 名称解析与浏览

```
端口：
  UDP 137 — NetBIOS Name Service (NBNS)
  UDP 138 — NetBIOS Datagram Service

功能：
  - WINS 服务器/客户端
  - NetBIOS 名称注册与解析
  - 浏览列表维护（网上邻居）
```

> 现代 SMB 直连（端口 445）**不需要 nmbd**。只在需要 NetBIOS 兼容性时启动。

```bash
# 测试名称解析
nmblookup -S __SAMBA__
nmblookup -A 192.168.1.10
```

---

## winbindd — AD 域集成

**职责**：让 Linux 系统像本地用户一样使用 AD 域用户

```
┌─────────┐     ┌───────────┐     ┌──────────┐
│ NSS/PAM │────▶│ winbindd  │────▶│ AD 域控  │
│ (系统层)│     │ (守护进程) │     │          │
└─────────┘     └───────────┘     └──────────┘

实现：
  - libnss_winbind.so — NSS 名称服务切换
  - pam_winbind.so   — PAM 认证模块
  - idmap 映射       — Windows SID ↔ Unix UID/GID
```

```bash
# 查询域用户
wbinfo -u                    # 列出域用户
wbinfo -g                    # 列出域组
wbinfo -i "DOMAIN\\alice"   # 查询用户信息

# 测试认证
wbinfo -a "DOMAIN\\alice%password"
```

---

## 辅助工具

| 工具 | 用途 |
|------|------|
| `smbstatus` | 查看当前连接、锁、共享使用情况 |
| `testparm` | 验证 smb.conf 语法，显示生效配置 |
| `smbcontrol` | 向 smbd/nmbd/winbindd 发送控制消息 |
| `smbpasswd` | 管理本地 Samba 密码数据库 |
| `pdbedit` | 管理用户账户（比 smbpasswd 功能更全） |
| `net` | Samba 管理瑞士军刀（ADS/RPC/域操作） |
| `smbcacls` | 查看/修改 Windows ACL |
| `smbclient` | 命令行 SMB 客户端（类似 ftp） |
| `rpcclient` | 命令行 DCE/RPC 客户端 |

---

## 启动与管理

```bash
# systemd 方式（现代）
systemctl start smbd nmbd winbind
systemctl enable smbd nmbd winbind

# 查看状态
systemctl status smbd

# 重载配置（不中断连接）
smbcontrol smbd reload-config

# 优雅重启
systemctl restart smbd
```

---

> 回到 [[Samba学习索引]]
