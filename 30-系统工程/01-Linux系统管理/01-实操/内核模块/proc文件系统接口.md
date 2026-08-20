---
title: proc文件系统接口
created: 2026-05-31
updated: 2026-05-31
type: concept
domain: 内核模块
tags:
  - OS
  - Linux
  - 内核模块
  - proc文件系统
  - 内核接口
---

> 属于 [[Linux实操MOC]] | 相关：[[内核模块入门]]、[[字符设备驱动基础]]

# proc 文件系统接口

## 什么是 /proc

`/proc` 是一个**虚拟文件系统**，不占用磁盘空间，是内核向用户空间暴露信息的接口。

```bash
cat /proc/cpuinfo      # CPU 信息
cat /proc/meminfo       # 内存信息
cat /proc/modules       # 已加载模块
cat /proc/version       # 内核版本
cat /proc/uptime        # 运行时间
```

> 读取这些"文件"时，内核动态生成内容返回给用户空间，不是真正从磁盘读取。

---

## 在模块中创建 /proc 条目

### 基本接口（seq_file，推荐）

```c
#include <linux/proc_fs.h>
#include <linux/seq_file.h>

// seq_file 的 show 回调：当用户读取 /proc/myinfo 时调用
static int myinfo_show(struct seq_file *m, void *v)
{
    seq_printf(m, "Hello from /proc/myinfo!\n");
    seq_printf(m, "Module loaded at: %pS\n", myinfo_show);
    seq_printf(m, "Jiffies: %lu\n", jiffies);
    return 0;
}

// open 回调
static int myinfo_open(struct inode *inode, struct file *file)
{
    return single_open(file, myinfo_show, NULL);
}

static const struct proc_ops myinfo_ops = {
    .proc_open    = myinfo_open,
    .proc_read    = seq_read,
    .proc_lseek   = seq_lseek,
    .proc_release = single_release,
};

static struct proc_dir_entry *my_proc_entry;

static int __init my_init(void)
{
    // 创建 /proc/myinfo
    my_proc_entry = proc_create("myinfo", 0444, NULL, &myinfo_ops);
    return 0;
}

static void __exit my_exit(void)
{
    proc_remove(my_proc_entry);
}

module_init(my_init);
module_exit(my_exit);
MODULE_LICENSE("GPL");
```

```bash
# 使用
sudo insmod proc_demo.ko
cat /proc/myinfo
# Hello from /proc/myinfo!
# Module loaded at: myinfo_show
# Jiffies: 12345678
```

---

## proc_ops 结构体

`proc_ops` 是 `/proc` 文件操作的核心（Linux 5.6+ 替代了 `file_operations`）：

| 成员 | 触发时机 | 说明 |
|------|----------|------|
| `.proc_open` | `open()` | 打开文件时调用 |
| `.proc_read` | `read()` | 读取文件时调用 |
| `.proc_write` | `write()` | 写入文件时调用（可实现运行时配置） |
| `.proc_lseek` | `lseek()` | 移动文件指针 |
| `.proc_release` | `close()` | 关闭文件时调用 |

---

## 可写的 /proc 条目

让 `/proc` 条目支持写入，实现**运行时配置**：

```c
#include <linux/proc_fs.h>
#include <linux/seq_file.h>
#include <linux/uaccess.h>

static int debug_level = 1;

static int myconfig_show(struct seq_file *m, void *v)
{
    seq_printf(m, "debug_level = %d\n", debug_level);
    return 0;
}

static int myconfig_open(struct inode *inode, struct file *file)
{
    return single_open(file, myconfig_show, NULL);
}

// 写入回调
static ssize_t myconfig_write(struct file *file, const char __user *buf,
                              size_t count, loff_t *ppos)
{
    char kbuf[16];
    int val;

    if (count >= sizeof(kbuf))
        return -EINVAL;

    if (copy_from_user(kbuf, buf, count))
        return -EFAULT;

    kbuf[count] = '\0';

    if (kstrtoint(kbuf, 10, &val))   // 字符串转整数
        return -EINVAL;

    debug_level = val;
    pr_info("debug_level changed to %d\n", val);
    return count;
}

static const struct proc_ops myconfig_ops = {
    .proc_open    = myconfig_open,
    .proc_read    = seq_read,
    .proc_write   = myconfig_write,
    .proc_lseek   = seq_lseek,
    .proc_release = single_release,
};
```

```bash
# 读取
cat /proc/myconfig
# debug_level = 1

# 写入
echo 3 > /proc/myconfig

# 验证
cat /proc/myconfig
# debug_level = 3
```

---

## 子目录

在 `/proc` 下创建子目录来组织多个条目：

```c
static struct proc_dir_entry *my_dir;

static int __init my_init(void)
{
    // 创建 /proc/mydriver/ 目录
    my_dir = proc_mkdir("mydriver", NULL);

    // 在子目录下创建文件
    proc_create("info", 0444, my_dir, &info_ops);
    proc_create("config", 0644, my_dir, &config_ops);

    return 0;
}

static void __exit my_exit(void)
{
    proc_remove(my_dir);  // 递归删除目录及所有子条目
}
```

效果：
```
/proc/mydriver/
├── info       (只读)
└── config     (可读写)
```

---

## seq_file 与 single_open

| 函数 | 用途 |
|------|------|
| `single_open(file, show, data)` | 适用于内容较少的情况，一次性输出 |
| `seq_open(file, ops)` | 适用于大量数据，分段输出（迭代器模式） |
| `seq_printf(m, fmt, ...)` | 在 show 回调中输出格式化内容 |
| `seq_puts(m, str)` | 输出字符串（无格式化） |
| `seq_putc(m, c)` | 输出单个字符 |

### 大量数据用 seq_iter

```c
// 定义迭代器
static void *my_seq_start(struct seq_file *m, loff_t *pos)
{
    if (*pos >= MAX_ITEMS)
        return NULL;
    return &my_array[*pos];
}

static void *my_seq_next(struct seq_file *m, void *v, loff_t *pos)
{
    (*pos)++;
    if (*pos >= MAX_ITEMS)
        return NULL;
    return &my_array[*pos];
}

static void my_seq_stop(struct seq_file *m, void *v) {}

static int my_seq_show(struct seq_file *m, void *v)
{
    struct my_item *item = v;
    seq_printf(m, "item: %d\n", item->value);
    return 0;
}

static const struct seq_operations my_seq_ops = {
    .start = my_seq_start,
    .next  = my_seq_next,
    .stop  = my_seq_stop,
    .show  = my_seq_show,
};
```

---

## 注意事项

| 要点 | 说明 |
|------|------|
| 不存数据 | `/proc` 是虚拟的，读取时动态生成，不存储数据 |
| 权限控制 | `0444` 只读，`0644` 可读写，`0400` 仅 root 可读 |
| 命名规范 | 避免与其他 `/proc` 条目冲突，用模块名作前缀 |
| 清理 | 模块卸载时**必须** `proc_remove()`，否则残留条目会导致内核崩溃 |
| proc_ops vs file_operations | Linux 5.6+ 用 `proc_ops`，旧内核用 `file_operations` |
