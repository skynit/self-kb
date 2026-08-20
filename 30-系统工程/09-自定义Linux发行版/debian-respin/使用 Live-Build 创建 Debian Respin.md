---
tags:
  - debian
  - linux
  - live-build
  - respin
  - custom-iso
created: 2026-05-31
source: "https://www.youtube.com/watch?v=XZ_QGjMiBpU"
channel: "OldTechBloke"
---

# 使用 Live-Build 创建 Debian Respin

> 基于 OldTechBloke 视频和 eznix 的构建脚本，使用 Debian Live-Build 工具创建带有自定义桌面、壁纸、主题、登录界面和 GRUB 启动画面的定制化 ISO 镜像。

---

## 1. 什么是 Debian Respin

**Respin** = 基于现有发行版重新打包的定制化 ISO 镜像。不是从零开发发行版，而是在 Debian 基础上预配置好个人偏好设置。

### 为什么要制作 Respin

| 目的 | 说明 |
|------|------|
| 快速部署 | 重装系统或装新机器时，一键恢复到个人配置状态 |
| 学习探索 | 深入了解 Linux 系统构建过程 |
| 批量分发 | 给团队/家庭成员提供统一配置的系统 |
| 兴趣爱好 | 纯粹的技术探索和 DIY 乐趣 |

> Respin ≠ 自建发行版。不需要维护软件仓库，不需要公开发布，只需满足个人需求。

---

## 2. 工具链概述

| 工具 | 作用 |
|------|------|
| **Debian Live-Build** | Debian 官方的 Live 系统构建框架 |
| **eznix 构建脚本** | 基于 Live-Build 的自动化脚本，封装了大量配置细节 |
| **VirtualBox** | 构建完成后用于测试 ISO 镜像 |
| **Etcher** | 将 ISO 写入 USB 闪存盘（用于实体机安装） |

### eznix 资源

- YouTube 频道：eznix — "How to use Debian Live Build soup-to-nuts"
- SourceForge 项目：[eznix OS](https://sourceforge.net/projects/eznixos/)
  - eznix OS ISO 镜像
  - **tar 包**（包含完整的构建脚本和目录结构）← **核心资源**
  - 转换脚本（将标准 Debian 转为 eznix OS）

> eznix OS 默认基于 **XFCE** 桌面环境，所有脚本围绕 XFCE 编写。

---

## 3. 完整构建流程

```
┌─────────────────────────────────────────┐
│  Phase 1: 环境准备                        │
│  ├── 安装 live-build                     │
│  ├── 下载并解压 eznix tar 包               │
│  └── 阅读文档                             │
├─────────────────────────────────────────┤
│  Phase 2: 资源收集                        │
│  ├── 收集壁纸                             │
│  ├── 收集 XFCE 配置文件                    │
│  ├── 收集 GRUB 启动画面                    │
│  ├── 收集 LightDM 登录界面配置              │
│  └── 收集额外 .deb 包或脚本                 │
├─────────────────────────────────────────┤
│  Phase 3: 脚本定制                        │
│  ├── 修改构建脚本                          │
│  ├── 配置 includes.chroot 目录结构          │
│  ├── 复制自定义文件到 chroot 环境             │
│  └── 添加/修改软件包列表                    │
├─────────────────────────────────────────┤
│  Phase 4: 执行构建                        │
│  ├── 运行构建脚本（约 30 分钟）              │
│  ├── 交互式检查阶段（验证文件是否正确）         │
│  └── 输入 exit 继续构建 ISO                │
├─────────────────────────────────────────┤
│  Phase 5: 测试与发布                       │
│  ├── VirtualBox 测试 Live 模式              │
│  ├── VirtualBox 测试安装模式                 │
│  ├── 验证自定义项是否生效                     │
│  └── 写入 USB 安装到实体机                   │
└─────────────────────────────────────────┘
```

---

## 4. Phase 1 — 环境准备

### 4.1 安装 Live-Build

```bash
sudo apt-get install live-build
```

### 4.2 下载 eznix 构建包

前往 [SourceForge eznix OS](https://sourceforge.net/projects/eznixos/) 下载 **tar 文件**（如 `eznix-10.2.tar.gz`）。

### 4.3 解压到主目录

```bash
tar xzf eznix-10.2.tar.gz
mv eznix-10.2 ~/
```

### 4.4 阅读文档

解压后会看到 `documents/` 目录，包含两份关键文档：

| 文档 | 内容 |
|------|------|
| `prepare-howto.txt` | 定制构建前的准备工作步骤 |
| `build-eznix-howto.txt` | 构建脚本的详细说明 |

> 还有一个 `live-build/` 目录，内含 Live-Build 的 HTML 手册。**务必花时间通读文档。**

---

## 5. Phase 2 — 资源收集与目录结构

### 5.1 eznix tar 包的目录结构

```
eznix-10.2/
├── build-eznix-10.2      # 构建脚本（核心）
├── documents/             # 文档
│   ├── prepare-howto.txt
│   └── build-eznix-howto.txt
├── backgrounds/           # 壁纸目录
├── bootloaders/           # GRUB 启动画面
│   ├── bios/              # Legacy BIOS 启动画面
│   ├── efi/               # UEFI 启动画面
│   └── ...
└── live-build/            # Live-Build 手册
```

### 5.2 壁纸准备

将个人壁纸复制到 `backgrounds/` 目录：

```
backgrounds/
├── existing-wallpapers...   # 保留 eznix 原有壁纸
├── my-wallpaper.jpg         # 个人壁纸（桌面背景）
└── background-2.jpg         # LightDM 登录背景（同壁纸的变体）
```

### 5.3 GRUB 启动画面

在 `bootloaders/` 的各子目录中放置自定义启动画面：

```
bootloaders/
├── bios/
│   └── splash.xpm.gz     # Legacy BIOS（必须是 .xpm.gz 格式，640×480）
├── efi/
│   └── splash.png         # UEFI（PNG 格式，640×480）
└── ...
```

> 关键要求：**640×480 像素**，与桌面壁纸保持一致视觉风格。

### 5.4 XFCE 配置文件

从当前使用的 XFCE 桌面复制以下目录：

```
~/.config/
├── xfce4/          # XFCE 桌面配置
├── deconf/         # 桌面配置守护进程
└── autostart/      # 自动启动应用（如 plank dock）
```

### 5.5 LightDM 配置

创建单独的 `lightdm/` 目录：

```
lightdm/
├── lightdm.conf           # LightDM 主配置
└── slick-greeter.conf     # Slick Greeter 配置（登录界面主题）
```

### 5.6 GRUB 配置

创建 `default-grub/` 目录：

```
default-grub/
├── grub                   # /etc/default/grub（禁用 Plymouth 启动画面）
└── grub-background.tga    # GRUB 背景图（.tga 格式）
```

在 `/etc/default/grub` 中禁用 Plymouth：

```bash
# 将以下行
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
# 改为
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
```

---

## 6. Phase 3 — 脚本定制

### 6.1 核心概念：`includes.chroot` 目录

`includes.chroot` 是 Live-Build 的特殊目录，其内容会**原样映射到目标系统的文件系统**：

```
includes.chroot/
├── etc/
│   ├── lightdm/          # → /etc/lightdm/
│   │   ├── lightdm.conf
│   │   └── slick-greeter.conf
│   ├── default/          # → /etc/default/
│   │   └── grub
│   └── skel/             # → /etc/skel/（新用户骨架目录）
│       └── .config/
│           ├── xfce4/
│           ├── deconf/
│           └── autostart/
├── boot/
│   └── grub/             # → /boot/grub/
│       └── grub-background.tga
└── usr/
    └── share/
        └── backgrounds/  # → /usr/share/backgrounds/
            └── my-wallpaper.jpg
```

> **映射规则**：`includes.chroot/etc/lightdm/` = 系统中的 `/etc/lightdm/`。目录结构必须完全对应。

### 6.2 复制文件到 chroot 环境

在构建脚本中添加文件复制指令：

```bash
# LightDM 配置
cp -r lightdm/* ${BUILD_DIR}/includes.chroot/etc/lightdm/

# GRUB 背景
cp grub-background.tga ${BUILD_DIR}/includes.chroot/boot/grub/

# GRUB 默认配置
cp grub ${BUILD_DIR}/includes.chroot/etc/default/grub/

# 壁纸
cp my-wallpaper.jpg ${BUILD_DIR}/includes.chroot/usr/share/backgrounds/

# XFCE 配置（放在 /etc/skel/ 下，新用户自动获得）
cp -r xfce4 ${BUILD_DIR}/includes.chroot/etc/skel/.config/
cp -r deconf ${BUILD_DIR}/includes.chroot/etc/skel/.config/
cp -r autostart ${BUILD_DIR}/includes.chroot/etc/skel/.config/
```

### 6.3 修改 XFCE 壁纸配置（关键技巧）

XFCE 壁纸路径配置在 XML 文件中，需要手动修改：

```
~/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-desktop.xml
```

**将所有壁纸引用路径**改为新壁纸文件名：

```xml
<!-- 修改前 -->
<property name="last-image" type="string" value="/usr/share/backgrounds/default.jpg"/>

<!-- 修改后 -->
<property name="last-image" type="string" value="/usr/share/backgrounds/my-wallpaper.jpg"/>
```

> 必须修改 XML 中**每一个**壁纸引用，否则安装后壁纸不生效。这是作者踩过的最大坑。

### 6.4 修改构建脚本

#### 交互式 Shell（调试用）

在脚本末尾、ISO 生成前加入交互式 shell：

```bash
# 在 lb build 之前加入
bash
# 或
lb chroot
```

这会在构建暂停在 `live@hostname:~$` 提示符，可以进入 chroot 环境验证：
- `ls /etc/lightdm/` — 检查 LightDM 配置
- `ls /usr/share/backgrounds/` — 检查壁纸
- `ls /boot/grub/` — 检查 GRUB 背景

验证完毕后输入 `exit` 继续构建。

#### 软件包列表（Phase 4）

在脚本的软件包安装部分（Phase 4），保留 eznix 默认包列表，并添加个人需要的包：

```bash
# 在默认列表末尾追加
chromium
materia-gtk-theme
slick-greeter
# ... 其他个人软件
```

### 6.5 运行构建

```bash
sudo -i
cd ~/eznix-10.2
bash build-eznix-10.2
```

构建过程约 **30 分钟**（取决于网速和机器性能）。

---

## 7. Phase 4 — 测试

### 7.1 输出文件

构建完成后，在构建目录中生成 ISO 文件：

```
eznix-os-10.2/
└── live-image-amd64.hybrid.iso
```

### 7.2 VirtualBox 测试

| 配置项 | 推荐值 |
|--------|--------|
| 内存 | 8 GB |
| 虚拟硬盘 | 32 GB |
| ISO 挂载 | live-image-amd64.hybrid.iso |

### 7.3 验证清单

- [ ] **GRUB 启动画面** — 自定义 splash 正常显示
- [ ] **Plymouth 已禁用** — 启动过程显示纯文本，无默认动画
- [ ] **LightDM 登录界面** — 显示自定义背景图
- [ ] **桌面壁纸** — 登录后显示自定义壁纸
- [ ] **GTK 主题** — Materia Dark 主题已应用
- [ ] **图标主题** — Papirus 图标已应用
- [ ] **自动启动应用** — plank 等自启动项正常运行
- [ ] **安装后系统** — 通过 Debian 安装器安装后，以上所有配置保持不变

### 7.4 安装测试

> Live 系统正常不等于安装后也正常。**必须测试安装模式。**

1. 重启虚拟机 → 选择 **Graphical Install**
2. 使用标准 Debian 安装器完成安装
3. 重启后验证所有自定义项是否保留

---

## 8. 常见问题与排坑

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 内核恐慌（Kernel Panic） | 尝试用非 XFCE 桌面（如 MATE） | 脚本围绕 XFCE 编写，换桌面需大幅修改脚本 |
| 壁纸不显示 | XFCE4-desktop.xml 中壁纸路径未修改 | 修改 `xfce4-desktop.xml` 中所有壁纸引用 |
| GRUB 画面不生效（安装后） | 仅在 Live 环境生效，未正确复制到 chroot | 确保 `includes.chroot/boot/grub/` 和 `includes.chroot/etc/default/grub/` 正确 |
| LightDM 无自定义背景 | 配置文件未放入正确路径 | 确保 `includes.chroot/etc/lightdm/` 存在且内容正确 |
| Plymouth 动画仍在 | `/etc/default/grub` 未修改 | 删除 `splash` 参数，禁用 Plymouth |
| 构建卡住或失败 | 网络问题或包依赖错误 | 检查网络、查看构建日志 |

---

## 9. 目录结构速查

```
~/eznix-10.2/                          # 源码包（原始 eznix）
~/eznix-10.2/build-eznix-10.2          # 构建脚本
~/eznix-10.2/backgrounds/              # 壁纸
~/eznix-10.2/bootloaders/              # GRUB 启动画面
~/eznix-10.2/documents/                # 文档

~/eznix-os-10.2/                       # 构建目录（脚本自动创建）
~/eznix-os-10.2/includes.chroot/       # 自定义文件映射目录
~/eznix-os-10.2/live-image-amd64.hybrid.iso  # 输出 ISO
```

---

## 参考

- 视频来源：[How to create a Debian respin using Live-Build](https://www.youtube.com/watch?v=XZ_QGjMiBpU)（OldTechBloke）
- eznix 构建脚本：[SourceForge - eznix OS](https://sourceforge.net/projects/eznixos/)
- eznix 详细教程：[How to use Debian Live Build soup-to-nuts](https://www.youtube.com/c/eznixOS)（YouTube 频道）
- Debian Live-Build 手册：`https://live-team.pages.debian.net/live-manual/`
