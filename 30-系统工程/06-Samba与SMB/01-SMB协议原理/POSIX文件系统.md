---
title: POSIX 文件系统
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: 操作系统
tags:
  - OS
  - POSIX
  - 文件系统
  - Linux
---

# POSIX 文件系统

> 属于 [[Samba学习索引]] | 相关：[[Samba概念讲解]]

---

## 一句话定义

POSIX（Portable Operating System Interface）是 IEEE 制定的**操作系统接口标准**。"POSIX 文件系统"不是某种具体文件系统（如 ext4），而是一组**规定文件系统必须提供的行为和接口的规范**。

---

## 核心含义

```
ext4 / xfs / btrfs / tmpfs ...    ← 具体文件系统实现
         │
         │  都遵循
         ▼
   POSIX 文件系统接口规范            ← 标准（行为 + API）
```

只要实现了 POSIX 规定的接口，就是"POSIX 兼容文件系统"。Linux 下的 ext4、xfs、btrfs 都是。

---

## POSIX 规定了什么

### 文件模型

| 概念 | POSIX 规定 |
|------|----------|
| **一切皆文件** | 普通文件、目录、设备、管道、socket 统一用文件 API 操作 |
| **文件描述符** | int fd，非负整数，0=stdin 1=stdout 2=stderr |
| **字节流** | 文件内容是无结构的字节序列，无记录边界 |
| **路径** | 用 `/` 分隔的层级目录树，绝对路径从 `/` 开始 |
| **权限模型** | owner / group / other × read / write / execute（9 位） |
| **硬链接** | 多个路径名指向同一 inode |
| **符号链接** | 指向另一个路径的特殊文件 |

### 核心 API

```c
// 文件操作
int     open(const char *path, int flags, mode_t mode);
ssize_t read(int fd, void *buf, size_t count);
ssize_t write(int fd, const void *buf, size_t count);
off_t   lseek(int fd, off_t offset, int whence);
int     close(int fd);

// 目录操作
DIR*           opendir(const char *name);
struct dirent* readdir(DIR *dirp);
int            closedir(DIR *dirp);

// 元数据
int   stat(const char *path, struct stat *buf);
int   chmod(const char *path, mode_t mode);
int   chown(const char *path, uid_t owner, gid_t group);

// 链接
int   link(const char *old, const char *new);       // 硬链接
int   symlink(const char *target, const char *link); // 符号链接
int   unlink(const char *path);                      // 删除

// 其他
int   mkdir(const char *path, mode_t mode);
int   rename(const char *old, const char *new);
int   truncate(const char *path, off_t length);
```

### 权限模型

```
  rwx rwx rwx
  ─── ─── ───
  拥  组  其
  有  权  他
  者  限  人

  7=rwx  6=rw-  5=r-x  4=r--  0=---

  chmod 755 = rwxr-xr-x  （目录/可执行）
  chmod 644 = rw-r--r--  （普通文件）
```

---

## POSIX 权限 vs Windows ACL

这是 Samba 需要做翻译的核心差异：

```
POSIX 权限（简单）                    Windows ACL（精细）
┌───────────────────┐               ┌────────────────────────────┐
│ owner: rwx        │               │ Alice: Read+Write+Delete   │
│ group: r-x        │               │ Bob: Read                  │
│ other: r--        │               │ Sales组: Read+Write        │
│                   │               │ Everyone: Read             │
│ 只有 3 个主体     │               │ 可为任意用户/组设独立权限  │
│ 每个主体只有 rwx  │               │ 权限类型：十几种           │
└───────────────────┘               └────────────────────────────┘
         ↕ Samba 翻译 ↕

映射方式：
  Windows Read     → POSIX r--
  Windows Write    → POSIX -w-
  Windows Execute  → POSIX --x
  Windows Full     → POSIX rwx
  Windows Deny     → 无直接对应（POSIX 没有 Deny 概念）
```

> **精度丢失**：POSIX 权限无法表达"Bob 能读但不能删"这种细粒度控制。Samba 的 `acl_xattr` VFS 插件把 Windows ACL 存为 xattr，尽量保留完整语义。

---

## 一句话总结

POSIX 文件系统 = **Unix/Linux 系统对"文件系统该怎么行为"的标准约定**——包括 API、权限模型、目录结构、文件语义。Samba 的核心工作就是把 Windows 的 SMB 语义翻译成 POSIX 语义，反之亦然。

---

> 回到 [[Samba学习索引]]
