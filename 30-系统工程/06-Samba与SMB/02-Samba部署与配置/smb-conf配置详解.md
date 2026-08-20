---
title: smb.conf 配置详解
created: 2026-06-23
updated: 2026-06-23
type: reference
domain: Samba
tags:
  - OS
  - Samba
  - smb.conf
  - 配置
---

# smb.conf 配置详解

> 属于 [[Samba学习索引]] | 相关：[[Samba架构与组件]]、[[Samba部署场景]]

---

## 文件位置

| 发行版 | 路径 |
|--------|------|
| Debian/Ubuntu | `/etc/samba/smb.conf` |
| RHEL/CentOS | `/etc/samba/smb.conf` |
| Arch | `/etc/samba/smb.conf` |

验证语法：`testparm`

---

## 配置结构

```ini
[global]           # 全局设置（必选）
    key = value

[share_name]       # 共享定义（可多个）
    key = value

[homes]            # 用户主目录共享（特殊段）
    key = value

[printers]         # 打印机共享（特殊段）
    key = value
```

---

## [global] 关键指令速查

### 身份与协议

| 指令                    | 默认值          | 说明         |
| --------------------- | ------------ | ---------- |
| `workgroup`           | WORKGROUP    | 工作组/域名     |
| `netbios name`        | 主机名          | NetBIOS 名称 |
| `server string`       | Samba %v     | 服务器描述      |
| `server min protocol` | SMB2 (4.11+) | 最低协议版本     |
| `server max protocol` | SMB3         | 最高协议版本     |

> **安全必设**：`server min protocol = SMB2`，拒绝 SMB1 客户端。

### 认证模式

| 指令 | 可选值 | 说明 |
|------|--------|------|
| `security` | USER / ADS / DOMAIN | USER=独立服务器，ADS=AD域成员，DOMAIN=NT4域 |
| `encrypt passwords` | yes | **必须 yes**（SMB2/3 强制） |
| `map to guest` | Never / Bad User / Bad Password | 访客映射策略 |
| `guest account` | nobody | 访客账户 |
| `kerberos method` | secrets only / system keytab / dedicated keytab / secrets and keytab | Kerberos 密钥获取方式 |

### 日志与调试

| 指令 | 默认值 | 说明 |
|------|--------|------|
| `log level` | 1 | 全局日志级别 0-10 |
| `log file` | /var/log/samba/log.%m | %m=客户端主机名 |
| `max log size` | 5000 | KB，超限轮转 |
| `debug pid` | no | 日志中包含 PID |
| `debug uid` | no | 日志中包含 UID |

**分级调试**：`log level = 1 auth:10 smb2:5 vfs:3`
- `auth:10` = 认证相关日志最高级别
- `smb2:5` = SMB2 协议中等级别
- `vfs:3` = VFS 层基本信息

### 性能优化

| 指令 | 默认值 | 说明 |
|------|--------|------|
| `socket options` | — | TCP_NODELAY IPTOS_LOWDELAY SO_RCVBUF=131072 SO_SNDBUF=131072 |
| `use sendfile` | yes | Linux 零拷贝读 |
| `strict locking` | auto | 文件锁策略 |
| `aio read size` | 1 | 异步读阈值（KB），0=禁用 |
| `aio write size` | 1 | 异步写阈值 |

### ID 映射（域环境必需）

```ini
# 默认映射（本地用户）
idmap config * : backend = tdb
idmap config * : range = 3000-7999

# AD 域映射
idmap config MYDOMAIN : backend = rid
idmap config MYDOMAIN : range = 10000-999999

# 或用 ad 后端（需在 AD 中预设 uidNumber/gidNumber）
idmap config MYDOMAIN : backend = ad
idmap config MYDOMAIN : range = 10000-999999
```

| 后端 | 说明 |
|------|------|
| `tdb` | 本地数据库，适合独立服务器 |
| `rid` | 用 Windows RID 算法映射，无需 AD 预设 |
| `ad` | 读 AD 中的 uidNumber/gidNumber 属性 |
| `autorid` | 自动化 rid 映射，适合多域 |

### SMB3 特性

| 指令 | 默认值 | 说明 |
|------|--------|------|
| `server smb encrypt` | disabled | off / desired / required / mandatory |
| `server multi channel support` | no | SMB3 多通道 |
| `server signing` | auto | mandatory / auto / disabled |

---

## 共享段关键指令

```ini
[data]
    path = /srv/data              # 共享路径（必须）
    read only = no                # 读写 / 只读
    browseable = yes              # 网上邻居可见
    valid users = @group1 alice   # 允许访问的用户/组
    invalid users = bob           # 禁止访问的用户
    writable = yes                # 同 read only = no
    create mask = 0664            # 新文件权限
    directory mask = 0775         # 新目录权限
    force user = www-data         # 强制以该用户身份写文件
    force group = www-data        # 强制以该组身份写文件
    veto files = /*.exe/*.dll/    # 禁止上传的文件类型
    vfs objects = acl_xattr       # VFS 插件（POSIX ACL）
```

### 特殊共享

```ini
# IPC$ — 自动创建，DCE/RPC 管道
[IPC$]
    path = /tmp

# print$ — 打印机驱动
[print$]
    path = /var/lib/samba/drivers

# netlogon — AD 域登录脚本
[netlogon]
    path = /srv/samba/netlogon
```

---

## 变量替换

| 变量 | 含义 |
|------|------|
| `%U` | 用户名 |
| `%G` | 主组名 |
| `%H` | 用户主目录 |
| `%m` | 客户端 NetBIOS 名 |
| `%I` | 客户端 IP |
| `%L` | 服务器 NetBIOS 名 |
| `%v` | Samba 版本 |
| `%T` | 当前日期时间 |
| `%S` | 当前共享名 |

---

## 常用配置模板

### 独立文件服务器

```ini
[global]
    workgroup = WORKGROUP
    server min protocol = SMB2
    security = USER
    map to guest = Bad User

[public]
    path = /srv/samba/public
    read only = no
    guest ok = yes
    create mask = 0664
    directory mask = 0775
```

### AD 域成员

```ini
[global]
    workgroup = MYDOMAIN
    realm = MYDOMAIN.COM
    security = ADS
    server min protocol = SMB2
    kerberos method = secrets and keytab
    idmap config * : backend = tdb
    idmap config * : range = 3000-7999
    idmap config MYDOMAIN : backend = rid
    idmap config MYDOMAIN : range = 10000-999999

[data]
    path = /srv/samba/data
    read only = no
    valid users = @"MYDOMAIN\Domain Users"
```

---

> 回到 [[Samba学习索引]]
