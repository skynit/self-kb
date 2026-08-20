---
date: 2026-07-29
tags: [iwlwifi, driver, firmware, kernel, wifi, intel]
category: linux-driver
---

# iwlwifi 驱动编译安装流程

> Intel WiFi 网卡手动编译安装最新 backport 驱动 + 匹配固件

## 背景

内核自带的 iwlwifi 驱动可能版本较旧。从 Intel 官方仓库编译安装 backport 驱动可获得最新 WiFi 功能支持（如 WiFi 7 802.11be）。参考 RDC#641996。

---

## 第一步：编译安装 backport-iwlwifi 驱动

```bash
# 1. 克隆 backport 驱动仓库（master 分支即可）
git clone https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/backport-iwlwifi.git
cd backport-iwlwifi

# 2. 编译（4 线程并行）
make -j4

# 3. 安装（需要 root）
sudo make install
```

> **说明**：backport 仓库将新版内核的 iwlwifi 驱动移植到当前内核版本编译使用。

---

## 第二步：安装匹配的固件 (firmware)

驱动编译安装后，必须安装**与驱动版本匹配的固件**，否则网卡无法正常工作。

```bash
# 1. 克隆固件仓库
git clone https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/linux-firmware.git
cd linux-firmware

# 2. 复制对应固件到系统目录
#    以 BZ-B0 系列为例（具体型号根据你的网卡确认）
sudo cp iwlwifi-bz-b0-wh-b0-* /lib/firmware/

# 3. 重启生效
sudo reboot
```

---

## 第三步：验证

```bash
# 检查驱动加载
lsmod | grep iwlwifi

# 检查固件加载日志
dmesg | grep -i iwlwifi | tail -20

# 查看无线设备
iw dev
iw phy

# 确认 WiFi 7 状态（N = 已启用）
cat /sys/module/iwlwifi/parameters/disable_11be
```

---

## 固件型号确认

不同 Intel 网卡对应不同固件文件前缀，安装前先确认：

```bash
# 查看网卡型号
lspci -nnk | grep -i network

# 根据输出选择固件：
# BZ-B0 系列  → iwlwifi-bz-b0-*
# TY-GF 系列  → iwlwifi-ty-gf-*
# MA 系列     → iwlwifi-ma-*
# SO 系列     → iwlwifi-so-*
```

固件文件完整列表见 https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/linux-firmware.git

---

## 常见问题

| 问题         | 解决                                                            |
| ---------- | ------------------------------------------------------------- |
| `make` 失败  | 确认已安装 `linux-headers`、`build-essential`、`flex`、`bison`        |
| 固件加载失败     | 检查固件文件是否匹配网卡型号；`dmesg` 查看具体缺失文件                               |
| 重启后旧驱动覆盖   | 确保 `/lib/modules/$(uname -r)/updates/` 中 backport 驱动优先于内核自带驱动 |
| WiFi 7 不可用 | 检查 `disable_11be` 参数值；某些地区可能需 `iw reg set XX`                 |

---
> 相关：[[30-系统工程/04-网络与无线/90-历史会话汇总/网络与无线技术|网络与无线技术]] | [[30-系统工程/02-Linux内核与驱动/90-LDD资料库/study/kernel/04-net/06-wifi/Linux-WiFi-Stack-详解|Linux WiFi Stack 详解]]
