---
title: "1内核模块   Linux最简单的内核模块，入门内核编程和设备驱动开发"
source: "https://www.youtube.com/watch?v=triv8bcVLSQ&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow"
author:
  - "[[大笑脸]]"
published: 2023-11-02
created: 2026-05-31
updated: 2026-05-31
description: "《Linux内核编程开发指南【从小工到专家】》完成播放列表：https://youtube.com/playlist?list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&si=YZeisJBVZnxjxLJB"
tags:
  - "clippings"
related:
  - "[[30-系统工程/01-Linux系统管理/01-实操/内核模块/内核模块入门|整理笔记]]"
---


![](https://www.youtube.com/watch?v=triv8bcVLSQ)

《Linux内核编程开发指南【从小工到专家】》完成播放列表：https://youtube.com/playlist?list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&si=YZeisJBVZnxjxLJB

---

## 视频转录

> 转录方式：yt-dlp 下载音频 + faster-whisper (base) 语音识别，人工校正专业术语。

嗨大家好，这期视频给大家用一个简单的内核模块来演示内核模块代码的编写、编译以及使用。

首先我们来看一下这个内核模块的代码。这个代码非常简单，主要实现的功能就是在模块加载的时候打印 "Hello Linux Module" 这句话，然后在模块移除的时候打印 "Goodbye Linux Module" 这句话。

我们来看一下这个代码。最开始的话我们需要包含 `linux/module.h` 头文件，这是内核模块必须包含的头文件。然后在有些教材或者是书籍中还会包含 `linux/init.h`，不过我发现在 `linux/module.h` 里面已经包含了这个头文件，所以在这里其实可以不用包含 `linux/init.h` 头文件。

下面这个是**初始化函数**（`hello_init`），在模块加载的时候会调用这个初始化函数，进行一些必要的初始化工作。那这里我们只是打印一个信息。在初始化函数前面有一个 `__init` 标识，这个标识就指明这个函数只在初始化期间被调用，然后在初始化完成也就是 `return` 以后这个函数就没有用了，内核就会把这个函数所占空间回收释放掉内存。所以说这个 `__init` 标识只能用在初始化函数，也就是只被调用一次的函数上使用。如果有一个函数被重复的调用是千万不能用这个标识的。

下面这个是**清除函数**（`hello_exit`），在模块移除之前会调用这个函数做一些清理的工作，这里我们也只是让它简单的打印一些信息。

下面这两个是宏：`module_init` 和 `module_exit`。`module_init` 宏指名模块的初始化函数是哪个，那这里就是 `hello_init`。`module_exit` 指名模块的清除函数是哪个，这里是 `hello_exit`。

在下面是一些描述性的定义：`MODULE_LICENSE` 指名的是许可的协议，`MODULE_AUTHOR` 指名的是作者，`MODULE_VERSION` 指名的是版本号。这里在每个模块中都要包含这个 `LICENSE`，指名模块的许可协议，不然的话在编译或者是在加载的时候会提示一些警告信息。

这就是我们最简单的内核模块的代码。

编译这个模块，我们还需要用到一个 **Makefile** 文件。我们来看一下这个 Makefile。这个 Makefile 也比较简单，首先是定义了两个宏变量：`KERN_DIR` 指名的是内核源码数的目录，`PWD` 指名的是工作目录，也就是当前目录。这个 `hello.o` 就是我们要生成的内核模块。然后在里面就是我们的 Make 目标。下面是一个 Make 命令：`make -C` 后面接的就是内核源码数的目录，`M=` 是这个工作目录，也就是生成内核模块的目录。`modules` 是一个 Makefile 目标。这里详细的我们不需要过多了解，我们只要知道这个命令的形式就可以了，它背后的一些复杂的机制我们暂时都不需要了解。然后 `clean` 就是清理一些生成的中间文件。

这里这个内核源码数是在 `/lib/modules/` 下，然后这个是内核版本。我们先来看一下我们的内核版本：`uname -r`。在编译这个内核模块之前，我们需要安装一下内核源码数目录。安装的方法是使用 `sudo apt-get install linux-headers-$(uname -r)`。这里我们需要把 `linux-headers` 和 `linux-headers-generic` 这两个都安装上。这里我已经安装过了，所以这里没有下载，那你没有安装的话它这里会需要安装一段时间。

那我们来看一下 `/lib/modules/$(uname -r)/build` 到底是什么。我们可以看到这个 `build` 其实指向的就是 `/usr/src/linux-headers-xxx` 这个目录。所以说在 Makefile 里面，这个不是真正的源码数目录，它只是一个软链接。那真正的目录是在 `/usr/src/linux-headers-xxx` 下。

Makefile 和那些必要的文件我们都安装好以后，我们使用 `make` 就可以进行编译。通过这里的提示信息，我们可以看到实际上就是进入了 `/usr/src/linux-headers` 的目录，跟我们之前看到的也是一样的。然后通过 CC 命令先生成 `.o`，然后再把它转成这个模块。这个 `hello.ko` 就是最后的内核模块文件。

加载内核模块需要用到一个命令叫 `insmod`，然后它需要用管理员权限，所以加 `sudo`。这样一个模块就加载进去了。我们可以通过 `lsmod` 来查看，在最上边这里，这个 `hello` 就是我们刚刚加载的内核模块。

通过 `dmesg` 可以查看我们刚才打出的信息，这个 "Hello Linux Module" 这句话就是我们刚才在加载模块的时候打出来的。

然后移除内核模块需要用到 `rmmod`，同样它需要用到管理员权限，所以加 `sudo`。`sudo rmmod hello`，这样内核模块就被移除了。再用 `lsmod` 来看，这里就没有 `hello` 那个模块了。同样看 `dmesg`，这里打出了在移除的时候那个清除的信息 "Goodbye Linux"。

这样一个最简单的 Linux 内核模块就给大家演示完成了。

之前我们说在源码中不加这个 `LICENSE` 信息会有一些提示或者警告，我们来看一下。我们把它注释掉，然后 `make`，这里就会提示 `MODULE_LICENSE` 缺失。所以说如果你看到这样的一个提示，就说明你也没有加这个。

查看内核模块除了可以用 `lsmod` 以外，还可以使用 Linux 的一些文件系统来查看。这里给大家演示一下，我们先把这个模块插进去。插进去以后，我们可以通过 `cat /proc/modules` 来查看，通过这里我们也可以看到我们刚刚插入这个 `hello` 模块。实际上 `lsmod` 命令其实就是在查询 `/proc/modules` 文件，然后输出这些信息。

另外我们也可以通过 **sysfs** 文件系统。在 `/sys/module/` 下有一个 `hello` 目录，就可以看到我们这个 `hello` 的文件夹。我们每加载一个模块就会在 `/sys/module/` 生成一个文件夹，这个文件夹下面就会有一些这个模块相关的一些信息。这些详细的我们后续用到再介绍，这里就不做过多的介绍了。

我们把模块移除，我们再来看，这里就没有那个 `hello` 了，然后 sysfs 文件系统下也没有 `hello` 文件夹了。

OK，这期视频关于 Linux 内核模块的一个简单的介绍就到这里了，希望对小伙伴们有所帮助。