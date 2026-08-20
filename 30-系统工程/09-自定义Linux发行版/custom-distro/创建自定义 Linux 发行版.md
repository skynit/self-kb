---
tags:
  - linux
  - archlinux
  - custom-distro
  - awesome-wm
  - automation
created: 2026-05-31
source: "https://www.youtube.com/watch?v=pZcbHdBs_TE"
channel: "Chris Titus Tech"
---

# 创建自定义 Linux 发行版（Arch Linux）

> 基于 Chris Titus Tech 直播整理。使用 Arch Linux 从零构建一个零冗余、完全定制的系统，窗口管理器选用 Awesome WM，自动化脚本为 archmatic。

---

## 1. 核心理念

### 三条主线

Chris Titus 认为，Linux 发行版本质上只有**三条主线**：

| 主线 | 包管理 | 代表发行版 |
|------|--------|-----------|
| **Debian** | apt / dpkg | Ubuntu, Mint, Pop!_OS |
| **Arch** | pacman | Manjaro, EndeavourOS |
| **Red Hat** | dnf / rpm | Fedora, CentOS, RHEL |

> 所有其他发行版都是这三条线的衍生版本，差异主要在于**定制化程度**。

### 选择 Arch 的理由

| 特点 | 说明 |
|------|------|
| **学习价值高** | 手动安装每个组件，深入理解系统架构 |
| **零冗余** | 每个包都明确安装，不会有不需要的预装软件 |
| **滚动更新** | 始终保持最新版本 |
| **AUR** | Arch User Repository，几乎无所不包 |

### Arch vs LFS（Linux From Scratch）

|      | Arch       | LFS      |
| ---- | ---------- | -------- |
| 内核   | 预编译内核包     | 手动编译内核   |
| 包管理器 | pacman（内置） | 需自行搭建    |
| 难度   | 中等（有文档）    | 极高（从零开始） |
| 适合   | 学习 + 日常使用  | 极客探索     |

---

## 2. archmatic 项目

GitHub 仓库：`Chris-Tech-Git/archmatic`

### 设计原则

```
脚本分阶段执行（便于调试）
  ├── Stage 0: Pre-install（磁盘分区、基础系统安装、bootloader）
  ├── Stage 1: Setup（用户创建、基础配置）
  ├── Stage 2: Base（Xorg 显示服务）
  ├── Stage 3: Software（软件包安装）
  ├── Stage 4: Post-install（最终配置）
  └── Stage 5: Desktop（窗口管理器 / 桌面环境配置）
```

> 分阶段的原因：**便于独立调试**，某个阶段失败不用从头来。最终目标是合并为一键脚本。

### 使用方式

```bash
# 安装基础工具
pacman -Syyy
pacman -S git curl wget

# 克隆仓库（两次：一次在安装介质，一次在安装后的系统）
git clone https://github.com/Chris-Tech-Git/archmatic
cd archmatic

# 运行预安装脚本（格式化磁盘！仅在虚拟机或测试机上运行）
bash 0-preinstall.sh
```

> **警告**：预安装脚本会**格式化指定磁盘**，不要在主力机上运行！

---

## 3. 构建流程详解

### Stage 0 — Pre-install（磁盘与引导）

```
自动执行：
├── 磁盘格式化（sgdisk 分区工具）
│   ├── EFI 分区
│   └── 根分区（ext4）
├── 基础系统安装（pacstrap）
├── fstab 生成
├── chroot 进入新系统
└── systemd-boot 安装
```

#### systemd-boot 配置

systemd-boot 比 GRUB 更轻量，但需要手动配置：

```bash
# 安装
bootctl install

# 创建启动项 /boot/loader/entries/arch.conf
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=UUID=<磁盘UUID> rw
```

**关键验证步骤**：

```bash
# 确认内核文件存在
ls /boot/vmlinuz-linux
ls /boot/initramfs-linux.img

# 确认 UUID 正确
blkid /dev/sda2
```

> 如果看到 `amd-ucode.img` 或 `intel-ucode.img`，需要在 loader entry 中添加对应的 `initrd` 行。

#### loader.conf

```ini
# /boot/loader/loader.conf
default arch.conf
timeout 3
console-mode max
editor  no    # 禁用编辑器（安全考虑）
```

### Stage 1 — Setup（用户与基础配置）

```
执行内容：
├── 创建用户账号
├── 设置主机名
├── 配置 sudo 权限
├── 设置时区和 locale
└── 配置网络
```

### Stage 2 — Base（Xorg 显示服务）

```bash
# 安装 X Window System
pacman -S xorg-server xorg-xinit xorg-xrandr ...
```

> 这是图形界面的基础，后续所有窗口管理器和桌面环境都依赖它。

### Stage 3 — Software（软件安装）

采用**分片模式**，每个脚本安装一类软件：

```
software/
├── 1-software-pacman.sh    # 官方仓库软件
├── 2-software-aur.sh       # AUR 软件
└── 3-software-flatpak.sh   # Flatpak 软件（可选）
```

#### Chris Titus 的软件选择

| 类别 | 选择 | 理由 |
|------|------|------|
| 窗口管理器 | **Awesome WM** | 平铺式、高度可定制、Lua 配置 |
| 终端 | Terminator | 分屏功能 |
| 文件管理器 | PCManFM | 轻量 |
| 浏览器 | Firefox / Chromium | — |
| 编辑器 | VS Code | — |
| 电源管理 | XFCE Power Manager | 轻量但功能完整 |
| 外观设置 | LXAppearance | GTK 主题切换 |
| 合成器 | Picom | 透明度和阴影效果（替代已废弃的 Compton） |
| 内核 | linux + linux-lts | LTS 内核作为备份 |

> **包数量控制目标**：不超过 1000 个包。最终约 800 个包，内存占用约 200MB。

### Stage 4 — Post-install（最终配置）

```
执行内容：
├── 启用 systemd 服务（NetworkManager、bluetooth 等）
├── 配置 shell（zsh/bash + alias）
├── 配置字体
└── 其他个性化设置
```

### Stage 5 — Desktop（窗口管理器配置）

```bash
# 克隆 Awesome WM 配置
git clone https://github.com/Chris-Tech-Git/material-awesome
mv material-awesome ~/.config/awesome

# 重启 Awesome
awesome restart
```

> Chris Titus 使用自定义的 **Material Awesome** 主题，基于 Material Design 风格。

---

## 4. 构建结果

### 最终系统指标

| 指标 | 数值 |
|------|------|
| 包数量 | ~800 |
| 内存占用 | ~200 MB |
| 磁盘占用 | 极小（无桌面环境） |
| 启动速度 | 极快 |

### 系统架构

```
Arch Linux Base
├── systemd-boot（引导）
├── Xorg（显示）
├── Awesome WM（窗口管理）
│   ├── Material Awesome 主题
│   ├── Picom（合成器）
│   └── Rofi / dmenu（启动器）
├── Terminator（终端）
├── Firefox（浏览器）
├── VS Code（编辑器）
└── XFCE Power Manager（电源管理）
```

---

## 5. 定制化策略

### 选择软件的原则

```
✓ 安装明确需要的单个组件
✗ 安装整个桌面环境（引入大量依赖）

例如：
✓ xorg-server + awesome + picom + lxappearance    → 200MB 内存
✗ gnome 或 kde-full                                → 1-2GB 内存
```

### Fork 与定制

Chris Titus 鼓励用户 fork archmatic 仓库并自行修改：

```bash
# Fork 后修改软件包列表
vim 3-software-pacman.sh
# 添加/删除你不需要的包

# 修改 Awesome WM 配置
vim ~/.config/awesome/rc.lua
```

### 从其他桌面环境提取组件

即使不用完整桌面环境，也可以提取单个好用的组件：

| 来源 | 提取的组件 | 用途 |
|------|-----------|------|
| XFCE | xfce4-power-manager | 电源管理 |
| XFCE | xfce4-terminal | 终端（可选） |
| GNOME | nautilus | 文件管理器（可选） |
| LXDE | lxappearance | 外观配置 |

---

## 6. systemd-boot 排错

### 常见问题

| 症状 | 原因 | 解决 |
|------|------|------|
| 启动后黑屏 | loader entry 中 UUID 错误 | `blkid` 获取正确 UUID |
| 找不到内核 | `/boot/vmlinuz-linux` 不存在 | 确认 `linux` 包已安装 |
| 启动项不显示 | `entries/` 目录为空 | 手动创建 `.conf` 文件 |
| UEFI 启动失败 | EFI 分区未正确挂载 | 检查 `/boot` 或 `/efi` 挂载点 |

### 验证清单

```bash
# 1. 检查 EFI 分区
ls /boot/EFI/

# 2. 检查 loader 配置
cat /boot/loader/loader.conf

# 3. 检查启动项
cat /boot/loader/entries/arch.conf

# 4. 验证文件存在
ls /boot/vmlinuz-linux
ls /boot/initramfs-linux.img

# 5. 验证 UUID
blkid
```

---

## 7. 经验总结

### 直播中遇到的问题

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| systemd-boot 无启动项 | `bootctl install` 未自动生成 entries | 手动创建 `/boot/loader/entries/arch.conf` |
| Awesome WM 无配置 | 忘记 git clone 配置文件 | 手动克隆 `material-awesome` 到 `~/.config/awesome` |
| 透明效果不生效 | Compton 已废弃 | 改用 Picom |

### 关键教训

1. **先分阶段调试，再合并为脚本** — 一次性脚本出错无法定位问题
2. **始终保留 LTS 内核** — Arch 滚动更新偶尔会出问题，LTS 是安全网
3. **安装介质 ≠ 安装后的系统** — 需要在两个环境中分别克隆脚本
4. **验证每一步的输出** — `ls`、`cat`、`blkid` 确认文件存在和内容正确

---

## 参考

- 视频来源：[Creating Your Own Linux Distribution](https://www.youtube.com/watch?v=pZcbHdBs_TE)（Chris Titus Tech）
- archmatic 仓库：`https://github.com/Chris-Tech-Git/archmatic`
- Material Awesome 配置：`https://github.com/Chris-Tech-Git/material-awesome`
- Arch Wiki（系统安装）：`https://wiki.archlinux.org/title/Installation_guide`
- Arch Wiki（systemd-boot）：`https://wiki.archlinux.org/title/Systemd-boot`
