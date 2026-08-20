---
title: RM520N-GL 5G 蜂窝模组可行性验证
created: 2026-08-06
updated: 2026-08-06
type: operation
tags:
  - Linux
  - WWAN
  - 5G
  - Quectel
---

# RM520N-GL 5G 蜂窝模组可行性验证

> RM520N-GL 是 5G 蜂窝网络模组，与 5 GHz Wi-Fi 是两套独立设备。未插 SIM 时仍应完成总线枚举、驱动绑定和控制面识别。相关 Wi-Fi AP 测试见 [[30-系统工程/02-Linux内核与驱动/Intel-BE213-WiFi7-驱动安装与测试手册|Intel BE213 Wi-Fi 7 驱动安装与测试手册]]。

## 1. 无 SIM 的首轮验证

### 1.1 检查 USB 枚举

```bash
lsusb
```

Quectel 设备通常使用 `2c7c:*` USB ID。即使没有 SIM，模组正常上电且 USB 通道正确时，也应该出现在该列表中。

查看 USB 拓扑和链路速率：

```bash
lsusb -t
```

### 1.2 检查控制节点和 WWAN 接口

```bash
ls -l /dev/cdc-wdm* /dev/ttyUSB* /dev/ttyACM* /dev/mhi* /dev/wwan* 2>/dev/null
```

```bash
ip -br link
```

正常结果取决于模组当前的 USB 组合和 QMI/MBIM 模式，常见节点包括 `/dev/cdc-wdm0`、`/dev/ttyUSB*` 或 `wwan0`。

### 1.3 检查 ModemManager

```bash
systemctl is-active ModemManager
```

```bash
mmcli -L
```

枚举成功后，读取模组信息：

```bash
mmcli -m 0
```

无 SIM 时允许显示 `SIM missing` 或类似状态，但仍应能读到制造商、型号、固件版本、设备标识和支持的无线模式。

### 1.4 检查内核日志

```bash
sudo journalctl -b -k --no-pager | grep -iE 'usb|wwan|mhi|modem|quectel|2c7c|qmi|mbim|cdc[_-]wdm|ttyUSB'
```

关键是确认“新 USB 设备”、驱动绑定、控制节点和 WWAN 网口的创建顺序。

### 1.5 检查内核驱动前置

```bash
modinfo -F filename qmi_wwan
```

```bash
modinfo -F filename cdc_mbim
```

```bash
modinfo -F filename option
```

```bash
modinfo -F filename mhi_wwan_mbim
```

驱动文件存在只能证明内核具备支持；如果 `lsusb` 中没有模组，盲目加载这些驱动不会解决上电或插槽通道问题。

## 2. 分层验收门槛

| 阶段 | 验收条件 | 是否需要 SIM |
| --- | --- | --- |
| 1. 硬件枚举 | `lsusb` 或对应总线能看到模组 | 否 |
| 2. 控制面 | `mmcli -L` 能列出 modem，`mmcli -m 0` 能读取基本信息 | 否 |
| 3. SIM 检测 | SIM 存在，PIN 状态正确 | 是 |
| 4. 运营商注册 | 能注册 LTE/NR，可读取 RSRP/RSRQ/SINR | 是 |
| 5. 数据面 | 可获取 IP，指定 WWAN 接口访问网络 | 是 |
| 6. 稳定性 | 持续流量、重连、温度和注册状态符合目标 | 是 |

在第 1 阶段通过之前，不应将问题归因于 SIM、APN 或运营商信号。

## 3. 2026-08-06 实测结果

| 项目 | 结果 |
| --- | --- |
| 系统 | Ubuntu 26.04 LTS |
| 内核 | `6.18.0-061800-generic` |
| BIOS | `5.39` |
| ModemManager | `active` |
| 用户态工具 | `mmcli`、`qmicli`、`mbimcli` 已安装 |
| 内核驱动 | `qmi_wwan`、`cdc_mbim`、`option`、`usb_wwan`、`mhi_wwan_ctrl`、`mhi_wwan_mbim` 均存在 |
| USB 枚举 | 只看到 USB root hub 和键盘，没有 `2c7c:*` |
| 设备节点 | 没有 `/dev/cdc-wdm*`、`/dev/ttyUSB*` 或 WWAN 网口 |
| `mmcli -L` | `No modems were found` |
| 启动日志 | 没有 Quectel/WWAN/QMI/MBIM 模组枚举记录 |
| DMI 插槽信息 | 只报告一个 `PCIe Gen4 Slot1 X2`，已由 NVMe 设备占用 |

结论：Linux 用户态和内核驱动前置基本齐全，但 RM520N-GL 未通过硬件总线枚举。当前阻塞在第 1 阶段，尚不能验证 SIM、注册、拨号、吞吐或稳定性。

## 4. 硬件隔离顺序

以下是基于“总线上完全看不到设备”的排查顺序，不是已确认的根因：

1. 断电后检查模组是否完全插入并固定。
2. 确认插槽是支持 USB 数据通道的 M.2 B-Key WWAN 插槽，不是仅提供 PCIe/NVMe 的 M-Key 插槽。
3. 确认插槽提供稳定 3.3 V 供电，并检查 `W_DISABLE#`、复位和上电控制信号。
4. 如果使用 M.2 转接板，确认 USB 数据线和所需供电都已连接。
5. 在启用蜂窝射频前连接对应频段的天线。
6. 将同一模组放到已知正常的 M.2 B-Key 转 USB 3.0 适配器上复测。

隔离结果的含义：

- 适配器可枚举，主板插槽不可枚举：优先检查主板插槽的 USB 布线、供电、BIOS/EC 上电控制。
- 适配器也不可枚举：优先检查模组安装、适配器供电和模组本体。

## 5. 与 5 GHz Wi-Fi 的区别

| 名称 | 设备 | 常用管理工具 | 依赖 |
| --- | --- | --- | --- |
| 5 GHz Wi-Fi | Intel BE213 等 WLAN 网卡 | `iw`、`hostapd`、`nmcli` | Wi-Fi 信道和监管域 |
| 5G 蜂窝网络 | Quectel RM520N-GL WWAN 模组 | `mmcli`、`qmicli`、`mbimcli` | SIM、APN、运营商注册和蜂窝天线 |

## 6. 记录原则

- 不在笔记中保存 SIM PIN、APN 密码、SSH/sudo 密码或完整 IMEI。
- 远程测试蜂窝数据面时，避免让 WWAN 默认路由覆盖有线管理路由，否则可能中断 SSH。
