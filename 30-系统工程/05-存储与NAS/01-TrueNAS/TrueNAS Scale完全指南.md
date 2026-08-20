---
tags:
  - truenas
  - nas
  - zfs
  - selfhosted
created: 2026-05-31
source: "https://www.youtube.com/watch?v=bgW3mdl2C1Q"
channel: "Learn Linux TV"
---

# TrueNAS Scale 完全指南

> 基于 Learn Linux TV 视频整理。涵盖安装、硬件选型、存储池、数据集、网络共享、快照、应用安装的完整入门教程。

---

## 1. TrueNAS 是什么

TrueNAS 是一个**网络附加存储（NAS）平台**，提供集中化服务器让网络中所有设备访问共享存储。

### 两个版本

| | TrueNAS Core | TrueNAS Scale |
|---|-------------|---------------|
| 底层系统 | FreeBSD | **Debian Linux** |
| 开发状态 | 仍在维护，但开发重心已转移 | 主力开发版本 |
| 新功能 | 较少 | 持续获得新功能 |
| 推荐 | 特殊需求才选 | **大多数人推荐** |

> 本文后续内容均基于 **TrueNAS Scale**。

### 典型用途

- 个人文件存储与备份
- 为虚拟化平台（如 Proxmox）提供共享存储
- 流媒体服务器
- 任何涉及大量数据管理的工作负载

### 获取方式

1. **预装系统** — 如 iXsystems 的 TrueNAS Mini 系列（即插即用，价格较高）
2. **旧硬件改造** — 旧台式机、旧服务器均可（环保且经济）
3. **自行组装** — 从零搭建全新服务器
4. **购买二手服务器** — eBay 等平台购买退役服务器

---

## 2. 硬件要求与最佳实践

### 最低配置

| 组件 | 最低要求 | 推荐配置 |
|------|---------|---------|
| **内存** | 8 GB | **16 GB+**（ZFS 需要充足内存） |
| **CPU** | 双核 | 核心越多越好（跑虚拟机/App 需要更多） |
| **启动盘** | 16 GB | SSD 或 NVMe（不要用 USB 做启动盘） |

### ECC 内存

> **不强制要求，但强烈推荐。**

ECC（Error Correcting Code）内存可以检测并纠正**单位内存错误**，在数据写入磁盘前修复问题。标准内存做不到这一点 — 一个翻转的 bit 可能导致 ZFS 在不知情的情况下校验并存储损坏的数据。

- 并非所有主板都支持 ECC，购买前需确认兼容性
- 不用 ECC 也能正常运行 TrueNAS，但追求最大数据完整性时应选择 ECC

### RAID 控制器 — 关键注意事项

ZFS **需要直接访问磁盘**。传统硬件 RAID 控制器会将多个磁盘隐藏在一个虚拟卷后面，导致 ZFS 无法：

- 检测单个磁盘故障
- 验证磁盘健康状态
- 自修复损坏数据

**解决方案：**

| 方案 | 说明 |
|------|------|
| RAID 卡支持 **HBA 模式**（IT Mode / JBOD 模式） | 让 TrueNAS 直接访问每个磁盘 |
| eBay 购买已刷 IT Mode 的 RAID 卡 | 专门有人出售这类卡 |
| 主板 SATA 口直连 | 最简单，直接跳过 RAID 卡 |

### 网卡

- **Intel 网卡**最为可靠（尤其是 10G 网络）
- Realtek 和 Broadcom 部分型号也可用，但兼容性参差不齐

---

## 3. 安装过程

### 3.1 下载 ISO

前往 TrueNAS 官网下载最新稳定版 ISO 镜像。

### 3.2 制作启动盘

使用 **Etcher**（balenaEtcher）将 ISO 写入 USB 闪存盘：

1. 打开 Etcher
2. 选择 ISO 文件
3. 选择目标 USB 设备
4. 点击 Flash

> 所有平台（Windows / macOS / Linux）均可使用 Etcher。

### 3.3 安装步骤

1. 将启动盘插入目标机器的 USB 口
2. 开机进入启动菜单（按键因厂商而异，常见 F2/F12/DEL/ESC）
3. 选择 USB 设备启动
4. 选择 **Install / Upgrade** 选项
5. 选择安装目标磁盘（**不要选数据盘**）
6. 设置管理员密码（Tab 键切换确认框）
7. 等待安装完成
8. 选择 **Reboot** 重启
9. 重启后屏幕会显示 **IP 地址**，用浏览器访问该地址即可进入 Web 界面

> 建议在路由器/DHCP 服务器上为 TrueNAS 设置**静态 IP 租约**，防止 IP 变化导致设备失联。

---

## 4. 初始登录与界面

- **默认用户名**：`truenas admin`
- **密码**：安装时设置的密码
- 访问方式：浏览器中输入 TrueNAS 服务器的 IP 地址

---

## 5. 存储池（Storage Pool）

### 5.1 清理磁盘

首次安装后，先到 **Storage → Disks** 查看所有已安装磁盘。如果有旧数据的磁盘，需要先执行 **Wipe**（快速擦除即可）。

> **注意**：第一个磁盘是**启动池（Boot Pool）**，不要动它！

### 5.2 创建存储池

1. 进入 **Storage → Create Pool**
2. 输入池名称（如 `volume1`）
3. 选择是否加密（生产环境建议加密）
4. 选择 **VDEV 类型**：

| RAID 级别 | 冗余磁盘数 | 说明 |
|----------|-----------|------|
| **RAID Z1** | 1 | 一块盘容错 |
| **RAID Z2** | 2 | 两块盘容错（推荐） |
| **RAID Z3** | 3 | 三块盘容错 |

5. 将磁盘拖入 VDEV（可拖出不想使用的磁盘）
6. 查看 **Configuration Preview** — 确认总容量估算
7. 点击 **Save and Go to Review**
8. 确认后点击 **Create Pool**

---

## 6. 数据集（Datasets）

数据集是 TrueNAS 中**最重要的功能之一**，允许将存储池划分为独立的逻辑分区，每个数据集可以有：

- 独立的权限配置
- 独立的快照策略
- 独立的共享设置

### 创建数据集

1. **Storage → Datasets**
2. 选中父级（如 `volume1`）
3. 点击 **Add Dataset**
4. 输入名称（如 `documents`、`music`、`videos`）
5. 保存

### 嵌套数据集

可以在数据集下创建子数据集：

```
volume1/
├── documents/
├── music/
└── videos/
    ├── tv/         ← 子数据集
    └── movies/     ← 子数据集
```

### 生产环境数据集布局示例

```
volume1/
├── archive/          # 不常用的归档文件
├── backups/          # 备份数据
├── clonezilla/       # 磁盘镜像
├── cold-storage/     # YouTube 视频项目（需要离线备份）
├── downloads/
├── home/
│   ├── user/         # 用户主目录（独立快照策略）
│   └── rsync/        # rsync 脚本使用的系统用户
├── logs/
├── media/
├── proxmox/
│   ├── backups/
│   └── iso-images/
└── public/           # 公共分享（Samba 完全开放）
```

> 每个数据集可以有**不同的快照频率** — 频繁修改的设高频快照，不常变化的设低频快照。

---

## 7. 网络共享

### 7.1 SMB / Samba（推荐大多数人使用）

**优点**：跨平台兼容性最好（Windows / macOS / Linux 均支持）。

#### 启用 Samba 服务

**Shares → SMB** → 点击三点菜单 → **Turn On Service**

#### 配置 Samba

三点菜单 → **Configure**：
- **NetBIOS Name**：默认即可（`truenas`）
- **Workgroup**：默认 `WORKGROUP`，可自定义

#### 创建 SMB 共享

1. **Shares → SMB → Add**
2. 选择要共享的**数据集路径**（如 `documents`）
3. 设置 **Purpose**（默认即可，也有 Time Machine 等选项）
4. 设置共享名称
5. 保存

### 7.2 NFS（常用于虚拟化场景）

NFS 适合为 Proxmox 等虚拟化平台提供存储。流程与 Samba 类似：

1. 启用 NFS 服务
2. 创建 NFS 共享
3. 设置 **Networks** — 限制哪些 IP/子网可访问（安全最佳实践）

### 7.3 用户与权限

#### 创建用户

**Credentials → Users → Add**
- 设置用户名
- 勾选 **Samba** 访问权限
- 设置密码

#### 设置数据集权限

**Datasets → 选中数据集 → Permissions → Edit**

1. 将 Owner 和 Group 从 `root` 改为新用户
2. 可勾选 **Apply Recursively**（递归应用权限到已有文件）
3. 保存

#### 设置 ACL

**Permissions → Edit → Set ACL**

- 可选择预设 ACL 或创建自定义 ACL
- 将 ACL 的 Owner 和 Group 设置为你的用户
- 给 Group 授予 **Full Control** 或 **Read/Write**
- 保存 ACL

> ACL（Access Control List）是 Samba 共享的最佳实践配置。

### 7.4 验证共享

在任意操作系统的文件管理器中：

1. 打开 **Network**
2. 找到 TrueNAS 服务器
3. 输入用户凭据连接
4. 访问共享文件夹，验证读写权限

#### Shell 验证

**System → Shell**

```bash
cd /mnt/volume1/documents
ls -la
```

可以看到文件的所有者和组是否正确。

---

## 8. 数据保护

### 8.1 快照（Snapshots）

ZFS 快照是**近乎即时**的数据备份，占用空间极小（只记录变化的数据）。

#### 手动创建快照

**Datasets → 选中数据集 → Data Protection → Take a Snapshot**

- 可选自定义名称
- 保存后立即生效

#### 定期快照任务

**Data Protection → Periodic Snapshot Tasks → Add**

| 设置 | 选项 |
|------|------|
| 数据集 | 选择目标数据集 |
| 频率 | 每小时 / 每天 / 每周 / 自定义 |
| 自定义时间 | 可选精确时间和日期 |
| 日期范围 | 可选特定星期几 / 月份 |

**快照策略建议：**

| 数据类型 | 建议频率 | 理由 |
|---------|---------|------|
| 文档 | 每小时 | 频繁修改 |
| 音乐 | 每周 | 变化不频繁 |
| 电影/电视 | 每月 | 很少变化 |

#### 恢复快照

**Datasets → 选中数据集 → View Snapshots**

- **Clone** — 克隆到新数据集（不影响原数据）
- **Rollback** — 直接回滚整个数据集到快照状态

> 恢复操作在秒级完成，非常快。

### 8.2 云端同步

支持将数据同步到 **Backblaze B2** 等云端存储服务：

1. **Credentials → Backup Credentials** — 添加云服务凭证（Access Key ID + Secret Access Key）
2. **Data Protection → Cloud Sync Tasks → Add** — 创建同步任务，选择凭证和数据集

---

## 9. 应用（Apps）

TrueNAS Scale 支持直接在 NAS 上运行应用程序（基于容器技术）。

### 9.1 浏览可用应用

**Apps → Discover Apps** — 查看应用商店中所有可用的应用。

### 9.2 安装应用（以 Syncthing 为例）

1. 在应用商店中找到 **Syncthing**
2. 点击 **Install**
3. 首次使用需要**选择存储池**用于应用数据（会自动创建系统数据集）
4. 配置应用设置（时区等，大多数保持默认即可）
5. 点击 **Install**

### 9.3 访问应用

**Apps → 选中应用 → Web UI** — 直接在浏览器中打开应用界面。

### 容器（Containers）

TrueNAS Scale 也支持直接创建和管理容器，但此功能目前标记为**实验性**，仅推荐高级用户使用。建议通过 Apps 界面间接使用容器。

---

## 10. 其他功能速览

| 功能 | 说明 |
|------|------|
| **Dashboard** | 可自定义布局，拖拽排列 CPU / 内存 / 存储信息 |
| **System → Shell** | Web 终端，直接操作底层 Linux 系统 |
| **Updates** | 保持系统更新（Dashboard 顶部有更新检查按钮） |
| **TrueNAS Connect** | 云管理平台（可选），提供集中监控和告警 |
| **TrueCommand** | 多系统管理、SSO（可选） |
| **API Keys** | 用户设置中可创建 API Key，用于自动化 |
| **Jobs** | 后台任务管理（快照、备份等任务的状态） |

---

## 速查清单

- [ ] 安装 TrueNAS Scale
- [ ] 创建存储池（选择 RAID 级别）
- [ ] 创建数据集（按用途分类）
- [ ] 创建用户账号
- [ ] 配置 SMB/NFS 共享
- [ ] 设置数据集权限和 ACL
- [ ] 配置定期快照任务
- [ ] 配置云端备份（可选）
- [ ] 安装需要的应用（如 Syncthing）
- [ ] 设置静态 IP 地址
- [ ] 检查系统更新

---

## 参考

- 视频来源：[TrueNAS Scale Complete Guide - Everything You Need to Know](https://www.youtube.com/watch?v=bgW3mdl2C1Q)
- 官网：[https://www.truenas.com](https://www.truenas.com)
- 下载：[https://www.truenas.com/download](https://www.truenas.com/download)
- Etcher（制作启动盘）：[https://etcher.balena.io](https://etcher.balena.io)
