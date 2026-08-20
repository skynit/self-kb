---
date: 2026-07-14
tags: [opencode, git, 版本控制]
category: git
---

# Git 版本控制

> 来源：OpenCode 历史会话 | ~6 条

## 修改提交人

```bash
# 修改最近一次提交
git commit --amend --author="Name <email>"

# 批量修改历史提交
git rebase -i HEAD~n
# 将需要修改的提交标记为 edit，然后：
git commit --amend --author="Name <email>" --no-edit
git rebase --continue
```

## 远程仓库切换

```bash
# origin 不存在时
git remote add origin <url>

# origin 已存在 → 报错
git remote remove origin
git remote add origin <url>

# 直接修改（推荐）
git remote set-url origin <url>

# 查看当前
git remote -v
```

## autocrlf 换行符配置

| 设置 | 行为 |
|------|------|
| `core.autocrlf input` | 提交时转 LF，检出时不做转换（Linux/Mac 推荐） |
| `core.autocrlf true` | 提交时转 LF，检出时转 CRLF（Windows 推荐） |
| `core.autocrlf false` | 不做任何转换 |

对应 `.gitattributes` 更精细控制：
```
* text=auto
*.sh text eol=lf
*.bat text eol=crlf
```

## gitignore 不生效

已纳入版本控制的文件不受 `.gitignore` 影响。

```bash
# 清除 Git 缓存（保留本地文件）
git rm --cached <file>
git rm -r --cached <dir>

# 单文件
git rm --cached file.log
git commit -m "chore: stop tracking file.log"
```

## 未版本管理文件展示

- **IDEA**：通过文件颜色标识未版本管理文件（可在 Version Control 设置中配置）
- **VSCode**：在源代码管理面板（Ctrl+Shift+G）可设置显示未跟踪文件；安装 GitLens 等扩展增强

VSCode 设置：
```json
{
  "git.untrackedChanges": "separate"
}
```
