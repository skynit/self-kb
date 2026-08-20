---
date: 2026-07-14
tags: [opencode, arch, pacman, yay, aur]
category: arch
---

# Arch Linux 与包管理 (pacman / yay / AUR)

> 来源：OpenCode 历史会话 | ~18 条

## pacman 数据库锁定

更新失败提示"无法锁定数据库"：
- 原因：另一个 pacman 进程运行中，或上次异常退出遗留锁文件
- 解决：删除 `/var/lib/pacman/db.lck`

```bash
sudo rm /var/lib/pacman/db.lck
sudo pacman -Syu
```

## yay AUR 操作

```bash
yay <package>        # 搜索并安装
yay -S <package>     # 安装
yay -R <package>     # 卸载
yay -Rns <package>   # 卸载 + 清除无用依赖
yay -Syu             # 系统更新（含 AUR）
```

## AUR 构建失败

- goland 安装报无效选项 `-s`：PKGBUILD 或 makepkg 版本兼容性
- visual-studio-code-bin 版本回退：在 AUR 页面找历史版本的 PKGBUILD 手动构建
- WindTerm-bin 构建错误：校验和不匹配或依赖问题

## AUR 多版本包区别

| 包名 | 类型 |
|------|------|
| `postman-bin` | 预编译二进制 |
| `postman` | 从源码构建 |
| `visual-studio-code-bin` | 预编译 |

区别在构建方式、更新策略和构建时间。

## Arch 安装 deb 包

不推荐直接 dpkg。优先级：
1. 从 AUR 查找对应包
2. 用 `debtap` 将 deb 转换为 Arch 包后安装
3. 手动解压使用（最后手段）

## 常用软件安装

| 软件 | 安装方式 |
|------|----------|
| Obsidian | `yay -S obsidian` |
| Go | `pacman -S go` |
| todesk | AUR |
| 企业微信 | deepin-wine AUR |
| lazylogit | `pacman -S lazygit` |
| Docker | `pacman -S docker` |
| MySQL | `pacman -S mysql` |

## 软件卸载

```bash
yay -R microsoft-edge-stable   # 卸载 Edge
yay -R hoppscotch              # 卸载 Hoppscotch
yay -R todesk-bin              # 卸载 ToDesk
yay -R harness                 # 卸载 Harness
```

## live-build 不在 AUR

Arch 无 live-build AUR 包。替代：
- 手动构建
- 使用 debian Docker 容器运行
- 也可用 `archiso` 构建 Arch 的 live 镜像

## AUR Postman 四个包区别

1. `postman-bin`：预编译二进制，更新快
2. `postman`：源码构建
3. 其他变体：canary/agent 等特殊版本

一般选 `postman-bin`。
