---
title: 机器人控制
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，
toc: true
thumbnail: 
---

Mujoco

<!--more-->

## Mujoco

https://github.com/google-deepmind/mujoco

斯坦福博士生 https://github.com/YanjieZe/awesome-humanoid-robot-learning



### 模型数据集

https://github.com/google-deepmind/mujoco_menagerie



### mjpython

```shell 
pip install -U mujoco glfw
export MUJOCO_GL=glfw   # 建议在 mac 上显式指定
mjpython record_mujoco_franka_lerobot.py
```



### mjcolab

mjcolab

https://zhuanlan.zhihu.com/p/705018707



https://colab.research.google.com/github/google-deepmind/mujoco/blob/main/python/tutorial.ipynb



### mujoco-py

https://github.com/openai/mujoco-py



https://www.cnaiplus.com/a/latest/1892211.html



### dm_control

https://github.com/google-deepmind/dm_control



### 内核



### learning

https://github.com/LitchiCheng/mujoco-learning?tab=readme-ov-file



### 任务规划

https://github.com/stack-of-tasks/pinocchio



## modbus

Modbus 协议是一种通信协议，允许设备通过各种类型的介质进行通信，如串行线和以太网。它是由生产可编程逻辑控制器（PLC）的 Modicon 公司于 1979 年开发的，旨在使这些设备能够相互通信。

Modbus 协议提供了一种消息传递结构，用于以主从通信方式在智能设备之间传递消息。主设备 A 发送的 Modbus 消息会触发从设备 B 的回应。Modbus 协议规定了通信内容、信息的封装方式和消息的发送和接收顺序。

Modbus 协议简单而强大，是工业控制系统的热门选择。作为一个开放标准，任何人都可以自由地使用和修改，这使得它在整个行业中得到了广泛应用

modbus 协议的历史和起源

Modbus 协议是一种通信协议，允许设备通过各种类型的介质进行通信，如串行线和以太网。它是由生产可编程逻辑控制器（PLC）的 Modicon 公司于 1979 年开发的。该协议的初衷是为了与其所生产的可编程逻辑控制器配套使用，而可编程逻辑控制器在制造业中广泛应用于工业机电过程的自动化。

Modbus 组织成立于 2002 年，由一群致力于推广和使用 Modbus 协议的独立用户和供应商组成。该组织的目标是向公众提供有关协议、规范和相关信息的支持。同时，该组织致力于确保 Modbus 协议在工业自动化市场的可用性、持续改进和广泛应用。

自问世以来，Modbus 协议已被广泛应用于各行各业的各类设备中，并由于简单、开放和易实施一直广受欢迎。

Modbus 数据模型基于一系列寄存器，这些寄存器是设备中存储数据的内存位置，它们代表设备内的存储区域。这些寄存器分为两种类型：

- **保持寄存器：**可由 Modbus 主设备进行读取和写入操作。
- **输入寄存器：**仅供主设备进行读取操作。

在 Modbus 系统中，有两种主要的输入类型：

- **线圈**是 Modbus 协议中的一种数据类型，表示二元状态，如`开/关`或`真/假`。它们可以由 Modbus 主设备进行读写操作。
- **离散输入**类似于线圈，也表示二元状态。然而，与线圈不同的是，离散输入只能被读取，而不能被写入。

Modbus 帧是 Modbus 消息的结构。它由起始帧、功能码、数据和结束帧组成。下表显示了 ASCII 协议变体（下文将详细介绍）中帧的结构：

| 开始 | 地址     | 功能     | 数据     | LRC      | 结束  |
| :--- | :------- | :------- | :------- | :------- | :---- |
| :    | 2 个字符 | 2 个字符 | N 个字符 | 2 个字符 | CR LF |

Modbus 协议有三种主要通信模式：

- RTU（远程终端单元）
- ASCII（美国信息交换标准码）
- TCP/IP（传输控制协议/互联网协议）

Modbus 协议有多种变体，主要包括：

modbus RTU: Modbus RTU 是 Modbus 协议的二进制实现版本。它通常用于串行通信，并以其紧凑的数据表示方式而闻名，这使得它具备高效和快速的特点。

Modbus ASCII: Modbus ASCII 是 Modbus 协议的 ASCII 实现版本。与 Modbus RTU 相比，它的效率较低，但由于使用人类可读的字符，因此更容易使用和调试。

Modbus TCP/IP 是在 TCP/IP 网络上使用的 Modbus 协议版本。它支持长距离和跨不同网络的通信。

Modbus UDP 是使用 UDP 传输协议的 Modbus 协议版本。它比 Modbus TCP/IP 快，也占用更少的网络资源，但它不太可靠，因为它不能保证数据包能送达或按顺序到达。

Modbus Plus（MB+ 或 Modbus+）是 Modbus 协议的专有变体，由施耐德电气推出。它是一种点对点通信协议，相较于标准 Modbus，Modbus Plus 提供更高的传输速度和更可靠的数据传输保证。

Modbus 协议广泛应用于工业自动化领域。在工业环境中，建立一个可靠且高效的通信网络对于维护众多设备和机器之间的良好通信至关重要。Modbus 协议实现了可编程逻辑控制器、传感器和执行器等各种设备之间简单且标准化的通信。它通常用于制造厂、发电厂、炼油厂以及其他工业环境中，用来监控设备和生产过程。

Modbus 协议在车辆系统，特别是电动汽车领域也得到了应用。它被用于监测和控制各种参数，如电池管理系统、充电系统和逆变器系统等。Modbus 提供了一种高效且易于实施的协议，能够保障这些系统的稳定运行。

随着[工业物联网](https://www.emqx.com/zh/blog/iiot-explained-examples-technologies-benefits-and-challenges)的发展，Modbus 协议在自动化、控制和数据分析方面与现代物联网设备的集成机会越来越多。通过工业物联网，Modbus 设备能够成为更大互联系统的一部分，通过收集、分析和利用这些设备的数据，来优化操作、提高安全性和降低成本。

[Neuron](https://github.com/emqx/neuron) 是一款开源的边缘工业协议网关，支持多种 Modbus 驱动程序，包括 Modbus TCP、Modbus RTU、Modbus UDP 和 Modbus RTU over TCP。这些驱动程序实现了 Modbus 设备与工业物联网应用之间的无缝集成。通过这些驱动程序，Neuron 能够使用不同的 Modbus 变体与各种工业设备进行通信，从而实现数据采集、控制，并与解决方案的其他组件进行互操作。

以下是对每个 Modbus 驱动程序的说明：

- **Modbus TCP：**Modbus TCP 是一种被广泛使用的通信协议，它允许在 TCP/IP 网络上传输 Modbus 消息。该协议实现了 Modbus 主设备（如 Neuron）与 Modbus 从设备（例如传感器、执行器或其他工业设备）之间的通信。通过 Neuron 的 Modbus TCP 驱动程序，这些设备能够无缝集成并进行通信，从而促进了数据交换和控制操作。
- **Modbus RTU：**Modbus RTU 是一种流行的串行通信协议，用于实现 Modbus 主设备和从设备之间通过串行接口（如 RS-485 或 RS-232）的通信。该协议使用二进制数据表示，并支持半双工通信，数据可以在两个方向上传输，但不能同时进行。Neuron 的 Modbus RTU 驱动程序实现了与使用该串行通信协议的 Modbus 设备的连接，允许在工业环境中进行数据交换和控制操作。
- **Modbus UDP：**Modbus UDP 是 Modbus 协议的另一种变体，使用 UDP 进行通信。UDP 是一种无连接协议，具有低开销和快速传输的特点。Modbus UDP 通常应用于对速度要求较高的场景，如实时控制应用。通过 Neuron 的 Modbus UDP 驱动程序，可以实现与使用 UDP 作为底层传输协议的 Modbus 设备之间的通信。
- **Modbus RTU over TCP：**Modbus RTU over TCP 是一种机制，它允许将 Modbus RTU 帧封装在 TCP/IP 报文中进行通信。这种机制将 Modbus RTU 的简单性和高效性与 TCP/IP 的强大网络功能相结合。通过 Neuron 的 Modbus RTU over TCP 驱动程序，可以与使用 Modbus RTU 协议但通过 TCP/IP 连接到网络的 Modbus 设备进行通信。

[EMQX](https://www.emqx.com/zh/products/emqx) 是业界领先的高性能 [MQTT Broker](https://www.emqx.com/zh/mqtt/public-mqtt5-broker)，为工业物联网通信提供快速可靠的消息传递服务。结合支持多种工业协议的开源边缘网关 Neuron 实现与 OT 设备的灵活连接和安全数据交换，两者构成了支持工业物联网基础设施建设的强大组合。通过它们的协同作用，确保了工业物联网环境中的无缝集成和稳定的数据通信，同时还实现了先进的分析和控制能力，为工业物联网应用提供了强大的支持。



## habitat

https://github.com/facebookresearch/habitat-lab

https://github.com/facebookresearch/habitat-sim



## pyBullet

PyBullet 基于著名的开源物理引擎 bullet 开发，封装成了 Python 的一个模块，用于机器人仿真和学习。

PyBullet 支持加载 [URDF](https://zhida.zhihu.com/search?content_id=194198632&content_type=Article&match_order=1&q=URDF&zhida_source=entity)、[SDF](https://zhida.zhihu.com/search?content_id=194198632&content_type=Article&match_order=1&q=SDF&zhida_source=entity)、[MJCF](https://zhida.zhihu.com/search?content_id=194198632&content_type=Article&match_order=1&q=MJCF&zhida_source=entity) 等多种机器人描述文件，并提供正/逆向运动学、正/逆向动力学、碰撞检测、射线相交查询等功能。除此之外，还提供了不少机器人的例程和实用的调试工具（滑动条、按钮、文本）等

https://zhuanlan.zhihu.com/p/477303099



## 英伟达机器人

NVIDIA Isaac GR00T 是一个用于构建机器人基础模型和数据管道的研发平台，旨在加速智能、适应性机器人的创建。

  Isaac GR00T N1.5，这是 Isaac GR00T N1 的首次重大更新，Isaac GR00T N1 是全球首个用于广义人形机器人推理和技能的开放基础模型。该跨具体化模型能够处理多模态输入（包括语言和图像），以便在各种环境中执行操作任务。它可以通过后期训练适应特定的具体化、任务和环境。

https://mp.weixin.qq.com/s/rI104nFvr3iyVZKwnqyDrg?click_id=15



NVIDIA Isaac Sim 是一个强大的机器人开发仿真平台，支持与 ROS2 和深度学习应用的无缝集成。本指南将介绍如何在 Isaac Sim 中使用 API 独立模式设置 LeRobot。该方法无需 GUI 即可完全控制机器人仿真。

确保已安装以下内容：

- Isaac Sim 4.5.0
- 系统上配置好 ROS2 Humble
- 有效的机器人 USD 模型（lerobot.usd）
- 安装所需依赖（numpy、pxr、omni.graph.core、isaacsim.core.api 等）

https://isaac-sim.github.io/IsaacLab/main/index.html

https://github.com/isaac-sim/IsaacLab



### holosoma

https://github.com/amazon-far/holosoma

https://github.com/amazon-far

https://www.amazon.science/research-areas/robotics



## 协作机器人

https://www.galbot.com/

https://www.sharpa.com/



## 视觉

### cv2





### mediapipe



### realsenseai

https://github.com/realsenseai/realsense-ros/tree/ros2-development



### 手眼标定

https://mp.weixin.qq.com/s/HOX-VZ_BwGd7dmGy8F3Z4w



### 图像分割

https://docs.ultralytics.com/zh/#yolo-a-brief-history



