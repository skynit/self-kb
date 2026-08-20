---
title: Linux启动流程详解
created: 2026-05-29
updated: 2026-05-31
type: reading-note
source: "https://www.youtube.com/watch?v=EjrAzulPsT4"
channel: "Joe Collins (EzeeLinux)"
tags:
  - OS
  - Linux
  - 启动流程
  - 学习笔记
aliases:
  - Linux boot process
  - BIOS/UEFI/GRUB
  - vmlinuz
  - initramfs
  - systemd
  - ESP分区
  - MBR
---

> 参考来源：[How Linux Boots - EzeeLinux](https://www.youtube.com/watch?v=EjrAzulPsT4)
> 参考书籍：《How Linux Works》Brian Ward

# Linux 启动流程详解

## 总览：四步启动链

```
按下电源键
    ↓
① POST（开机自检）
    ↓
② BIOS / UEFI（固件）
    ↓
③ GRUB（引导加载器）→ 加载内核 + initramfs
    ↓
④ Kernel 初始化 → 加载驱动 → 挂载根文件系统 → 启动 init(PID=1)
    ↓
⑤ systemd（用户空间）→ 启动服务 → 登录界面
```

整个过程通常在 **5~10 秒**内完成。

---

## 一、POST — 开机自检

- **全称**：Power-On Self-Test
- **执行者**：计算机硬件本身（ 不由操作系统控制）
- **功能**：检测 CPU、内存、硬盘、外设等硬件是否正常
- **成功**：过去会发出一声"嘀"响，现在正常启动通常静默
- **失败**：蜂鸣报警，停止启动 → 说明有硬件故障

---

## 二、BIOS vs UEFI — 固件阶段

POST 完成后，进入固件阶段，有两条路径：

| 对比项  | BIOS（传统）                  | UEFI（现代）                               |
| ---- | ------------------------- | -------------------------------------- |
| 全称   | Basic Input/Output System | Unified Extensible Firmware Interface  |
| 分区方案 | MBR（MS-DOS 分区表）           | GPT（GUID 分区表）                          |
| 引导分区 | 无专门分区，MBR 仅 441 字节        | ESP 分区（EFI System Partition），512MB~1GB |
| 磁盘寻址 | LBA（效率低）                  | 原生支持大容量磁盘                              |
| 安全启动 | 无                         | 支持 Secure Boot                         |
| 状态   | 逐渐淘汰（Legacy）              | 当前主流                                   |

### BIOS 引导过程

1. POST 完成后，BIOS 搜索磁盘起始位置的 **MBR**（主引导记录）
2. MBR 只有 441 字节，不足以容纳完整的 GRUB
3. MBR 实际是一个"跳转链接"，指向 `/boot/grub` 中的完整引导程序
4. BIOS 使用 **LBA**（逻辑块寻址）读取硬盘，效率不高但足以启动

### UEFI 引导过程

1. POST 完成后，UEFI 固件直接读取 **ESP 分区**（通常挂载在 `/boot/efi`）
2. ESP 分区中存放 **GRUB 可执行文件**
3. 固件直接启动该文件，比 BIOS 更高效
4. ESP 通常 512MB~1GB，安装时标记为 `boot` + `esp`

### 进入 GRUB 菜单的方法

| 固件类型 | 按键 | 时机 |
|----------|------|------|
| BIOS | **Shift**（长按） | BIOS 画面出现时 |
| UEFI | **Esc**（轻按） | 品牌 Logo 出现后 |

> UEFI 模式下 Esc 键也可能进入 BIOS 设置界面，**时机是关键**，可能需要多次尝试。

### 术语全称速查

| 缩写 | 全称 | 中文 |
|------|------|------|
| BIOS | Basic Input/Output System | 基本输入输出系统 |
| EFI | Extensible Firmware Interface | 可扩展固件接口 |
| UEFI | Unified Extensible Firmware Interface | 统一可扩展固件接口 |
| MBR | Master Boot Record | 主引导记录 |
| ESP | EFI System Partition | EFI 系统分区 |
| FAT | File Allocation Table | 文件分配表 |
| GPT | GUID Partition Table | GUID 分区表 |
| CSM | Compatibility Support Module | 兼容支持模块 |
| LBA | Logical Block Addressing | 逻辑块寻址 |

### BIOS 与 UEFI 引导路径深度对比

#### BIOS 路径：MBR 四段跳

```
磁盘物理起始位置（第 0 扇区，512 字节）
┌─────────────────────────────────────────────────────┐
│  MBR（Master Boot Record，主引导记录）               │
│  ┌──────────────┬───────────────┬─────────────────┐ │
│  │ 引导代码 446B │ 分区表 64B    │ 签名 0x55AA 2B  │ │
│  └──────────────┴───────────────┴─────────────────┘ │
└─────────────────────────────────────────────────────┘
```

**第一步：BIOS 读取 MBR**
POST 完成后，BIOS 固件按启动顺序找磁盘，读取磁盘**第 0 扇区**（512 字节），检查末尾是否为 `0x55AA` 签名确认可引导。

**第二步：MBR 引导代码执行（446 字节）**
这 446 字节是 GRUB **Stage 1**（`boot.img`），极其微小，唯一任务是找到 GRUB 第二阶段代码的位置。446 字节连一个文件系统驱动都放不下，只能硬编码一个扇区地址。

**第三步：跳转到 GRUB Stage 1.5**
MBR 和第一个分区之间有一个**未使用的间隙**（约 31KB~1MB），GRUB 的 Stage 1.5（`core.img`）藏在这里，包含基本的文件系统驱动（能读懂 ext4 等）。

**第四步：读取 /boot/grub → 加载内核**
Stage 1.5 读取 `grub.cfg` 配置文件，加载完整的 GRUB Stage 2，再加载 `/boot/vmlinuz` 和 `/boot/initramfs`。

```
BIOS 固件
  │ 读取第 0 扇区
  ▼
MBR 引导代码 (boot.img, 446B)
  │ 硬编码扇区地址，跳转
  ▼
GRUB Stage 1.5 (core.img, MBR后间隙)
  │ 自带文件系统驱动
  ▼
/boot/grub/grub.cfg → /boot/vmlinuz + initramfs
  ▼
Linux 内核启动
```

> **痛点**：链条长、446 字节极其有限、MBR 最大只能寻址 **2TB** 磁盘、只能有 **4 个主分区**。

#### UEFI 路径：ESP 直读

```
GPT 磁盘布局：
┌─────────┬──────────────────────────────────────────────┐
│ GPT 头   │  ESP 分区 (FAT32, 512MB~1GB)  │  数据分区 ... │
│  保护MBR  │  /EFI/BOOT/BOOTX64.EFI       │               │
│          │  /EFI/debian/grubx64.efi      │               │
└─────────┴──────────────────────────────────────────────┘
```

**第一步：UEFI 固件读取 GPT 头**
POST 完成后，UEFI 固件读取磁盘的 **GPT 分区表**，找到类型标记为 `EFI System Partition` 的分区。

**第二步：直接挂载 ESP 分区（FAT32）**
UEFI 固件**内置 FAT32 驱动**（规范强制要求），直接定位到 ESP 分区中的引导文件路径。

**第三步：直接执行 grubx64.efi**
`grubx64.efi` 是 **PE 格式的可执行文件**，包含完整的 GRUB2，**不存在 441 字节限制**。

```
UEFI 固件
  │ 内置 FAT32 驱动，读取 GPT 分区表
  ▼
ESP 分区 (FAT32)
  │ 直接找到 grubx64.efi
  ▼
GRUB2 (grubx64.efi, 完整功能)
  ▼
/boot/vmlinuz + initramfs
  ▼
Linux 内核启动
```

#### 为什么叫 ESP 分区？

ESP = **E**FI **S**ystem **P**artition，三个单词首字母。

UEFI 固件启动时**不认识** ext4、btrfs 等 Linux 文件系统，只认 FAT32。所以必须划出一个独立分区格式化为 FAT32，专门存放引导文件，固件一上电就能直接读取。

ESP 分区内容：
```
/boot/efi/
└── EFI/
    ├── BOOT/
    │   └── BOOTX64.EFI          # 默认引导程序（兜底）
    └── debian/
        └── grubx64.efi          # GRUB2 的 EFI 可执行文件
```

#### 两条路径总结对比

```
BIOS 路径：4 跳，每跳都有限制
  固件 ──446B──→ boot.img ──硬编码──→ core.img ──驱动──→ grub.cfg ──→ 内核

UEFI 路径：2 跳，直接了当
  固件 ──内置FAT驱动──→ grubx64.efi ──→ 内核
```

| 对比项 | BIOS + MBR | UEFI + ESP |
|--------|-----------|------------|
| 固件找引导程序 | 读固定位置（第 0 扇区） | 读分区表 → 找 ESP → 读文件 |
| 引导程序大小 | **446 字节**（Stage 1） | **无限制**（ESP 可达 1GB） |
| 跳转次数 | 4 次 | **2 次** |
| 文件系统理解 | Stage 1 无法理解 | **固件内置 FAT32 驱动** |
| 最大磁盘 | 2TB | **无限制** |
| 最大主分区 | 4 个 | **128 个** |

### 当前还在使用 BIOS 的场景

| 场景 | 说明 |
|------|------|
| 2012 年前的旧 PC/服务器 | 基本都是纯 BIOS |
| 虚拟机（VirtualBox/VMware） | **默认 BIOS 模式**，需手动切换 UEFI |
| 老旧工控机/嵌入式设备 | 生命周期长，十几年不换 |
| 银行/电信/制造业遗留系统 | 关键业务不敢轻易换 |
| POS 收银/ATM 机 | 大量运行在 BIOS + Win XP/7 上 |
| UEFI 的 CSM 兼容模式 | UEFI 主板模拟 BIOS，支持 Legacy 启动 |

> **趋势**：Intel 2020 年停止支持传统 BIOS，Windows 11 强制 UEFI + Secure Boot，新硬件基本都是纯 UEFI。

### 从 U 盘安装 Debian 的启动模式

Debian 官方 ISO 是 **Hybrid ISO**，同时内置 BIOS 和 UEFI 两种引导：

```
Debian ISO 内部结构：
  MBR 引导代码 (grub-pc)     → BIOS 启动
  ESP 分区 (grub-efi)        → UEFI 启动
  /boot/                     → 内核文件
  /pool/                     → 软件包
```

最终是 BIOS 还是 UEFI，由三个因素共同决定：
1. **主板固件**（硬件决定，不可更改）
2. **U 盘制作方式**（`dd` 写入支持双模，Rufus 取决于选项）
3. **启动菜单选择**（UEFI 主板通常显示两个选项）

```bash
# 确认当前启动模式
[ -d /sys/firmware/efi ] && echo "UEFI 模式" || echo "BIOS 模式"
```

---

## 三、GRUB — 引导加载器

- **全称**：Grand Unified Bootloader
- **存在两个版本**：BIOS 版（Legacy）和 UEFI 版（GRUB 2）

### GRUB 的功能

1. **多系统选择**：双系统（Linux + Windows）启动菜单
2. **多内核切换**：保留上一个内版本，新内核出问题可回退
3. **恢复模式**（Recovery Mode）：类似"安全模式"
4. **内存测试**（memtest，仅 BIOS 版）
5. **自带 Shell**：可在引导失败时手动排查

### GRUB 的关键文件

| 文件                    | 作用                |
| --------------------- | ----------------- |
| `/boot/grub/grub.cfg` | 主配置文件（**不要手动编辑**） |
| `/etc/default/grub`   | 用户配置文件（**在此修改**）  |
| `update-grub`         | 将用户修改同步到主配置       |

> **为什么不能直接编辑 grub.cfg？** 因为发行版会自动维护该文件，直接编辑会被更新覆盖。正确做法是修改 `/etc/default/grub` 后运行 `update-grub`。

### /etc/default/grub 配置详解（Arch Linux 示例）

#### 启动菜单行为

| 变量 | 值 | 含义 |
|------|-----|------|
| `GRUB_DEFAULT` | `0` | 默认选中第 0 个菜单项（第一个内核） |
| `GRUB_TIMEOUT` | `5` | 倒计时 5 秒（0=直接启动，-1=无限等待） |
| `GRUB_DISTRIBUTOR` | `"Arch"` | 菜单中显示的发行版名称 |
| `GRUB_TIMEOUT_STYLE` | `menu` | `menu`(显示菜单) / `countdown`(只显示倒计时) / `hidden`(隐藏) |

#### 内核命令行参数（重点）

```bash
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=5 nowatchdog modprobe.blacklist=iTCO_wdt acpi_osi=! acpi_osi=\"Windows 2022\""
GRUB_CMDLINE_LINUX=""
```

**两个变量的区别**：

```
正常启动：  kernel ...  [GRUB_CMDLINE_LINUX]  [GRUB_CMDLINE_LINUX_DEFAULT]
恢复模式：  kernel ...  [GRUB_CMDLINE_LINUX]  （不加 DEFAULT）
```

| 变量 | 正常启动 | 恢复模式 | 用途 |
|------|---------|---------|------|
| `GRUB_CMDLINE_LINUX_DEFAULT` | ✅ | ❌ | 日常优化参数 |
| `GRUB_CMDLINE_LINUX` | ✅ | ✅ | 关键基础参数（系统能否正常运行） |

`GRUB_CMDLINE_LINUX_DEFAULT` 各参数含义：

| 参数 | 作用 |
|------|------|
| `loglevel=5` | 内核日志级别 5（notice），减少启动刷屏 |
| `nowatchdog` | 禁用内核看门狗，减少中断开销、省电 |
| `modprobe.blacklist=iTCO_wdt` | 黑名单 Intel TCO 看门狗驱动 |
| `acpi_osi=!` | 清除所有 ACPI OSI 字符串 |
| `acpi_osi="Windows 2022"` | 声明为 Windows 2022，解锁固件完整功能 |

`GRUB_CMDLINE_LINUX=""` 留空 = 没有需要无条件传递的参数。常见填写场景：LVM 根分区、LUKS 加密、Btrfs 子卷等。

```bash
# 查看当前内核实际收到的参数
cat /proc/cmdline
```

#### 引导兼容性与显示

| 变量 | 值 | 含义 |
|------|-----|------|
| `GRUB_PRELOAD_MODULES` | `"part_gpt part_msdos"` | 预加载 GPT + MBR 分区表模块，兼容两种分区方案 |
| `GRUB_TERMINAL_INPUT` | `console` | 使用基础控制台输入 |
| `GRUB_GFXMODE` | `auto` | 图形终端分辨率（auto=自动检测） |
| `GRUB_GFXPAYLOAD_LINUX` | `keep` | 内核保持 GRUB 分辨率，传给 Plymouth |
| `GRUB_DISABLE_RECOVERY` | `true` | 禁用恢复模式菜单项 |

#### 外观与主题

```bash
GRUB_THEME="/usr/share/grub/themes/1CyberGRUB-2077/theme.txt"  # 赛博朋克 2077 主题
```

#### 记忆、子菜单与多系统探测

```bash
#GRUB_SAVEDEFAULT=true          # 配合 GRUB_DEFAULT=saved，记住上次选择
#GRUB_DISABLE_SUBMENU=y         # 所有内核平铺显示，不折叠成子菜单
#GRUB_DISABLE_OS_PROBER=false   # 安装 os-prober 后扫描其他系统加入菜单
```

> 修改后执行：Arch 用 `sudo grub-mkconfig -o /boot/grub/grub.cfg`，Debian 用 `sudo update-grub`

### GRUB 加载内核

1. 从 `/boot/` 目录加载选定的 **Linux 内核**（vmlinuz）和对应的 **initramfs**
2. 根据 grub.cfg 中的命令行参数启动内核
3. 加载完毕后 GRUB 退出，控制权交给内核

---

## 四、Linux 内核初始化

### 4.1 vmlinuz 与 initramfs 是什么

**vmlinuz = 压缩的 Linux 内核**

```
vm  → virtual memory（虚拟内存支持）
lin → Linux
uz  → 压缩格式（gzip/zstd）
```

本质是一个**自解压的可执行文件**，约 10~15MB（压缩后），解压后约 50~80MB。

**initramfs = 初始内存文件系统**

```
init → 初始化
ram  → RAM（内存）
fs   → file system（文件系统）
```

本质是一个 **cpio 压缩归档**（类似 .tar.gz），约 30~80MB，内容是根文件系统的"骨架"：

```
initramfs 内容：
├── /bin/busybox          最小工具集
├── /lib/modules/         必要的内核驱动模块
│   ├── ext4.ko           ext4 文件系统驱动
│   ├── ahci.ko           SATA 磁盘控制器驱动
│   ├── nvme.ko           NVMe SSD 驱动
│   └── ...
├── /etc/fstab            挂载配置
└── /init                 初始化脚本
```

### 4.2 vmlinuz 和 initramfs 的运行与解压顺序

```
① GRUB 把两个文件从 /boot 读到内存（仍是压缩态）
   ┌──────────────┐    ┌──────────────────┐
   │ vmlinuz 压缩  │    │ initramfs 压缩    │
   │ ~15MB        │    │ ~30~80MB         │
   └──────────────┘    └──────────────────┘

② vmlinuz 自解压（内核自己解压自己）
   CPU 执行 vmlinuz 头部的解压程序，展开到内存
   ┌──────────────────────┐    ┌──────────────────┐
   │ 内核解压后 ~50~80MB    │    │ initramfs 仍压缩  │
   └──────────────────────┘    └──────────────────┘

③ 内核初始化（初始化 CPU、内存、中断等基本硬件）
   注意：此时内核还没有磁盘驱动，读不了硬盘

④ 内核解压 initramfs（内核解压别人）
   解压到内存，形成临时根文件系统（tmpfs）
   ┌──────────────────────┐    ┌────────────────────────┐
   │ 内核在内存中运行        │    │ initramfs 解压后         │
   │                      │    │ /bin/busybox             │
   │                      │    │ /lib/modules/*.ko        │
   └──────────────────────┘    └────────────────────────┘

⑤ 从 initramfs 中加载驱动模块
   ext4.ko → 能读 ext4 文件系统
   ahci.ko → 能访问 SATA 磁盘
   nvme.ko → 能访问 NVMe SSD

⑥ 切换根文件系统
   用刚获得的驱动能力，挂载真正的根分区（/dev/sda2）
   pivot_root：根目录从 initramfs 切换到真实磁盘

⑦ 启动 init（PID=1）
   执行真实根分区上的 /sbin/init（systemd）
   initramfs 的使命完成，内存被释放
```

> **谁解压谁？** GRUB 不解压，原样加载到内存。vmlinuz **自解压**。内核**解压 initramfs**。

### 4.3 每次启动都解压，不会浪费资源吗？

**不会**，解压只花 0.1~0.5 秒，占整个开机过程的 1~5%。

为什么不直接存未压缩版本？

| | 压缩 | 不压缩 |
|--|------|--------|
| 磁盘占用 | ~15MB | ~50~80MB |
| 启动时 CPU | 多花 0.1~0.5 秒解压 | 无解压开销 |
| 从磁盘加载 | 读 15MB | 读 60MB |

**读小文件 + 解压 < 读大文件**。磁盘 IO 是瓶颈，CPU 解压很快，压缩后总体反而更快。而且解压只发生在开机那一瞬间，完成后压缩包就被释放。

### 4.4 为什么不用 initramfs？为什么不把驱动直接编进内核？

驱动**确实可以**编进内核（built-in），很多系统就是这么做的：

| | 全部 built-in | initramfs 按需加载 |
|--|--------------|-------------------|
| 内核体积 | **200~500MB** | **10~15MB** |
| 启动时加载 | 全部加载，不管用不用 | 只加载当前硬件需要的 |
| 适配新硬件 | 重新编译整个内核 | 只装一个模块包 |
| 适用场景 | 嵌入式/路由器（硬件固定） | 桌面/服务器（硬件多样） |

**initramfs 的真正价值**：一个内核包适配无数硬件。

```
发行版的打包逻辑：
内核包（linux-6.x.x-x86_64.pkg.tar.zst）
├── vmlinuz          ← 通用内核，所有机器共用
└── /lib/modules/    ← 3000+ 个驱动模块，全在这里

安装时：
├── mkinitcpio/dracut  ← 扫描当前硬件，生成对应的 initramfs
└── 只把"你的机器需要的模块"打包进去（30~50 个）
```

> **硬件越固定，越不需要 initramfs（嵌入式/路由器）；硬件越多样，越需要 initramfs（桌面/服务器）。**

### 4.5 切换到真实根文件系统

1. 内核挂载**真实的根文件系统**（`/`）
2. 从 initramfs 切换到物理磁盘上的根文件系统（`pivot_root`）
3. 启动 **init 进程**（PID = 1）

> **这一刻是内核空间和用户空间的分界线**。init 启动后，用户空间（user space）正式开始。

### 4.6 Debian netinst 的驱动策略

Debian netinst ISO 约 600~700MB，**不携带全部驱动**（完整驱动库约 3~5GB）。

**阶段一：安装程序需要的驱动（ISO 内置）**
- 存储驱动：SATA / NVMe / virtio / USB 存储（要能读硬盘）
- 网络驱动：有线网卡（要能联网下载）
- 基础显示驱动（安装界面要能显示）
- 总计约 100~200 个核心模块

**阶段二：安装完成后**
- `apt` 下载 `linux-image` 包 → 完整驱动库（3000+ 模块）
- `update-initramfs` 扫描当前硬件 → 只打包需要的模块 → 生成 initramfs

> WiFi 驱动多数属于 non-free 固件。推荐下载 `debian-xx-amd64-netinst-firmware.iso`（含 non-free 固件），否则 WiFi 可能不工作。

### 4.7 查看内核启动日志

```bash
sudo dmesg          # 查看内核环形缓冲区日志
dmesg | less        # 分页查看
```

启动时按 **Esc** 可以跳过 Logo 画面，直接查看内核启动信息。

---

## 五、Init 系统 — systemd

init 进程（PID=1）是所有用户空间进程的**祖先**，它从启动到关机一直运行。

### 5.1 Init 系统的演进

| 系统 | 年代 | 特点 | 执行方式 |
|------|------|------|----------|
| **SysVinit** | 1983 年起 | 源自 Unix，使用脚本 | **串行**执行，一个挂起全停 |
| **Upstart** | Ubuntu 开发 | 反应式系统 | **并行**执行 |
| **systemd** | 2010 年起 | 目标驱动 | **并行** + 依赖分析，最快 |

> Ubuntu 在 2015 年抛弃 Upstart，全面切换到 systemd。目前几乎所有主流发行版都使用 systemd。

### 5.2 SysVinit vs systemd

| 对比项 | SysVinit | systemd |
|--------|----------|---------|
| 执行方式 | 串行（逐个脚本） | 并行 + 依赖分析 |
| 系统状态 | Run Levels（0~6） | Targets（更灵活） |
| 服务管理 | Shell 脚本 | Unit 文件（类 INI 格式） |
| 速度 | 慢（一个卡住全停） | 快（自动绕过慢服务） |
| 日志 | 文本文件 | 二进制格式（journal） |

### 5.3 systemd 的核心功能

- 启动、停止、管理系统服务（daemon）
- 支持 **定时器**（替代 cron）
- 内置**日志系统**（journalctl）
- **按需激活**：加载但不启动，需要时才激活
- 处理**系统关机**流程
- 资源管理（cgroups）

### 5.4 systemd 的 Unit 类型

systemd 把一切管理对象称为 **Unit**，共有以下类型：

| Unit 类型       | 功能                      |
| ------------- | ----------------------- |
| **Service**   | 管理系统服务/守护进程的生命周期        |
| **Socket**    | 定义网络或 IPC 套接字，收到连接时激活服务 |
| **Target**    | 启动同步点/分组（替代 Run Level）  |
| **Mount**     | 管理文件系统挂载点               |
| **Automount** | 按需自动挂载（如插入 USB 时）       |
| **Swap**      | 管理交换分区/文件               |
| **Path**      | 监控文件路径变化，触发其他 Unit      |
| **Timer**     | 定时触发（替代 cron）           |
| **Snapshot**  | 创建系统状态快照，可回滚            |
| **Slice**     | 资源管理分组（配合 cgroups）      |
| **Scope**     | 管理外部创建的进程组              |

> systemd 的功能覆盖面极广，有人戏称"它本身就快成一个操作系统了"。

---

## 六、实用命令速查

### GRUB 相关

```bash
# 查看 GRUB 菜单（启动时）
# BIOS: 按住 Shift
# UEFI: 按 Esc

# 编辑 GRUB 配置
sudo nano /etc/default/grub
sudo update-grub              # 同步到 grub.cfg
```

### systemd 相关

```bash
# 查看所有运行中的 Unit
systemctl

# 查看某个服务状态
sudo systemctl status ssh

# 查看默认 Target
systemctl get-default

# 查看某个服务的日志
journalctl -u ssh
journalctl -u fstrim.timer

# 启动/停止/重启服务
sudo systemctl start <service>
sudo systemctl stop <service>
sudo systemctl restart <service>

# 设置服务开机自启
sudo systemctl enable <service>
```

### 内核日志

```bash
sudo dmesg              # 查看内核启动日志
dmesg | grep -i error   # 搜索错误信息
```

---

## 七、关键概念总结

| 概念 | 全称 | 一句话解释 |
|------|------|-----------|
| POST | Power-On Self-Test | 硬件自检，不由 OS 控制 |
| BIOS | Basic Input/Output System | 传统固件，1975 年诞生，通过 MBR 引导 |
| UEFI | Unified Extensible Firmware Interface | 现代固件，通过 ESP 分区引导 |
| MBR | Master Boot Record | 磁盘第 0 扇区 512 字节（446 引导代码 + 64 分区表 + 2 签名） |
| ESP | EFI System Partition | UEFI 专用 FAT32 分区，存放引导文件 |
| FAT | File Allocation Table | 文件分配表，UEFI 固件唯一能直接读的文件系统格式 |
| GPT | GUID Partition Table | GUID 分区表，支持 128 个分区、无 2TB 限制 |
| CSM | Compatibility Support Module | UEFI 主板的 BIOS 兼容层 |
| GRUB | Grand Unified Bootloader | 引导加载器，加载内核和 initramfs |
| vmlinuz | vmLinux compressed | 压缩的 Linux 内核，自解压后运行 |
| initramfs | init RAM file system | 内存中的临时根文件系统，提供驱动让内核能读到硬盘 |
| init (PID=1) | - | 用户空间的第一个进程，所有进程的祖先 |
| systemd | - | 现代 init 系统，并行启动，管理所有服务 |
| Target | - | systemd 的目标状态（替代 Run Level） |
| Unit | - | systemd 的管理单元（服务、挂载点、定时器等） |
| journalctl | - | systemd 的日志查询工具（非纯文本格式） |
