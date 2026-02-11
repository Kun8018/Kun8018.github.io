---
title: ROS与机器人控制
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，
toc: true
thumbnail: 
---

<!--more-->

## 机器人实验室/科研团队

香港大学 mmlab/opendrivelab https://opendrivelab.com/publications  https://mmlab.hk/member   顶级团队 https://github.com/OpenDriveLab/UniVLA。  和上海人工智能实验室有深度合作，在上海创智学院也有任职 李弘扬。与稚晖君也有合作 https://github.com/OpenDriveLab/AgiBot-World。https://lihongyang.info/  实验室人员：https://yhyang-myron.github.io/

浙大流体动力与机电系统国家重点实验室：http://sklofp.zju.edu.cn/skl/

邹俊：邹俊研究小站  https://mp.weixin.qq.com/s/om0yqxboZeJwl_H-agXkrA

浙大 graspLab: https://grasplab2022.github.io/

Fast-lab:https://space.bilibili.com/257271972

熊蓉：https://www.zjubh.com/home/yjzx/yjzx2/cate_id/24.html

方斌：https://ccf.org.cn/cirac2024/speaker_d_2114

北京大学前沿计算中心 王一周。王鹤：https://cfcs.pku.edu.cn/ https://space.bilibili.com/28217340

北京大学 人工智能研究院 仉尚航 https://www.bilibili.com/video/BV16YrhBxEfS/?spm_id_from=333.1007.tianma.8-1-27.click&vd_source=e898668a475e6de0272851f4a5fc6328   https://www.shanghangzhang.com/about-me

北大博士毕业生 https://cypypccpy.github.io/ 

北大仿生设计实验室 https://en.ibdl.pku.edu.cn/research/publications/index.htm

宾夕法尼亚大学工程学院：https://www.grasp.upenn.edu/

UC伯克利：https://bair.berkeley.edu/about

南科大周博宇：https://robotics-star.com/ https://zhuanlan.zhihu.com/p/681286732

北航副院长 文力 https://softrobotics.buaa.edu.cn/

清华 赵慧婵

清华 交叉信息研究院老师：

https://zhuanlan.zhihu.com/p/474615424

https://yang-gao.weebly.com/publications.html   https://jialeihuang.github.io/  同一团队

https://zhuanlan.zhihu.com/p/1934048726348981419 tealab

贵州大学 MFPIR: 多模态与智能感知实验室 杨静老师

博士生：

交大：

https://alvinwen428.github.io/

https://www.cs.sjtu.edu.cn/~wang-xb/

https://www.mvig.org/index.html 

穆姚 写的很详细 https://scalelab-sjtu.github.io/embodied_guide.html

吕峻

opai人员：https://xingyu-lin.github.io/

吕江然 https://jiangranlv.github.io/

陈嘉毅  https://jychen18.github.io/

王乾旭 Zekun Qi： https://qizekun.github.io/sofar/

德州libero团队 https://libero-project.github.io/main

上海ai lab IDC组 https://sheldontsui.github.io/  https://zhaoyanglyu.github.io/

​	https://internrobotics.shlab.org.cn/aboutus.html

上科大：https://sist.shanghaitech.edu.cn/2025/1009/c2858a1115684/page.htm https://star-center.shanghaitech.edu.cn/labs.html

哥伦比亚大学/纽约大学：https://venkyp.com/。   https://raunaqb.com/

卡内基梅隆大学：https://iamlab-cmu.github.io/publications/  https://scholar.google.com/citations?user=_tbXjP4AAAAJ&hl=en 

宾夕法尼亚大学 grasp lab  https://www.grasp.upenn.edu/role/faculty/  史建波

清华 视觉实验室 https://fundamentalvision.github.io/publications/ 孔涛  https://jifengdai.org/ 代季峰

哈工大 袁晗 https://homepage.hit.edu.cn/yuanhan

南大苏州/仙林 高阳团队 https://cs.nju.edu.cn/rl/papers.htm



苏黎世联邦理工。rsl。https://rsl.ethz.ch/the-lab.html

麻省理工

 https://interactive.mit.edu/research/

https://mcube.mit.edu/people.html mcube 做触觉传感器的

南洋理工大学 RCC https://www.ntu.edu.sg/rrc

北航 https://leonhlj.github.io/

字节跳动。seed xiaoma  https://yusufma03.github.io/

德国航空航天中心（DLR, Deutsches Zentrum für Luft- und Raumfahrt）https://www.dlr.de/en/rm/about-us/institute

德国卡尔研究中心  https://mbreuss.github.io/

 http://joschu.net/ 

普林斯顿大学 https://pixl.cs.princeton.edu/labs.php



## 人工智能老师/实验室/团队

南大苏州校区 https://lsk-robot.github.io/



## 机器人运动学

### 雅各比矩阵

Jacobian Matrix 在[机械臂运动学](https://zhida.zhihu.com/search?content_id=187118345&content_type=Article&match_order=1&q=机械臂运动学&zhida_source=entity)中用来计算机械臂末端执行器的速度与各个关节运动速度之间的关系。

雅各比矩阵的实质是微分方程式。对于机械臂来说，我们可以通过[正向运动学](https://zhida.zhihu.com/search?content_id=187118345&content_type=Article&match_order=1&q=正向运动学&zhida_source=entity)，由关节运动信息，得到机器人位姿，即 

对于一个有 6 自由度的机器人来说，我们可以直接对矩阵求逆。但对于一个没有 6 自由度的机器人来说，就不能直接求逆，而是要用 pseudo-inverse 的方法来求逆



### 轨迹规划

在[机械臂](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=机械臂&zhida_source=entity)中，我们将机器人末端执行器移动时的位姿随时间变化而得出的曲线称为其轨迹。我们之所以需要对机器人的轨迹进行规划是因为通常来说，我们对机器人的输入只有工具的起点和终点。而我们需要让机器人能够自然平滑的按照我们想要的方式从起点移动到终点，所以需要设计一个算法，使得机器人在移动的过程中，无论是位置的变化还是速度的变化都是连续且平滑的，有时，也会要求加速度的变化为连续的

对于机械臂来说由两种空间下的轨迹规划，一种是基于关节空间 ([Joint-space](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=Joint-space&zhida_source=entity))， 另外一种是基于世界空间 ([Cartesian-space](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=Cartesian-space&zhida_source=entity))。

对 Joint-Space 来说进行轨迹规划需要以下步骤：

1. 确定轨迹的起点(Initial Point) - 途径点(Via Point) - 终点(Finial Point)
2. 通过 Inverse Kinematics (IK) 计算出以上所有点的 Joint Angle，即 Joint-space 下的所有点关于时间的位置.
3. 设计一条轨迹将 Joint-space 所有点都平滑的连接起来。
4. 再通过 Forword Kinematics (FK) 算出在这种情况下末端关于世界坐标的曲线。
5. 检查世界坐标下的曲线是否合理。

对 Cartesian-space 来说进行轨迹规划需要以下步骤：

1. 确定轨迹的起点(Initial Point) - 途径点(Via Point) - 终点(Finial Point)
2. 设计一条曲线将 Cartesian-space 所有点都平滑的连接起来。
3. 使用 IK 计算出这条曲线在 Joint-space 的曲线。
4. 检查 Joint-space 的曲线是否平滑。

Cartesian-space 来做轨迹规划是一种直观的对位姿轨迹的规划。但可能出现机器人的运动速度上的不平滑。

Joint-space 可以保证运动速度的平滑，但是运动位姿的轨迹可能与设计前预想的轨迹有区别，不会出现尖锐的拐角。

一般来说需要根据实际应用的情况来选择使用那种方法来设计轨迹。

对于多轴机器人来说，当机器人的位置处于奇点的时候，机器人会失去至少一个自由度。这可能会导致机器人在此处卡住。所以应该尽量避免奇点。

由几何法找奇点会比较复杂，较为简单的方法是根据[雅各比矩阵](https://zhuanlan.zhihu.com/p/445449208)来判断。当：

> determinat(det) 即行列式。

时，机器人就在奇点上。

这里所说的轨迹规划算法，实际上就是经过多点的平滑曲线拟合算法。如果使用多项式拟合，通常需要使用三次以上的多项式来拟合。线性拟合也可以，如[抛物线混合](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=抛物线混合&zhida_source=entity) (Parabolic Blends)。由于曲线需要经过每一个点，所以不能用贝塞尔曲线来设计轨迹。



## 机器人动力学

### 牛顿欧拉方程

牛顿-欧拉方程描述了刚体的组合平移和旋转动力。传统上，[牛顿-欧拉方程](https://zhida.zhihu.com/search?content_id=190538889&content_type=Article&match_order=3&q=牛顿-欧拉方程&zhida_source=entity)是使用列向量和矩阵将刚体的欧拉两个运动定律组合成一个具有 6 个分量的单个方程。这些定律将刚体重心的运动与作用在刚体上的力和扭矩（或[力矩](https://zhida.zhihu.com/search?content_id=190538889&content_type=Article&match_order=1&q=力矩&zhida_source=entity)）之和联系起来。

对于原点与物体质心重合的坐标系，可以用[矩阵](https://zhida.zhihu.com/search?content_id=190538889&content_type=Article&match_order=2&q=矩阵&zhida_source=entity)形式表示为



## 软体机器人

### RecurDyn仿真

https://recurdyn.cn/product/professional.html

**RecurDyn/Professional**是RecurDyn软件最核心基础的组成构件（基本包），其包括：提供动力学仿真求解分析所许的前/后处理器 **Modeler**，模拟和分析动力学系统的求解器 **MFBD Solver**，另外还包括一个完整的定制化开发环境 **ProcessNet**。

用户可在前处理中将机械系统整个随时间变化的运动过程以视频动画的形式查看；后处理可以获取仿真结果数据并自动生成随时间变化的运动曲线。另外，用户透过RecurDyn/Professional才能使用RecurDyn软件内各式各样的工具包与接口模块；透过工具包向RecurDyn/Professional添加功能，如用于特殊应用领域图形用户界面（建模方便）和求解方法（解算迅速）

**主要特点**

- 快速可靠的机械系统仿真
- 熟悉的用户界面：类似于CAD和Windows本地化
- 强大的2D/3D接触模拟
- MFBD多柔性体动力学
- 使用工具包自动建模

RecurDyn/Modeler是RecurDyn的心脏，拥有简单直观的图形用户界面。它具有多体动力学分析的前处理与后处理的完整环境。利用RecurDyn/Modeler进行建模，设置模型参数和数据，启动和控制求解器，通过绘图和动画展示模拟结果，以及导出数据结果文件。RecurDyn/Modeler允许使用简单的鼠标和键盘命令以图形化方式创建模型，从而直观地定位物体，定义运动副，力元素，接触和其他因素。

- 几何模型可以直接从CAD导入到RecurDyn/Modeler当中

- 在图形用户界面中可以使用建模工具建立几何模型。支持的CAD文件格式包括：

  - Parasolid (.x_t, .x_b, .xmt_txt, .xmt_bin)
  - STEP (.step, .stp)
  - ACIS (.sat)
  - IGES (.igs)
  - CATIA (.CATPart, CATProduct)
  - Shell (.shl)
  - STL (.stl)

  （* STEP，CATIA，ACIS和IGES导入功能，每个都需要额外购买软件）

RecurDyn/Modeler可以通过多样的模块化工具包扩展用途，包括柔性体，媒介传输系统如打印机和卷绕型介质传输，汽车跟踪等。

RecurDyn/Solver是同类中领先的多体动力学求解器。RecurDyn/Solver在先进的计算和数值技术的基础上，拥有快速，稳定的求解速度，即使系统中包含有非常复杂的接触条件。RecurDyn/Professional中的RecurDyn/Solver用于求解刚体系统，可以通过工具包提供其他功能。例如，RecurDyn可以通过扩展工具包来模拟柔性体，介质传输系统如打印机，流体弹性动力套管，机电一体化控制系统。RecurDyn/Solver将这些系统合成一个耦合的方程组，同时求解而不是通过联合仿真，这使得RecurDyn/Solver更快更强大。RecurDyn/Solver在求解刚体系统时提供隐式的广义-α方法，和DASSl用于求解微分方程的数值积分。RecurDyn/Solver既可以提供静力学分析和动力学分析。

- 预分析
- 静力学分析
- 动力学分析
- 利用相对坐标求解递归方程
- DASSl / 隐式的广义-α/ 广义-α积分器

Recurdyn/ProcessNet内置在RecurDyn/Professional中，是一个强大的，基于脚本定制的环境。Recurdyn/ProcessNet允许用户为Recurdyn/Modeler创建自己的图形用户界面，可以操作模型数据，创建自定义的对话框和用户界面，自动化任务，以及封装领域内知识和最佳时间成果。Recurdyn/ProcessNet可以访问和操作预处理数据和后处理数据。Recurdyn/ProcessNet使用Microsoft.NET作为脚本环境。脚本可以使用C#，Visual Basic创建。通过Recurdyn/ProcessNet，用户可以大大扩展RecurDyn/Professional的功能，以满足独特，个性化的需求。

RecurDyn/Professional 中包含的一些其它功能

- Contact

  RecurDyn/Professional包括一系列的接触元素。接触元素可以分为各种类型。广义接触类型通常用于一般几何形状的过程接触。广义接触类型中用于三维情况，以及可以过度到二维接触的情况的接触类型可以提高计算效率。原始接触类型提供了简单形状之间的接触分析能力，例如盒，球，圆台，圆柱体以及基本形状。原始接触类型对于基本几何形状与分析形状的情况是非常有效的。此外，各种工具包扩展了接触类型库，增加了针对于这些特定工具包的接触技术。

- Subsystem

  子系统是元件，运动副，力元素，接触以及其他因素的集合。子系统可以被导入到其他模型或另一个子系统当中。子系统可以独立计算。这样可以允许将模型的元素分组重复使用来简化建模。

- Sensor

  RecurDyn/Professional包含可在模型内用于许多目的的传感器功能，例如用于帮助分析结果或向机电一体化控制系统模拟提供输入。软件中包含各种不同的传感器。例如，用于测量物体几何特征之间距离的传感器，用于判断物体是否在指定区域的传感器，用于指示物体上特定点的位置，速度，加速度的传感器。

- Expressions

  RecurDyn允许用户编写在模型模拟期间执行和评估的简单表达式。表达式是诸如sin(5*time) 的文本字符串，在RecurDyn中可以用于多种目的，例如指定物体的位置随时间变化，指定反作用力，或创建用户定义的约束方程。表达式直接在RecurDyn/Modeler中编写，并为用户提供了一种非常简单并强大的方法来控制模型的各部分或生成可在模拟后分析的数据。



### 软体机器人集群



### 高性能驱动器

电活性聚合物因其柔韧性、响应快、质轻等优势，在软体机器人、生物医学设备等领域具有广泛应用前景。然而，EAP 材料长期以来面临一个根本性矛盾：**高变形能力**（大应变）通常要求材料具有低模量；**高输出力**（高刚度）则要求材料具有高模量。传统方法如引入大体积单体单元（如 CTFE）或使用介电弹性体虽能提升变形能力，但往往以牺牲刚度为代价，导致输出力不足、易发生电击穿或机械失稳。Tailoring conductive nanofiller alignment for high actuation strain and output force in electroactive polymers。该研究提出并验证了一种**通过电场调控导电纳米填料取向**的策略，成功解决了电活性聚合物在执行器中**高变形能力与高输出力之间的固有矛盾**。



### 绳驱动机器人

Adaptive-Interaction-Based Online Reconfiguration of Cable-Driven Parallel Robots - **IEEE Transactions on Robotics**。该研究提出了一种**基于自适应交互的在线重构策略**，用于提升缆驱并联机器人在物理人机交互任务中的动态性能。传统刚性机器人存在**工作空间有限、安全性差、负载能力受限**等问题。

缆驱并联机器人因其**轻质、柔性、大工作空间和高负载能力**，成为pHRI的理想平台。现有方法的局限-**被动策略**（如串联弹性驱动器）和**主动策略**（如阻抗/导纳控制）虽能提升柔顺性，但存在**结构复杂、控制难度大、适应性有限**等问题。

现有重构策略多关注**任务轨迹优化**，缺乏对**动态交互意图**的响应能力。



### 感知

https://mp.weixin.qq.com/s/hyHsWrRCucSxe1jNRW76Iw



### 相关论文

哈尔滨工业大学机器人技术与系统全国重点实验室的**谢晖教授团队**受到这种现象的启发，**开发出了能够模仿鱼群行为的微型机器人集群系统，这些“小鱼”不仅能够像自然界鱼群一样自由游动，未来还能在体内病灶处高效精准递送药物** https://mp.weixin.qq.com/s/VD9dYnjn-J_UydR6kKrU9w

【Science Advances】伊利诺伊大学团队：一种可自主游动的活体肌肉驱动软体机器人 https://mp.weixin.qq.com/s/c_HJR5wGaG1SvGze_OooHQ?click_id=15

**Nature**论文：Ultrasound-driven programmable artificial muscles 一种基于声学共振微气泡阵列的新型人造肌肉，它通过播放**特定频率**序列的超声波，选择性激活材料上不同尺寸的微气泡，利用气泡振动产生的局部微推力，实现了对柔性材料复杂多模态变形的无线编程控制 苏黎世联邦理工学院Daniel Ahmed团队

Nature Communications-用于低磁场下可编程大形变的磁活性双稳态软执行器 Magnetoactive bistable soft actuators for programmable large shape transformations at low magnetic fields 华中科技大学 将双稳态结构引入磁驱动系统可能带来重要突破。双稳态是指结构在经历机械失稳后能够保持两种稳定状态的能力，已成为提升软执行器性能与功能的有效策略。通过利用双稳态机制，可在无需持续外部驱动的情况下实现力放大、高速运动和形态保持。当与磁性材料及相应磁驱动策略结合时，这些双稳态元件可协同实现非接触驱动与机械稳定性，进一步提升系统可靠性、能效和可控性。这一新兴领域近年来研究活跃，已涌现出包括梁形、穹顶形、折纸与剪纸结构在内的多种磁活性双稳态结构，涵盖从一维到三维的构型。其中，穹顶形结构作为典型的三维双稳态构型，因其结构简单且能实现比平面结构更显著的体积变化，在磁活性软执行器中具有应用前景



### 相关资源

公众号：

机器人与仿生工程

柔性电子

Soft Electronics

先进感知

新一代柔性电子



## 微创手术机器人



### 相关论文

https://mp.weixin.qq.com/s/6-9GK0P4IZOdPMoBb4d1Vg?click_id=21 苏州大学机电工程学院和江苏省具身智能机器人技术重点实验室刘会聪教授团队

**瑞士洛桑联邦理工学院（EPFL）的研究团队给出了一个绝妙的答案：****与其“推”它进去，不如让血流“带”它进去。** 他们研发出了一种名为 **“MagFlow”** 的血流驱动磁控微导管系统 **Pancaldi, L., Özel, E., Gadiri, M. A., Raub, J., Mosimann, P. J., & Sakar, M. S. (2025). Flow-driven magnetic microcatheter for superselective arterial embolization.** *Science Robotics*, *10*(105), eadu4003. https://doi.org/10.1126/scirobotics.adu4003

软体仿人灵巧手是一种由柔性材料构成的机器人末端执行器，可以实现类人的抓取及操作，具有高度的灵巧性、出色的环境适应性和人机交互中的安全性等特点。仿人化设计是灵巧手的主要发展方向之一，通过模仿人手的形状、结构和功能，可以实现部分类人运动，在抓取和操作中具有广泛的适应性和可操作性。软体仿人灵巧手的核心技术包括结构设计、制备工艺以及驱动和控制技术。随着机器人技术的发展，软体仿人灵巧手将成为机器人末端执行器发展的热点方向，在工业自动化、医疗康复和家庭服务等领域得到广泛的应用。

据麦姆斯咨询报道，针对该领域研究，中国石油大学（北京）肖华平副教授团队在《机械传动》期刊上发表了题为“软体仿人灵巧手的研究进展”的综述文章，概述了国内外软体仿人灵巧手在灵巧性和抓取操作能力方面的进展，从驱动方式、材料与制造、建模和控制方面对软体仿人灵巧手的研究现状进行了详细分析，讨论了软体仿人灵巧手所面临的潜在挑战与可能的发展方向，指出提升其灵巧性和触觉感知能力是未来研究的重点方向。

Jianfeng Zhang, Huazhong University of Science and Technology, Science Robotics, 2025(87) 磁控软体微纤维机器人技术（microfiberbots），具备远程操控、高机动性和可逆形变能力，可通过磁场诱导实现血管内精确导航和靶向栓塞。该机器人依托螺旋磁极构型，在血流环境中可实现延展、聚集与螺旋推进，实现对磁性软体微纤维机器人的精准部署，以及对深层靶病灶的精准栓塞



## 人形机器人/具身智能

### 相关论文

https://mp.weixin.qq.com/s/1XK9A5chRFuI2BxxftsiTA 来自东京大学、牛津大学和德克萨斯大学奥斯汀分校的研究人员对机器人领域的视觉-语言-动作 (VLA) 模型进行了全面、全栈式的综述，系统化了它们的演变、架构、训练策略以及实际应用挑战。该综述旨在标准化理解并指导通用机器人系统的发展。

谭建荣 综述 https://mp.weixin.qq.com/s/F_KcKG2uwCQqabqQj50_PQ



benchmark平台：https://robochallenge.ai/home



## 灵巧手

当前，机器人学习精细操作（尤其那种需要精确力度控制的任务）的难点在于缺乏高质量的“示范数据”。

以往的主流方式有两种，一种是遥操，一种是视频学习。前者的操作者缺乏真实“手感”，效率低容易失败；后者通过看人类视频模仿学习，但人与灵巧手之间存在差异，动作不匹配，且同样没有触觉信息。

总的来说，这两种方法都难以收集到包含高保真触觉和力度信息的数据来训练机器人。

在此背景下，团队提出了KineDex解决方案，其核心思想非常直观：**手把手教学**。

硬件配置上，包含配备灵巧手的机械臂。团队采用两台RGB相机采集视觉观测数据：一台固定于工作台前方提供场景全局视图，另一台安装于末端执行器腕部以实现对操作区域的近距离感知。

首先，采集数据。KineDex数据采集系统的核心设计理念是让操作者能够”穿戴”灵巧手自由移动，实时执行需要精细接触的操作任务。为实现这种手把手控制，团队在灵巧手四根手指（非拇指）的背侧安装了个环形绑带。

这样一来，可以确保运动过程中产生的接触力可实时传递至操作者手部，在整个示教过程中提供自然的触觉反馈。

每次演示，都会记录包括视觉观测、本体感知（机械臂末端执行器位姿及灵巧手的关节位置）、触觉传感、指尖力等数据信息。

接下来，处理数据。系统采集到的数据没有办法直接用于视觉运动策略学习，因为摄像头肯定会拍到操作者的手，这会干扰机器人的学习，而之后它自己操作时是没有人手的，因此要是直接使用此类数据训练，将会导致显著的分布外偏移。

因此，团队采用图像修复技术从视觉观测中移除操作者的身体部位。

Dexonomy突破机器人灵巧抓取新极限：该方法的出发点是为每种手型和抓取类型仅需一个由人工标注的抓取模板，通过两阶段流程实现抓取合成：首先对物体位姿进行采样与优化以适配手部模板，随后在物理仿真环境中对手部姿态进行局部微调以进一步贴合物体。为了验证合成抓取的质量，论文提出了一种接触感知的控制策略，使手部能够在每个接触点上施加合适的力

http://byte-dexter.github.io/gr-dexter/



## 微型机器人(磁驱动机器人)

纳米机器人的定义较为宽泛，通常指尺寸小于100纳米、并且具备可控或自主运动能力的微型装置。这种运动能力使它们区别于其他技术，例如纳米载体（nanocarriers）——后者虽然也能运输分子货物，但只能被动完成这一过程。而体积稍大的微型机器人（microrobots），尺寸约为1毫米，通常也依赖某些纳米技术（例如用于驱动的纳米马达），或利用在纳米尺度上发生的物理现象。因此，研究人员常将二者统称为微纳机器人（micro-nanorobots，MNRs）

研究人员越来越多地开始制造称为生物混合机器人（biohybrid robots）的装置，即在细菌、藻类或精子等生物体中引入合成元件。科学家们很快意识到，自然已经设计出了许多有助于纳米机器人成功运作的特性——包括用于运动的鞭毛，以及用于检测梯度的传感器。捷克中欧科技研究院（Central European Institute of Technology）的纳米机器人研究员Martin Pumera说：“细菌本身就有一个‘马达’，配备了我们所知的所有组件。我们现在追求的，是合成世界与生物世界的理想融合

### 相关论文

Multichamber magnetic capsule robot for selective liquidsampling and drug delivery  **为突破****传统****胶囊机器人依赖被动蠕动、难以兼顾诊断与治疗的****瓶颈****，澳门大学的Qingsong Xu团队联合香港大学提出“多腔体磁驱动胶囊机器人（macabot）”构型：通过在单舱模块内嵌柔顺机构磁阀并赋予各阀“定向低刚度—非对称触发”特性，利用梯度磁场按方向选择性开启单一腔体，从而在一枚胶囊内实现“分舱互不串扰”的液体采样与药物释放；**其滚动运动由均匀旋转磁场驱动并与开阀动作解耦。机器人在胃内以“整体模式”精准滚动，在肠段遇高**pH**则通过**pH**敏感胶粘接失效转为“分体模式”，提升狭窄腔道中的可达性与灵活性

蒙特利尔理工学院的Sylvain Martel 操作载药纳米机器人

香港中文大学深圳 在Nature Machine Intelligence发表文章 Active Exploration and Reconstruction of Vascular Networks Using Microrobot Swarms 引入磁性微型机器人集群作为载体，开发了复杂三维腔道的主动探测与结构重建策略，在磁场主动引导下可以实现精准、完整的三维血管网络成像，重建完整血管网络的三维结构，可以大幅提升全场景造影效果，帮助医生精准定位病变血管内的血栓、狭窄部位以及渗漏点，是后续精准介入治疗的技术基础

哈尔滨工业大学常晓丛团队开发出一种温度响应微型机器人（TRM）并结合磁驱动技术、热致变色材料与多层感知机（MLP）回归模型，首次实现复杂受限环境下 160℃~240℃的离线高温定量检测，有望为工业领域内狭小非透明空间的温度监测提供全新技术手段与解决方案。相关研究以 “Temperature-Responsive Microrobot for High-Temperature Sensing in Constrained Environments” 为题，发表在*Research*上。哈尔滨工业大学常晓丛等提出一种温度响应微型机器人(Temperature-Responsive Microrobot, TRM)，它能在狭小曲折的高温区域中运动、感知温度，并能将感知的温度信息带出复杂多孔环境，可以实现160℃-240 ℃的离线温度测量，为复杂受限环境的高温检测提供全新思路，并进行了概念性说明（图1）

Thermal and Magnetic Dual-Responsive Catheter-Assisted Shape Memory Microrobots for Multistage Vascular Embolization。 导管在复杂血管中导航，如急转弯或多次U型转弯，对于血管栓塞术来说仍然具有挑战性。在此，我们提出了一种针对难以到达血管的新型多阶段血管栓塞策略，该策略将未系绳的游泳形状记忆磁性微机器人（SMMs）从先前的导管释放到血管分叉处。SMMs由含磁性颗粒的有机凝胶制成，确保生物相容性、放射不透性、血栓形成以及快速的热磁响应。 SMM 最初是直径0.5毫米的线性形状，在20°C时插入导管中。在38°C的血液温度下，SMMs在2秒内从导管推出进入血液后，会转变为预定的螺旋形状。SMMs能够在狭窄和迂曲的血管中灵活游动，并利用旋转磁场的螺旋推进实现逆流运动。此外，我们在活体兔子中验证了这种多阶段血管栓塞术，完成了100厘米的行程，并在2分钟内实现了肾动脉栓塞。4周后，SMMs仍保持栓塞位置，肾脏体积减少了36% 深圳中科院徐天天团队 《Research》
