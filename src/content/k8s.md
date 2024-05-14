---
title: k8s(一)
date: 2020-03-0221:40:33
categories: 技术博客
tags:
    -  IT
toc: true
thumbnail: 
---

​        其实本章原来名为k8s，后来感觉云的概念/应用太多了，远远不止一个k8s，所以就以云为开始

<!--more-->

## 云原生的定义

云原生（Cloud Native）这个词汇由来已久，以致于何时出现已无据可考。云原生开始大规模出现在受众视线中，与 Pivotal 提出的云原生应用的理念有着莫大的关系。我们现在谈到云原生，更多的指的是[一种文化](https://cloudnative.to/blog/cloud-native-culture-not-container/)，而不具象为哪些技术体系。

早在 2015 年 Pivotal 公司的 Matt Stine 写了一本叫做 [迁移到云原生应用架构](https://jimmysong.io/migrating-to-cloud-native-application-architectures/) 的小册子，其中探讨了云原生应用架构的几个主要特征：

- 符合 12 因素应用
- 面向微服务架构
- 自服务敏捷架构
- 基于 API 的协作
- 抗脆弱性

到了 2015 年 Google 主导成立了云原生计算基金会（CNCF），起初 CNCF 对云原生（Cloud Native）的定义包含以下三个方面：

- 应用容器化
- 面向微服务架构
- 应用支持容器的编排调度

到了 2018 年，随着近几年来云原生生态的不断壮大，所有主流云计算供应商都加入了该基金会，且从 [Cloud Native Landscape](https://i.cncf.io/) 中可以看出云原生有意蚕食原先非云原生应用的部分。CNCF 基金会中的会员以及容纳的项目越来越多，该定义已经限制了云原生生态的发展，CNCF 为云原生进行了重新定位。

以下是 CNCF 对云原生的[重新定义](https://github.com/cncf/toc/blob/main/DEFINITION.md)（中英对照）

云原生技术有利于各组织在公有云、私有云和混合云等新型动态环境中，构建和运行可弹性扩展的应用。云原生的代表技术包括容器、服务网格、微服务、不可变基础设施和声明式 API。

这些技术能够构建容错性好、易于管理和便于观察的松耦合系统。结合可靠的自动化手段，云原生技术使工程师能够轻松地对系统作出频繁和可预测的重大变更。

云原生计算基金会（CNCF）致力于培育和维护一个厂商中立的开源生态系统，来推广云原生技术。我们通过将最前沿的模式民主化，让这些创新为大众所用。

### 设计理念

云原生系统的设计理念如下:

- 面向分布式设计（Distribution）：容器、微服务、API 驱动的开发；
- 面向配置设计（Configuration）：一个镜像，多个环境配置；
- 面向韧性设计（Resistancy）：故障容忍和自愈；
- 面向弹性设计（Elasticity）：弹性扩展和对环境变化（负载）做出响应；
- 面向交付设计（Delivery）：自动拉起，缩短交付时间；
- 面向性能设计（Performance）：响应式，并发和资源高效利用；
- 面向自动化设计（Automation）：自动化的 DevOps；
- 面向诊断性设计（Diagnosability）：集群级别的日志、metric 和追踪；
- 面向安全性设计（Security）：安全端点、API Gateway、端到端加密；



### 容器

容器最初是通过开发者工具而流行，可以使用它来做隔离的开发测试环境和持续集成环境，这些都是因为容器轻量级，易于配置和使用带来的优势，docker 和 docker-compose 这样的工具极大的方便的了应用开发环境的搭建，开发者就像是化学家一样在其中小心翼翼的进行各种调试和开发。

随着容器的在开发者中的普及，已经大家对 CI 流程的熟悉，容器周边的各种工具蓬勃发展，俨然形成了一个小生态，在 2016 年达到顶峰，

容器生态涵盖了容器应用中从镜像仓库、服务编排、安全管理、持续集成与发布、存储和网络管理等各个方面，随着在单主机中运行容器的成熟，集群管理和容器编排成为容器技术亟待解决的问题。譬如化学家在实验室中研究出来的新产品，如何推向市场，进行大规模生产，成了新的议题。

在单机上运行容器，无法发挥它的最大效能，只有形成集群，才能最大程度发挥容器的良好隔离、资源分配与编排管理的优势，而对于容器的编排管理，Swarm、Mesos 和 Kubernetes 的大战已经基本宣告结束，Kubernetes 成为了无可争议的赢家。

随着 Kubernetes 的日趋成熟，“Kubernetes is becoming boring”，基于该 “操作系统” 之上构建的适用于不同场景的应用将成为新的发展方向，就像我们将石油开采出来后，提炼出汽油、柴油、沥青等等，所有的材料都将找到自己的用途，Kubernetes 也是，毕竟我们谁也不是为了部署和管理容器而用 Kubernetes，承载其上的应用才是价值之所在

### OCI

https://xuanwo.io/2019/08/06/oci-intro/

OCI，[Open Container Initiative](https://www.opencontainers.org/)，是一个轻量级，开放的治理结构（项目），在 Linux 基金会的支持下成立，致力于围绕容器格式和运行时创建开放的行业标准。OCI 项目由 Docker，CoreOS（后来被 Red Hat 收购了，相应的席位被 Red Hat 继承）和容器行业中的其他领导者在 2015 年 6 月的时候启动。OCI 的技术委员会成员包括 Red Hat，Microsoft，Docker，[Cruise](https://getcruise.com/)，IBM，Google，Red Hat 和 SUSE，其中 Docker 公司有两名成员，且其中的一位是现任主席

OCI 目前提出的规范有如下这些：

| 名称                                                         | 版本       |
| :----------------------------------------------------------- | :--------- |
| [Runtime Specification](https://github.com/opencontainers/runtime-spec) | v1.0.1     |
| [Image Format](https://github.com/opencontainers/image-spec) | v1.0.1     |
| [Distribution Specification](https://github.com/opencontainers/distribution-spec) | v1.0.0-rc0 |

其中 runtime 和 image 的规范都已经正式发布，而 distribution 的还在工作之中。runtime 规范中介绍了如何运行解压缩到磁盘上的 [`Filesystem Bundle`](https://github.com/opencontainers/runtime-spec/blob/master/bundle.md)。在 OCI 标准下，运行一个容器的过程就是下载一个 OCI 的镜像，将其解压到某个 `Filesystem Bundle` 中，然后某个 OCI Runtime 就会运行这个 Bundle。细节此处不再展开，感兴趣的同学可以直接阅读 Spec

OCI in docker

自从 2013 年 docker 发布之后，docker 项目本身逐渐成为了一个庞然大物。为了能够降低项目维护的成本，内部代码能够回馈社区，docker 公司提出了 “基础设施管道宣言” (Infrastructure Plumbing Manifesto)：

- 只要有可能，重新使用现有的管道并提供改进：当您需要创建新的管道时，可以轻松地重复使用并提供改进。 这增加了可用组件的公共池，每个人都受益。
- 遵循 UNIX 原则：几个简单的组件比一个复杂的组件要好
- 定义标准接口：可用于将许多简单组件组合到更复杂的系统中

docker 开始自行拆分自己项目中的管道代码并形成一个个新的开源项目：他们于 2014 年开源了 [libcontainer](https://github.com/docker/libcontainer)，并在随后的几年中陆续开源了 [libnetwork](https://github.com/docker/libnetwork), [notary](https://github.com/docker/notary), [hyperkit](https://github.com/docker/hyperkit) 等项目。在 OCI 项目启动后，docker 公司将 `libcontainer` 的实现移动到 [runC](https://github.com/opencontainers/runc) 并捐赠给了 OCI。此时，容器社区有了第一个 OCI Runtime 的参考实现。runC 是一个轻量可移植的容器运行时，包括了所有之前 docker 所使用的容器相关的与系统特性的代码，它的目标是：`make standard containers available everywhere`

随后在 2016 年，docker 开源并将 [containerd](https://github.com/containerd/containerd) 捐赠给了 CNCF，containerd 几乎囊括了单机运行一个容器运行时所需要的一切：执行，分发，监控，网络，构建，日志等。为了能够支持多种 OCI Runtime，containerd 内部使用 `containerd-shim`，每启动一个容器都会创建一个新的 `containerd-shim` 进程，指定容器 ID，Bundle 目录，运行时的二进制（比如 runc）

于是，现代 docker 启动一个标准化容器需要经历这样的流程：

![img](https://xuanwo.io/2019/08/06/oci-intro/docker-to-oci.svg)

OCI in k8s

Kubernetes 最初只支持 docker 作为运行时，为了能够让 Kubernetes 变得更具有可扩展性，在 1.5 版本增加了 [CRI: the Container Runtime Interface](https://github.com/kubernetes/kubernetes/blob/242a97307b34076d5d8f5bbeb154fa4d97c9ef1d/docs/devel/container-runtime-interface.md)，在随后的演进中，CRI 被抽出来做成了独立的项目：https://github.com/kubernetes/cri-api/。

CRI 是一套通过 protocol buffers 定义的 API

kubelet 实现了 client 端，CRI shim 实现 server 端。只要实现了对应的接口，就能接入 k8s 作为 Container Runtime

k8s 1.5 中自己实现了 [docker CRI shim](https://github.com/kubernetes/kubernetes/tree/release-1.5/pkg/kubelet/dockershim)，此时启动容器的流程如下：

![img](https://xuanwo.io/2019/08/06/oci-intro/cri-docker.png)

从 containerd 1.0 开始，为了能够减少一层调用的开销，containerd 开发了一个新的 daemon，叫做 CRI-Containerd，直接与 containerd 通信，从而取代了 dockershim：

![img](https://xuanwo.io/2019/08/06/oci-intro/cri-containerd.png)

但是这仍然多了一个独立的 daemon，从 containerd 1.1 开始，社区选择在 containerd 中直接内建 CRI plugin，通过方法调用来进行交互，从而减少一层 gRPC 的开销，最终的容器启动流程如下：

![img](https://xuanwo.io/2019/08/06/oci-intro/containerd-built-in-plugin.png)

最终的结果是 k8s 的 Pod 启动延迟得到了降低，CPU 和内存占用率都有不同程度的降低。

但是这还不是终点，为了能够直接对接 OCI 的 runtime 而不是 containerd，社区孵化了 [CRI-O](https://github.com/cri-o/cri-o) 并加入了 CNCF。CRI-O 的目标是让 kubelet 与运行时直接对接，减少任何不必要的中间层开销。CRI-O 运行时可以替换为任意 OCI 兼容的 Runtime，镜像管理，存储管理和网络均使用标准化的实现，目前还在积极开发中，前途无量。

基于OCI相关的开源项目

**Runtime**

- [opencontainers/runc](https://github.com/opencontainers/runc)：前面已经提到过很多次了，是 OCI Runtime 的参考实现。
- [kata-containers/runtime](https://github.com/kata-containers/runtime)：容器标准反攻虚拟机，前身是 [clearcontainers/runtime](https://github.com/clearcontainers/runtime) 与 [hyperhq/runv](https://github.com/hyperhq/runv)，通过 [virtcontainers](https://github.com/kata-containers/runtime/tree/master/virtcontainers) 提供高性能 OCI 标准兼容的硬件虚拟化容器，Linux Only，且需要特定硬件。
- [google/gvisor](https://github.com/google/gvisor)：gVisor 是一个 Go 实现的用户态内核，包含了一个 OCI 兼容的 Runtime 实现，目标是提供一个可运行非受信代码的容器运行时沙盒，目前是 Linux Only，其他架构可能会支持。

**Image Build**

- [moby/buildkit](https://github.com/moby/buildkit)：从 docker build 拆分出来的项目，支持自动 GC，多种输入和输出格式，并发依赖解析，分布式 Worker 和 Rootless 执行等特性
- [genuinetools/img](https://github.com/genuinetools/img)：对 buildkit 的一层封装，单独的二进制，没有 daemon，支持 Rootless 执行，会自动创建 SUBUID，比 buildkit 使用起来更加容易
- [uber/makisu](https://github.com/uber/makisu)：uber 开源的内部镜像构建工具，目标是在 Mesos 或 Kubernetes 上进行 Rootless 构建，支持的 Dockerfile 有些许不兼容，在非容器环境下运行会有问题，比如 [Image failed to build without modifyfs](https://github.com/uber/makisu/issues/233)
- [GoogleContainerTools/kaniko](https://github.com/GoogleContainerTools/kaniko)：Google 出品，目标是 Daemon free build on Kubernetes，要求运行镜像 `gcr.io/kaniko-project/executor` 进行构建，直接在别的镜像中使用二进制可能会不工作，很蠢
- [containers/buildah](https://github.com/containers/buildah)：开源组织 [Containers](https://github.com/containers) 推出的项目，目标是构建 OCI 容器镜像，Daemon free，支持 Rootless 构建

**Tools**

- [containers/skopeo](https://github.com/containers/skopeo)：这是一个用来查看容器镜像信息的工具，可以在不用下载到本地的前提下查看远端 Registry 中的镜像信息
- [containers/libpod](https://github.com/containers/libpod)：二进制名为 `podman`，支持管理 Pod，容器，镜像和存储卷，命令行与 docker CLI 完全兼容，基本上能视为 docker CLI 的 drop-in replace，镜像部分的代码主要使用了 buildah，未来还会支持 cgroups v2，人类文明之光





## 技术名词概念

### 超分

CPU“超分”是公有云（阿里云、华为云等）应用虚拟化技术时的一个必然现象，即：当设备满载或接近满载时，一台物理机被VMVare、ESXi等虚拟化系统平台自动划分成多个虚拟机，以求最大限度利用资源。

“超分”是虚拟化平台的优势，能够将可分配给客户机器的内存总合大于实际可用的物理内存总数。因为物理机中的客户机不可能都处于高负荷的状态，所以适当的超分有助于资源的充分利用。

当一台物理机被分割为N虚拟机时，每台虚拟机的计算性能缩减到了1/N的性能，这就产生了不少用户：测试时很快，实际渲染时却很慢，而且费用也很高，甚至不同机器费用高低不一”的现象；带来的后果是用户测试时感觉费用很低或速度很快，实际渲染却发现速度慢，实际支付的渲染费用高。但这不是欺诈行为，而是VMVare等虚拟化平台默认开启的功能。

 适当超分会有助于资源的充分利用，但当平台用户量过多时， CPU超分率数值过大可能会严重影响物理机性能，导致业务性能卡顿，影响用户渲染、服务体验。



### 虚拟化

虚拟化（技术）是一种资源管理技术，是将计算机的各种实体资源（CPU、内存、磁盘空间、网络适配器等），予以抽象、转换后呈现出来并可供分割、组合为一个或多个电脑配置环境。

对于一台计算机，我们可以简单的划分为三层：从下到上依次是物理硬件层，操作系统层、应用程序层

1974年，两位计算机科学家Gerald Popek 和 Robert Goldberg发表了一篇重要的论文 **《虚拟化第三代体系结构的正式要求》**，在这篇论文中提出了虚拟化的三个基本条件：

- `等价性`：程序在本地计算机执行和在虚拟机中执行应该表现出一样的结果（不包括执行时间的差异）
- `安全性`：虚拟机彼此隔离，与宿主计算机隔离
- `性能`：绝大多数情况下虚拟机中的代码指令应该直接在物理CPU中执行，少部分特殊指令可由VMM参与。

那如何实现对计算机底层的物理资源的虚拟化分割呢？在计算机技术的发展历史上，出现了两种著名的方案，分别是I型虚拟化和II型虚拟化

Type I: 直接凌驾于硬件之上，构建出多个隔离的操作系统环境

Type II: 依赖于宿主操作系统，在其上构建出多个隔离的操作系统环境

我们熟知的VMware事实上有两个产品线，一个是VMware ESXi，直接安装在裸金属之上，不需要额外的操作系统，属于第一类虚拟化。另一个是我们普通用户更加熟知的VMware WorkStation，属于第二类虚拟化。

如何实现

一个典型的做法是——`陷阱 & 模拟`技术

什么意思？**简单来说就是正常情况下直接把虚拟机中的代码指令放到物理的CPU上去执行，一旦执行到一些敏感指令，就触发异常，控制流程交给VMM，由VMM来进行对应的处理，以此来营造出一个虚拟的计算机环境。**

不过这一经典的虚拟化方案在Intel x86架构上却遇到了问题

全虚拟化：VMware 二进制翻译技术

不同于8086时代16位实地址工作模式，x86架构进入32位时代后，引入了保护模式、虚拟内存等一系列新的技术。同时为了安全性隔离了应用程序代码和操作系统代码，其实现方式依赖于x86处理器的工作状态。

这就是众所周知的x86处理器的Ring0-Ring3四个“环”。

操作系统内核代码运行在最高权限的Ring0状态，应用程序工作于最外围权限最低的Ring3状态，剩下的Ring1和Ring2主流的操作系统都基本上没有使用。

这里所说的权限，有两个层面的约束：

- 能访问的内存空间
- 能执行的特权指令

来关注一下第二点，特权指令。

CPU指令集中有一些特殊的指令，用于进行硬件I/O通信、内存管理、中断管理等等功能，这一些指令只能在Ring0状态下执行，被称为特权指令。这些操作显然是不能让应用程序随便执行的。处于Ring3工作状态的应用程序如果尝试执行这些指令，CPU将自动检测到并抛出异常。

回到我们的主题虚拟化技术上面来，如同前面的定义所言，虚拟化是将计算资源进行逻辑或物理层面的切割划分，构建出一个个独立的执行环境。

按照我们前面所说的`陷阱 & 模拟`手段，可以让虚拟机中包含操作系统在内的程序统一运行在低权限的Ring3状态下，一旦虚拟机中的操作系统进行内存管理、I/O通信、中断等操作时，执行特权指令，从而触发异常，物理机将异常派遣给VMM，由VMM进行对应的模拟执行。

这本来是一个实现虚拟化很理想的模式，不过x86架构的CPU在这里遇到了一个跨不过去的坎。

到底是什么问题呢？

回顾一下前面描绘的理想模式，要这种模式能够实现的前提是执行敏感指令的时候**能够触发异常**，让VMM有机会介入，去模拟一个虚拟的环境出来。

但现实是，x86架构的CPU指令集中有那么一部分指令，它不是特权指令，Ring3状态下也能够执行，但这些指令对于虚拟机来说却是敏感的，不能让它们直接执行。一旦执行，没法触发异常，VMM也就无法介入，虚拟机就**露馅儿**了！

这结果将导致虚拟机中的代码指令出现无法预知的错误，更严重的是影响到真实物理计算机的运行，虚拟化所谓的安全隔离、等价性也就无从谈起。

怎么解决这个问题，让x86架构CPU也能支持虚拟化呢？

VMware和QEMU走出了两条不同的路。

VMware创造性的提出了一个**二进制翻译技术**。VMM在虚拟机操作系统和宿主计算机之间扮演一个桥梁的角色，将虚拟机中的要执行的指令“翻译”成恰当的指令在宿主物理计算机上执行，以此来模拟执行虚拟机中的程序。你可以简单理解成Java虚拟机执行Java字节码的过程，不同的是Java虚拟机执行的是字节码，而VMM模拟执行的就是CPU指令。

另外值得一提的是，为了提高性能，也并非所有的指令都是模拟执行的，VMware在这里做了不少的优化，对一些“安全”的指令，就让它直接执行也未尝不可。所以VMware的二进制翻译技术也融合了部分的直接执行。

对于虚拟机中的操作系统，VMM需要完整模拟底层的硬件设备，包括处理器、内存、时钟、I/O设备、中断等等，换句话说，VMM用纯软件的形式“模拟”出一台计算机供虚拟机中的操作系统使用。

这种完全模拟一台计算机的技术也称为**全虚拟化**，这样做的好处显而易见，虚拟机中的操作系统感知不到自己是在虚拟机中，代码无需任何改动，直接可以安装。而缺点也是可以想象：完全用软件模拟，转换翻译执行，性能堪忧！

而QEMU则是完全软件层面的“模拟”，乍一看和VMware好像差不多，不过实际本质是完全不同的。VMware是将原始CPU指令序列翻译成经过处理后的CPU指令序列来执行。而QEMU则是完全模拟执行整个CPU指令集，更像是“解释执行”，两者的性能不可同日而语。

半虚拟化：Xen内核定制修改

既然有全虚拟化，那与之相对的也就有半虚拟化，前面说了，由于敏感指令的关系，全虚拟化的VMM需要捕获到这些指令并完整模拟执行这个过程，实现既满足虚拟机操作系统的需要，又不至于影响到物理计算机。

但说来简单，这个模拟过程实际上相当的复杂，涉及到大量底层技术，并且如此模拟费时费力。

而试想一下，如果把操作系统中所有执行敏感指令的地方都改掉，改成一个接口调用（**HyperCall**），接口的提供方VMM实现对应处理，省去了捕获和模拟硬件流程等一大段工作，性能将获得大幅度提升。

这就是**半虚拟化**，这项技术的代表就是**Xen**，一个诞生于2003年的开源项目。

这项技术一个最大的问题是：需要修改操作系统源码，做相应的适配工作。这对于像Linux这样的开源软件还能接受，充其量多了些工作量罢了。但对于Windows这样闭源的商业操作系统，修改它的代码，无异于痴人说梦。

硬件辅助虚拟化 VT/AMD-v

折腾来折腾去，全都是因为x86架构的CPU天然不支持经典虚拟化模式，软件厂商不得不想出其他各种办法来在x86上实现虚拟化。

如果进一步讲，CPU本身增加对虚拟化的支持，那又会是一番怎样的情况呢？

在软件厂商使出浑身解数来实现x86平台的虚拟化后的不久，各家处理器厂商也看到了虚拟化技术的广阔市场，纷纷推出了硬件层面上的虚拟化支持，正式助推了虚拟化技术的迅猛发展。

这其中为代表的就是Intel的VT系列技术和AMD的AMD-v系列技术

硬件辅助虚拟化细节较为复杂，简单来说，新一代CPU在原先的Ring0-Ring3四种工作状态之下，再引入了一个叫工作模式的概念，有`VMX root operation` 和 `VMX non-root operation` 两种模式，每种模式都具有完整的Ring0-Ring3四种工作状态，前者是VMM运行的模式，后者是虚拟机中的OS运行的模式。

VMM运行的层次，有些地方将其称为Ring -1，VMM可以通过CPU提供的编程接口，配置对哪些指令的劫持和捕获，从而实现对虚拟机操作系统的掌控

换句话说，原先的VMM为了能够掌控虚拟机中代码的执行，不得已采用“中间人”来进行翻译执行，现在新的CPU告诉VMM：不用那么麻烦了，你提前告诉我你对哪些指令哪些事件感兴趣，我在执行这些指令和发生这些事件的时候就通知你，你就可以实现掌控了。完全由硬件层面提供支持，性能自然高了不少。

上面只是硬件辅助虚拟化技术的一个简单理解，实际上还包含更多的要素，提供了更多的便利给VMM，包括内存的虚拟、I/O的虚拟等等，让VMM的设计开发工作大大的简化，VMM不再需要付出昂贵的模拟执行成本，整体虚拟化的性能也有了大幅度的提升。

VMware从5.5版本开始引入对硬件辅助虚拟化的支持，随后在2011年的8.0版本中正式全面支持。于是乎，我们在创建虚拟机的时候，可以选择要使用哪一种虚拟化引擎技术，是用原先的二进制翻译执行，还是基于硬件辅助虚拟化的新型技术。

同一时期的XEN从3.0版本也加入对硬件辅助虚拟化的支持，从此基于XEN的虚拟机中也能够运行Windows系统了

KVM_QEMU

有了硬件辅助虚拟化的加持，虚拟化技术开始呈现井喷之势。VirtualBox、Hyper-V、KVM等技术如雨后春笋般接连面世。这其中在云计算领域声名鹊起的当属开源的KVM技术了。

KVM全称**for Kernel-based Virtual Machine**，意为基于内核的虚拟机。

在虚拟化底层技术上，KVM和VMware后续版本一样，都是基于硬件辅助虚拟化实现。不同的是VMware作为独立的第三方软件可以安装在Linux、Windows、MacOS等多种不同的操作系统之上，而KVM作为一项虚拟化技术已经集成到Linux内核之中，可以认为Linux内核本身就是一个HyperVisor，这也是KVM名字的含义，因此该技术只能在Linux服务器上使用

KVM技术常常搭配QEMU一起使用，称为KVM-QEMU架构。前面提到，在x86架构CPU的硬件辅助虚拟化技术诞生之前，QEMU就已经采用全套软件模拟的办法来实现虚拟化，只不过这种方案下的执行性能非常低下。

KVM本身基于硬件辅助虚拟化，仅仅实现CPU和内存的虚拟化，但一台计算机不仅仅有CPU和内存，还需要各种各样的I/O设备，不过KVM不负责这些。这个时候，QEMU就和KVM搭上了线，经过改造后的QEMU，负责外部设备的虚拟，KVM负责底层执行引擎和内存的虚拟，两者彼此互补，成为新一代云计算虚拟化方案的宠儿

容器技术

前面谈到的无论是基于翻译和模拟的全虚拟化技术、半虚拟化技术，还是有了CPU硬件加持下的全虚拟化技术，其虚拟化的目标都是一台完整的计算机，拥有底层的物理硬件、操作系统和应用程序执行的完整环境。

为了让虚拟机中的程序实现像在真实物理机器上运行“近似”的效果，背后的HyperVisor做了大量的工作，付出了“沉重”的代价。

虽然HyperVisor做了这么多，但你有没有问过虚拟机中的程序，这是它想要的吗？或许HyperVisor给的太多，而目标程序却说了一句：你其实可以不用这样辛苦。

确实存在这样的情况，虚拟机中的程序说：我只是想要一个单独的执行执行环境，不需要你费那么大劲去虚拟出一个完整的计算机来

这样做的好处是什么？

虚拟出一台计算机的成本高还是只虚拟出一个隔离的程序运行环境的成本高？答案很明显是前者。一台物理机可能同时虚拟出10台虚拟机就已经开始感到乏力了，但同时虚拟出100个虚拟的执行环境却还是能够从容应对，这对于资源的充分利用可是有巨大的好处。

近几年大火的容器技术正是在这样的指导思想下诞生的。

不同于虚拟化技术要完整虚拟化一台计算机，容器技术更像是操作系统层面的虚拟化，它只需要虚拟出一个操作系统环境。

**LXC**技术就是这种方案的一个典型代表，全称是LinuX Container，通过Linux内核的**Cgroups**技术和**namespace**技术的支撑，隔离操作系统文件、网络等资源，在原生操作系统上隔离出一个单独的空间，将应用程序置于其中运行，这个空间的形态上类似于一个容器将应用程序包含在其中，故取名容器技术。

举个不是太恰当的比喻，一套原来是三居室的房子，被二房东拿来改造成三个一居室的套间，每个一居室套间里面都配备了卫生间和厨房，对于住在里面的人来说就是一套完整的住房。

如今各个大厂火爆的**Docker**技术底层原理与LXC并不本质区别，甚至在早期Docker就是直接基于LXC的高层次封装。Docker在LXC的基础上更进一步，将执行执行环境中的各个组件和依赖打包封装成独立的对象，更便于移植和部署。

容器技术的好处是轻量，所有隔离空间的程序代码指令不需要翻译转换，就可以直接在CPU上执行，大家底层都是同一个操作系统，通过软件层面上的逻辑隔离形成一个个单独的空间。

容器技术的缺点是安全性不如虚拟化技术高，毕竟软件层面的隔离比起硬件层面的隔离要弱得多。隔离环境系统和外面的主机共用的是同一个操作系统内核，一旦利用内核漏洞发起攻击，程序突破容器限制，实现逃逸，危及宿主计算机，安全也就不复存在

超轻虚拟化 firecracker

虚拟完整的计算机隔离性好但太过笨重，简单的容器技术又因为太过轻量纯粹靠软件隔离不够安全，有没有一个折中的方案同时兼具两者的优点，实现既轻量又安全呢？

近年来，一种**超轻虚拟化**的思想开始流行开来，亚马逊推出的**firecracker**就是一个典型的代表。

firecracker将虚拟化技术的强隔离性和容器技术的轻量性进行融合，提出了一个`microVM`的概念，底层通过KVM虚拟化技术实现各个microVM的强隔离，而隔离的虚拟机中运行的是一个个精简版的微型操作系统，砍掉了大量无用的功能，专为容器设计的微型OS。

超轻虚拟化如今成为一个新的浪潮，除了AWS的firecracker，谷歌的gVisor, Intel主导的NEMU也在向这个领域开始发力。

#### KVM

基于内核的虚拟机（KVM）是一种软件功能，您可以将其安装在物理 Linux 机器上以创建虚拟机。虚拟机是一种软件应用程序，可作为另一台实体计算机中的独立计算机使用。虚拟机与实体计算机共享 CPU 周期、网络带宽和内存等资源。KVM 是 Linux 操作系统组件，它为 Linux 上的虚拟机提供原生支持。自 2007 年以来，它已在 Linux 发行版中推出。 

KVM的特点

KVM 是 Linux 的一部分。Linux 也是 KVM 的一部分。Linux 有的，KVM 全都有。然而，KVM 的某些特点让它成为了企业的首选虚拟机监控程序

安全防护

KVM 利用[安全增强型 Linux（SELinux）](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/rhlcommon-appendix-0005)和安全虚拟化（sVirt）组合来加强虚拟机的安全性和隔离性。SELinux 在虚拟机周围建立安全边界。sVirt 则扩展 SELinux 的功能，使强制访问控制 （MAC）安全机制应用到客户虚拟机，并且预防手动标记错误。

存储空间

KVM 能够使用 Linux 支持的任何存储，包括某些本地磁盘和[网络附加存储（NAS）](https://www.redhat.com/zh/topics/data-storage/network-attached-storage)。还可以利用多路径 I/O 来增强存储并提供冗余能力。KVM 还支持共享文件系统，因此虚拟机镜像可以由多个主机共享。磁盘镜像支持精简置备，可以按需分配存储，不必预先备妥一切。

硬件支持

KVM 可以使用多种多样的认证 Linux 兼容硬件平台。由于硬件供应商经常助力内核开发，所以 Linux 内核中通常能快速采用最新的硬件功能。

内存管理

KVM 继承了 Linux 的内存管理功能，包括非统一内存访问和内核同页合并。虚拟机的内存可以交换，也可通过大型宗卷支持来提高性能，还可由磁盘文件共享或支持。

实时迁移

KVM 支持实时迁移，也就是能够在物理主机之间移动运行中的虚拟机，而不会造成服务中断。虚拟机保持开机状态，网络连接保持活跃，各个应用也会在虚拟机重新定位期间正常运行。KVM 也会保存虚拟机的当前状态，从而存储下来供日后恢复。

性能和可扩展性

KVM 继承了 Linux 的性能，针对客户机和请求数量的增长进行扩展，满足负载的需求。KVM 可让要求最苛刻的应用工作负载实现虚拟化，而这也是许多企业虚拟化设置的基础，如数据中心和私有云等（通过 [OpenStack®](https://www.redhat.com/zh/topics/openstack)）

调度和资源控制

在 KVM 模型中，虚拟机是一种 Linux 进程，由内核进行调度和管理。通过 Linux 调度程序，可对分配给 Linux 进程的资源进行精细的控制，并且保障特定进程的服务质量。在 KVM 中，这包括完全公平的调度程序、控制组、网络命名空间和实时扩展。

更低延迟，更高优先级

Linux 内核提供实时扩展，允许基于虚拟机的应用以更低的延迟、更高的优先级来运行（相对于裸机恢复）。内核也将需要长时间计算的进程划分为更小的组件，再进行相应的调度和处理。

运行

KVM 将 Linux 转变为 1 类(裸机恢复)虚拟机监控程序。所有虚拟机监控程序都需要一些操作系统层面的组件才能运行虚拟机，如内存管理器、进程调度程序、输入/输出(I/O)堆栈、设备驱动程序、安全管理器以及网络堆栈等。由于 KVM 是 Linux 内核的一部分，因此所有这些组件它都有。每个虚拟机都像普通的 Linux 进程一样实施，由标准的 Linux 调度程序进行调度，并且使用专门的虚拟硬件，如网卡、图形适配器、CPU、内存和磁盘等。

安装KVM的前提条件

1、确定机器有VT

终端输入命令： grep vmx /proc/cpuinfo (INTEL芯片)

grep svm /proc/cpuinfo (AMD芯片)

不知道芯片的生产厂商则输入：egrep '(vmx|svm)' /proc/cpuinfo

如果flags: 里有vmx 或者svm就说明支持VT；如果没有任何的输出，说明你的cpu不支持，将无法成功安装KVM虚拟机。

2、确保BIOS里开启VT

Intel(R) Virtualization Tech [Enabled]

如有必要，还需在BIOS中开启VT-d

3、确保内核版本较新，支持KVM

用uname -r查看内核版本，如果在2.6.20以下的linux版本，需升级内核。

启用KVM

在安装 Linux 内核以后，您需要在 Linux 设备上安装以下额外的软件组件：

- 主机内核模块
- 特定于处理器的模块
- 仿真器
- 一系列用于扩展 KVM 功能和性能的其他 Linux 程序包

一旦加载，服务器管理员会通过命令行工具或图形用户界面创建虚拟机。然后，KVM 启动虚拟机作为独立的 Linux 进程。虚拟机监控器会为每台虚拟机分配虚拟内存、存储、网络、CPU 和资源。

管理KVM

若不借助[管理工具](https://www.linux-kvm.org/page/Management_Tools)，单一工作站上部署的少数虚拟机尚且可以手动管理。大型企业则必须使用与虚拟环境和底层物理硬件对接的[虚拟化管理](https://www.redhat.com/zh/topics/virtualization/what-is-virtualization-management)软件，来简化资源管理、增强数据分析并简化运营

