---
date: 2026-07-14
tags: [opencode, golang, goroutine, closure]
category: golang
---

# Go 语言

> 来源：OpenCode 历史会话 | ~10 条

## goroutine 闭包陷阱

```go
// ❌ 错误：所有 goroutine 共享同一个 u 变量引用
for _, u := range users {
    go func() {
        fmt.Println(u)  // 可能全部打印最后一个值
    }()
}

// ✅ 正确：参数传递，值在创建时拷贝
for _, u := range users {
    go func(u string) {
        fmt.Println(u)
    }(u)  // 实参，拷贝当前值
}
```

- `func(u string)` 中的 `u`：**形参名**
- `(u)` 中的 `u`：**实参**，从外部作用域传入
- 不传参会怎样：闭包捕获循环变量引用，goroutine 执行时循环可能已完成

## Go 闭包

闭包 = 函数 + 其引用的外部变量环境。Go 中闭包捕获变量**引用**而非值拷贝，这是 goroutine 陷阱的根源。

## os.Stat 开销

- 触发系统调用 `stat` / `lstat`
- 开销远大于内存操作
- 高频调用需考虑缓存或批量 stat
- 与 `os.Lstat` 区别：lstat 不跟随符号链接

## Go 构建外部命令（os/exec）

```go
// 注意参数转义与注入风险
cmd := exec.Command("powershell", "-Command", script)
```

PowerShell 用户/组管理示例：
```powershell
$user = Get-LocalUser -Name "username"
$group = Get-LocalGroup -SID 'S-1-5-32-545'  # Users 组
Remove-LocalGroupMember -Group $group -Member $user
```

## OpenCurrentProcessToken 弃用

- 原 API：获取进程主令牌句柄
- 弃用原因：UWP 沙箱等场景行为不可预测
- 替代：`OpenProcessToken(CurrentProcess(), desiredAccess)` + 明确指定访问权限
- 或 `GetCurrentProcessToken` 获取 TOKEN_QUERY 令牌

## Gin 框架端口冲突

```
listen tcp :8088: bind: address already in use
```

原因：8088 端口已被其他进程占用。解决：`ss -tlnp | grep 8088` 找到进程后释放。

## Go 测试规范

- 测试文件与被测源文件同目录
- `_test.go` 后缀
- 表驱动测试（table-driven）
- 清理未导出符号引用的测试 / 废弃测试模式
- 批量修复 import 路径

## int64 类型在 SMB 包中的使用追踪

工具链辅助的数据模型分析，用于理解 SMB 包的类型依赖和重构影响面。
