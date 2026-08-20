---
title: Linux实操MOC
created: 2026-05-29
updated: 2026-05-31
type: moc
tags:
  - OS
  - MOC
  - Linux
---

# Linux 实操 MOC

## 内核模块

- [[内核模块入门]] — 最简单的内核模块，编译、加载、卸载全流程
- [[模块参数]] — 加载时传参、运行时通过 sysfs 修改参数
- [[符号导出与模块依赖]] — EXPORT_SYMBOL、modprobe 自动依赖
- [[字符设备驱动基础]] — cdev、file_operations、copy_to/from_user
- [[内核日志与printk]] — 日志级别、dmesg、pr_*/dev_* 宏
- [[proc文件系统接口]] — /proc 条目创建、seq_file、可读写接口
- [[内核内存管理]] — kmalloc/vmalloc/slab、GFP 标志、常见内存错误
- [[内核模块调试技巧]] — Oops 解读、goto 清理、防御性编程

## 基础命令

- [[基础命令速查]] — ls/cd/cp/mv/rm/find/touch/tree 全量速查

## 文本处理

- [[文本处理三剑客]] — grep 搜索、sed 流编辑、awk 列处理 + sort/uniq/cut/tr

## 进程管理

- [[进程管理命令]] — ps/top/htop、kill 信号、systemctl 服务管理、journalctl 日志

## 文件系统操作

- [[文件系统操作命令]] — fdisk/parted 分区、mount/fstab 挂载、df/du 空间管理

## 网络工具

- [[网络工具速查]] — ip/ss 网络配置、ssh/scp/rsync 远程、curl/wget HTTP、防火墙

## 用户与权限

- [[用户与权限管理]] — useradd/usermod、sudo/sudoers、chmod/chown、umask

## Shell 编程

- [[Shell编程基础]] — 变量、数组、条件判断、循环、函数、管道重定向、错误处理
