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



## cloudstack

CloudStack是一个开源的具有高可用性及扩展性的云计算平台。目前Cloudstack支持管理大部分主流的hypervisors，如KVM，XenServer，VMware，Oracle VM，Xen等。同时CloudStack是一个开源云计算解决方案。可以加速高伸缩性的公共和私有云（IaaS）的部署、管理、配置。使用CloudStack作为基础，数据中心操作者可以快速方便的通过现存基础架构创建云服务。

架构：

Zone：Zone 对应于现实中的一个数据中心，它是 CloudStack 中最大的一个单元。

Pod：Pod 对应着一个机架。同一个 pod 中的机器在同一个子网（网段）中。

Cluster：Cluster 是多个主机组成的一个集群。同一个 cluster 中的主机有相同的硬件，相同的 Hypervisor，和共用同样的存储。同一个 cluster 中的虚拟机，可以实现无中断服务地从一个主机迁移到另外一个上。

Host：Host 就是运行虚拟机（VM）的主机。

即从包含关系上来说，一个 zone 包含多个 pod，一个 pod 包含多个 cluster，一个 cluster 包含多个 host。

Primary storage：一级存储与 cluster 关联，它为该 cluster 中的主机的全部虚拟机提供磁盘卷。一个 cluster 至少有一个一级存储，且在部署时位置要临近主机以提供高性能。

Secondary storage：二级存储与 zone 关联，它存储模板文件，ISO 镜像和磁盘卷快照。

- 模板：可以启动虚拟机的操作系统镜像，也包括了诸如已安装应用的其余配置信息。
- ISO 镜像：包含操作系统数据或启动媒质的磁盘镜像。
- 磁盘卷快照：虚拟机数据的已储存副本，能用于数据恢复或者创建新模板。

在每个kvm的宿主机上都需要部署agent程序

cloudstack的API

- Web Services
- REST架构
- 支持POST / GET请求
- 返回XML或JSON响应格式
- root管理员、域管理员和用户

| 比较项       | CloudStack                                                   | OpenStack                                                    |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 服务层次     | IaaS                                                         | IaaS                                                         |
| 授权协议     | Apache 2.0                                                   | Apache 2.0                                                   |
| Apache 2.0   | 不需要                                                       | 不需要                                                       |
| 动态资源调配 | 主机Maintainance模式下自动迁移VM                             | 无现成功能，需通过Nova-scheduler组件自己实现                 |
| VM模板       | 支持                                                         | 支持                                                         |
| VM Console   | 支持                                                         | 支持                                                         |
| 开发语言     | Java                                                         | Python                                                       |
| 用户界面     | Web Console,功能较完善                                       | DashBoard，较简单                                            |
| 负载均衡     | 软件负载均衡(Virtual Router)、硬件负载均衡                   | 软件负载均衡(Nova-network或 OpenStack Load Balance API)、硬件负载均衡 |
| 虚拟化技术   | XenServer,Oracle VM，vCenter,KVM,Bare Metal                  | XenServer,Oracle VM,KVM,QEMU,ESX/ESXi,LXC(Liunx Container)等 |
| 最小化部署   | 一管理节点，一主机节点                                       | 支持All in one（Nova,Keystone,Glance组件必选）               |
| 支持数据库   | MySQL                                                        | PostgreSQL,MySQL,SQLite                                      |
| 组件         | Console Proxy VM,Second Storage VM,Virtual Router VM,Host Agent,Management Server | Nova,Glance,Keystone,Horizon,Swift                           |
| 网络形式     | Isolation（VLAN），Share                                     | VLAN,FLAT,FLATDhcp                                           |
| 版本问题     | 版本发布稳定，不存在兼容性问题                               | 存在各版本兼容性问题                                         |
| VLAN         | 不能VLAN间互访                                               | 支持VLAN间互访                                               |

## OpenStack

