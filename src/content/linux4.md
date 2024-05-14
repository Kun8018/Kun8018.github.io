---
title: Linux4
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - IT，
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OI3V.th.jpg
---

​     服务器配置

<!--more-->

对于容器技术而言，它实现资源层面上的限制和隔离，依赖于 Linux 内核所提供的 cgroup 和 namespace 技术。

我们先对这两项技术的作用做个概括：

- cgroup 的主要作用：管理资源的分配、限制；
- namespace 的主要作用：封装抽象，限制，隔离，使命名空间内的进程看起来拥有他们自己的全局资源；

## cgroups

cgroups 是 Linux 内核提供的一种可以限制单个进程或者多个进程所使用资源的机制，可以对 cpu，内存等资源实现精细化的控制，目前越来越火的轻量级容器 Docker 就使用了 cgroups 提供的资源限制能力来完成 cpu，内存等部分的资源控制。

另外，开发者也可以使用 cgroups 提供的精细化控制能力，限制某一个或者某一组进程的资源使用。比如在一个既部署了前端 web 服务，也部署了后端计算模块的八核服务器上，可以使用 cgroups 限制 web server 仅可以使用其中的六个核，把剩下的两个核留给后端计算模块。

cgroup 主要有两个组成部分：

- core - 负责分层组织过程；
- controller - 通常负责沿层次结构分配特定类型的系统资源。每个 cgroup 都有一个 `cgroup.controllers` 文件，其中列出了所有可供 cgroup 启用的控制器。当在 `cgroup.subtree_control` 中指定多个控制器时，要么全部成功，要么全部失败。在同一个控制器上指定多项操作，那么只有最后一个生效。每个 cgroup 的控制器销毁是异步的，在引用时同样也有着延迟引用的问题；

所有 cgroup 核心接口文件都以 `cgroup` 为前缀。每个控制器的接口文件都以控制器名称和一个点为前缀。控制器的名称由小写字母和 “*” 组成，但永远不会以 “*” 开头。

cgroups 子系统

cgroups 的全称是 control groups，cgroups 为每种可以控制的资源定义了一个子系统。典型的子系统介绍如下：

1. cpu 子系统，主要限制进程的 cpu 使用率。
2. cpuacct 子系统，可以统计 cgroups 中的进程的 cpu 使用报告。
3. cpuset 子系统，可以为 cgroups 中的进程分配单独的 cpu 节点或者内存节点。
4. memory 子系统，可以限制进程的 memory 使用量。
5. blkio 子系统，可以限制进程的块设备 io。
6. devices 子系统，可以控制进程能够访问某些设备。
7. net_cls 子系统，可以标记 cgroups 中进程的网络数据包，然后可以使用 tc 模块（traffic control）对数据包进行控制。
8. freezer 子系统，可以挂起或者恢复 cgroups 中的进程。
9. ns 子系统，可以使不同 cgroups 下面的进程使用不同的 namespace。

这里面每一个子系统都需要与内核的其他模块配合来完成资源的控制，比如对 cpu 资源的限制是通过进程调度模块根据 cpu 子系统的配置来完成的；对内存资源的限制则是内存模块根据 memory 子系统的配置来完成的，而对网络数据包的控制则需要 Traffic Control 子系统来配合完成。本文不会讨论内核是如何使用每一个子系统来实现资源的限制，而是重点放在内核是如何把 cgroups 对资源进行限制的配置有效的组织起来的，和内核如何把 cgroups 配置和进程进行关联的，以及内核是如何通过 cgroups 文件系统把 cgroups 的功能暴露给用户态的。

cgroups 层级结构（Hierarchy）

内核使用 cgroup 结构体来表示一个 control group 对某一个或者某几个 cgroups 子系统的资源限制。cgroup 结构体可以组织成一颗树的形式，每一棵 cgroup 结构体组成的树称之为一个 cgroups 层级结构。cgroups 层级结构可以 attach 一个或者几个 cgroups 子系统，当前层级结构可以对其 attach 的 cgroups 子系统进行资源的限制。每一个 cgroups 子系统只能被 attach 到一个 cpu 层级结构中。

在创建了 cgroups 层级结构中的节点（cgroup 结构体）之后，可以把进程加入到某一个节点的控制任务列表中，一个节点的控制列表中的所有进程都会受到当前节点的资源限制。同时某一个进程也可以被加入到不同的 cgroups 层级结构的节点中，因为不同的 cgroups 层级结构可以负责不同的系统资源。所以说进程和 cgroup 结构体是一个多对多的关系。

cgroups 文件系统

Linux 使用了多种数据结构在内核中实现了 cgroups 的配置，关联了进程和 cgroups 节点，那么 Linux 又是如何让用户态的进程使用到 cgroups 的功能呢？ Linux 内核有一个很强大的模块叫 VFS (Virtual File System)。 VFS 能够把具体文件系统的细节隐藏起来，给用户态进程提供一个统一的文件系统 API 接口。 cgroups 也是通过 VFS 把功能暴露给用户态的，cgroups 与 VFS 之间的衔接部分称之为 cgroups 文件系统。下面先介绍一下 VFS 的基础知识，然后再介绍下 cgroups 文件系统的实现。

VFS

VFS 是一个内核抽象层，能够隐藏具体文件系统的实现细节，从而给用户态进程提供一套统一的 API 接口。VFS 使用了一种通用文件系统的设计，具体的文件系统只要实现了 VFS 的设计接口，就能够注册到 VFS 中，从而使内核可以读写这种文件系统。 这很像面向对象设计中的抽象类与子类之间的关系，抽象类负责对外接口的设计，子类负责具体的实现。其实，VFS 本身就是用 c 语言实现的一套面向对象的接口。

通用文件模型

VFS 通用文件模型中包含以下四种元数据结构：

1. 超级块对象 (superblock object)，用于存放已经注册的文件系统的信息。比如 ext2，ext3 等这些基础的磁盘文件系统，还有用于读写 socket 的 socket 文件系统，以及当前的用于读写 cgroups 配置信息的 cgroups 文件系统等。
2. 索引节点对象 (inode object)，用于存放具体文件的信息。对于一般的磁盘文件系统而言，inode 节点中一般会存放文件在硬盘中的存储块等信息；对于 socket 文件系统，inode 会存放 socket 的相关属性，而对于 cgroups 这样的特殊文件系统，inode 会存放与 cgroup 节点相关的属性信息。这里面比较重要的一个部分是一个叫做 inode_operations 的结构体，这个结构体定义了在具体文件系统中创建文件，删除文件等的具体实现。
3. 文件对象 (file object)，一个文件对象表示进程内打开的一个文件，文件对象是存放在进程的文件描述符表里面的。同样这个文件中比较重要的部分是一个叫 file_operations 的结构体，这个结构体描述了具体的文件系统的读写实现。当进程在某一个文件描述符上调用读写操作时，实际调用的是 file_operations 中定义的方法。 对于普通的磁盘文件系统，file_operations 中定义的就是普通的块设备读写操作；对于 socket 文件系统，file_operations 中定义的就是 socket 对应的 send/recv 等操作；而对于 cgroups 这样的特殊文件系统，file_operations 中定义的就是操作 cgroup 结构体等具体的实现。
4. 目录项对象 (dentry object)，在每个文件系统中，内核在查找某一个路径中的文件时，会为内核路径上的每一个分量都生成一个目录项对象，通过目录项对象能够找到对应的 inode 对象，目录项对象一般会被缓存，从而提高内核查找速度。

委派和迁移

https://moelove.info/2021/11/17/%E4%B8%80%E7%AF%87%E6%90%9E%E6%87%82%E5%AE%B9%E5%99%A8%E6%8A%80%E6%9C%AF%E7%9A%84%E5%9F%BA%E7%9F%B3-cgroup/

https://tech.meituan.com/2015/03/31/cgroups.html

https://zhuanlan.zhihu.com/p/81668069

## namespace

namespace 是 Linux 内核的一项特性，它可以对内核资源进行分区，使得一组进程可以看到一组资源；而另一组进程可以看到另一组不同的资源。该功能的原理是为一组资源和进程使用相同的 namespace，但是这些 namespace 实际上引用的是不同的资源。

简单来说 namespace 是由 Linux 内核提供的，用于进程间资源隔离的一种技术。将全局的系统资源包装在一个抽象里，让进程（看起来）拥有独立的全局资源实例。同时 Linux 也默认提供了多种 namespace，用于对多种不同资源进行隔离。

在之前，我们单独使用 namespace 的场景比较有限，但 namespace 却是容器化技术的基石

cgroup namespace 提供的了一系列的隔离支持：

- 防止信息泄漏（容器不应该看到容器外的任何信息）。
- 简化了容器迁移。
- 限制容器进程资源，因为它会把 cgroup 文件系统进行挂载，使得容器进程无法获取上层的访问权限。

每个 cgroup namespace 都有自己的一组 cgroup 根目录。这些 cgroup 的根目录是在 `/proc/[pid]/cgroup` 文件中对应记录的相对位置的基点。当一个进程用 `CLONE_NEWCGROUP`（clone (2) 或者 unshare (2)） 创建一个新的 cgroup namespace 时，它当前的 cgroups 的目录就变成了新 namespace 的 cgroup 根目录

Network namespaces 隔离了与网络相关的系统资源（这里罗列一些）：

- network devices - 网络设备
- IPv4 and IPv6 protocol stacks - IPv4、IPv6 的协议栈
- IP routing tables - IP 路由表
- firewall rules - 防火墙规则
- /proc/net （即 /proc/PID/net）
- /sys/class/net
- /proc/sys/net 目录下的文件
- 端口、socket
- UNIX domain abstract socket namespace

使用 Network namespaces 需要内核支持 CONFIG_NET_NS 选项



https://moelove.info/2021/12/10/%E6%90%9E%E6%87%82%E5%AE%B9%E5%99%A8%E6%8A%80%E6%9C%AF%E7%9A%84%E5%9F%BA%E7%9F%B3-namespace-%E4%B8%8A/

## 超频检测工具

turbostat 是超频检测工具

## Kernel

`kernel` RPM 是一个元数据软件包，它不包含任何文件，而是保证正确安装了以下子软件包

- `kernel-core`

  包含 Linux 内核的二进制镜像 (`vmlinuz`)。

- `kernel-modules-core`

  包含基本内核模块，以确保核心功能正常工作。这包括最常用硬件正常功能的基本模块。

- `kernel-modules`

  包含 `kernel-core` 中不存在的其余内核模块。

`kernel-core` 和 `kernel-modules-core` 子软件包可以一起用在虚拟和云环境中，为 RHEL 9 内核提供快速引导时间和小磁盘空间。在此类部署通常不需要 `kernel-modules` 子软件包。

可选内核软件包：

- `kernel-modules-extra`

  包含用于罕见硬件的内核模块，以及默认加载被禁用的模块。

- `kernel-debug`

  包含一个为内核诊断启用了大量调试选项的内核，但代价是降低了性能。

- `kernel-tools`

  包含用于操作 Linux 内核和支持文档的工具。

- `kernel-devel`

  包含足以针对 `kernel` 软件包构建模块的内核标头和 makefile。

- `kernel-abi-stablelists`

  包含与 RHEL 内核 ABI 相关的信息，包括外部 Linux 内核模块所需的内核符号列表和帮助强制执行的 `dnf` 插件。

- `kernel-headers`

  包括指定 Linux 内核和用户空间库以及程序间接口的 C 标头文件。头文件定义了构建大多数标准程序所需的常量结构和常量。

- `kernel-uki-virt`

  包含 RHEL 内核的统一内核镜像 (UKI)。UKI 将 Linux 内核、`initramfs` 和内核命令行合并到一个签名二进制中，这个二进制文件可以直接从 UEFI 固件引导。`kernel-uki-virt` 包含在虚拟化和云环境中运行所需的内核模块，并可以用来代替 `kernel-core` 子软件包



## 升级Kernel的6种方式

https://linux.cn/article-12125-1.html

1. 使用 dpkg 升级 Linux 内核
2. 使用apt-get升级

```shell
$ sudo apt-get update
$ sudo apt-get upgrade
```

3. 使用Ukuu升级

**Ukuu** 是一个 Gtk GUI 和命令行工具，它可以从 kernel.ubuntu.com 下载最新的 Linux 主线内核，并自动安装到你的 Ubuntu 桌面版和服务器版中。Ukku 不仅简化了手动下载和安装新内核的过程，同时也会帮助你安全地移除旧的和不再需要的内核。更多细节可以参照以下指南。

- [Ukuu：在 Ubuntu 系统中安装和升级 Linux 内核的简单方法](https://www.ostechnix.com/ukuu-an-easy-way-to-install-and-upgrade-linux-kernel-in-ubuntu-based-systems/)

**优势：** 易于安装使用。自动安装主线内核。

**缺点：** 需要联网，需要重启。

4. 使用UKTools

跟 Ukuu 差不多，**UKTools** 也会从 kernel.ubuntu.com 网站获取最新的稳定内核并且自动安装到 Ubuntu 以及类似于 Linux Mint 的延伸发行版中。关于 UKTools 的更多详情，请参见下面的链接。

- [UKTools：升级 Ubuntu 及其衍生产品中的最新 Linux 内核](https://www.ostechnix.com/uktools-upgrade-latest-linux-kernel-in-ubuntu-and-derivatives/)

**优势：** 简单，自动。

**缺点：** 需要联网，需要重启。

5. 使用Linux 内核实用程序更新 Linux 内核

**Linux 内核实用程序**是目前另一个用于升级类 Ubuntu 系统 Linux 内核的程序。实质上，它是一个由一系列 Bash 脚本构成的合集，用于编译并且可以选择性地为 Debian（LCTT 译注：Ubuntu 的上游发行版）及其衍生发行版升级内核。它包含三个实用程序，一个用于手动编译、安装来自于 [http://www.kernel.org](http://www.kernel.org/) 网站的源码内核，另一个用于安装来自 [https://kernel.ubuntu.com](https://kernel.ubuntu.com/) 网站的预编译的内核，第三个脚本用于移除旧内核。更多细节请浏览以下链接。

- [Linux 内核实用程序：编译和更新最新的 Linux 内核的脚本，适用于 Debian 及其衍生产品](https://www.ostechnix.com/linux-kernel-utilities-scripts-compile-update-latest-linux-kernel-debian-derivatives/)

**优势：** 简单，自动。

**缺点：** 需要联网，需要重启。

6. #### 使用 Canonical 实时补丁服务来更新 Linux 内核

7. 
