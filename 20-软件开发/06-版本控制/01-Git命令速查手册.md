# Git 命令速查手册

---

## 目录

- [配置 (config)](#配置-config)
- [初始化与克隆](#初始化与克隆)
- [工作区状态](#工作区状态)
- [暂存与提交](#暂存与提交)
- [分支操作](#分支操作)
- [合并与变基](#合并与变基)
- [远程仓库](#远程仓库)
- [撤销与回退](#撤销与回退)
- [删除与清理](#删除与清理)
- [查看历史](#查看历史)
- [标签 (tag)](#标签-tag)
- [储藏 (stash)](#储藏-stash)
- [子模块 (submodule)](#子模块-submodule)
- [高级操作](#高级操作)
- [Git 内部命令](#git-内部命令)

---

## 配置 (config)

```bash
# 查看所有配置
git config --list
git config --list --show-origin  # 显示配置来源文件

# 三级配置
git config --system   # 系统级 (/etc/gitconfig)
git config --global   # 用户级 (~/.gitconfig)
git config --local    # 仓库级 (.git/config，默认)

# 设置用户信息
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# 设置默认编辑器
git config --global core.editor "vim"
git config --global core.editor "code --wait"  # VSCode

# 设置默认分支名
git config --global init.defaultBranch main

# 命令别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# 彩色输出
git config --global color.ui auto

# 换行符处理
git config --global core.autocrlf true   # Windows: LF→CRLF
git config --global core.autocrlf input  # macOS/Linux: 保留 LF

# 忽略文件权限变化
git config --global core.fileMode false

# 查看单个配置
git config user.name
git config --get user.email

# 删除配置
git config --unset user.name
git config --global --unset alias.st

# 编辑配置文件
git config --global --edit
```

---

## 初始化与克隆

```bash
# 初始化新仓库
git init
git init my-project          # 初始化并创建目录
git init --bare repo.git     # 创建裸仓库（服务器用）

# 克隆仓库
git clone <url>
git clone <url> <dir>                    # 克隆到指定目录
git clone -b <branch> <url>              # 克隆指定分支
git clone --depth 1 <url>                # 浅克隆（只拉最新提交）
git clone --single-branch <url>          # 只克隆默认分支
git clone --recursive <url>              # 同时克隆子模块
git clone --mirror <url> repo.git        # 镜像克隆（含所有分支/标签）

# 克隆后设置
git remote -v                            # 查看远程仓库
git remote set-url origin <new-url>      # 修改远程地址
```

---

## 工作区状态

```bash
# 查看状态
git status
git status -s                # 简洁模式
git status -sb               # 简洁模式 + 分支信息

# 查看差异
git diff                     # 工作区 vs 暂存区
git diff --staged            # 暂存区 vs 最新提交
git diff HEAD                # 工作区 vs 最新提交
git diff <branch>            # 当前分支 vs 指定分支
git diff <commit1> <commit2> # 两个提交之间
git diff --stat              # 只显示统计信息
git diff --name-only         # 只显示文件名
git diff --word-diff         # 单词级差异

# 查看指定文件差异
git diff <file>
git diff HEAD <file>
git diff <commit1>:<file> <commit2>:<file>
```

---

## 暂存与提交

```bash
# 添加到暂存区
git add <file>               # 添加指定文件
git add .                    # 添加当前目录所有变更
git add -A                   # 添加所有变更（包括删除）
git add *.js                 # 添加所有 .js 文件
git add -p                   # 交互式暂存（选择部分内容）
git add -u                   # 只添加已跟踪文件的修改（不添加新文件）

# 提交
git commit                   # 打开编辑器输入提交信息
git commit -m "message"      # 直接指定提交信息
git commit -am "message"     # 暂存 + 提交已跟踪文件
git commit --amend           # 修改最后一次提交
git commit --amend --no-edit # 修改最后一次提交但不改消息
git commit --allow-empty -m "empty commit"  # 空提交

# 交互式暂存
git add -i                   # 进入交互模式
# 选项：
#   1: status   - 查看状态
#   2: update   - 选择文件暂存
#   3: revert   - 从暂存区移除
#   4: add untracked - 添加未跟踪文件
#   5: patch    - 部分暂存
#   6: diff     - 查看差异

# 取消暂存
git reset HEAD <file>        # 从暂存区移除（保留修改）
git restore --staged <file>  # Git 2.23+ 新命令
```

---

## 分支操作

```bash
# 查看分支
git branch                   # 本地分支
git branch -r                # 远程分支
git branch -a                # 所有分支
git branch -v                # 显示最后一次提交
git branch -vv               # 显示跟踪关系
git branch --merged          # 已合并到当前分支的分支
git branch --no-merged       # 未合并的分支

# 创建分支
git branch <branch>          # 创建但不切换
git checkout -b <branch>     # 创建并切换
git switch -c <branch>       # Git 2.23+ 新命令
git branch <branch> <commit> # 从指定提交创建

# 切换分支
git checkout <branch>
git switch <branch>          # Git 2.23+ 推荐

# 重命名分支
git branch -m <old> <new>    # 重命名
git branch -M <old> <new>    # 强制重命名

# 删除分支
git branch -d <branch>       # 删除已合并分支
git branch -D <branch>       # 强制删除
git push origin --delete <branch>  # 删除远程分支
git push origin :<branch>    # 删除远程分支（旧语法）

# 设置上游分支
git branch -u origin/<branch>
git branch --set-upstream-to=origin/<branch>
git push -u origin <branch>  # 推送并设置跟踪

# 查看分支图
git log --graph --oneline --all
git log --graph --oneline --decorate --all
```

---

## 合并与变基

```bash
# 合并
git merge <branch>           # 合并指定分支到当前分支
git merge --no-ff <branch>   # 禁用快进合并（保留分支历史）
git merge --squash <branch>  # 压缩合并（所有提交变为一个）
git merge --abort            # 中止合并
git merge --continue         # 解决冲突后继续

# 变基
git rebase <branch>          # 将当前分支变基到指定分支
git rebase -i HEAD~3         # 交互式变基最近 3 个提交
git rebase --onto <new> <old> <branch>  # 复杂变基
git rebase --abort           # 中止变基
git rebase --continue        # 解决冲突后继续
git rebase --skip            # 跳过当前提交

# 交互式变基操作
# pick   = 保留提交
# reword = 保留但修改提交信息
# edit   = 保留但停下来修改
# squash = 合并到上一个提交
# fixup  = 合并但丢弃提交信息
# drop   = 删除提交

# Cherry-pick（拣选提交）
git cherry-pick <commit>     # 应用指定提交
git cherry-pick <c1> <c2>    # 应用多个提交
git cherry-pick <c1>..<c3>   # 应用提交范围
git cherry-pick --abort      # 中止
git cherry-pick --continue   # 继续

# 解决冲突
# 1. 手动编辑冲突文件
# 2. git add <file>
# 3. git merge --continue 或 git rebase --continue
# 或查看冲突：
git diff --name-only --diff-filter=U  # 列出冲突文件
```

---

## 远程仓库

```bash
# 查看远程仓库
git remote                   # 列出远程仓库
git remote -v                # 显示 URL
git remote show origin       # 详细信息

# 添加/删除远程仓库
git remote add <name> <url>
git remote remove <name>
git remote rename <old> <new>

# 修改远程地址
git remote set-url origin <new-url>

# 拉取
git fetch                    # 拉取所有远程更新
git fetch origin             # 拉取指定远程仓库
git fetch origin <branch>    # 拉取指定分支
git fetch --all              # 拉取所有远程仓库
git fetch --prune            # 拉取并删除远程已删除的分支引用

# 拉取并合并
git pull                     # = git fetch + git merge
git pull --rebase            # = git fetch + git rebase
git pull origin <branch>     # 拉取指定分支

# 推送
git push                     # 推送当前分支
git push origin <branch>     # 推送到指定分支
git push -u origin <branch>  # 推送并设置上游
git push --all               # 推送所有分支
git push --tags              # 推送所有标签
git push origin --delete <branch>  # 删除远程分支
git push --force             # 强制推送（危险）
git push --force-with-lease  # 安全的强制推送

# 跟踪远程分支
git checkout -b <local> origin/<remote>
git checkout --track origin/<remote>
```

---

## 撤销与回退

```bash
# 撤销工作区修改
git checkout -- <file>       # 恢复工作区文件（旧命令）
git restore <file>           # Git 2.23+ 推荐
git restore .                # 恢复所有文件

# 取消暂存
git reset HEAD <file>        # 从暂存区移除
git restore --staged <file>  # Git 2.23+ 推荐

# 回退提交
git reset --soft HEAD~1      # 回退提交，保留暂存区和工作区
git reset --mixed HEAD~1     # 回退提交和暂存区（默认）
git reset --hard HEAD~1      # 回退所有（危险！丢失修改）
git reset <commit>           # 回退到指定提交

# Revert（创建反向提交）
git revert <commit>          # 撤销指定提交（安全，保留历史）
git revert HEAD              # 撤销最后一次提交
git revert <c1>..<c3>        # 撤销提交范围

# 恢复删除的提交
git reflog                   # 查看所有操作历史
git reset --hard <commit>    # 恢复到指定记录

# 恢复删除的文件
git checkout HEAD -- <file>  # 从最新提交恢复
git checkout <commit> -- <file>  # 从指定提交恢复
```

---

## 删除与清理

```bash
# 删除文件
git rm <file>                # 删除文件并暂存删除操作
git rm --cached <file>       # 从 Git 删除但保留本地文件
git rm -r <dir>              # 递归删除目录
git rm -f <file>             # 强制删除（已修改的文件）

# 删除未跟踪文件
git clean -n                 # 预览要删除的文件（dry run）
git clean -f                 # 删除未跟踪文件
git clean -fd                # 删除未跟踪文件和目录
git clean -fx                # 删除未跟踪文件（包括 .gitignore 中的）
git clean -i                 # 交互式删除

# 清理缓存
git rm -r --cached .         # 删除所有缓存（重新应用 .gitignore）
git rm --cached <file>       # 删除指定文件缓存

# 清理.gitignore后重新提交
git rm -r --cached .
git add .
git commit -m "清理缓存并重新应用 .gitignore"

# 清理历史中的大文件/敏感信息
git filter-branch --tree-filter 'rm -f <file>' HEAD
git filter-repo --path <file> --invert-paths  # 推荐工具
git filter-repo --strip-blobs-bigger-than 10M

# 清理 reflog
git reflog expire --expire=now --all
git gc --prune=now --aggressive  # 垃圾回收

# 压缩仓库
git gc                       # 垃圾回收
git gc --aggressive          # 激进压缩
git repack -ad               # 重新打包对象
```

---

## 查看历史

```bash
# 查看提交历史
git log                      # 完整日志
git log -5                   # 最近 5 条
git log --oneline            # 单行显示
git log --graph              # 图形化
git log --all                # 所有分支
git log --oneline --graph --all --decorate  # 组合

# 格式化输出
git log --pretty=oneline
git log --pretty=short
git log --pretty=full
git log --pretty=format:"%h - %an, %ar : %s"
# 格式占位符：
#   %H  提交哈希
#   %h  简短哈希
#   %an 作者名字
#   %ae 作者邮箱
#   %ad 作者日期
#   %ar 作者日期（相对时间）
#   %s  提交信息

# 过滤
git log --author="Alice"     # 按作者
git log --since="2 weeks"    # 最近两周
git log --after="2024-01-01" --before="2024-12-31"
git log --grep="fix"         # 搜索提交信息
git log -S "function_name"   # 搜索代码变更
git log -- <file>            # 指定文件的历史

# 统计
git log --stat               # 显示文件变更统计
git log --shortstat          # 简化统计
git log --name-only          # 只显示文件名
git log --name-status        # 显示文件名和状态(A/M/D)

# 查看某个提交
git show <commit>            # 显示完整内容
git show <commit>:<file>     # 显示文件内容
git show --stat <commit>     # 只显示统计

# 查看提交者
git shortlog                 # 按作者分组
git shortlog -sn             # 统计提交次数
git shortlog -sn --no-merges # 排除合并提交

# Blame（追溯每行作者）
git blame <file>             # 显示每行的作者和提交
git blame -L 10,20 <file>    # 只看 10-20 行
git blame -C <file>          # 检测跨文件的代码移动
```

---

## 标签 (tag)

```bash
# 查看标签
git tag                      # 列出所有标签
git tag -l "v1.*"            # 模糊搜索
git show <tag>               # 查看标签详情

# 创建标签
git tag <tag>                # 轻量标签（指向提交的引用）
git tag -a <tag> -m "message"  # 附注标签（完整对象）
git tag <tag> <commit>       # 给指定提交打标签

# 推送标签
git push origin <tag>        # 推送单个标签
git push origin --tags       # 推送所有标签
git push --follow-tags       # 只推送当前分支相关标签

# 删除标签
git tag -d <tag>             # 删除本地标签
git push origin --delete <tag>  # 删除远程标签
git push origin :refs/tags/<tag>  # 删除远程标签（旧语法）

# 检出标签
git checkout <tag>           # 分离 HEAD 模式
git checkout -b <branch> <tag>  # 基于标签创建分支
```

---

## 储藏 (stash)

```bash
# 储藏当前工作
git stash                    # 储藏修改
git stash save "message"     # 储藏并添加描述
git stash -u                 # 包括未跟踪文件
git stash -a                 # 包括所有文件（含忽略文件）
git stash --keep-index       # 保留暂存区内容

# 查看储藏
git stash list               # 列出所有储藏
git stash show               # 显示最新储藏的差异
git stash show stash@{0}     # 显示指定储藏
git stash show -p            # 显示完整差异

# 应用储藏
git stash apply              # 应用最新储藏（保留储藏）
git stash apply stash@{1}    # 应用指定储藏
git stash pop                # 应用并删除最新储藏
git stash pop stash@{0}      # 应用并删除指定储藏

# 删除储藏
git stash drop               # 删除最新储藏
git stash drop stash@{1}     # 删除指定储藏
git stash clear              # 删除所有储藏

# 基于储藏创建分支
git stash branch <branch>    # 从储藏创建新分支
```

---

## 子模块 (submodule)

```bash
# 添加子模块
git submodule add <url> <path>
git submodule add -b <branch> <url> <path>  # 指定分支

# 克隆含子模块的仓库
git clone --recursive <url>
# 或克隆后初始化
git submodule init
git submodule update

# 更新子模块
git submodule update --remote           # 拉取远程更新
git submodule update --remote --merge   # 拉取并合并
git submodule update --init --recursive # 递归初始化

# 查看子模块
git submodule status
git submodule foreach git status        # 在每个子模块执行命令

# 删除子模块
git submodule deinit <path>
git rm <path>
rm -rf .git/modules/<path>
```

---

## 高级操作

```bash
# Bisect（二分查找bug）
git bisect start
git bisect bad                # 标记当前提交有问题
git bisect good <commit>      # 标记某个提交正常
# Git 自动切换到中间提交，测试后标记
git bisect good  # 或 git bisect bad
# 重复直到找到第一个坏提交
git bisect reset              # 退出二分查找

# Worktree（多工作树）
git worktree add <path> <branch>  # 创建新工作树
git worktree list                 # 列出所有工作树
git worktree remove <path>        # 删除工作树
git worktree prune                # 清理已删除的工作树引用

# Archive（打包）
git archive -o output.zip HEAD           # 打包当前分支
git archive --format=tar HEAD | gzip > output.tar.gz
git archive -o output.zip HEAD <dir>     # 只打包指定目录

# Bundle（离线传输）
git bundle create repo.bundle --all      # 打包所有分支
git bundle create repo.bundle HEAD       # 打包当前分支
git clone repo.bundle repo               # 从 bundle 克隆
git fetch repo.bundle                    # 从 bundle 拉取

# 查找提交
git rev-parse HEAD               # HEAD 的哈希
git rev-parse --short HEAD       # 短哈希
git rev-list --all               # 所有提交哈希

# 对象操作
git cat-file -t <hash>           # 查看对象类型
git cat-file -p <hash>           # 查看对象内容
git ls-tree HEAD                 # 查看树对象
git ls-files                     # 查看暂存区文件

# 维护
git fsck                         # 检查仓库完整性
git fsck --full                  # 完整检查
git count-objects -vH            # 统计对象数量
```

---

## Git 内部命令

```bash
# Plumbing 命令（底层）
git hash-object <file>           # 计算文件哈希
git hash-object -w <file>        # 计算并写入对象库
git cat-file -p <hash>           # 查看对象内容
git update-index --add <file>    # 添加文件到索引
git write-tree                   # 写入树对象
git commit-tree <tree> -m "msg"  # 创建提交对象
git update-ref refs/heads/main <commit>  # 更新引用

# 查看引用
git show-ref                     # 显示所有引用
git show-ref --heads             # 只显示分支
git show-ref --tags              # 只显示标签
git symbolic-ref HEAD            # 查看 HEAD 指向

# Reflog（引用日志）
git reflog                       # 查看 HEAD 历史
git reflog show <branch>         # 查看分支历史
git reflog expire --expire=now --all  # 清理 reflog
```

---

## 常用组合命令

```bash
# 拉取远程分支并创建本地分支
git fetch origin
git checkout -b <local> origin/<remote>

# 强制同步远程分支（覆盖本地）
git fetch origin
git reset --hard origin/<branch>

# 查看未推送的提交
git log origin/<branch>..<branch>
git log @{u}..                   # 简写

# 查看未拉取的提交
git log <branch>..origin/<branch>
git log ..@{u}                   # 简写

# 删除已合并的分支
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# 查看所有包含某次提交的分支
git branch --contains <commit>

# 修改最近 N 次提交的作者信息
git rebase -i HEAD~N
# 将 pick 改为 edit，然后：
git commit --amend --author="New Author <email@example.com>" --no-edit
git rebase --continue

# 统计代码量
git log --author="Alice" --pretty=tformat: --numstat | \
    awk '{ add += $1; subs += $2; loc += $1 - $2 } END \
    { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

---

## .gitignore 语法

```bash
# 注释
# 空行会被忽略

# 忽略文件
file.txt
*.log
*.tmp

# 忽略目录
dir/
node_modules/

# 否定模式（不忽略）
!important.log

# 通配符
*.log          # 所有 .log 文件
**/*.log       # 所有目录下的 .log 文件
/TODO          # 根目录下的 TODO
doc/**/*.pdf   # doc 及子目录下的 .pdf

# 忽略除外
*
!.gitignore
!src/
```

---

## .gitattributes 语法

```bash
# 换行符规范化
* text=auto
*.sh text eol=lf
*.bat text eol=crlf

# 二进制文件
*.png binary
*.jpg binary

# 导出时排除
.gitattributes export-ignore
.gitignore export-ignore
test/ export-ignore

# Diff 驱动
*.md diff=markdown
*.xlsx diff=excel

# 合并策略
database.conf merge=ours
```

---

## 故障排查

```bash
# 查看配置问题
git config --list --show-origin
git config --list --show-scope

# 查看 Git 版本
git --version

# 诊断连接问题
GIT_TRACE=1 git clone <url>
GIT_CURL_VERBOSE=1 git clone <url>
GIT_SSH_COMMAND="ssh -vvv" git clone <url>

# 查看仓库大小
git count-objects -vH

# 查找大文件
git rev-list --objects --all | \
    git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
    sed -n 's/^blob //p' | sort -k2 -n | tail -20

# 修复损坏的仓库
git fsck --full
git fsck --unreachable

# 恢复丢失的提交
git reflog
git fsck --lost-found
```

---

## 性能优化

```bash
# 压缩仓库
git gc
git gc --aggressive --prune=now

# 重新打包
git repack -adf --depth=250 --window=250

# 清理未使用的对象
git prune

# 浅克隆（节省带宽和空间）
git clone --depth 1 <url>
git fetch --deepen 10           # 加深历史

# 稀疏检出（只检出部分文件）
git sparse-checkout init --cone
git sparse-checkout set <dir>

# 部分克隆（按需下载对象）
git clone --filter=blob:none <url>  # 不下载文件内容
git clone --filter=tree:0 <url>     # 不下载树对象
```

---

## Git Hooks

常用 hooks（放在 `.git/hooks/`）：

```bash
# pre-commit - 提交前执行
#!/bin/bash
npm run lint

# commit-msg - 验证提交信息
#!/bin/bash
commit_msg=$(cat $1)
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore):"; then
    echo "错误：提交信息必须以 feat/fix/docs 等开头"
    exit 1
fi

# pre-push - 推送前执行
#!/bin/bash
npm test

# post-merge - 合并后执行
#!/bin/bash
npm install
```

启用 hook：

```bash
chmod +x .git/hooks/pre-commit
```

---

## Git Flow 工作流

```bash
# 初始化
git flow init

# Feature 分支
git flow feature start <name>
git flow feature finish <name>
git flow feature publish <name>

# Release 分支
git flow release start <version>
git flow release finish <version>

# Hotfix 分支
git flow hotfix start <name>
git flow hotfix finish <name>
```

---

## 参考资源

- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 书籍](https://git-scm.com/book/zh/v2)
- [Git Reference](https://git-scm.com/docs)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

---

**最后更新：** 2026-06-25
