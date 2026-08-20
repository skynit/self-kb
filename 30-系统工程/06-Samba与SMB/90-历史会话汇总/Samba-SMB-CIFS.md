---
date: 2026-07-14
tags: [opencode, samba, smb, cifs, 文件共享]
category: samba
---

# Samba / SMB / CIFS 文件共享

> 来源：OpenCode 历史会话 | ~30 条

## CIFS 挂载

### 基本命令

```bash
mount -t cifs //server/share /mnt -o username=user,vers=3.0
```

### 错误排查

| 错误 | 原因 | 解决 |
|------|------|------|
| `mount error 13 Permission denied` | 凭据或 `sec` 选项不匹配 | 检查用户名密码、指定 `sec=ntlmssp` |
| `filesystem not supported` | 未安装 cifs-utils | `pacman -S cifs-utils` |
| `SMB 503 / RPC 服务器不可用` | 服务端 rpcbind 或 SMB 异常 | 检查服务端 SMB 服务状态 |

### 挂载选项

```bash
-o username=user,password=pass,uid=$(id -u),gid=$(id -g),vers=3.0,iocharset=utf8
```

## Samba 服务管理

- `smbd`：SMB 协议核心守护进程
- `nmbd`：NetBIOS 名称解析
- `smbd vs samba` 服务单元：smbd 是守护进程，samba 是 systemd 单元名，不应重复

```bash
systemctl start smb       # 启动
systemctl enable smb      # 开机自启
systemctl status smb      # 状态
systemctl reload smb      # 在线重载配置
```

## Samba 配置重载

```bash
smbcontrol all reload-config    # 通知所有 smbd 进程重载
systemctl reload smb             # systemd 方式
```

## smb.conf 配置要点

- `%Y`：Samba 宏变量，展开为客户端 DNS 主机名
- `force user = root`：所有共享以 root 身份操作（内网信任环境，不安全）
- `read only = no` / `writable = yes`：开启读写
- 全局 root 权限配置：

```ini
[global]
force user = root
force group = root
```

## smbstatus 与审计

```bash
smbstatus              # 当前连接、锁定、打开文件
smbstatus -v           # 详细信息
```

`full_audit` 模块可记录文件访问审计日志。

## pdbedit 与 smbpasswd

```bash
pdbedit -L                  # 列出所有 Samba 用户
pdbedit -x <user>           # 删除用户
pdbedit -a <user>           # 添加用户
smbpasswd -d <user>         # 禁用（disable）用户
smbpasswd -a <user>         # 添加用户
```

## wsdd / wsdd2 (Web Service Discovery)

- 实现 WS-Discovery 协议，让 Samba 在 Windows"网络"中可见
- wsdd2：轻量级实现
- 端口：3702（组播）、5357（HTTP）
- 安装：`yay -S wsdd2`

```bash
systemctl enable wsdd2
systemctl start wsdd2
```

## FTP 与 FTPS

- 挂载 FTP：`curlftpfs ftp://user:pass@host /mnt`
- 本机启动 FTP 服务：`vsftpd`、`proftpd`、`pure-ftpd`
- **显式 FTPS** (FTPES)：通过 AUTH TLS 命令在同端口（21）升级加密
- **隐式 FTPS**：默认 TLS 的 990 端口
- FTP 权限类型：
  - `upload_only`：允许上传操作（STOR/APPE/MKD），禁止下载（RETR）和删除（DELE/RMD）

## DACL (自由访问控制列表)

定义谁能访问资源的 Windows 安全模型。`windowsShareSecurityDescriptor` 函数用于构造 Windows 共享的安全描述符结构（DACL/SACL）。

## ACL 权限（Linux 端）

```bash
setfacl -m u:<smb_user>:rwx <path>   # 为 SMB 用户授权
getfacl <path>                         # 查看
```

## Samba 查询用户命令

```bash
pdbedit -L                # 列出 Samba 用户
pdbedit -L -v             # 详细信息
smbpasswd -e <user>       # 启用用户
smbpasswd -d <user>       # 禁用用户
```

## smbstatus 日志分析

- 通过 smbstatus 查看当前连接、文件锁定
- full_audit 模块记录所有文件操作
- 配合 journalctl 查看 Samba 服务日志
