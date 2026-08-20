---
title: CWNAS 安装器 ISO 构建与兼容性验证
created: 2026-08-11
updated: 2026-08-11
type: project/operations
tags:
  - CWNAS
  - installer
  - ISO
  - Ubuntu
  - QEMU
---

# CWNAS 安装器 ISO 构建与兼容性验证

## 主构建流程

权威构建目录是 `/home/skynit/workspace/build/cwnas-installer`。该目录包含 GUI/TUI 安装器、介质检测 helper、GRUB 主题和最新 CWNAS 服务输入 `cw/cwnas`。

进入构建目录：

```bash
cd /home/skynit/workspace/build/cwnas-installer
```

完整重建 RootFS 和 ISO：

```bash
sudo env OUT_DIR=/home/skynit/workspace/build/ubuntu/out-ubuntu ./build-ubuntu.sh all
```

RootFS 未变化时，只重新编译安装器并重制 initrd/ISO：

```bash
sudo env OUT_DIR=/home/skynit/workspace/build/ubuntu/out-ubuntu ./build-ubuntu.sh iso
```

若 Go 模块代理不稳定，使用已经准备好的本机模块代理缓存：

```bash
sudo env OUT_DIR=/home/skynit/workspace/build/ubuntu/out-ubuntu GUI_GO_PROXY_CACHE=/home/skynit/go/pkg/mod/cache/download ./build-ubuntu.sh iso
```

> [!warning] 不要混用旧构建入口
> 2026-08-10 检查时，`/home/skynit/workspace/build/ubuntu/build-ubuntu.sh` 是旧的纯 TUI 构建链，会删除 GTK/X11 运行时并只注入 `/usr/bin/cwnas-installer`。GUI 优先镜像必须从 `cwnas-installer/build-ubuntu.sh` 构建。

## 构建后验证

进入输出目录：

```bash
cd /home/skynit/workspace/build/ubuntu/out-ubuntu
```

验证 ISO sidecar：

```bash
sha256sum -c cwnas-ubuntu-26.04-amd64.iso.sha256
```

验证 RootFS sidecar：

```bash
sha256sum -c target-rootfs.squashfs.sha256
```

核对源服务与 RootFS 内服务是否字节一致：

```bash
sha256sum /home/skynit/workspace/build/cwnas-installer/cw/cwnas
```

```bash
unsquashfs -cat target-rootfs.squashfs opt/cwnas/cwnas | sha256sum
```

检查最终 ISO 的 UEFI 菜单：

```bash
7z x -so cwnas-ubuntu-26.04-amd64.iso boot/grub/grub.cfg | rg 'menuentry|cwnas.ui='
```

检查最终 ISO 的 BIOS 菜单：

```bash
7z x -so cwnas-ubuntu-26.04-amd64.iso isolinux/isolinux.cfg | rg 'menu label|cwnas.ui='
```

检查 BIOS/UEFI El Torito 记录：

```bash
xorriso -indev cwnas-ubuntu-26.04-amd64.iso -report_el_torito plain
```

## 当前产物基线

2026-08-11 10:57 的当前产物：

| 产物 | SHA-256 | 说明 |
| --- | --- | --- |
| `cwnas-ubuntu-26.04-amd64.iso` | `6e5f08fa3914e3855e00cb3cd18592ef94ee289290d446d7d0bf88dba91aeef1` | sidecar 校验通过，大小 `2266103808` bytes |
| `target-rootfs.squashfs` | `83524e185d2ba8cd106fb6211e0a6d1110815cfac5c7929230deb9545c1e8a52` | sidecar 校验通过 |
| `cw/cwnas` | `934934784b7ce59a3154c936bc310c0d0ac14ac68c6a7fe8918381605c49bf6a` | 与 squashfs 内 `/opt/cwnas/cwnas` 一致 |

RootFS 已确认包含且可执行：

- `/usr/lib/qemu/qemu-bridge-helper`
- `/usr/local/sbin/cwnas-console-status`
- `zh_CN.UTF-8` locale
- `fbterm` 与中文字体

initrd 构建现场已确认包含且可执行：

- `/usr/bin/cwnas-installer-gui`
- `/usr/bin/cwnas-installer`
- `/usr/sbin/cwnas-prepare-media`
- `/sbin/debian-installer`

## 启动模式

UEFI GRUB 与 BIOS ISOLINUX 均生成三个菜单项：

| 菜单 | 内核参数 | 行为 |
| --- | --- | --- |
| `Install CWNAS (Graphical)` | `cwnas.ui=auto` | 默认优先启动 GTK3 GUI，异常时按 supervisor 策略回退 |
| `Install CWNAS (Text/Safe Mode)` | `nomodeset cwnas.ui=tui` | 使用 fbterm/bterm/普通终端运行 TUI |
| `Rescue Shell` | `cwnas.ui=rescue` | 直接进入 initrd 救援 shell |

启动 supervisor 位于 `initrd-assets/debian-installer`。`UI_MODE` 默认值是 `auto`，不是纯 TUI。

## 安装介质检查与超时

当前检查预算形成明确余量：

```text
cwnas-prepare-media --wait-seconds 50
Service.prepareMedia context 55s
GUI inspectionTimeout 60s
```

即 helper `50s` < Service `55s` < GUI `60s`。GUI 的“检查安装环境”步骤使用 60 秒总超时，避免 Service 即将完成时被外层提前取消。

`cwnas-prepare-media` 使用只读挂载、只读 loop、`flock` 和 `timeout --kill-after=2s`。匹配的 ISO loop 场景会保留 `/cdrom`、loop 和 `/iso-holder`，供安装磁盘检测通过 backing file 回溯宿主介质。

## Go chroot 构建故障

### `failed to start telemetry sidecar`

典型日志：

```text
failed to start telemetry sidecar: os.Executable: readlink /proc/self/exe: no such file or directory
```

原因是构建 root 中没有可用的 `/proc/self/exe`。该消息是 Go telemetry 的非致命警告；实际成功构建中也出现过，不能单独判定构建失败。

### `proxy.golang.org ... EOF`

典型日志：

```text
go: github.com/atotto/clipboard@v0.1.4: Get "https://proxy.golang.org/...": EOF
```

这是 `go mod download` 的网络传输中断，才是该次构建停止的直接原因。默认 `GOPROXY=https://proxy.golang.org,direct` 使用逗号分隔，遇到 `EOF` 不保证回退到 `direct`。优先通过 `GUI_GO_PROXY_CACHE` 使用本机 `cache/download`，避免 remaster 阶段依赖公网即时下载。

含“复制 GUI 源码到 Debian 构建 root”的中文日志来自 `build-debian.sh`；Ubuntu 构建链的对应日志以 `[gui-build]` 开头。定位故障时先确认执行的是哪一个脚本。

## 控制台约定

- tty1 运行中文状态页；tty2 保留标准 `getty@tty2` 登录环境。
- 状态页通过 `fbterm` 显示中文，字段和绿色边框使用 ANSI 固定列定位，不能再用 shell 的 `%-Ns` 或 `${#字符串}` 计算中文显示宽度。
- `Alt+F2`/`Alt+F1` 用于 tty2/tty1 切换。
- 已移除会在 fbterm 中显示为 `008;start=...` 的 systemd OSC profile。
- Logo 不属于终端对齐修复范围，不能因字体/边框修复而替换。

## VM 兼容性边界

此前差分矩阵针对旧 ISO 哈希 `a17b135064ec695420d4923f3236ba4b5420bc92d3527c0064defa616636b303`，结论只能作为当前镜像的回归测试输入：

- 1 GiB BIOS：CWNAS 与 fnOS 均失败，但症状不同。
- 2 GiB BIOS：CWNAS 出现 `No working init found`，fnOS 可进入 GUI。
- 4 GiB：已在旧镜像上验证 BIOS、UEFI Secure Boot off、只读 CD、只读 raw USB、PS/2 鼠标、late USB mouse hotplug 和混合存储枚举。
- CWNAS 磁盘页曾显示并默认高亮 `/dev/fd0` 4 KiB，为待回归的可用性问题。

> [!important] 当前 ISO 尚未完成同一套 VM 回归
> 当前哈希 `6e5f08fa...aeef1` 与旧测试镜像不同。不能把旧镜像的 VM、真实 USB、Ventoy、Secure Boot enforcing 或完整安装结论直接声明为当前产物已通过。

## 发布前剩余门禁

1. 对当前固定 ISO 哈希重新执行 BIOS 与 UEFI GUI-first 启动检查。
2. 重测 2 GiB/4 GiB 内存阈值并保留 early-boot 证据。
3. 检查 TUI、Rescue 菜单实际进入对应模式。
4. 验证真实 USB/Ventoy、物理输入设备和 OEM firmware。
5. 在受控目标盘上完成安装、重启和 RootFS 身份校验。
6. Secure Boot 只有同时证明 `SecureBoot=1` 且 `SetupMode=0` 才能记为 enforcing PASS。

