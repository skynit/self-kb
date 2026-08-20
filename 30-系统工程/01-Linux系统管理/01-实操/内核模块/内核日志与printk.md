---
title: 内核日志与printk
created: 2026-05-31
updated: 2026-05-31
type: concept
domain: 内核模块
tags:
  - OS
  - Linux
  - 内核模块
  - printk
  - 内核日志
---

> 属于 [[Linux实操MOC]] | 相关：[[内核模块入门]]、[[内核模块调试技巧]]

# 内核日志与 printk

## printk — 内核的 printf

`printk` 是内核空间的输出函数，类似用户空间的 `printf`，但有一些关键区别：

| 对比 | printf | printk |
|------|--------|--------|
| 运行空间 | 用户空间 | 内核空间 |
| 输出目标 | stdout | 内核环形缓冲区（ring buffer） |
| 查看方式 | 屏幕直接显示 | `dmesg` 命令查看 |
| 线程安全 | 否 | 是 |
| 可中断上下文 | 否 | 是（中断处理函数中也可调用） |

---

## 日志级别

printk 的第一个参数是**日志级别**，格式为 `KERN_LEVEL "message"`：

```c
printk(KERN_EMERG   "emergency: system is unusable\n");       // 0
printk(KERN_ALERT   "alert: action must be taken immediately\n"); // 1
printk(KERN_CRIT    "critical: critical conditions\n");       // 2
printk(KERN_ERR     "error: error conditions\n");             // 3
printk(KERN_WARNING "warning: warning conditions\n");         // 4
printk(KERN_NOTICE  "notice: normal but significant\n");      // 5
printk(KERN_INFO    "info: informational\n");                 // 6
printk(KERN_DEBUG   "debug: debug-level messages\n");         // 7
```

| 级别 | 值 | 含义 | 何时使用 |
|------|-----|------|----------|
| `KERN_EMERG` | 0 | 系统不可用 | 极端情况，几乎不用 |
| `KERN_ALERT` | 1 | 需要立即处理 | 硬件严重故障 |
| `KERN_CRIT` | 2 | 临界条件 | 严重错误 |
| `KERN_ERR` | 3 | 错误 | 驱动错误、操作失败 |
| `KERN_WARNING` | 4 | 警告 | 可恢复的异常 |
| `KERN_NOTICE` | 5 | 正常但重要 | 状态变化 |
| `KERN_INFO` | 6 | 信息 | 模块加载/卸载信息 |
| `KERN_DEBUG` | 7 | 调试 | 开发调试用 |

> 数字越小优先级越高。只有级别值**小于**控制台日志级别的消息才会显示在控制台。

---

## 控制台日志级别

```bash
# 查看当前控制台日志级别
cat /proc/sys/kernel/printk
# 输出：4  4  1  7
#       ↑  ↑  ↑  ↑
#       当前 默认 最低 缺省启动级别
# 含义：级别 0-3 的消息会显示在控制台

# 临时修改（显示所有级别）
echo 8 > /proc/sys/kernel/printk

# 恢复默认
echo 4 > /proc/sys/kernel/printk
```

---

## dmesg 命令

```bash
# 查看所有内核日志
dmesg

# 实时监控（类似 tail -f）
dmesg -w

# 只看错误和警告
dmesg -l err,warn

# 带时间戳
dmesg -T

# 清空缓冲区
sudo dmesg -c

# 按级别过滤
dmesg -l 3          # 只看 KERN_ERR
dmesg -l 6          # 只看 KERN_INFO
```

---

## 环形缓冲区

内核日志存储在**环形缓冲区**（ring buffer）中：

```
大小：默认 256KB ~ 1MB（取决于内核配置）

查看大小：
dmesg --buffer-size
cat /proc/sys/kernel/printk

修改大小（内核启动参数）：
log_buf_len=4M
```

缓冲区满时，最老的日志会被覆盖。如果开机信息被冲掉，可以增大缓冲区。

---

## pr_* 系列宏（推荐用法）

内核提供了更方便的 `pr_*` 宏，自动添加模块名前缀：

```c
#include <linux/printk.h>

pr_emerg("message\n");
pr_alert("message\n");
pr_crit("message\n");
pr_err("message\n");
pr_warn("message\n");
pr_notice("message\n");
pr_info("message\n");
pr_debug("message\n");    // 需要定义 DEBUG 或 CONFIG_DYNAMIC_DEBUG
```

等价于：
```c
printk(KERN_ERR "my_module: " "message\n");
//                ^^^^^^^^^ 自动加模块名
```

---

## dev_* 系列宏（设备驱动推荐）

对于设备驱动，推荐使用 `dev_*` 宏，自动附加设备信息：

```c
#include <linux/device.h>

// 需要 struct device *dev 指针
dev_emerg(dev, "message\n");
dev_alert(dev, "message\n");
dev_crit(dev, "message\n");
dev_err(dev, "message\n");
dev_warn(dev, "message\n");
dev_notice(dev, "message\n");
dev_info(dev, "message\n");
dev_dbg(dev, "message\n");
```

输出格式：
```
[  123.456789] my_device 0000:01:00.0: device initialized
```

包含设备名称和 PCI 地址，比 `printk` 更清晰。

---

## 动态调试（Dynamic Debug）

编译时用 `dev_dbg` / `pr_debug` 的消息默认不输出，运行时可动态开启：

```bash
# 开启某个文件中所有调试消息
echo 'file simple_char.c +p' > /sys/kernel/debug/dynamic_debug/control

# 开启某个函数的调试消息
echo 'func my_read +p' > /sys/kernel/debug/dynamic_debug/control

# 开启某个模块的调试消息
echo 'module mymodule +p' > /sys/kernel/debug/dynamic_debug/control

# 关闭
echo 'file simple_char.c -p' > /sys/kernel/debug/dynamic_debug/control

# 查看所有已开启的调试点
cat /sys/kernel/debug/dynamic_debug/control | grep '=p'
```

---

## 常见错误

```c
// ❌ 错误：忘记换行
printk(KERN_INFO "hello");

// ✅ 正确：必须加 \n
printk(KERN_INFO "hello\n");

// ❌ 错误：级别和消息之间有逗号
printk(KERN_INFO, "hello\n");

// ✅ 正确：级别和消息是字符串拼接（无逗号）
printk(KERN_INFO "hello\n");

// ❌ 错误：在 printk 中使用浮点
printk("value = %f\n", 3.14);    // 内核不支持浮点格式化

// ✅ 正确：用整数表示
printk("value = %d\n", 314);     // 或用定点数
```

---

## 快速对照表

| 场景 | 推荐用法 |
|------|----------|
| 通用内核消息 | `pr_info("msg\n")` / `pr_err("msg\n")` |
| 设备驱动消息 | `dev_info(dev, "msg\n")` / `dev_err(dev, "msg\n")` |
| 调试（可动态开关） | `pr_debug("msg\n")` / `dev_dbg(dev, "msg\n")` |
| 查看日志 | `dmesg` / `dmesg -w` / `dmesg -l err` |
