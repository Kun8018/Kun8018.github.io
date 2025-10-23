---
title: ROS与机器人控制 
date: 2020-03-18 21:40:33
categories: 技术博客
tags:
    - 技术，Robotic，IT
toc: true
thumbnail: http://cdn.kunkunzhang.top/ros-logo.jpg
---

## 机器人控制

　　其实现在看起来，ROS并不难，不管是配置的文件，或者是编程语言都不是新的，只是把它们整合成一个机器人控制系统里。但是我当时要学习的东西很多，所处的环境特殊，自己又毫无心情，所以并没有深入学习，真是非常遗憾。

![image-20250804151818947](/Users/kun/Library/Application Support/typora-user-images/image-20250804151818947.png)

<!--more-->

## 操作系统

　　ROS的运行环境是Linux系统，我用过Ubuntu16.0，Debian9.0，不同版本系统下可运行的ROS版本不同。

​     ROS目前使用较多的版本是Melodic、Kinetic和Indigo，分别对应Ubuntu系统18.0、16.0、14.0。Indigo使用最多，最稳定，但是现在已经停止维护 和开发，因此本教程在Ubuntu16.0上安装  Kinetic。

### RTOS

rtos与嵌入式linux的区别：

1.实时性

实时性即保证任务在特定时间内完成。衡量一个实时操作系统坚固性的重要指标，是系统从接收一个任务，到完成该任务所需的时间，其时间的变化称为**[抖动](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=抖动&zhida_source=entity)**。

可以依抖动将实时操作系统分为两种：**硬实时**操作系统及**软实时**操作系统，[硬实时操作系统](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=硬实时操作系统&zhida_source=entity)比[软实时操作系统](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=软实时操作系统&zhida_source=entity)有更少的抖动：

- 硬实时操作系统**必须**使任务在确定的时间内完成。
- 软实时操作系统能让**绝大多数**任务在确定时间内完成。

实时性是嵌入式RTOS与嵌入式Linux最本质的区别。

**实时性对比：**

- **嵌入式RTOS**：**硬实时（μs级响应）**，中断延迟通常<10μs。具有强实时性，采用抢占式多任务调度算法，能确保关键任务在严格的时间期限内完成，响应时间可预测，适用于对实时性要求极高的场景，如工业自动化控制、航空航天等。
- **嵌入式Linux**：Linux是作为通用操作系统开发的，其内核在实时处理能力上先天不足，需通过**CONFIG_PREEMPT_RT**补丁优化实时性，**默认软实时（延迟>50μs）**，。虽然经过实时补丁等改进可实现一定的实时性，但本质上是**分时操作系统**，其内核不是专门为实时性设计，在处理高实时性任务时，响应时间存在不确定性，一般用于对实时性要求不苛刻的场景。

2. 内核架构

操作系统内核根据架构来分，可分为：[宏内核](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=宏内核&zhida_source=entity)（Monolithic kernel）、[微内核](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=微内核&zhida_source=entity)（Microkernel）、[混合内核](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=混合内核&zhida_source=entity)（Hybrid kernel）。

- **宏内核（Monolithic kernel）：**宏内核被视作为运行在单一地址空间的单一的进程，内核提供的所有服务，都以特权模式，在这个大型的内核地址空间中运作，这个地址空间被称为内核态（kernel space）。
- **微内核（Monolithic kernel）：**微核心的设计理念，是将系统服务的实现，与系统的基本操作规则区分开来。它实现的方式，是将核心功能模块化，划分成几个独立的进程，各自运行，这些进程被称为服务（service）。所有的服务进程，都运行在不同的地址空间。
- **混合内核（Hybrid kernel）：**混合内核结合了宏内核与微内核两种内核架构。混合内核的基本设计理念，是以微内核架构来设计操作系统内核，但在实现上则采用宏内核的作法。混合内核实质上是微内核，只不过它让一些微核结构执行在用户空间的代码执行在内核空间，这样让内核的执行效率更高些。

**内核架构对比：**

- **嵌入式RTOS**：多为微内核（如Zephyr），无[虚拟内存管理](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=虚拟内存管理&zhida_source=entity)（无MMU），系统调用为直接函数调用。内核通常很精简，只包含基本的任务调度、内存管理、中断处理等功能，以保证系统的高效运行和快速响应，可根据具体需求进行高度定制。
- **嵌入式Linux**：宏内核，依赖MMU实现虚拟内存，需用户/内核态切换（syscall接口）。内核相对庞大复杂，支持多用户、多任务，具备完善的内存管理、进程调度、文件系统等功能，提供了丰富的系统服务和接口，但也因此占用更多的资源。

3. 资源需求

- **嵌入式RTOS**：由于内核精简，对硬件资源要求较低，可在资源有限的[微控制器](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=微控制器&zhida_source=entity)（MCU）等设备上运行，如一些简单的传感器节点、小型智能设备等。
- **嵌入式Linux**：因功能丰富、内核复杂，需要较多的硬件资源支持，通常运行在具有一定处理能力和内存空间的[微处理器](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=微处理器&zhida_source=entity)（MPU）上，如工业控制计算机、智能终端等。

4. 安全性

- **嵌入式RTOS**：系统功能相对单一，代码量小，经过严格的测试和验证，安全漏洞相对较少，且可针对特定安全需求进行定制化设计，适用于对安全性要求极高的[安全关键系统](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=安全关键系统&zhida_source=entity)。
- **嵌入式Linux**：开源特性使其容易受到安全威胁，不过开源也便于社区及时发现和修复安全漏洞。同时，通过安全增强技术如SELinux等可提高其安全性，适用于对安全性有一定要求但非绝对安全关键的场景。

5. 学习难度/开发难度

- **嵌入式RTOS**：学习和开发相对简单，其功能集中在实时任务处理，开发框架和API相对简洁，开发者主要关注任务的划分、调度和通信等，适合初学者和对实时性开发经验较少的人员。
- **嵌入式Linux**：学习和开发难度较大，涉及内核原理、文件系统、网络协议等复杂知识，开发过程需要掌握多种工具和技术，如[交叉编译](https://zhida.zhihu.com/search?content_id=256466373&content_type=Article&match_order=1&q=交叉编译&zhida_source=entity)、内核裁剪、驱动开发等，对开发者的技术水平和经验要求较高

6. 核心内容

- **嵌入式RTOS**：核心是实时任务调度和管理，确保任务在规定时间内完成，重点关注任务的优先级分配、时间片管理以及任务间的同步与通信。
- **嵌入式Linux**：核心是提供一个通用的、功能丰富的操作系统平台，支持多种应用开发，包括文件系统管理、网络服务、设备驱动等，以满足不同领域的多样化需求。

7. 网络能力

- **嵌入式RTOS**：网络功能相对较弱，需集成轻量级协议（如LwIP）。通常只支持基本的网络协议，如简单的TCP/IP协议栈，以满足实时数据传输需求，对于复杂的网络应用支持有限。
- **嵌入式Linux**：具有强大的网络功能，支持完整的网络协议栈，包括TCP/IP、UDP等多种协议，能轻松实现网络服务器、网络客户端等复杂网络应用，适用于需要频繁进行网络通信的设备，如网络路由器、智能网关等。

8. 开发方式

- **嵌入式RTOS**：开发方式相对简单，通常使用特定的集成开发环境（IDE），如IAR、Keil等，配合相应的开发板进行开发。开发环境配置相对容易，主要关注任务的编写和调试。
- **嵌入式Linux**：开发方式较为复杂，需要搭建交叉编译环境，涉及到宿主机和目标机之间的通信和协作。常用的开发工具有GCC、Make等，开发过程需要进行内核编译、驱动开发、文件系统制作等多个环节，开发环境的搭建和配置相对繁琐。

9. 学习资料

- **嵌入式RTOS**：学习资料相对丰富，主要集中在特定的RTOS产品手册、官方文档以及一些专业的嵌入式开发书籍上。
- **嵌入式Linux**：学习资料非常丰富，有大量的书籍、在线文档、开源项目以及活跃的社区论坛，如Linux内核官方文档、Linux公社等，开发者可以方便地获取各种技术知识和解决方案。

10. 运行功耗

- **嵌入式RTOS**：由于系统简单，在运行时可以根据任务需求灵活控制硬件资源的使用，能较好地实现低功耗管理，支持μA级休眠模式（STOP/STANDBY），动态电压频率调节（DVFS）优化能耗。**适用于电池供电的设备**，如物联网传感器节点等。
- **嵌入式Linux**：因内核复杂，需要持续运行多个后台进程和服务，对硬件资源的使用相对较多，运行功耗一般较高（通常>100mW），但通过一些节能技术和优化措施，如电源管理框架（如CPUFreq），也可在一定程度上降低功耗，**适合插电设备**。

11. 启动速度

- **嵌入式RTOS**：毫秒级启动，启动速度快，内核初始化时间短，能在短时间内完成系统启动并进入工作状态，满足一些对快速启动有要求的应用场景，如工业控制中的紧急响应设备。
- **嵌入式Linux**：秒级启动，启动过程涉及U-Boot引导、内核加载、文件系统挂载、服务启动等多个环节，启动速度相对较慢，一般需要几秒到几十秒的时间，不过对于一些非实时性要求高的设备，如智能电视等，启动速度的影响相对较小。

12. 驱动开发

- **嵌入式RTOS**：驱动开发相对简单，通常针对特定硬件平台提供简洁的驱动接口，开发者只需根据硬件特性和RTOS的要求编写基本的驱动程序，主要关注硬件的初始化和数据传输等功能。
- **嵌入式Linux**：驱动开发较为复杂，需要深入理解Linux内核的驱动模型，如设备树、字符设备驱动、块设备驱动等，开发过程需要遵循严格的内核编程规范，涉及到大量的内核代码编写和调试工作。

13. 应用开发

- **嵌入式RTOS**：应用开发主要围绕实时任务展开，注重任务的逻辑实现和时间控制，开发语言一般以C、C++为主，开发框架相对简单，主要用于实现特定的实时控制功能。
- **嵌入式Linux**：应用开发更加多样化，支持多种编程语言，如C、C++、Python等，可借助丰富的库和框架进行开发，能实现包括图形界面、网络应用、数据处理等在内的复杂应用。

14. 图形用户界面开发

- **嵌入式RTOS**：本身对图形用户界面（GUI）的支持有限，若要实现GUI，需要额外添加专用的轻量级GUI库（如LVGL、emWin），且功能相对简单，适用于对界面要求不高的设备，如简单的工业控制终端。
- **嵌入式Linux**：有丰富的GUI库和工具，如Qt、GTK等，能开发出功能强大、界面美观的图形用户界面，适用于智能终端、车载信息娱乐系统等对用户体验要求较高的设备。

15. 岗位发展路线、前景

- **嵌入式RTOS**：岗位发展路线主要集中在实时控制系统开发、硬件驱动工程师等方向，随着工业自动化、智能制造等领域的发展，对掌握嵌入式RTOS技术的人才需求持续增长，尤其是在汽车电子、航空航天等高端制造业，具有较好的发展前景。
- **嵌入式Linux**：岗位发展路线较为广阔，可从事内核开发工程师、系统移植工程师、应用开发工程师等多种岗位。在物联网、人工智能、智能交通等热门领域有广泛应用，市场需求大，发展前景良好，且由于技术难度较高，相关人才的薪资待遇也相对较高。

## ROS安装

 完整的教程其实在[ROS官网](ros.org)都有，本文旨在梳理

在Ubuntu下打开命令行窗口，

更新应用市场

```shell
sudo apt-get update
```

安装ROS内核、rviz、2D/3D simulator、robot-generic libraries、navigation and 2D/3D perception

```shell
sudo apt-get install ros-kinetic-desktop-full
```

安装ROS依赖包

```shell
sudo rosdep init
rosdep update
```

注：ROS自带python语言环境

安装

```shell
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 工作空间/文件夹

Workspace下一般有三个文件夹，

src放代码文件，

build文件夹和devel文件夹是编译后生成的文件。

install是安装空间

log是日志空间



catkin_init_workspace

catkin make   //编译目标文件，生成可执行文件

source  ~/catkin_ws/devel/setup.bash //刷新工作环境

### rosdep

`rosdep` 是一种 ROS 的依赖管理工具，用于识别和安装构建 ROS 包所需的系统依赖项。它不是一个包管理器，而是一个元包管理器，它根据当前 ROS 发行版和系统平台，找到并调用系统包管理器（如 `apt` 或 `dnf`）来实际安装缺失的依赖

常用命令：

1. **`rosdep init`**: 初始化 `rosdep`，使其能够理解您的系统和 ROS 发行版。通常在首次安装 ROS 或设置新工作区时运行此命令。
2. **`rosdep update`**: 更新 `rosdep` 的依赖信息。这确保 `rosdep` 知道最新可用的依赖项。
3. **[rosdep install](https://www.google.com/search?q=rosdep+install&sca_esv=972409babe1b9eaa&sxsrf=AE3TifMh1qHnGTT1OGPhEBtBGffBkoX6Hg%3A1759620370740&ei=Eq3haKz7LLu9seMPw6mieA&ved=2ahUKEwi3kb7e2IuQAxU2a2wGHRddH3IQgK4QegQIBRAD&uact=5&oq=rosdep命令&gs_lp=Egxnd3Mtd2l6LXNlcnAaAhgDIgxyb3NkZXDlkb3ku6QyDhAAGIAEGKIEGPgFGIsDMgsQABiABBiiBBiLAzILEAAYgAQYogQYiwMyCBAAGIsDGO8FMgsQABiABBiiBBiLA0jFMFCBAljWLnAReACQAQGYAaIBoAGpEKoBBDAuMTa4AQPIAQD4AQGYAh6gArMNwgIIEAAYgAQYsAPCAg4QABiABBiwAxiiBBiLA8ICCxAAGLADGIsDGO8FwgIKECMYgAQYJxiKBcICCBAAGIAEGMsBwgIFEAAYgATCAgQQABgewgIHEAAYgAQYDcICCRAAGIAEGAoYDcICCBAAGIAEGKIEwgIFEAAY7wXCAggQABgIGA0YHsICBxAhGKABGAqYAwCIBgGQBgWSBwUxNy4xM6AHwyuyBwQwLjEzuAeYDcIHBTE0LjE2yAcj&sclient=gws-wiz-serp&mstk=AUtExfBWNOIj2vZZ9CI-V6-CyQqz-4-8FQt5R0J9Duw4h7N9XFimRe818-FjsRD7SrA4JhEZq4y0CfGA5poqLszX8GbCZcGsRHtPKu6q9atogV4706TJDX9HrZni7m83bG7T01_YlLcYQDPiqct2i3b315XK45yzrBvdB6JWATFk_cqejpXYM-5rAXyNDgMnWLd8BRGIpq8aPAc22xz3gqrp83IT9P6jhGnTY2Yea_RKDBftARnFGLsOwYWWl8M20QSAs5xM8hBd8rq_9mfeLNDHkHWVH9JDs_-zaaUOEFModATvYw&csui=3)**: 安装 ROS 包所需的所有系统依赖。
   - **`rosdep install -r --from-paths src --ignore-src`**: 这是在构建 ROS 工作区时常用的命令。它会检查 `src` 目录下的所有包，并安装它们在本地源文件中声明的依赖项。
   - **`-r`**: 表示在遇到错误时不要退出。
   - **`--from-paths src`**: 指定要安装依赖的源文件位于 `src` 目录中。
   - **`--ignore-src`**: 告诉 `rosdep` 忽略在 `src` 目录中找到的包的依赖，只安装外部依赖。

### 功能包



```shell
ros2 pkg create --build-type ament-cmake learning-pkg
ros2 pkg create --build-type ament-python learning-pkg
```



### ROS Master与Node

roscore

rosrun

#### 

### Launch 文件

roslaunch  .launch

<node>

rostopic





<ros.h>

## URDF

标准标记语言文件，与xml语言类似

stl

##  SLAM 

　　

## MoveIt

https://moveit.ai/

## Gazebo



## Rviz





## ROS2

![image-20250922091934131](/Users/kun/Library/Application Support/typora-user-images/image-20250922091934131.png)

命令行精简

实时性

- **ROS 1**：原生不支持硬实时（Hard Real-Time），其通信机制引入的非确定性延迟，使其不适合对时间敏感的应用（如无人驾驶和手术机器人）。
- **ROS 2**：通过DDS和对实时操作系统的兼容性，提供了原生实时计算能力。其可配置的QoS策略和执行管理机制，能够实现有界延迟和抖动控制，更好地支持时间敏感型任务

多机器人支持

- **ROS 1**：处理多机器人系统较为复杂，需要复杂的网络配置，且依赖于ROS主节点。
- **ROS 2**：得益于DDS的分布式特性，支持无缝的**点对点通信**，使得多机器人系统（例如机器人集群）的扩展变得更加容易

节点（Nodes）

- **ROS 1**：一个可执行文件通常对应一个节点。后来引入的`nodelet`机制允许在同一进程中运行多个节点，但实现更复杂。
- **ROS 2**：**组件（components）**的概念被引入核心，允许在同一进程中运行多个节点。这有助于提高进程间通信效率，特别是在资源受限的硬件上。

### 构建系统和工作空间

ros1使用catkin作为其构建空间

ros2使用colcon作为构建系统

### 语言支持

- **ROS 1**：C++和Python有各自独立的后端实现。
- **ROS 2**：C++和Python使用共享的底层客户端库API（`rclcpp`和`rclpy`），确保不同语言下的功能一致性。





## setuptools

setuptools库的前身是distutils（一个python标准库），**setuptools本身不是标准库，所以需要自行安装**。setuptools提供的主要的功能有：

- **python库的打包分发**
- **依赖包安装与版本管理**
- **python环境限制**
- **生成脚本**
- **c/c++ 拓展**

python库的打包分发方式有两种：**源码包source dist**（简称sdist）、**二进制包binary dist**（简称bdist）。

源码包sdist就是我们熟悉的 .zip 、.tar.gz 等后缀文件。就是一个压缩包，里面包含了所需要的的所有源码文件以及一些静态文件（txt文本、css、图片等）。

setup.py后面会介绍，总之setup.py指定了打包分发的配置信息。`--formats` 参数用来指定压缩格式，若不指定format格式，那么 sdist 将根据当前平台创建默认格式。在类 Unix 平台上，将创建后缀名为`.tar.gz`分发包，而在Windows上为 `.zip` 文件。

执行完该命令，我们可以看到文件夹下多了dist文件夹（包含压缩源码的分发包）和egg-info文件夹（中间临时配置信息）。

安装源码包

安装源码包有两种方法，先解压缩源码包，或者直接安装源码包。

- 先解压缩源码包，再执行setup.py

```shell
# 
$ python setup.py install
等价于
$ python setup.py build
$ python setup.py install
'''
python setup.py install包括两步：python setup.py build python setup.py install。
这两步可分开执行， 也可只执行python setup.py install, 因为python setup.py install总是会先build后install.
'''
```

直接pip安装源码包

```shell
pip install  xxx.zip 
```

python目前主流的二进制包格式是[wheel](https://zhida.zhihu.com/search?content_id=190404147&content_type=Article&match_order=1&q=wheel&zhida_source=entity)（.whl后缀），它的前身是egg。wheel本质也还是一个压缩包，可以像像zip一样解压缩。**与源码包相比，二进制包的特点是不用再编译，也就是安装更快！**在使用wheel之前，需要先安装wheel：`$ pip install wheel`

设置python库的基本信息

```python
from setuptools import setup

def readme():
    with open('README.md', encoding='utf-8') as f:
        content = f.read()
    return content

setup(
    name = 'myapp', # 包名称
    version = '1.0', # 版本
    author = 'lihua', # 作者
    author_email = 'lihua@163.com', # 作者邮箱
    description='a example for pack python', # 描述
    long_description=readme(), # 长文描述
    long_description_content_type='text/markdown', # 长文描述的文本格式
    keywords='pack', # 关键词
    url='https://github.com/lihua/myapp', # 项目主页
    classifiers=[ # 包的分类信息，见https://pypi.org/pypi?%3Aaction=list_classifiers
            'Development Status :: 5 - Production/Stable',
            'License :: OSI Approved :: Apache Software License',
            'Operating System :: OS Independent',
            'Programming Language :: Python :: 3',
            'Programming Language :: Python :: 3.6',
            'Programming Language :: Python :: 3.7',
            'Programming Language :: Python :: 3.8',
            'Programming Language :: Python :: 3.9',
        ],
    license='Apache License 2.0', # 许可证
)
```

### 混合编程

python开发成本低，但运行速度堪忧，c/c++速度无人能敌，但内存管理、模板编程等对程序猿来说，开发成本太高。那么python+c的混合编程的解决方案应运而生，博采众长，python就像前端，简单的语言赏心悦目，c就像后端，适合做计算密集型任务。而且c/c++还可以有效规避掉python的GIL锁，速度更上一层楼。

python+c/c++混合编程的技术路径有很多，如：

- 原生的Python.h
- [cython](https://zhida.zhihu.com/search?content_id=190404147&content_type=Article&match_order=1&q=cython&zhida_source=entity)
- ctypes、cffi
- [SWIG](https://zhida.zhihu.com/search?content_id=190404147&content_type=Article&match_order=1&q=SWIG&zhida_source=entity)
- Boost.Python：逐渐被[pybind11](https://zhida.zhihu.com/search?content_id=190404147&content_type=Article&match_order=1&q=pybind11&zhida_source=entity)取代
- pybind11：目前比较流行推荐的方法，学习成本低，pytorch也采用了该方法。

本质上setuptools是根据setup.py配置来**指导生成gcc命令行**，当然你也可以粗暴地直接用gcc命令行来编译c/c++拓展源码，但工程量太大，setuptools支持很多混合编程技术cython、SWIG等等。所以甭管你采用什么混合编程技术，绕不开setuptools。setuptools编译c/c++拓展源码的过程主要是把**源代码**编译成**动态连接库**（linux下是**.so**，windows下是**.pyd**）。这样就可以在.py中愉快import并使用拓展模块了。

setuptools.Extension类有几个重要的构造参数（详见[API文档](https://link.zhihu.com/?target=https%3A//docs.python.org/3/distutils/apiref.html)）

- name：在python中import该拓展的名称
- sources：源代码文件名
- language：默认'c'，如果要用C++，改成'c++'
- include_dirs：其实就是传递给 gcc 的 -I(大写i)指定include的头文件目录
- library_dirs：其实就是传递给 gcc 的 -L 指定连接文件的目录
- libraries：其实就是传给 gcc 的 -l(小写的L)指定连接文件，在L指定的位置找
- extra_compile_args：其实传给 gcc 的额外的编译参数，比方'-std=c++11'
- extra_link_args：其实传给 gcc 的额外的链接参数（生成动态链接库）
- define_macros：定义宏
- undef_macros：取消定义宏



## 视觉

手眼标定

https://mp.weixin.qq.com/s/HOX-VZ_BwGd7dmGy8F3Z4w



## 机器人实验室/科研团队

浙大流体动力与机电系统国家重点实验室：http://sklofp.zju.edu.cn/skl/

邹俊：邹俊研究小站  https://mp.weixin.qq.com/s/om0yqxboZeJwl_H-agXkrA

Fast-lab:https://space.bilibili.com/257271972

熊蓉：https://www.zjubh.com/home/yjzx/yjzx2/cate_id/24.html

方斌：https://ccf.org.cn/cirac2024/speaker_d_2114

北京大学前沿计算中心 王一周。王鹤：https://cfcs.pku.edu.cn/ https://space.bilibili.com/28217340

宾夕法尼亚大学工程学院：https://www.grasp.upenn.edu/

UC伯克利：https://bair.berkeley.edu/about

南科大周博宇：https://robotics-star.com/ https://zhuanlan.zhihu.com/p/681286732

博士生：

交大：https://alvinwen428.github.io/

opai人员：https://xingyu-lin.github.io/

吕江然 https://jiangranlv.github.io/

https://jychen18.github.io/



## 协作机器人

https://www.galbot.com/

https://www.sharpa.com/



## 机器人资讯

### ieee spectrum robotics

https://spectrum.ieee.org/

### singularityhub

https://singularityhub.com/topics/#robotics

### newaltas

https://newatlas.com/robotics/

### techxplore

https://techxplore.com/robotics-news/

### sciencedaily

https://www.sciencedaily.com/news/computers_math/robotics/

### stanford ai lab

https://ai.stanford.edu/blog/robotics/

### mit robotics

https://news.mit.edu/topic/robotics

### weekly robotics

https://github.com/msadowski/awesome-weekly-robotics?tab=readme-ov-file

### robot report

https://x.com/therobotreport

### techcrunch

https://techcrunch.com/category/robotics/



## 科研

### research rabbit

科研神器

Research Rabbit是一款实用的科研工具。 **它能依据你输入的文献或关键词智能推荐相关文献，帮你发现更多有用资料。****还可展示文献间的关联，形成网络，让你看清研究间的联系。 有着不错的文献管理功能，方便你整理、标记文献。同时支持团队协作，界面简洁易上手，核心功能免费，能助力科研工作更高效开展。** 



## b站资源

1.胡月居：https://www.bilibili.com/video/BV16B4y1Q7jQ?spm_id_from=333.788.videopod.episodes&bvid=BV16B4y1Q7jQ&vd_source=e898668a475e6de0272851f4a5fc6328&p=2   https://www.guyuehome.com/Bubble/index

2.鱼香ros：https://www.bilibili.com/video/BV1GW42197Ck?spm_id_from=333.788.recommend_more_video.1&vd_source=e898668a475e6de0272851f4a5fc6328
