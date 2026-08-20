---
title: Samba 调试与排错
created: 2026-06-23
updated: 2026-06-23
type: reference
domain: Samba
tags:
  - OS
  - Samba
  - 调试
  - Wireshark
  - 排错
---

# Samba 调试与排错

> 属于 [[Samba学习索引]] | 相关：[[Samba架构与组件]]、[[smb-conf配置详解]]

---

## 排错工具箱

| 工具           | 用途       | 命令示例                                   |
| ------------ | -------- | -------------------------------------- |
| `testparm`   | 验证配置语法   | `testparm -s`                          |
| `smbstatus`  | 查看连接/锁   | `smbstatus -b`                         |
| `smbclient`  | 测试连接     | `smbclient -L server -U alice`         |
| `wbinfo`     | 测试 AD 集成 | `wbinfo -t`                            |
| `net`        | 域操作      | `net ads testjoin`                     |
| `journalctl` | 看日志      | `journalctl -u smbd -f`                |
| `Wireshark`  | 抓包分析     | 过滤: `tcp.port == 445`                  |
| `strace`     | 系统调用追踪   | `strace -p <PID> -f -e trace=file`     |
| `tcpdump`    | 抓包       | `tcpdump -i eth0 port 445 -w smb.pcap` |

---

## 常见错误排查

### 1. 连接被拒

```
Error: NT_STATUS_CONNECTION_REFUSED
```

```
检查清单：
  □ smbd 是否运行？    → systemctl status smbd
  □ 端口是否监听？    → ss -tlnp | grep 445
  □ 防火墙是否放行？  → iptables -L -n | grep 445
  □ SELinux 是否阻止？ → getenforce + ausearch -m avc
```

### 2. 认证失败

```
Error: NT_STATUS_LOGON_FAILURE
```

```
检查清单：
  □ 用户是否存在？    → pdbedit -L | grep alice
  □ 密码是否正确？    → smbclient -L localhost -U alice
  □ 密码后端是否正确？→ testparm -s | grep "passdb backend"
  □ AD 域是否正常？   → wbinfo -t
  □ Kerberos 票据？   → klist
  □ 时间是否同步？    → ntpdate -q dc1  (Kerberos ±5min)
```

### 3. 权限被拒

```
Error: NT_STATUS_ACCESS_DENIED
```

```
检查清单：
  □ 共享权限？        → testparm -s | section data
  □ valid users？     → 共享段 valid users 配置
  □ 文件系统权限？    → ls -la /srv/samba/data
  □ SELinux 上下文？  → ls -laZ /srv/samba/data
  □ create mask？     → 共享段 create mask / directory mask
```

### 4. 协议版本不匹配

```
Error: NT_STATUS_INVALID_NETWORK_RESPONSE
```

```
检查清单：
  □ 客户端支持的协议 ≥ server min protocol？
  □ smb.conf 中 server min/max protocol 设置
  □ 用 smbclient 测试: smbclient -L server -m SMB3
```

---

## Wireshark 抓包分析

### 过滤器

```
# 基础过滤
tcp.port == 445

# 只看 SMB2
smb2

# 特定命令
smb2.cmd == 0x0000    # NEGOTIATE
smb2.cmd == 0x0001    # SESSION_SETUP
smb2.cmd == 0x0005    # CREATE
smb2.cmd == 0x0008    # READ

# 错误响应
smb2.flags.response == 1 && smb2.nt_status != 0x00000000

# 特定会话
smb2.session_id == 0x0000000000000001
```

### 关键分析点

1. **NEGOTIATE** → 看双方协商的方言版本和能力
2. **SESSION_SETUP** → 看 SPNEGO token 是否正常，认证几轮完成
3. **CREATE** → 看 DesiredAccess 和 CreateDisposition
4. **错误响应** → NT Status 码直接说明问题

### NT Status 常见码

| 状态码 | 含义 |
|--------|------|
| `0x00000000` | SUCCESS |
| `0xC0000016` | MORE_PROCESSING_REQUIRED（认证中间步骤，正常） |
| `0xC000005E` | NO_LOGON_SERVERS（域控不可达） |
| `0xC000006D` | LOGON_FAILURE（用户名或密码错） |
| `0xC000006E` | ACCOUNT_RESTRICTION（账户受限） |
| `0xC0000224` | PASSWORD_MUST_CHANGE（密码过期） |
| `0xC0000022` | ACCESS_DENIED（权限不足） |

---

## 日志级别指南

```ini
# 快速排错
log level = 3

# 认证问题
log level = 3 auth:10

# 协议问题
log level = 3 smb2:10

# 全量调试（性能差，仅排查时用）
log level = 10

# 分进程日志
log file = /var/log/samba/log.%m
```

---

## 性能排查

```bash
# 1. 查看连接数
smbstatus -b | wc -l

# 2. 查看锁争用
smbstatus -L

# 3. I/O 统计
smbstatus -S

# 4. 检查异步 I/O
testparm -s | grep aio

# 5. 网络延迟
ping server
traceroute server

# 6. 磁盘瓶颈
iostat -x 1 5
```

---

> 回到 [[Samba学习索引]]
