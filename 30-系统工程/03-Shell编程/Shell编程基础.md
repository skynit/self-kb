---
title: Shell 编程基础
created: 2026-06-01
updated: 2026-06-01
type: concept
domain: Shell编程
tags:
  - Linux
  - Shell
  - Bash
  - 脚本
---

> 属于 [[Linux实操MOC]]

# Shell 编程基础

## 脚本基本结构

```bash
#!/bin/bash                     # Shebang：指定解释器
# 这是注释

echo "Hello, World!"            # 输出
```

```bash
chmod +x script.sh              # 添加执行权限
./script.sh                     # 执行脚本
bash script.sh                  # 用 bash 执行（不需要执行权限）
```

---

## 变量

### 定义与使用

```bash
name="Linux"                    # 定义变量（等号两边不能有空格！）
echo $name                      # 使用变量
echo ${name}                    # 花括号形式（推荐，避免歧义）
echo "${name}_server"           # 拼接字符串

readonly PI=3.14                # 只读变量
unset name                      # 删除变量
```

### 字符串操作

```bash
str="Hello World"
echo ${#str}                    # 长度：11
echo ${str:0:5}                 # 截取：Hello
echo ${str/World/Linux}         # 替换：Hello Linux
echo ${str,,}                   # 转小写
echo ${str^^}                   # 转大写
```

### 特殊变量

| 变量 | 含义 |
|------|------|
| `$0` | 脚本名称 |
| `$1` ~ `$9` | 第 1~9 个参数 |
| `${10}` | 第 10 个参数起 |
| `$#` | 参数个数 |
| `$@` | 所有参数（各自独立） |
| `$*` | 所有参数（合成一个字符串） |
| `$?` | 上一条命令的退出码 |
| `$$` | 当前脚本的 PID |
| `$!` | 最后一个后台进程的 PID |

### 命令替换

```bash
today=$(date +%Y-%m-%d)         # 推荐用法
today=`date +%Y-%m-%d`          # 旧写法（反引号）
files=$(ls -la | wc -l)
```

---

## 数组

```bash
# 索引数组
arr=("apple" "banana" "cherry")
echo ${arr[0]}                  # 第一个元素
echo ${arr[@]}                  # 所有元素
echo ${#arr[@]}                 # 数组长度
arr[3]="date"                   # 添加元素
unset arr[1]                    # 删除元素

# 遍历
for item in "${arr[@]}"; do
    echo "$item"
done
```

---

## 条件判断

### if 语句

```bash
if [ "$name" = "Linux" ]; then
    echo "It's Linux"
elif [ "$name" = "Windows" ]; then
    echo "It's Windows"
else
    echo "Unknown"
fi
```

> ⚠️ `[` 后面和 `]` 前面**必须有空格**！

### 条件测试

```bash
# 字符串
[ -z "$str" ]                   # 字符串为空
[ -n "$str" ]                   # 字符串非空
[ "$a" = "$b" ]                 # 相等
[ "$a" != "$b" ]                # 不相等

# 数字
[ $a -eq $b ]                   # 等于
[ $a -ne $b ]                   # 不等于
[ $a -gt $b ]                   # 大于
[ $a -lt $b ]                   # 小于
[ $a -ge $b ]                   # 大于等于
[ $a -le $b ]                   # 小于等于

# 文件
[ -f file ]                     # 文件存在且是普通文件
[ -d dir ]                      # 目录存在
[ -e path ]                     # 路径存在
[ -r file ]                     # 可读
[ -w file ]                     # 可写
[ -x file ]                     # 可执行
[ -s file ]                     # 文件非空

# 逻辑
[ "$a" = "1" -a "$b" = "2" ]    # AND（旧写法）
[ "$a" = "1" ] && [ "$b" = "2" ] # AND（推荐）
[ "$a" = "1" ] || [ "$b" = "2" ] # OR
[ ! "$a" = "1" ]                 # NOT
```

### [[ ]]（Bash 扩展，推荐）

```bash
# 支持正则
[[ "$str" =~ ^[0-9]+$ ]]        # 判断是否为纯数字
[[ "$str" == *.txt ]]            # 通配符匹配

# 不需要担心变量为空
[[ -z $var ]]                    # 安全（即使 var 未定义也不会报错）
```

---

## 循环

### for 循环

```bash
# 列表
for i in 1 2 3 4 5; do
    echo $i
done

# 范围
for i in {1..10}; do
    echo $i
done

# C 风格
for ((i=0; i<10; i++)); do
    echo $i
done

# 遍历文件
for file in *.txt; do
    echo "Processing $file"
done

# 遍历命令输出
for user in $(cat /etc/passwd | cut -d: -f1); do
    echo $user
done
```

### while 循环

```bash
count=0
while [ $count -lt 5 ]; do
    echo $count
    ((count++))
done

# 读取文件每一行
while IFS= read -r line; do
    echo "$line"
done < file.txt
```

### until 循环

```bash
count=0
until [ $count -ge 5 ]; do
    echo $count
    ((count++))
done
```

### break / continue

```bash
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        continue                    # 跳过5
    fi
    if [ $i -eq 8 ]; then
        break                       # 到8停止
    fi
    echo $i
done
```

---

## 函数

```bash
# 定义
greet() {
    local name=$1                   # local 声明局部变量
    echo "Hello, $name!"
    return 0                        # 返回码（0=成功）
}

# 调用
greet "Linux"
echo "Return code: $?"

# 带返回值的函数
add() {
    echo $(( $1 + $2 ))             # 用 echo 输出结果
}
result=$(add 3 5)                   # 用命令替换获取结果
echo $result                        # 8
```

---

## 输入输出

```bash
# 读取用户输入
read -p "Enter your name: " name
read -sp "Enter password: " pass    # 不显示输入
echo

# 格式化输出
printf "Name: %-10s Age: %d\n" "Alice" 30
```

---

## 管道与重定向

```bash
# 管道
ls -la | grep ".txt" | wc -l

# 重定向
echo "hello" > file                 # 覆盖写入
echo "world" >> file                # 追加写入
command 2> error.log                # 标准错误重定向
command > out.log 2>&1              # 标准输出和标准错误合并
command &> all.log                  # 同上（Bash 简写）
command < input.txt                 # 标准输入重定向

# Here Document
cat <<EOF
line 1
line 2
$variable
EOF

# Here String
grep "pattern" <<< "$string"
```

---

## 错误处理

```bash
# 严格模式（推荐脚本开头加上）
set -euo pipefail
# -e：命令失败时立即退出
# -u：使用未定义变量时报错
# -o pipefail：管道中任意命令失败则整体失败

# trap 信号处理
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/tempfile
}
trap cleanup EXIT                   # 脚本退出时自动执行 cleanup

trap 'echo "Ctrl+C pressed"' INT   # 捕获 Ctrl+C

# 错误处理模式
if ! command; then
    echo "Command failed" >&2
    exit 1
fi

# || 简写
command || { echo "Failed" >&2; exit 1; }
```

---

## 实用脚本模板

```bash
#!/bin/bash
set -euo pipefail

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m'                        # No Color

log()   { echo -e "${GREEN}[INFO]${NC} $*"; }
error() { echo -e "${RED}[ERROR]${NC} $*" >&2; }

# 清理
cleanup() {
    rm -f /tmp/myscript_*
}
trap cleanup EXIT

# 参数检查
if [[ $# -lt 1 ]]; then
    error "Usage: $0 <argument>"
    exit 1
fi

# 主逻辑
main() {
    log "Starting with argument: $1"
    # ... 业务逻辑 ...
    log "Done"
}

main "$@"
```
