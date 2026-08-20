---
title: Samba 概念讲解
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: Samba
tags:
  - OS
  - Samba
  - SMB
  - 概念
---

# Samba 概念讲解

> 属于 [[Samba学习索引]] | 相关：[[SMB协议总览]]、[[Samba架构与组件]]

---

## 一句话定义

**Samba** = SMB 协议的**开源实现**，让 Linux/Unix 机器能和 Windows 机器互传文件、共享打印机、加入 AD 域。

---

## Samba 解决什么问题

```
┌──────────┐          ┌──────────┐
│ Windows  │─── SMB ──│ Windows  │  ✅ 原生互通
└──────────┘          └──────────┘

┌──────────┐          ┌──────────┐
│ Linux    │─── ??? ──│ Windows  │  ❌ 不通！Linux 不懂 SMB
└──────────┘          └──────────┘

┌──────────┐  Samba   ┌──────────┐
│ Linux    │─── SMB ──│ Windows  │  ✅ Samba 翻译 SMB ↔ POSIX
└──────────┘          └──────────┘
```

Windows 用 SMB 协议共享文件，Linux 用 POSIX 文件系统。两者"语言不通"。Samba 就是中间的**翻译官**：

- 把 Windows 的 SMB 请求翻译成 Linux 的 POSIX 文件操作
- 把 Linux 的文件/权限翻译成 Windows 能理解的格式

---

## 核心概念速查

| 概念             | 一句话解释                           |
| -------------- | ------------------------------- |
| **SMB**        | 协议——Windows 文件共享的"语言"           |
| **CIFS**       | SMB1 的别称，已弃用                    |
| **Samba**      | 软件——SMB 协议的开源实现                 |
| **smbd**       | Samba 的核心守护进程，处理文件共享和认证         |
| **nmbd**       | NetBIOS 名称服务守护进程（旧式浏览）          |
| **winbindd**   | AD 域集成守护进程，让 Linux 识别域用户        |
| **共享 (Share)** | 服务端暴露给客户端访问的目录                  |
| **IPC$**       | 特殊共享，用于 DCE/RPC 管道通信            |
| **smb.conf**   | Samba 的配置文件                     |
| **workgroup**  | 工作组名，Windows 网络中的逻辑分组           |
| **realm**      | Kerberos 领域名（AD 域环境）            |
| **pdb**        | Samba 密码数据库（passdb backend）     |
| **idmap**      | Windows SID ↔ Linux UID/GID 的映射 |
| **Oplock**     | 机会锁——客户端缓存授权，减少网络往返             |
| **VFS**        | 虚拟文件系统层，Samba 的插件机制             |

---

## Samba 能做什么

```
┌─────────────────────────────────────────────────┐
│                 Samba 功能矩阵                   │
│                                                  │
│  文件共享     Linux 目录 → Windows 网络驱动器    │
│  打印共享     Linux 打印机 → Windows 打印        │
│  域成员       Linux 加入 AD 域，域用户可登录     │
│  域控制器     Samba 替代 Windows Server 做 DC    │
│  WINS 服务器  NetBIOS 名称解析                   │
│  DFS          分布式文件系统                      │
│  ACL 映射     Windows ACL ↔ POSIX ACL            │
│  回收站       共享级回收站（VFS 插件）            │
│  审计日志     文件操作审计（VFS 插件）            │
│  病毒扫描     上传文件自动扫描（VFS 插件）        │
│  快照         Shadow Copy（VFS 插件）            │
└─────────────────────────────────────────────────┘
```

---

## Samba vs Windows SMB 服务器

| 维度      | Samba                      | Windows Server   |
| ------- | -------------------------- | ---------------- |
| 协议支持    | SMB1/2/3                   | SMB1/2/3         |
| AD 域控制器 | ✅（Samba 4+）                | ✅（原生）            |
| 文件共享    | ✅                          | ✅                |
| 打印共享    | ✅                          | ✅                |
| DFS     | ✅（有限）                      | ✅（完整）            |
| 成本      | 免费                         | 需授权              |
| 底层文件系统  | POSIX（ext4/xfs/btrfs…）     | NTFS/ReFS        |
| ACL 模型  | POSIX ACL ↔ Windows ACL 映射 | 原生 Windows ACL   |
| 性能      | 接近原生                       | 原生最优             |
| 管理      | smb.conf + CLI             | GUI + PowerShell |

---

## 共享的类型

```
┌──────────────────────────────────────────────┐
│              Samba 共享类型                    │
│                                               │
│  ① 磁盘共享 (Disk Share)                     │
│     共享一个目录，客户端像用本地磁盘一样读写   │
│     ShareType = 0x01                          │
│                                               │
│  ② 打印机共享 (Print Share)                  │
│     共享打印机，客户端发送打印任务             │
│     ShareType = 0x02                          │
│                                               │
│  ③ IPC 共享 (IPC$)                           │
│     特殊共享，用于 DCE/RPC 命名管道通信       │
│     ShareType = 0x03                          │
│     自动创建，不需要手动配置                   │
│                                               │
│  ④ ADMIN$ 共享                               │
│     Windows 管理共享，指向 %SystemRoot%       │
│     Samba 中不常用                            │
│                                               │
│  ⑤ C$ 共享                                   │
│     Windows 默认隐藏共享，指向根目录          │
│     Samba 中可配置                            │
└──────────────────────────────────────────────┘
```

---

## 认证模型

```
security = USER        独立服务器，本地 Samba 密码库认证
  │
  │  smbpasswd / pdbedit / tdbsam 管理用户
  │  适合：家庭、小公司
  │
  ▼

security = ADS         AD 域成员，认证交给域控制器
  │
  │  Kerberos 认证 + winbind 身份映射
  │  适合：企业环境
  │
  ▼

security = DOMAIN      NT4 域成员（已弃用）

server role = DC       Samba 作为 AD 域控制器
  │
  │  samba-tool 管理用户/组/策略
  │  适合：替代 Windows Server 做域控
```

---

## 密码后端（passdb backend）

| 后端          | 说明                               |
| ----------- | -------------------------------- |
| `tdbsam`    | 本地 TDB 数据库（默认，适合独立服务器，用户数 < 250） |
| `ldapsam`   | LDAP 目录（适合大规模部署）                 |
| `smbpasswd` | 旧式文本文件（已弃用）                      |
|             |                                  |

```bash
# 查看当前用户
pdbedit -L

# 添加用户
smbpasswd -a alice

# 查看用户详情
pdbedit -Lv alice
```

---

## 权限三层模型

```
┌──────────────────────────────────────────┐
│         Samba 权限三层模型                │
│                                          │
│  第一层：共享权限 (Share Permissions)     │
│    smb.conf 中的 read only / valid users │
│    控制：谁能访问这个共享                 │
│                                          │
│  第二层：文件系统权限 (POSIX Permissions) │
│    Linux 的 owner/group/other + chmod    │
│    控制：能读/写/执行哪些文件             │
│                                          │
│  第三层：Windows ACL (可选)               │
│    通过 acl_xattr VFS 插件存储           │
│    控制：更细粒度的权限（如单文件级）     │
│                                          │
│  最终权限 = 三层取交集（最严格者生效）    │
└──────────────────────────────────────────┘
```

> **常见坑**：smb.conf 允许写，但文件系统 chmod 555，结果写不了。三层都要检查。

---

## VFS 插件

Samba 通过 VFS（Virtual File System）插件扩展功能：

| 插件             | 功能                     |
| -------------- | ---------------------- |
| `acl_xattr`    | 存储 Windows ACL 为 xattr |
| `recycle`      | 回收站（删除→移到回收目录）         |
| `audit`        | 文件操作审计日志               |
| `full_audit`   | 详细审计（谁/何时/做了什么）        |
| `shadow_copy2` | 快照支持（Shadow Copy）      |
| `clamav`       | 上传文件自动病毒扫描             |
| `catia`        | 字符映射（Windows 非法字符替换）   |
| `fruit`        | macOS SMB 兼容增强         |
| `glusterfs`    | GlusterFS 后端           |
| `ceph`         | Ceph 后端                |

```ini
[data]
    vfs objects = acl_xattr recycle
    recycle:repository = .recycle
    recycle:keeptree = yes
    recycle:versions = yes
```

---

## Samba 与 NFS 的区别

| 维度   | Samba                | NFS                        |
| ---- | -------------------- | -------------------------- |
| 协议   | SMB/CIFS             | NFS (v3/v4)                |
| 客户端  | Windows + Linux      | 主要是 Linux/Unix             |
| 认证   | 用户级（用户名+密码/Kerberos） | 主机级（IP/主机名）或 Kerberos (v4) |
| 文件锁  | Oplock/Lease         | NLM / NFSv4 锁              |
| ACL  | Windows ACL          | NFSv4 ACL / POSIX ACL      |
| 打印   | 支持                   | 不支持                        |
| 适用场景 | Windows 混合环境         | 纯 Linux/Unix 环境            |

> **选择**：有 Windows 客户端 → Samba；纯 Linux 环境 → NFS 更轻量。

---

## Samba 版本演进

| 版本 | 年份 | 里程碑 |
|------|------|--------|
| 1.x | 1992 | 最初发布，Andrew Tridgell 逆向 SMB |
| 2.x | 1999 | Windows NT 域支持 |
| 3.0 | 2003 | Active Directory 域成员支持 |
| 3.6 | 2011 | SMB2 实验性支持 |
| 4.0 | 2012 | ★ AD 域控制器 + 完整 SMB2 支持 |
| 4.5 | 2016 | SMB3 加密/多通道 |
| 4.11 | 2019 | ★ 默认禁用 SMB1 |
| 4.17 | 2022 | SMB3.1.1 改进 |
| 4.21 | 2024 | 最新稳定版 |

---

> 回到 [[Samba学习索引]]
