---
date: 2026-07-14
tags: [opencode, windows, wine, deepin]
category: windows
---

# Windows 互操作

> 来源：OpenCode 历史会话 | ~3 条

## 企业微信 deepin-wine

安装企业微信 Linux 版：
```bash
yay -S com.qq.weixin.work.deepin
```

常见问题：
- 依赖缺失：deepin-wine 基础包未安装
- wine 配置问题：`WINEPREFIX` 环境
- 卸载普通版后重试

## 卸载企业微信

```bash
yay -R com.qq.weixin.work.deepin
```

或检查其他变体：
```bash
yay -Qs weixin
yay -Qs wechat
```

## QQ 表情导出到微信

QQ 表情包为自定义格式，导出方法：
- 第三方工具提取（QQ 表情目录通常在安装目录的 `CustomFace` 等文件夹）
- 手动提取后导入微信

## 跨平台文件共享

Windows ↔ Linux 使用 SMB/CIFS（详见 [[30-系统工程/06-Samba与SMB/90-历史会话汇总/Samba-SMB-CIFS|Samba 笔记]]）。

## deepin-wine 故障处理

企业微信安装故障：
1. 确认 deepin-wine 基础包：`yay -S deepin-wine5`
2. 重建 wine 前缀：删除 `~/.deepinwine/` 下对应目录
3. 检查 32 位库依赖：`lib32-*` 系列包
