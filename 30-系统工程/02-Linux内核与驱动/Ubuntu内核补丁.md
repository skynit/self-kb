
> 适用目标：CWNAS 上的 Intel Wi-Fi 7 BE213。
>
> Ubuntu 26.04 优先使用发行版官方的 `backport-iwlwifi-dkms` 或预编译签名模块；只有需要更新能力时才从 Intel 官方 `backport-iwlwifi` 和 `linux-firmware` 仓库编译安装。
>
> 每条命令均可单独执行，便于不支持多行粘贴的终端使用。

## 0. Ubuntu 26.04 官方包主流程

本节适用于 Ubuntu 26.04 Resolute。`bz-b0-wh-b0` 公开固件从 `c102` 开始，Ubuntu `7.0.0-14/28/29-generic` 的内核自带驱动最高只接受 `c101`，因此只更新 `linux-firmware` 不能解决问题。

### 0.1 已验证路径：Ubuntu 官方 DKMS 包

从 Ubuntu 26.04 官方软件源安装：

```bash
sudo apt update
```

```bash
sudo apt install backport-iwlwifi-dkms
```

2026-08-12 的 Resolute Updates 候选版本为：

```text
1:0~103.14434-gitdf6b5bf4-0ubuntu3.26.04.2
```

不需要额外添加 PPA，也不应安装其他 Ubuntu 发行版的 `0~104...` 包。

这个 DKMS 包会替换整套无线模块，不只是 `iwlwifi`：

```text
cfg80211 mac80211 iwlwifi iwlmvm iwlmld iwldvm iwlwifi_compat
```

### 0.2 签名替代方案：当前内核的预编译模块

重视 Secure Boot 和模块签名时，先确认当前内核存在对应软件包：

```bash
apt-cache policy linux-main-modules-iwlwifi-$(uname -r)
```

候选版本不为空时可安装：

```bash
sudo apt install linux-main-modules-iwlwifi-$(uname -r)
```

该软件包由 Ubuntu `linux-main-signed` 提供。已检查的 `7.0.0-14-generic` 对应包由 `Canonical Ltd. Kernel Module Signing` 签名，驱动支持到 `c103`；目标机尚未完成从 DKMS 切换后的运行验证。

不要同时保留 DKMS 版本来判断签名模块是否生效；`/lib/modules/<内核>/updates/dkms/` 通常具有更高模块优先级。

### 0.3 重启并验证

```bash
sudo reboot
```

```bash
modinfo -n iwlwifi
```

```bash
sudo journalctl -k -b --no-pager | grep -i iwlwifi
```

```bash
iw phy
```

```bash
iw dev
```

DKMS 路径的已验证成功日志为：

```text
Loading modules backported from iwlwifi
iwlwifi-stack-public:release/core103:14434:6861514a
loaded firmware version 103.1a5cc88d.0 bz-b0-wh-b0-c103.ucode op_mode iwlmld
wlo1: renamed from wlan0
```

### 0.4 ZFS 与稳定性检查

依赖 ZFS 的系统不建议为了 Wi-Fi 安装非 Ubuntu Mainline Linux 7.1。优先保留 Ubuntu 官方内核，只替换无线模块。

```bash
dkms status
```

```bash
zpool status
```

实机上 `backport-iwlwifi` 与 `zfs/2.4.1` 均已为 `7.0.0-14-generic` 构建成功。DKMS 无线包不会替换 ZFS，但每次内核升级后仍需确认两个模块都为新内核构建成功。

如果日志出现以下内容，表示 DKMS 模块未被运行内核信任，内核会被标记为 tainted：

```text
module verification failed: signature and/or required key missing
loading out-of-tree module taints kernel
```

这与固件是否成功加载是两个问题。启用 Secure Boot 时，未受信任模块还可能被直接拒绝。
