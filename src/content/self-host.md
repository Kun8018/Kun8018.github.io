---
title: self-host
date: 2020-03-02 21:40:33
categories: IT
tags:
    - 
toc: true
thumbnail: 
---

　　自己搭建服务器

<!--more-->

## homer

https://github.com/bastienwirtz/homer



## glance

https://github.com/glanceapp/glance

dashboard

## netmaker

https://github.com/gravitl/netmaker/

[Netmaker](https://github.com/gravitl/netmaker) 是一个开源的、基于 [[WireGuard]] 的网络（overlay network) 控制工具，可以非常快速的用来组建 WireGuard 网络

如果你有两台连接互联网的设备，那么 Netmaker 可以组建一个安全的网络，并打通一个安全的隧道提供给两台机器通信。而如果你有数千台机器分布在不同的地区，不同的数据中心，不同的网络中，那么 Netmaker 也可以组建一个网络来提供给这些不同的节点通信

如果熟悉 AWS，那么 Netmaker 就像 VPC 一样，不过 Netmaker 可以应用在任意的机器中。从 Netmaker 网络中的机器来看，同一个网络中的机器尽管在世界各地，但其相互通信就像是在同一个局域网中一样

Netmaker 和其他一些产品非常相似，比如 [[Tailscale]], [[ZeroTier]]，[[Nebula]] 但不同于这些产品的是，Netmaker 连接更快，更加灵活。Netmaker 使用 [[WireGuard]] 所以更快，Netmaker 中的节点不管是服务端还是Agent都完全可配置，所以提供了更大的灵活性

Netmaker 优于 [[Tailscale]] 的地方还在于 ，Netmaker 不需要 Google, Microsoft 或者 GitHub 账号。Netmaker 可以认为是一个可以自行托管的 Tailscale

Netmaker 依赖于 WireGuard 来在机器间创建隧道（tunnel), Netmaker 通过管理不同机器的 WireGuard 来创建网络。简单来说，Netmaker 实现 Client/Server 架构：

- the admin server 管理端，称为 Netmaker，管理网络的界面
- the agent，客户端，称为 Netclient，客户端通过 gRPC 与服务端通信

作为 Network 管理端，你可以通过管理端来创建网络，管理连接的设备。服务端会保存所有网络和设备的配置信息，这些信息会被 netclient (agent) 来获取使用。

客户端（netclient) 是一个二进制文件，netclient 会在节点被添加到网络中的时候安装到不同的机器中，netclient 可以在大多数系统中运行，不管是虚拟机，物理机，或者 IoT 设备都可以运行 netclient。netclient 会连接服务端，通过服务端的配置来自动管理 WireGuard，动态更新 Peers。通过不断向网络中添加节点的方式，可以创建一个动态的，完全可以配置的虚拟网络。

Netmaker server 不会路由网络流量，否则这个网络就变成了一个中心辐射模型（hub-and-spoke model），这会使得中心服务器变成瓶颈，并且拖慢网络。相反，Network 会告诉网络中的节点他们之间可以相互直接通信，这被称为 full mesh network（网状网络），这会让节点和节点的连接更快。即使管理端宕机，只要现存的节点没有变化，那么这个网络依然可以正常工作

### WireGuard

[[WireGuard]] 相对比较新，但非常强大，WireGuard 被加入到了 Linux kernel。WireGuard 可以非常简单快速地在设备之间创建加密通道。从 [WireGuard](https://www.wireguard.com/) 官网的介绍中可以看到，“WireGuard 可以被认为是工业界最安全，最方便使用，最简单的 VPN 解决方案

之前的解决方案，比如 OpenVPN，或者 IPSec 都被认为又重又复杂，并且性能也不是很好。所有现存的 VPN tunneling 方案都会导致网络延迟增大。WireGuard 是第一个实现了几乎接近有线连接网络速度的 VPN，可以看到 WireGuard 对现有网络连接几乎没有影响。随着 WireGuard 的发布，没有任何理由去使用其他隧道加密技术了

Mesh Network

当提到 mesh network （网状网络）的时候通常也会说 「full mesh」。一个 full [mesh network](https://www.bbc.co.uk/bitesize/guides/zr3yb82/revision/2) 指的是网络中的任何节点都可以相互直接连接

比如在路由器后面的家庭网络，所有的电脑都会通过私有局域网地址相互连接。

Mesh network 通常会和 hub-and-spoke (中心辐射) 网络放到一起对比，中心辐射的网络中，一个节点必须通过 relay server 才能和另外一个节点进行通信。

在一些场景中，你可以需要部分的 mesh network，网络中只有部分设备需要相互直接通信，而其他设备都需要将流量转发给一个 relay/gateway 。Netmaker 在某些时候也可以实现这类模型。在第一张图片中，这个设置就是一个部分的 mesh network，因为节点A-D 是网状网络，而其他的客户端通过 gateway 连接。

Mesh networks 通常比其他拓扑的网络更快，但通常设置也会更加复杂。WireGuard 提供了在设备之间创建加密隧道的方法，但是它不提供设置完整网络的方法。这是 Netmaker 诞生的理由。

### 安装

服务器：

- 一台可用的 VPS（最好比较干净，没有占用端口，否则需要根据自己的需要自行调整），不推荐 Oracle Cloud 的机器，网络接口有问题
- 公开的 IP 地址
- 至少 1GB RAM，1CPU（4GB RAM，2 CPU 生产环境）
- 2GB+ 存储
- Ubuntu 20.04

### 软件要求

域名：

- 一个可用的域名（可选）
- 可以操作管理 DNS 服务(53端口)
- 保证 443(tcp)， 53(tcp udp), 51821-518XX(udp) 端口可用
  - 443 端口，Dashboard，REST API 和 gRPC
  - 53 端口，CoreDNS
  - 51821-518XX，WireGuard，每一个网络需要一个端口，起始端口会使用 51821，可以根据自己的网络端数量需要设定端口范围
  - 允许防火墙 `sudo ufw allow proto tcp from any to any port 443 && sudo ufw allow 53/udp && sudo ufw allow 53/tcp && sudo ufw allow 51821:51830/udp`



https://einverne.github.io/post/2021/12/netmaker.html

https://www.mereith.com/post/134#%E5%A4%8D%E5%86%99%20ip

