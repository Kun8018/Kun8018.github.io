---
title: 虚拟化相关
date: 2020-03-06 21:40:33
categories: IT
tags:
    - IT,Web，IOS
toc: true
thumbnail: 
---

​	虚拟化相关概念和技术栈

<!--more-->

## 简介

虚拟机监控程序为你的虚拟化平台提供基础，从传统供应商到各种开源替代品，可供选择的虚拟机监控程序有很多。VMware 是一款热门的虚拟化产品，可以提供 ESXi 虚拟机监控程序和 vSphere 虚拟化平台。基于内核的虚拟机（KVM）是一个[开源](https://www.redhat.com/zh/about/open-source)的选择，是 [Linux®](https://www.redhat.com/zh/topics/linux) 系统的一部分

VMware 可以提供 ESXi 虚拟机监控程序和 vSphere 虚拟化平台。VMware ESXi 是一个能够直接安装到物理服务器上的裸机虚拟机监控程序，可以帮你整合硬件。你可以用 VMware 的虚拟化技术来创建和部署虚拟机（VM），从而现代化改造自己的基础架构，来交付和管理各种新应用和传统应用。

选用 VMware vSphere 意味着你需要使用 VMware 的控制堆栈来管理虚拟机，而且有多个许可证授权级别可供使用。

[KVM](https://www.redhat.com/zh/topics/virtualization/what-is-KVM) 是一种开源虚拟化技术，能将 Linux 内核转变成可以实现虚拟化的虚拟机监控程序，而且可以替代专有的虚拟化技术（比如 VMware 提供的专有虚拟化技术）。

[迁移到基于 KVM 的虚拟化平台](https://www.redhat.com/zh/topics/virtualization/why-migrate-your-virtual-infrastructure-to-red-hat)，你就可以检查、修改和完善虚拟机监控程序背后的源代码。能够访问源代码，就如同开启了创新之门，能够让你虚拟化传统工作负载和应用，并为[云原生](https://www.redhat.com/zh/topics/cloud-native-apps)和[基于容器](https://www.redhat.com/zh/topics/containers-v1)的工作负载奠定基础。由于 KVM 内置于 Linux 内核中，所以使用和部署起来非常方便。

https://www.redhat.com/zh/topics/virtualization/kvm-vs-vmware-comparison
