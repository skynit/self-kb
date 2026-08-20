---
date: 2026-07-14
tags: [opencode, linux, 系统管理]
category: linux
---

# Linux 系统管理与命令

> 来源：OpenCode 历史会话 | ~25 条

## dmesg 命令

查看内核环形缓冲区日志（启动诊断消息）。内核用固定大小的循环日志区，通过 `printk` 写入，`/dev/kmsg` 或 `dmesg` 读取。

- `-i`：过滤严重级别
- `-T`：显示可读时间戳
- 缓冲区是内核态环形数据结构，大小编译时确定

## 线程与进程创建

`clone()` 系统调用通过不同标志位区分创建进程还是线程：

| 标志 | 含义 |
|------|------|
| `CLONE_VM` | 共享地址空间（线程） |
| `CLONE_THREAD` | 创建线程而非进程 |
| `CLONE_FS` / `CLONE_FILES` | 共享文件系统信息/文件描述符 |

`SIGCHLD`：子进程退出时发给父进程的信号。

## setfacl 与文件 ACL 权限

```bash
setfacl -m u:<user>:rwx <file>   # 设置 ACL
getfacl <file>                     # 查看 ACL
```

未找到命令需安装 `acl` 包。ACL 在传统 rwx 模型之上提供用户级别的细粒度权限。

## systemctl 原理

- `systemctl enable`：在 `/etc/systemd/system/` 创建符号链接
- `systemctl status` 中：
  - `preset`：发行版预设策略（enabled/disabled）
  - `active`：当前运行状态
- `systemctl reload` vs `restart`：reload 在线重载配置，restart 完全重启

## Linux 文件系统大小写敏感

- ext4 / XFS：大小写敏感
- NTFS / FAT：默认不敏感，取决于挂载选项
- `ls` 区分大小写，`find -iname` 忽略大小写

## POSIX 与文件系统

Linux 文件系统底层遵循 POSIX 标准，提供统一接口：`open` / `read` / `write` / `close`。具体实现因文件系统类型而异（ext4、btrfs、xfs 各有优化），但 API 层一致。

## ZRAM 与 Swap

- `1:1 zram`：压缩比 1:1
- zram：在 RAM 中创建压缩块设备用作 swap，比硬盘 swap 快得多
- 查看 swap：`swapon --show` / `free -h`
- 创建 swap 文件：
  ```bash
  fallocate -l 4G /swapfile
  mkswap /swapfile
  swapon /swapfile
  echo '/swapfile none swap defaults 0 0' >> /etc/fstab
  ```

## passwd 与用户管理

- `passwd`：修改密码
- `userdel -r <user>`：删除用户及家目录
- 批量创建 1000 个用户：取决于 PAM 模块和磁盘 IO，一般不卡死，但建议脚本化

## 端口占用排查

```bash
ss -tlnp              # 查看所有监听端口
lsof -i :<port>        # 查看特定端口
fuser <port>/tcp       # 查看占用端口的进程 PID
```

## tar.gz 解压

```bash
tar -xzf file.tar.gz
# -x: 解压  -z: gzip  -f: 文件
```

## Shell 脚本编写

- 首行 `#!/bin/bash`
- `chmod +x script.sh` 赋予执行权限
- 注意路径和环境变量
- 服务器上推荐使用绝对路径

## 终端转义序列乱码

终端无法解析 ANSI 转义序列时将原始控制字符打印出来。原因：
- `TERM` 环境变量不正确
- 程序输出的转义序列不被当前终端支持

## SSH 远程与密钥

```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
ssh-keygen -t ed25519 -C "comment"
```

- 上传文件：`scp localfile user@host:/path`
- 下载文件：`scp user@host:/path localfile`
- 确保 sshd 服务运行且防火墙放行 22 端口

## 系统休眠后程序行为

- suspend (S3)：内存保持供电，进程暂停后恢复
- hibernate (S4)：内存写入磁盘，恢复后进程继续
- 网络连接可能超时断开
- 定时任务可能错过触发时间（需 anacron 补充）

## 合盖/锁屏发热

合盖后电脑持续发热：系统未正确挂起/休眠。检查：
- `/etc/systemd/logind.conf` 中 `HandleLidSwitch=suspend`
- 电源管理设置
- `dmesg` 检查 suspend 相关错误

## power-profiles-daemon

Linux freedesktop 标准的电源配置管理。三种模式：
- `power-saver`：节能
- `balanced`：平衡
- `performance`：性能

切换：`powerprofilesctl set <mode>`

## 电池电流单位

`/sys/class/power_supply/BAT0/current_now` 单位：微安（μA）。
`1254000` = 1254 mA = 1.254 A。

## 任务管理器消失

桌面面板进程崩溃，重启面板：
- XFCE：`xfce4-panel -r`
- KDE：`plasmashell --replace`
- GNOME：Alt+F2 输入 `r` 重启 shell
