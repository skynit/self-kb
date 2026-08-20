---
date: 2026-07-29
tags: [iwlwifi, iwlmld, wifi7, be213, firmware, kernel, networkmanager, hotspot]
category: linux-driver
---

## 0A. 源码编译路径依赖

更新 APT 软件包索引：

```bash
sudo apt update
```

一次安装本文驱动编译、固件下载和测试所需的软件包：

```bash
sudo apt install -y git build-essential linux-headers-$(uname -r) pkgconf libelf-dev libnl-3-dev libnl-genl-3-dev libssl-dev bc bison flex iw network-manager iperf3 pciutils
```

## 1. 源码编译与功能测试流程

这一节严格按照 [[iwlwifi-驱动编译安装]] 的流程整理，适用于发行版官方包能力不足或需要验证更新固件时。生产系统优先使用第 0 节的 Ubuntu 官方包。

### 1.1 编译安装 backport-iwlwifi

```bash
cd ~
```

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/backport-iwlwifi.git
```

```bash
cd ~/backport-iwlwifi
```

```bash
make -j4
```

```bash
sudo make install
```

### 1.2 安装匹配固件

```bash
cd ~
```

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/linux-firmware.git
```

```bash
cd ~/linux-firmware
```

固件实际位于 `intel/iwlwifi/` 子目录：

```bash
sudo cp intel/iwlwifi/iwlwifi-bz-b0-wh-b0-* /lib/firmware/
```

### 1.3 重启

```bash
sudo reboot
```

### 1.4 验证

```bash
lsmod | grep iwlwifi
```

```bash
sudo journalctl -k -b --no-pager | grep -i iwlwifi | tail -20
```

```bash
iw dev
```

```bash
iw phy
```

```bash
cat /sys/module/iwlwifi/parameters/disable_11be
```

`disable_11be` 输出 `N` 表示未禁用 Wi-Fi 7。

### 1.5 扫描并连接 Wi-Fi

启动 NetworkManager：

```bash
sudo systemctl enable --now NetworkManager
```

打开 Wi-Fi：

```bash
sudo nmcli radio wifi on
```

重新扫描：

```bash
sudo nmcli device wifi rescan ifname wlo1
```

列出 AP：

```bash
sudo nmcli --colors no -f IN-USE,SSID,BSSID,CHAN,FREQ,SIGNAL,SECURITY device wifi list ifname wlo1 | cat
```

连接 AP：

```bash
sudo nmcli device wifi connect "SSID" password "CHANGE_ME" ifname wlo1
```

检查连接：

```bash
iw dev wlo1 link
```

```bash
ip -br address show wlo1
```

### 1.6 基本联网测试

```bash
ping -c 4 192.168.100.254
```

```bash
ping -c 4 8.8.8.8
```

```bash
getent ahostsv4 www.baidu.com
```

### 1.7 吞吐测试

在有线测试服务器上启动：

```bash
iperf3 -s
```

在 CWNAS 上正向测试：

```bash
iperf3 -c <SERVER_IP> -P 4 -t 30
```

在 CWNAS 上反向测试：

```bash
iperf3 -c <SERVER_IP> -P 4 -t 30 -R
```

稳定性测试：

```bash
iperf3 -c <SERVER_IP> -P 4 -t 600
```

### 1.8 创建 2.4 GHz AP

切换 AP 会断开 `wlo1` 的 STA 连接。操作前应使用有线 SSH。

```bash
sudo nmcli device wifi hotspot ifname wlo1 con-name cwnas-ap ssid CWNAS-AP band bg channel 1 password 'CHANGE_ME_12345'
```

检查 AP：

```bash
iw dev wlo1 info
```

查看热点网关地址：

```bash
ip -4 address show wlo1
```

NetworkManager 通常配置 `10.42.0.1/24`。

启用互联网转发：

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

查看连接到 AP 的客户端：

```bash
sudo iw dev wlo1 station dump
```

停止 AP：

```bash
sudo nmcli connection down cwnas-ap
```

恢复原 STA 连接：

```bash
sudo nmcli connection up CWOS
```

### 1.9 使用 hostapd 创建 2.4 GHz AP

`hostapd` 只负责 Wi-Fi 信标、认证、关联和 WPA 握手。手工使用 `hostapd` 时，AP 地址、DHCP、DNS、转发和 NAT 需要另行配置。

`/etc/hostapd/hostapd_2G_20M_11ax.conf`：

```ini
ctrl_interface=/var/run/hostapd
ctrl_interface_group=0

interface=wlo1
driver=nl80211
ssid=CWNAS-2G-TEST

country_code=CN
ieee80211d=1
channel=6
hw_mode=g

ieee80211ax=1
ieee80211n=1
wmm_enabled=1

macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0

wpa=2
wpa_passphrase=CHANGE_ME_12345
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP

ht_capab=[LDPC][SHORT-GI-20]
```

前台启动，便于直接查看认证事件：

```bash
sudo /usr/local/bin/hostapd -dd /etc/hostapd/hostapd_2G_20M_11ax.conf
```

验证接口和已关联客户端：

```bash
iw dev wlo1 info
```

```bash
sudo iw dev wlo1 station dump
```

### 1.10 使用 hostapd 创建 5 GHz 80 MHz AP

`/etc/hostapd/hostapd_5G_80M_11be.conf`：

```ini
ctrl_interface=/var/run/hostapd
ctrl_interface_group=0

interface=wlo1
driver=nl80211
ssid=CWNAS-5G-TEST

hw_mode=a
channel=36

ieee80211n=1
ieee80211ac=1
ieee80211ax=1
ieee80211be=1

beacon_int=100
dtim_period=3

macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0

wpa=2
wpa_passphrase=CHANGE_ME_12345
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP

ht_capab=[HT40+][LDPC][SHORT-GI-20][SHORT-GI-40]

vht_capab=[RXLDPC][SHORT-GI-80][SU-BEAMFORMEE][RX-STBC1][MAX-MPDU-11454][MAX-A-MPDU-LEN-EXP7]
vht_oper_chwidth=1
vht_oper_centr_freq_seg0_idx=42

he_oper_chwidth=1
he_oper_centr_freq_seg0_idx=42

eht_oper_chwidth=1
eht_oper_centr_freq_seg0_idx=42
```

这组参数对应主信道 36，80 MHz 子信道 36/40/44/48，中心信道 42，中心频率 5210 MHz。VHT、HE 和 EHT 的工作宽度必须保持一致；开启 `ieee80211be=1` 却不设置 `eht_oper_*` 时，EHT 默认值可能使实际接口只使用 20/40 MHz。

前台启动：

```bash
sudo /usr/local/bin/hostapd -dd /etc/hostapd/hostapd_5G_80M_11be.conf
```

确认内核实际宽度：

```bash
iw dev wlo1 info
```

已验证的 80 MHz 运行状态为：

```text
channel 36 (5180 MHz), width: 80 MHz, center1: 5210 MHz
```

确认 hostapd 的 VHT/HE/EHT 广播参数：

```bash
sudo /usr/local/bin/hostapd_cli -i wlo1 status
```

关键值应为：

```text
state=ENABLED
channel=36
vht_oper_chwidth=1
vht_oper_centr_freq_seg0_idx=42
he_oper_chwidth=1
he_oper_centr_freq_seg0_idx=42
eht_oper_chwidth=1
eht_oper_centr_freq_seg0_idx=42
```

### 1.11 为手工 hostapd AP 配置地址和 DHCP

给 AP 接口设置地址：

```bash
sudo ip addr replace 192.168.3.1/24 dev wlo1
```

```bash
sudo ip link set wlo1 up
```

`ip addr replace` 只修改当前运行态。重启、卸载 `iwlwifi` 或删除并重建接口后，`192.168.3.1/24` 会消失。`hostapd` 不会自动恢复该地址；如果需要重启后自动恢复，必须由明确的网络服务或 AP 启动单元持久配置。

`dnsmasq` 依赖该地址已经存在。否则即使配置语法正确，也可能在启动时失败：

```text
failed to create listening socket for 192.168.3.1: Cannot assign requested address
```

`/etc/dnsmasq.d/hostapd-wlo1.conf`：

```ini
interface=wlo1
listen-address=192.168.3.1
bind-interfaces

dhcp-range=192.168.3.10,192.168.3.200,255.255.255.0,12h
dhcp-option=option:router,192.168.3.1
dhcp-option=option:dns-server,192.168.3.1

dhcp-authoritative
```

检查配置：

```bash
sudo dnsmasq --test
```

启动并设置开机启动：

```bash
sudo systemctl enable --now dnsmasq
```

```bash
sudo systemctl restart dnsmasq
```

验证 DHCP 监听：

```bash
sudo systemctl status dnsmasq --no-pager
```

```bash
sudo ss -lunp | grep ':67'
```

已验证的成功日志包含：

```text
DHCP, IP range 192.168.3.10 -- 192.168.3.200
DHCP, sockets bound exclusively to interface wlo1
```

客户端重新连接时观察 DHCP：

```bash
sudo journalctl -fu dnsmasq
```

正常时依次出现 `DHCPDISCOVER`、`DHCPOFFER`、`DHCPREQUEST`、`DHCPACK`。查看租约：

```bash
sudo cat /var/lib/misc/dnsmasq.leases
```

DHCP 只解决客户端获取 IP 地址的问题。要通过上联网口访问外网，还需要 IPv4 forwarding 和 NAT/MASQUERADE。

## 2. 2026-07-29 验证基线

| 项目 | 实测值 |
| --- | --- |
| 内核 | `7.0.0-28-generic` |
| Wi-Fi PCI ID | `8086:4d40` |
| Wi-Fi Subsystem | `8086:0314` |
| 识别型号 | Intel Wi-Fi 7 BE213 160MHz |
| RF | WH，`rfid=0x20113100` |
| backport 仓库 HEAD | `278a8d13b` |
| 驱动启动标识 | `iwlwifi-stack-public:master:14899:c4e3abc6` |
| 固件仓库提交 | `2ab4ce21` |
| 固件 | `iwlwifi-bz-b0-wh-b0-c106.ucode` |
| 固件 SHA-256 | `ecdc58ba83c6ae84f0bd61f87b6aa65844a579696ae091822c35fac0dd80102e` |
| 接口 | `wlo1` |
| BIOS | American Megatrends `NASES.01`，2026-03-26 |

已验证结果：

| 测试项 | 结果 |
| --- | --- |
| PCI 枚举 | 通过 |
| `iwlwifi` 绑定 | 通过 |
| c106 固件启动 | 通过 |
| `phy0` 注册 | 通过 |
| `wlo1` 注册 | 通过 |
| STA 扫描 | 通过 |
| STA 连接 2.4 GHz AP | 通过 |
| AP 模式声明 | 通过 |
| 2.4 GHz Soft AP | 通过 |
| AP 客户端认证和 DHCP | 通过 |
| 5 GHz Soft AP | 当时受 `no IR` 限制；已被 2026-08-05 实测结论取代 |
| 6 GHz STA/AP | 尚未完成正式验证 |

### 2.1 2026-08-05 手工 hostapd 验证

| 项目 | 实测结果 |
| --- | --- |
| 内核 | `6.18.0-061800-generic` |
| hostapd | `/usr/local/bin/hostapd`，支持 802.11ac/ax/be 配置 |
| 2.4 GHz AP | 信道 6，20 MHz，802.11ax，认证和四次握手通过 |
| 5 GHz AP | 信道 36，80 MHz，中心频率 5210 MHz，802.11be 启用 |
| 5 GHz 信道能力 | 信道 36/40/44/48 可主动发射；AP 模式声明支持 EHT 80/160 MHz |
| WPA | WPA2-PSK/CCMP 认证和 EAPOL 四次握手通过 |
| DHCP | `dnsmasq` 在 `wlo1` UDP 67 监听，地址池 `192.168.3.10-200` |

已观察到的二层成功判据：

```text
AP-STA-CONNECTED <CLIENT_MAC>
EAPOL-4WAY-HS-COMPLETED <CLIENT_MAC>
authorized: yes
authenticated: yes
associated: yes
```

### 2.2 2026-08-06 5 GHz 80 MHz 信号与 AP 地址诊断

本次检查使用内核 `6.18.0-061800-generic` 和固件 `101.6e695a70.0`。

| 项目 | 直接观测 |
| --- | --- |
| AP 状态 | `hostapd` 已启用，信道 36，80 MHz，中心频率 5210 MHz |
| 发射功率 | `22.00 dBm` |
| 天线链 | `TX 0x3 / RX 0x3`，两路收发均已配置 |
| rfkill | 软、硬阻塞均为 `no` |
| 客户端上行 RSSI | AP 短时观测到 `-75 [-76, -75] dBm` |
| 5180 MHz survey | 噪声 `-93 dBm`，忙时 `3/105 ms` |
| 5200 MHz survey | 噪声 `-76 dBm`，忙时 `81/111 ms`，约 73% |
| 驱动日志 | 未发现固件 Fatal、assert、过热降功率或天线错误 |

当时客户端随后断开，因此没有完成连续双端 RSSI 和吞吐对照。`station dump` 中的 `signal` 是 AP 接收客户端帧时的信号，不能单独证明 AP 发射链异常。

当时 `wlo1` 没有 IPv4 地址。现场配置中，NetworkManager 通过 `unmanaged-devices` 不管理该接口，而已有 netplan Wi-Fi profile 均指向 NetworkManager；`systemd-networkd` 也没有为 AP 定义 `192.168.3.1/24` 静态地址。因此手工启动 `hostapd` 后只有二层 AP，`dnsmasq` 因监听地址不存在而失败。

### 2.3 2026-08-12 Ubuntu 26.04 官方 DKMS 验证

本次基线直接验证 Ubuntu 26.04 官方 `resolute-updates/universe` 中的 `backport-iwlwifi-dkms`，未使用 PPA。

| 项目 | 直接观测 |
| --- | --- |
| 内核 | `7.0.0-14-generic` |
| Ubuntu DKMS 包 | `1:0~103.14434-gitdf6b5bf4-0ubuntu3.26.04.2` |
| 驱动标识 | `iwlwifi-stack-public:release/core103:14434:6861514a` |
| 固件 | `103.1a5cc88d.0 bz-b0-wh-b0-c103.ucode` |
| 接口 | `phy0`、`wlo1` |
| ZFS | `zfs/2.4.1` 已为同一内核构建 |
| 签名状态 | DKMS 模块未被运行内核信任，日志显示 verification failed/taint |

这证明保留 Ubuntu 官方 generic 内核、仅使用官方无线 backport 包即可解决 `no suitable firmware found`，不需要切换到非 LTS Mainline Linux 7.1。

## 3. 驱动、固件和内核的关系

### 3.1 哪些内容需要编译

| 内容 | 是否现场编译 | 安装位置 |
| --- | --- | --- |
| Ubuntu 预编译签名 backport | 否 | `/lib/modules/<内核>/kernel/iwlwifi/` |
| DKMS backport `iwlwifi`、`iwlmld`、`mac80211`、`cfg80211` | DKMS 自动编译 | `/lib/modules/<内核>/updates/dkms/` |
| 手工 backport `iwlwifi`、`iwlmld`、`mac80211`、`cfg80211` | 是 | `/lib/modules/<内核>/updates/` |
| `.ucode` 固件 | 否 | `/lib/firmware/` |
| `iw`、NetworkManager、`iperf3` | 使用发行版软件包 | `/usr/bin/` 等 |

### 3.2 模块路径

内核自带模块：

```text
/lib/modules/<内核>/kernel/drivers/net/wireless/intel/iwlwifi/
```

手工安装的 backport 模块：

```text
/lib/modules/<内核>/updates/drivers/net/wireless/intel/iwlwifi/
```

Ubuntu DKMS 模块：

```text
/lib/modules/<内核>/updates/dkms/
```

Ubuntu 预编译签名 backport 模块：

```text
/lib/modules/<内核>/kernel/iwlwifi/
```

### 3.3 切换内核的影响

第三方模块只属于编译时对应的内核。切换内核后不会自动复制或重新编译驱动。

固件位于 `/lib/firmware/`，由不同内核共享。

每次切换内核后都应检查：

```bash
uname -r
```

```bash
modinfo -F filename iwlwifi
```

```bash
modinfo -F vermagic iwlwifi
```

### 3.4 c108/c107 缺失不是当前故障

当前驱动启动时会优先尝试 c108、c107，再回退到 c106：

```text
Direct firmware load for iwlwifi-bz-b0-wh-b0-c108.ucode failed with error -2
Direct firmware load for iwlwifi-bz-b0-wh-b0-c107.ucode failed with error -2
loaded firmware version 106.c88d33af.0 bz-b0-wh-b0-c106.ucode
```

`-2` 是文件不存在。只要随后成功加载 c106 并注册 `wlo1`，就是正常回退。

不要将不同 API 的固件改名冒充其他版本。

### 3.5 BE213 `wh-b0` 固件版本边界

需要匹配完整固件前缀，不能只看末尾的 `c101/c102/c103`：

```text
驱动请求：iwlwifi-bz-b0-wh-b0-<版本>.ucode
```

官方 `linux-firmware` 曾发布 `iwlwifi-bz-b0-fm-c0-c101.ucode`，但没有发布 `iwlwifi-bz-b0-wh-b0-c101.ucode`。`fm-c0` 与 `wh-b0` 是不同 RF 配置，不能通过改名互换。

| 驱动/内核 | BE213 `bz-b0-wh-b0` 支持范围 | 结果 |
| --- | --- | --- |
| Ubuntu `7.0.0-14/28/29-generic` 内置驱动 | 最高 `c101` | 无匹配公开固件，失败 |
| Ubuntu `7.0.0-1010-oem` 内置驱动 | 最高 `c102` | 可加载 `c102` |
| Ubuntu 26.04 `backport-iwlwifi-dkms` core103 | 最高 `c103` | 已加载 `c103` |
| Ubuntu `linux-main-modules-iwlwifi-7.0.0-14-generic` | 最高 `c103` | 模块包已检查，签名有效；尚未在目标机替换 DKMS 实测 |
| 上游 Linux 7.1.x | 最高 `c102` | 可加载 `c102`，但不是 LTS |
| Debian 13 Trixie Backports 7.1.x | 最高 `c102` | Debian 官方 Backports 路径 |

Linux 7.0 日志列出 `c101` 仅表示驱动会尝试该文件，不证明该硬件组合的 `c101` 固件曾公开发布。

### 3.6 方案选择

| 目标 | 建议 |
| --- | --- |
| Ubuntu 26.04、采用已实测路径 | 官方 `backport-iwlwifi-dkms` |
| Ubuntu 26.04、重视签名与 Secure Boot | `linux-main-modules-iwlwifi-$(uname -r)`，切换后重新验证 |
| 希望不使用 DKMS | Ubuntu `linux-oem-26.04`，并保留旧 generic 内核回退 |
| 依赖 ZFS 的生产系统 | 避免非 Ubuntu Mainline 7.1；切换内核前确认对应 ZFS 模块 |
| Debian 13 | Stable + `trixie-backports` 的内核与 `firmware-iwlwifi` |

`Backports` 指 Debian 将较新软件重新构建并适配到 Stable；它仍由 Debian 官方提供，但更新程度和风险高于 Stable 默认包。

## 4. 依赖说明

| 软件包 | 作用 |
| --- | --- |
| `git` | 下载 `backport-iwlwifi` 和 `linux-firmware` 仓库 |
| `build-essential` | GCC/G++、make、libc 开发头文件等基础工具 |
| `linux-headers-$(uname -r)` | 编译当前内核的外部模块 |
| `pkgconf` | 让构建脚本查找依赖库 |
| `libelf-dev` | 内核模块 ELF 支持 |
| `libnl-3-dev`、`libnl-genl-3-dev` | nl80211/Netlink 开发接口 |
| `libssl-dev` | 加密相关开发接口 |
| `bc`、`bison`、`flex` | 配置和代码生成工具 |
| `iw` | 直接测试 nl80211 和无线驱动 |
| `network-manager` | 管理扫描、认证、DHCP 和热点 |
| `iperf3` | 吞吐测试 |
| `pciutils` | 提供 `lspci`，用于确认无线设备及驱动绑定 |

`build-essential` 不是 Wi-Fi 驱动，它只是编译驱动所需的基础工具集合。

## 5. 内核切换

查看 GRUB 内核条目：

```bash
grep -E "^(submenu|[[:space:]]*menuentry) " /boot/grub/grub.cfg
```

一次性启动 Linux 7.0：

```bash
sudo grub-reboot "Advanced options for Ubuntu>Ubuntu, with Linux 7.0.0-28-generic"
```

```bash
sudo reboot
```

重启后确认：

```bash
uname -r
```

Ubuntu 的 `update-grub`：

```bash
sudo update-grub
```

不要在 Arch Linux 主机上使用 Ubuntu 的 `update-grub` 流程。Arch 的 `/boot/vmlinuz-linux` 文件名本身不包含版本号。

## 6. 日志和成功判据

### 6.1 查看内核日志

普通用户可能无权读取 `dmesg`，使用：

```bash
sudo journalctl -k -b --no-pager
```

Wi-Fi 关键日志：

```bash
sudo journalctl -k -b --no-pager | grep -Ei 'iwlwifi|iwlmld|firmware|fseq|rfkill|renamed from wlan0'
```

持续观察：

```bash
sudo journalctl -kf | grep -Ei 'iwlwifi|iwlmld|firmware|fatal|assert|restart|timeout'
```

### 6.2 成功判据

按顺序判断：

1. `lspci` 看见 `8086:4d40`：PCI 枚举成功。
2. `Kernel driver in use: iwlwifi`：驱动绑定成功。
3. `loaded firmware version ...`：固件文件加载成功；具体可能是已验证的 `c103` 或源码路径使用的 `c106`。
4. `base HW address`：固件初始化已完成关键阶段。
5. `wlo1: renamed from wlan0`：无线接口注册成功。
6. `iw phy` 和 `iw dev` 有输出：无线框架注册成功。
7. 能扫描和连接 AP：基本无线收发通过。
8. `iperf3` 通过：数据面和吞吐通过。

### 6.3 失败指纹

```bash
sudo journalctl -k -b --no-pager | grep -E 'TOP Fatal|NMI_INTERRUPT|ADVANCED_SYSASSERT|FSEQ_ERROR_CODE|Microcode SW error|FW error in SYNC|Failed to send init'
```

如果出现固件 Fatal 且 `iw phy` 为空，说明失败发生在注册无线设备之前。

## 7. STA 客户端测试

### 7.1 `iw` 与 `nmcli`

| 命令 | 层次 | 用途 |
| --- | --- | --- |
| `ip link` | 网络接口 | 设置接口 UP/DOWN |
| `iw` | 内核 nl80211 | 直接测试驱动、扫描和链路 |
| `nmcli` | NetworkManager | 认证、密码、DHCP、自动重连 |

驱动适配测试优先使用 `iw`，正常联网使用 `nmcli`。

低层扫描：

```bash
sudo ip link set wlo1 up
```

```bash
sudo iw dev wlo1 scan | grep -E 'SSID:|freq:|signal:'
```

NetworkManager 正在扫描时，`iw scan` 可能返回 `Device or resource busy`，这不一定是驱动故障。

### 7.2 SSH 下的 nmcli 权限

SSH 普通用户可能不属于本地活动桌面会话，Polkit 会拒绝扫描：

```text
not authorized
```

`sudo -v` 只缓存 sudo 密码，不会给普通 `nmcli` 增加 Polkit 权限。直接使用 `sudo nmcli`。

终端分页警告可通过以下格式规避：

```bash
sudo nmcli --colors no -f IN-USE,SSID,BSSID,CHAN,FREQ,SIGNAL,SECURITY device wifi list ifname wlo1 | cat
```

### 7.3 分频段测试

| 频率 | 频段 |
| --- | --- |
| 2400-2500 MHz | 2.4 GHz |
| 5000-5900 MHz | 5 GHz |
| 5955 MHz 以上的 Wi-Fi 信道 | 6 GHz |

每个频段记录：

- AP 型号和固件版本。
- SSID、BSSID、频率、信道和带宽。
- 信号强度。
- TX/RX bitrate。
- 正向和反向吞吐。
- 是否掉线、重连或出现固件重启。

## 8. Soft AP 测试

### 8.1 确认 AP 模式

```bash
iw phy phy0 info | sed -n '/Supported interface modes:/,/Band 1:/p'
```

本机已确认支持：

```text
AP
AP/VLAN
```

### 8.2 AP 客户端能连接但不能上网

必须分层判断失败位置：

| 阶段 | 成功判据 | 失败表现 |
| --- | --- | --- |
| 发现 | 客户端扫描到 SSID | 信道、监管域、信标或信号问题 |
| 认证/关联 | `AP-STA-CONNECTED` | 密码、安全能力或客户端策略问题 |
| WPA 握手 | `EAPOL-4WAY-HS-COMPLETED` | PSK、PMF 或 WPA 参数问题 |
| DHCP | `DHCPACK` | AP 无 IPv4 地址、DHCP 未启动或 UDP 67 未监听 |
| 外网 | 客户端可访问上联网络 | forwarding、NAT、DNS 或上联网口问题 |

`hostapd` 的 `num_sta` 和 `station dump` 能证明二层连接，但不能证明 DHCP 或外网正常。手机卡在“正在获取 IP”时，先检查：

```bash
ip -4 addr show dev wlo1
```

```bash
sudo ss -lunp | grep ':67'
```

NetworkManager 创建的热点曾使用 `10.42.0.x` 并自动配置 dnsmasq/NAT；手工 hostapd 流程使用 `192.168.3.0/24`，必须按 1.11 节单独配置 AP 地址和 DHCP。

异常状态曾为：

```text
net.ipv4.ip_forward = 0
net.ipv4.conf.wlo1.forwarding = 1
net.ipv4.conf.enp1s0.forwarding = 0
```

临时启用转发：

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

持久化：

```bash
echo 'net.ipv4.ip_forward=1' | sudo tee /etc/sysctl.d/90-cwnas-hotspot.conf
```

```bash
sudo sysctl --system
```

验证：

```bash
sysctl net.ipv4.ip_forward net.ipv4.conf.wlo1.forwarding net.ipv4.conf.enp1s0.forwarding
```

### 8.3 5 GHz `no IR` 历史状态与当前结论

2026-07-29 的旧状态中，5 GHz channel 36/40 创建热点时曾出现：

```text
Hotspot network creation took too long
supplicant-timeout
802.1X supplicant took too long to authenticate
```

这不是热点密码错误。当时 self-managed regulatory 的 5 GHz 信道显示：

```text
5180 MHz [36] (no IR)
5200 MHz [40] (no IR)
```

`no IR` 表示不能主动发起辐射，因此当时不能直接在该信道创建 AP。

2026-08-05 实测已取代上述限制性结论：信道 36/40/44/48 可用，`hostapd` 已在信道 36 成功创建 80 MHz AP，客户端完成认证、关联和 EAPOL 四次握手。每次 BIOS、内核、驱动或固件变更后仍需要重新查看当前信道标志，不能用旧快照推断当前能力。

查看监管域：

```bash
iw reg get
```

查看信道限制：

```bash
iw phy phy0 info | grep -E 'MHz|no IR|disabled|radar'
```

对于 self-managed 设备，单独运行 `iw reg set CN` 不一定能覆盖固件和平台下发的限制。不要通过修改驱动绕过无线电法规。

### 8.4 NetworkManager+iwd 显示虚假 BSSID/频段

在一台使用 NetworkManager+iwd 后端的客户端上，`nmcli device wifi list` 曾将多个网络显示为合成 BSSID，并统一标成 channel 2 / 2417 MHz；同时原始 `iw scan` 能看到真实 BSSID、信道和频率。

如果 NetworkManager profile 强制 `802-11-wireless.band=a` 或绑定 `802-11-wireless.bssid`，NetworkManager 可能在 `prepare` 阶段立即失败，iwd 不会发出认证帧，AP 端也不会看到 `AUTH`。

对比原始扫描：

```bash
sudo iw dev wlan0 scan
```

查看 NetworkManager 视图：

```bash
nmcli --colors no -f IN-USE,BSSID,SSID,CHAN,FREQ,SIGNAL,SECURITY device wifi list ifname wlan0 | cat
```

清除 profile 的频段和 BSSID 约束：

```bash
sudo nmcli connection modify "<PROFILE>" 802-11-wireless.band '' 802-11-wireless.bssid ''
```

实测中，清除 `band=a` 后客户端成功触发 `AP-STA-CONNECTED` 和 `EAPOL-4WAY-HS-COMPLETED`。

### 8.5 6 GHz AP

6 GHz AP 还需要：

- 国家和地区允许 6 GHz Wi-Fi。
- 驱动、固件和平台提供可主动发射的信道。
- WPA3-SAE。
- PMF required。
- AP 和客户端均支持对应信道及带宽。

在 `iw phy` 显示允许主动发射之前，不应直接判定 6 GHz AP 功能通过。

### 8.6 5 GHz AP 弱信号分层诊断

首先在 AP 端同时查看每链 RSSI、当前速率和重试：

```bash
sudo iw dev wlo1 station dump
```

查看当前信道噪声和忙时：

```bash
sudo iw dev wlo1 survey dump
```

核对接口的实际信道、带宽和发射功率：

```bash
iw dev wlo1 info
```

核对天线链位图：

```bash
iw phy phy0 info | grep -i -A2 -B2 antenna
```

判断时需要分开三类问题：

1. RSSI 在 AP 和客户端两侧都低：先检查距离、墙体、金属机箱遮挡、天线频段、馈线和 MAIN/AUX 接头。
2. RSSI 正常但速率低、重试高：查看 survey 忙时和同频 AP，对比 20 MHz 与 80 MHz。
3. 只有单方向 RSSI 低：需要双端同时采样，区分 AP 发射链、AP 接收链和客户端能力。

信道 36 的 80 MHz 带宽覆盖 36/40/44/48。其中任一子信道高忙时都可能降低吞吐并增加重试，但同频干扰通常不能单独解释近距离仍然很低的 RSSI。在不变更监管限制的前提下，先用近距离、无遮挡、20 MHz 做基线，比继续提高发射功率更有诊断价值。

## 9. Bluetooth

Bluetooth 的内核驱动由 Linux 提供：

```text
btintel_pcie
btintel
bluetooth
```

检查设备：

```bash
lspci -nnk -s 00:14.7
```

检查模块：

```bash
lsmod | grep -E 'btintel|btintel_pcie|bluetooth'
```

查看日志：

```bash
sudo journalctl -k -b --no-pager | grep -E 'Bluetooth:|btintel_pcie'
```

Wi-Fi 和 Bluetooth 不需要作为一个内核模块同时编译安装。蓝牙已正常工作时，不要为了排查 Wi-Fi 盲目替换蓝牙固件。

## 10. BIOS、CNVi 和平台检查

BE213 依赖 CPU/PCH/CNVi/BIOS 平台，不是完全独立的普通 PCIe 网卡。

查看 BIOS：

```bash
sudo dmidecode -t bios
```

查看 ACPI/CNVi 信息：

```bash
sudo journalctl -k -b --no-pager | grep -Ei 'CNVW|CNVI|_DSM|ACPI Error|ACPI Warning'
```

如果匹配的新版驱动和固件仍无法初始化，应向主板供应商确认：

- BIOS/EC 是否正式支持 BE213。
- 是否有更新 BIOS、EC 和 CPU/PCH 微码。
- `CNVW._DSM` 是否符合 Intel CNVi 规范。
- Subsystem `8086:0314` 对应的 SKU 和平台配置。
- 国家码和无线监管信息是否正确下发。

## 11. Secure Boot

检查：

```bash
mokutil --sb-state
```

如果 Secure Boot 已启用而 backport 模块未签名，模块可能无法加载，并出现：

```text
Key was rejected by service
```

测试环境可以关闭 Secure Boot，生产环境应为模块签名并完成 MOK 注册。

## 12. 常见问题

| 现象 | 原因或处理 |
| --- | --- |
| `no suitable firmware found`，驱动最高 `c101`，目录只有 `c102/c103/c106` | 内核驱动与公开固件版本错配；使用 Ubuntu 官方签名 backport、DKMS backport 或支持 `c102` 的 OEM 内核 |
| 找到 `fm-c0-c101`，但驱动请求 `wh-b0-c101` | RF 配置不匹配，不能改名使用；`wh-b0` 公开固件从 `c102` 开始 |
| `lspci` 可见但 `iw dev` 为空 | 固件可能在注册 phy 前失败，查看 Fatal/SYSASSERT |
| `loaded firmware` 后仍没有接口 | 文件已读入不等于初始化完成，继续找 `base HW address` |
| c108/c107 报 `-2` | 文件不存在；后续加载 c106 成功则属于正常回退 |
| `cannot stat iwlwifi-bz...` | 文件在 `linux-firmware/intel/iwlwifi/` |
| `pkg-config not found` | 安装 `pkgconf` |
| `pcap.h not found` | 安装 `libpcap-dev` |
| `dmesg` 不允许操作 | 使用 `sudo journalctl -k -b --no-pager` |
| `nmcli` 报 not authorized | SSH Polkit 权限不足，使用 `sudo nmcli` |
| terminal is not fully functional | 使用 `--colors no ... | cat` |
| `wlo1` 显示 DOWN 但 flags 有 UP | 接口已启用但尚未关联 AP |
| 2.4 GHz AP 可连接但不能上网 | 检查 DHCP、NAT 和 `net.ipv4.ip_forward` |
| 5 GHz AP supplicant timeout | 检查 `no IR`、DFS 和 self-managed regulatory |

## 13. 验收清单

- [ ] `uname -r` 是目标内核。
- [ ] `lspci -nnk -s 00:14.3` 显示 `Kernel driver in use: iwlwifi`。
- [ ] `modinfo -F filename iwlwifi` 指向预期的签名模块或 `updates/dkms/` 下的 backport。
- [ ] 日志显示 `iwlwifi-stack-public`。
- [ ] 日志显示加载与驱动兼容的 `bz-b0-wh-b0` 固件；官方 DKMS 基线为 `c103`，源码基线为 `c106`。
- [ ] 日志显示 `base HW address`。
- [ ] 日志没有新的 Fatal、SYSASSERT 或固件重启。
- [ ] `iw phy` 显示 `phy#0`。
- [ ] `iw dev` 显示 `wlo1`。
- [ ] 2.4 GHz STA 扫描、连接和吞吐通过。
- [ ] 5 GHz STA 扫描、连接和吞吐通过。
- [ ] 6 GHz STA 在法规允许条件下测试。
- [ ] 2.4 GHz AP 认证、DHCP、DNS、NAT 通过。
- [ ] 5/6 GHz AP 按实际监管能力测试。
- [ ] Bluetooth `hci0` 正常。
- [ ] 重启后驱动和网络仍正常。

## 14. 参考

- [[iwlwifi-驱动编译安装]]
- [Intel backport-iwlwifi](https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/backport-iwlwifi.git)
- [Intel linux-firmware](https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/linux-firmware.git)
- [Ubuntu backport-iwlwifi-dkms](https://packages.ubuntu.com/resolute-updates/backport-iwlwifi-dkms)
- [Bz/Wh Core 102 固件提交](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/commit/?id=b21b48725314dcca554a8b23bc9c6004cece1042)
- [Linux Wireless documentation](https://wireless.docs.kernel.org/)

## 15. 安全注意事项

- 文档不记录 SSH、sudo 或真实 Wi-Fi 密码。
- 驱动安装、重启和 STA/AP 切换可能中断无线 SSH。
- 操作前保留有线连接或本地控制台。
- DKMS 或手工 backport 模块必须针对当前内核重新编译；Ubuntu 预编译模块必须与完整内核 ABI 匹配。
- 不要通过改名固件或修改驱动绕过版本、签名和监管检查。
