---
date: 2026-07-14
tags: [opencode, vscode, ide]
category: ide
---

# VSCode / IDE 配置

> 来源：OpenCode 历史会话 | ~2 条

## TypeScript 语言服务器 ENOENT

```
TypeScript language server ENOENT error
```

原因：tsserver 找不到 Node.js 可执行文件。

解决：
- 检查 `typescript.tsserver.nodePath` 设置
- 确认系统已安装 Node.js：`node --version`
- 在 VSCode settings.json 中指定路径：
  ```json
  {
    "typescript.tsserver.path": "/usr/bin/node"
  }
  ```

## lazygit 安装

- **VSCode 集成**：扩展市场安装 lazygit 集成插件（如 GitLens 已有类似功能）
- **系统安装**：
  ```bash
  pacman -S lazygit    # Arch
  ```
  安装后在 VSCode 终端中直接使用 `lazygit`

## 未版本管理文件

IDEA 用文件颜色标识未跟踪文件，VSCode 在源代码管理面板显示。详见 [[20-软件开发/06-版本控制/90-历史会话汇总/Git版本控制|Git 笔记]]。
