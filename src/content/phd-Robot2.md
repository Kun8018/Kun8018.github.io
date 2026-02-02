---
title: 机器人研究(二)
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，
toc: true
thumbnail:
---

<!--more-->

## 具身智能

具身智能指南：https://github.com/TianxingChen/Embodied-AI-Guide

### VLA

**Vision-Language-Action Models for Robotics: A Review Towards Real-World Applications**

感知运动模型目前主要有七种架构变体。

（1）**Transformer + 离散动作Token**：这种架构将图像和语言都表示为token，输入到Transformer中以预测下一个动作，动作通常以离散token的形式呈现（如图4（1）所示）。这类模型还包括使用CLS token并通过MLP生成连续动作的模型。VIMA和Gato是典型示例，它们利用语言tokenizer、视觉Transformer、MLP等组件对多种模态进行token化处理，并输出离散动作（如分箱值）。VIMA采用以多种任务模态为条件的编码-解码结构Transformer，而Gato则使用仅解码器结构的Transformer，以自回归方式处理所有token序列。

与VIMA和Gato采用自回归方式生成动作token不同，RT-1采用了另一种方法：通过TokenLearner对输入进行压缩，并利用仅解码器结构的Transformer以非自回归方式预测所有动作token。在实际应用中，将48个token输入到Transformer中，提取最后11个token作为动作输出。这种架构已被MOO、RT-Sketch和RT-Trajectory等多种方法采用，同时也成为了Robocat、RoboFlamingo等众多VLA模型的常用设计选择，这得益于其简洁性和可扩展性。

（2）**Transformer + 扩散动作头部**：这种架构在（1）的结构基础上，在Transformer之后加入了一个扩散策略作为动作头部。离散动作token通常缺乏实时响应性和平滑性，而这类模型利用扩散模型实现了连续且稳定的动作输出。Octo和NoMAD是典型示例。Octo将图像和语言token作为单个序列通过Transformer处理，然后以读取token为条件，应用扩散动作头部。与之不同的是，NoMAD用目标图像替代了语言输入，通过平均池化对Transformer输出进行压缩，并利用得到的向量对扩散模型进行条件约束。TinyVLA、RoboBERT和VidBot也采用了这种架构。

（3）**扩散Transformer**：如图4（3）所示，扩散Transformer模型将Transformer和扩散动作头部整合在一起，在Transformer内部直接执行扩散过程。这使得模型能够在图像和语言token的直接条件约束下执行扩散过程。例如，基于这种架构构建的RDT-1B，通过与视觉和语言查询的交叉注意力生成动作token序列，然后通过MLP将这些token映射为可执行的机器人动作。此外，大型行为模型（LBMs）也采用了扩散Transformer架构，并强调大规模多样化预训练的重要性。另外，StructDiffusion、MDT、DexGraspVLA、UVA、FP3、PPL、PPI和Dita等也采用了这种架构。

（4）**VLM + 离散动作Token**：如图4（4）所示，VLM + 离散动作Token模型通过用在大规模互联网数据上预训练的视觉-语言模型（VLM）替代（1）中的Transformer，提升了模型的泛化能力。利用VLM能够使这些模型融入人类常识知识，并具备上下文学习能力。例如，RT-2以PaLM-E或PaLI-X等大规模VLM为骨干网络，该骨干网络以图像和语言token为输入，输出下一个动作的离散token。此外，LEO、GR-1、RTH、RoboMamba、QUAR-VLA、OpenVLA、LLARA、ECoT、3D-VLA、RoboUniView和CoVLA等也采用了这种架构。

（5）**VLM + 扩散动作头部**：如图4（5）所示，VLM + 扩散动作头部模型在（2）的基础上，用VLM替代了Transformer。这种架构将能够实现更好泛化的VLM与能够生成平滑连续机器人动作指令的扩散模型相结合。例如，Diffusion-VLA、DexVLA、ChatVLA、ObjectVLA、GO-1（AgiBot World Colosseo）、PointVLA、MoLe-VLA、Fis-VLA和CronusVLA等都采用了这种架构。HybridVLA进一步结合了（4）和（5）的特点，在单个模型中既能够自回归生成离散token，又能够利用扩散动作头部生成连续动作。

（6）**VLM + 流匹配动作头部**：如图4（6）所示，VLM + 流匹配动作头部模型用流匹配动作头部替代了（5）中的扩散模型，在保持平滑连续控制的同时，提升了实时响应性。是典型示例，它以PaliGemma为基础，实现了高达50Hz的控制速率。此外，GraspVLA、OneTwoVLA、Hume和SwitchVLA等也采用了这种架构。整合了（4）和（6）的架构，在一个统一框架中同时支持离散token和流匹配。

（7）**VLM + 扩散Transformer**：如图4（7）所示，VLM + 扩散Transformer模型将VLM与（3）中描述的扩散Transformer相结合。通常，VLM作为高层策略（系统2），而扩散Transformer作为低层策略（系统1）。扩散Transformer既可以采用扩散技术，也可以采用流匹配技术实现。GR00T N1是典型示例，它通过扩散Transformer对VLM token进行交叉注意力处理，并利用流匹配生成连续动作。CogACT、TrackVLA、SmolVLA和MinD等也采用了这种设计。



#### tokenizers

http://github.com/huggingface/tokenizers

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE

tokenizer = Tokenizer(BPE())
```





#### openVLA

https://github.com/openvla/openvla

https://openvla.github.io/



https://github.com/xiaomi-mlab/Orion 小米VLA 框架



#### VLA与VLM



#### TVLA

[视觉-语言-动作模型](https://zhida.zhihu.com/search?content_id=260372831&content_type=Article&match_order=1&q=视觉-语言-动作模型&zhida_source=entity)凭借其强大的语义理解和跨模态泛化能力，已成为通用型机器人代理研发的核心驱动力。这类模型依托预训练的视觉-语言backbone网络，能够解读抽象指令并在多种任务中展现出优异的泛化性能。然而，现有[VLA模型](https://zhida.zhihu.com/search?content_id=260372831&content_type=Article&match_order=1&q=VLA模型&zhida_source=entity)在接触密集型场景中存在显著短板——难以将决策精准扎根于物理现实，尤其在需要精细力控制的交互任务中表现乏力。

造成这一局限的关键原因在于**[触觉感知](https://zhida.zhihu.com/search?content_id=260372831&content_type=Article&match_order=1&q=触觉感知&zhida_source=entity)的缺失**。与视觉和语言提供的高层语义信息不同，触觉感知能传递物理交互中丰富、局部且动态的反馈，如摩擦、柔顺性和材料特性等，这些信息对涉及接触的操作任务至关重要。尽管已有研究尝试将触觉纳入机器人系统，但多将其视为补充性感知模态，未能真正实现与决策过程的深度融合。

针对这一问题，[Tactile-VLA](https://zhida.zhihu.com/search?content_id=260372831&content_type=Article&match_order=1&q=Tactile-VLA&zhida_source=entity)旨在通过深度融合视觉、语言、动作与触觉感知，激活VLA模型中隐含的物理知识，实现接触密集型任务中的精准力控制与泛化能力

https://zhuanlan.zhihu.com/p/1928484559713990654

##### 视触觉发展史

人类对世界的感知有六个维度，眼耳鼻舌身意，对应视觉、听觉、嗅觉、味觉、触觉、意识。我们假设机器人进化的终极能量来源还是电力，而不是食物，那么味觉和嗅觉对机器人来说似乎没什么用。剩下四个维度，视觉、听觉、触觉和意识在机器人领域的进展分别是什么样子？

视觉，硬件以摄像头为主，融合激光雷达等传感器，过去几年自动驾驶和服务机器人的发展让视觉算法有很大发展；听觉，硬件以手机和智能音箱听筒和麦克风器件为主，近些年语音识别的算法也相对成熟；意识，ChatGPT的出现让人们相信，也许AI很快会拥有自我意识。那么触觉呢，相比之下，不管是硬件还是算法目前都非常不成熟。

尽管近些年，学术界也有很多paper证明，加入触觉感知，机器人操作物体的准确性可以提高，但触觉感知依然没有完美的方案，甚至到目前为止都没有成熟的硬件平台。在和一些做操作相关的老师交流时，常常听到视触觉传感器，尤其是GelSight。这篇文章以GelSight学术进展为时间轴，呈现从提出到后面每个时期的关键进展。

触觉传感器模仿生物的皮肤触觉，检测外部物理环境引起的刺激。常见的触觉传感器类型包括压阻式、压电式、光学式、电容式、电磁式和视触觉传感器。我们今天这篇文章探讨的GelSight是视触觉传感器的典型代表，所谓视触觉传感器就是基于视觉的触觉传感器（Vision-Based Tactile Sensor，VBTS）。

人类在操作物体的时候，手部的触觉信息包含两个维度：物体本身的状态和接触的状态；其中物体本身的状态信息包含表面纹理、物体形状、软硬度；接触状态信息包含法向力（垂直人手皮肤）、剪切力（平行人手皮肤）、相对滑动和物体的位姿。对上述触觉信息，大多数触觉传感器比如压阻式、压电式、电容式和电磁式大都只能感受法向力，而对其他信息无法采集或灵敏度很低。VBTS和其他触觉方案相比，最大的优点就是可以高灵敏地感知上述全部信息，非常接近人手的触觉信息维度

基于荧光点的视触觉传感器早在2004年就由东京大学团队提出过（GelForce），但分辨率不高。GelSight是第一个超高分辨率的视触觉传感器，它于2009年由[MIT计算机科学与人工智能实验室](https://zhida.zhihu.com/search?content_id=241824304&content_type=Article&match_order=1&q=MIT计算机科学与人工智能实验室&zhida_source=entity)（CSAIL）的Edward Howard Adelson（简称[Edward Adelson](https://zhida.zhihu.com/search?content_id=241824304&content_type=Article&match_order=1&q=Edward+Adelson&zhida_source=entity)）研究小组提出。GelSight是由“Gel”和“Sight”组成，Gel（凝胶）是传感器弹性接触表面使用的材料；Sight（视觉），传感机制是使用摄像头采集视觉图像。在GelSight触觉传感器接触物体时，内置的摄像头在LED灯光的辅助下捕捉接触物体的凝胶产生的形变，通过计算机视觉的算法将凝胶形变信息与触觉信息进行映射

GelSight触觉传感器结构 （GelSight 2017论文）

从2009年提出至今，Edward Adelson的小组经历了几代人持续十几年的努力，将视触传感器推到了机器人触觉感知领域的最前沿，获得了全球顶尖高校和科研机构的认可和跟随，AI机器人领域的顶级高校MIT、Stanford、CMU、UCB、UIUC、ETH、牛津、清华、北大、上交、港科大、中科院等高校以及Meta、TRI（丰田）等大企业都推出过基于GelSight的各项工作。近些年，基于GelSight的论文也常常获得机器人领域顶会[RSS](https://zhida.zhihu.com/search?content_id=241824304&content_type=Article&match_order=1&q=RSS&zhida_source=entity)、ICRA、IROS的Best Paper。接下来就让我们进入GelSight的历史长河，回顾它从诞生至今15年所经历的重要时刻。

https://zhuanlan.zhihu.com/p/691621404

**GelSight提出——GelSight @ MIT 2009**

提到GelSight视触觉传感器，就绕不开他的发明人MIT教授Edward Adelson。Edward Adelson是一位美国神经科学家，目前是麻省理工学院John and Dorothy Wilson视觉科学教授，同时也美国国家科学院和美国艺术与科学学院的两院院士。他近些年的研究重点是机器人的人工触觉感知，他于2009年在[CVPR](https://zhida.zhihu.com/search?content_id=241824304&content_type=Article&match_order=1&q=CVPR&zhida_source=entity)首次提出GelSight视触觉感知技术（Micah Johnson and Edward Adelson, 2009）。

Edward Adelson 2009年的工作最初只是用于物体表面微观结构的观测，和机器人的触觉感知并没有关系。2009年的GelSight传感器由透明弹性体（一种凝胶状材料）和涂有颜色的外皮组成，可以测量触碰其表面的物体的纹理和几何形状。从背后观察，被按压到传感器上的物体呈现为一个具有均匀反射度的阴影表面。这些特性允许对物体表面进行三维重建。

**GelSight @ MIT 2011**

在2011年[SIGGRAPH](https://zhida.zhihu.com/search?content_id=241824304&content_type=Article&match_order=1&q=SIGGRAPH&zhida_source=entity)，Edward Adelson和团队对GelSight做了进一步优化，提出了一个用于捕捉微观表面几何的系统，将2009年的工作扩展到微观领域，展示了小至 2 微米的空间分辨率（Micah Johnson et al. and Edward Adelson, 2011）。该系统不受被测表面的光学特性的影响 - 无论物体是哑光的、光亮的还是透明的，它都能捕捉相同的几何形状。此外，硬件设计支持多种形式，包括手持设备，可用于在现场捕捉高分辨率表面几何。相较于 Johnson 和 Adelson 2009年提出的原始传感器，2011年的工作改进了传感器材料、照明设计和重建算法，也提出了在表面缺陷检测方面的应用可能。但至此，GelSight和机器人以及触觉依然没有什么关系。

**GelSight进化——GelSight @ MIT 2013**

在2013年的CVPR，Edward Adelson团队首次将GelSight与机器人触觉感知进行结合，构建了一个包含40个触觉纹理类别的数据库，使用了诸如织物、木材和砂纸等材料（Rui Li and Edward Adelson，2013）。这个系统可以正确分类来自该数据库的材料，表明 GelSight 传感器可以帮助机器人进行材料识别

**GelSight @MIT 2014**

在2014年IROS，Adelson团队首次提出了使用 GelSight触觉传感来进行机器人小零件的精确定位和操控（Rui Li et al. and Edward Adelson, 2014）。这也是全球第一个用于机器人的超高分辨率（10微米级别）指尖触觉传感器Fingertip GelSight Sensor，并首次将GelSight用于机器人插拔USB的任务，通过视觉和触觉结合实现类人的精细操作和闭环控制。至此，以GelSight为代表的视触觉用于机器人操作的学术研究正式拉开了帷幕。下图可以看到10年前第一款机器人指尖传感器的结构和外观设计和如今GelSight Mini（后文会介绍）非常类似。

这里值得一提的是，2009-2011年的GelSight设备比较大（最小的也有易拉罐大小）且不够实时，不适合用于机器人的操作。自2011年起，Adelson团队将GelSight应用于机器人场景，在软硬件方面进行诸多改进，使得2014年提出的GelSight指尖触觉传感器同时具备小型化和超高分辨率的特性，且具备实时测量法向力、剪切力、相对滑动等能力。

与基于压电、压阻、磁学等传统触觉传感器相比，GelSight指尖触觉传感器拥有超高分辨率、同时测量法向力和剪切力（很多操作需要剪切力）、软硬度测量（可用于抓取柔性物体），且不受环境磁场和温湿度影响。因此，用同一套GelSight软硬件，可以用于自适应抓取不同硬度、大小、形状的物体，如鸡蛋、薯片、草莓，甚至是树叶（见以下视频），可以根据多维力信息形成闭环控制，非常类似于人类操作物体的方式。这些性能对于通用机器人的通用和泛化操作显得尤为重要

**GelSight @MIT 2015**

在2015年ICRA，Adelson团队首次在GelSight 指尖触觉传感器系统中引入marker点来测量接触表面剪切力、相对滑动和扭转负荷（Wenzhen Yuan et al. and Edward Adelson, 2015）。至此，同时测量接触表面的法向力、剪切力和相对滑动，作为GelSight在机器人触觉领域应用最大的特性之一被普遍认知。可以参看2015年这篇工作的演示视频。

**GelSight加速进化——GelSight @MIT 2017**

2017年发表在Sensors的一篇论文GelSight: High-Resolution Robot Tactile Sensors for Estimating Geometry and Force可以说对GelSight在机器人领域的应用有极大的推动作用（Wenzhen Yuan et al, 2017）。这篇论文回顾了GelSight的发展，重点介绍了传感原理，传感器设计，光学系统的设计，形状、力和滑动测量的算法，以及不同传感器版本的硬件设计和制造，并系统的总结了GelSight在机器人领域应用的软硬件问题。通过对形状和接触力的高分辨率测量，该传感器成功地辅助了机器人进行材料感知和识别等任务。

在2017年IROS，一款用于测量滑动的GelSight触觉传感器被提出（Siyuan Dong et al, 2017）。2017年Sensors和2017年IROS的这两篇论文采用的核心硬件结构设计都如下图所示。2017年的这款硬件结构和2014年的Fingertip GelSight Sensor是之后诸多科研lab里视触觉传感器的参考原型。

2017年-2018年，Adelson团队发表了多篇让机器通过GelSight触觉传感来感知物体和材料的物理特性的论文。在应用GelSight测量物体软硬度方面也有两个工作（Wenzhen Yuan et al. and Edward Adelson, 2017和Wenzhen Yuan and Edward Adelson, 2017），这两项工作开创性地用GelSight指尖触觉传感器，在不需要准确控制接触条件或对象形状的情况下，让机器人可以估算接触物体的硬度。同时用深度卷积（和递归）神经网络对数据进行分析，估算具有不同形状的物体硬度。

在CVPR 2017，Edward Adelson团队的一篇工作（Wenzhen Yuan et al, 2017）使用了118个织物样本的集合，拍摄了垂挂织物的彩色和深度图像，并使用高分辨率触觉传感器获取了触觉数据。然后通过同时训练卷积神经网络（CNN）跨足三个模态来关联视觉和触觉的信息。通过CNN，每个输入，无论模态如何，都生成一个嵌入向量，记录了织物的物理特性。通过比较嵌入向量，这个系统能够查看织物图像并预测其手感，反之亦然。

在上文的基础上，Edward Adelson团队构建了一个机器人系统，通过触觉可以自主感知物体的属性（Wenzhen Yuan et al, 2019）。机器人在外部Kinect传感器的引导下移动，并用GelSight触觉传感器挤压衣物，然后根据触觉数据识别衣物的11种属性。这些属性包括物理属性，如厚度、毛绒度、柔软度和耐久性，以及语义属性，如穿着季节和首选洗涤方法。作者收集了包含153件各种衣物的数据集，对触觉数据应用了卷积神经网络（CNN）来识别衣物的属性。

**GelSlim @MIT 2018**

2018年，Adelson团队在2014年工作的基础上对GelSight指尖传感器进一步小型化，提出了GelSlim指尖传感器。下图左侧的结构是2014年提出的GelSight指尖传感器结构，GelSlim对GelSight内部结构进行调整，将传感器厚度减薄。

**GelSight @ MIT RSS 2020 Best Paper Finalist**

Edward Adelson团队一篇用Gelsight操作绳子的论文获得了2020年RSS这最佳论文入围奖（Yu She et al, 2020），作者依靠GelSight，来估计握持电缆时的姿态以及电缆滑动过程中的摩擦力。将跟随电缆的行为分解为两个基于触觉的控制器：电缆握持控制器和电缆姿态控制器。

**GelSight @ MIT IROS 2020 Best Paper**

2020年IROS，Edward Adelson团队和上海交通大学学者一篇Swingbot获得最佳论文（Chen Wang et al. and Edward Adelson, 2020）。SwingBot通过手持触觉传感器，探索物体物理特征以实现动态摆动操纵。通过使用重力或手臂加速度来操纵物体，从而增加了质量、质心和摩擦信息的重要性。

**GelSight Wedge @ MIT 2021**

2021年，Edward Adelson团队提出了GelSight Wedge传感器（Shaoxiong Wang et al. and Edward Adelson，2021），它具有紧凑的形状适用于机器人手指，同时实现高分辨率的3D重建。

**GelSlim 3.0 @ MIT 2022**

在2018年GelSlim的基础上，Alberto Rodriguez团队进一步推出GelSlim 3.0，在更紧凑的形状中感知高分辨率、压力和滑动。为了实现紧凑的集成，作者优化了从照明源到相机的光学路径。

**GelSight Baby Fin Ray @MIT 2023**

2023年，Ted Adelson团队推出了GelSight Fin Ray，将触觉感知整合到软性柔顺机器人中（如Fin Ray手指)。过往的GelSight手指传感器都是刚性的，外界接触仅会引起凝胶形变而不会引起刚性手指形变。而对Fin Ray这类柔性手指，GelSight触觉传感器的挑战在于在柔性手指也会形变的同时测量凝胶形变。

**GelSight EndoFlex @ MIT 2023**

除了夹爪外，Edward Adelson团队也推出了多指的灵巧手版本，GelSight EndoFlex是一种新颖的三指机器人手，其整个手指长度都具有高分辨率的触觉感知。手指是柔软的，由柔软的外壳和灵活的内骨架支撑构建而成。每个手指包含两个摄像头，允许沿手指的前侧和侧面收集触觉数据。该夹爪可以对物体进行包围式抓取，并在单次抓取中提取大量丰富的触觉数据。

**GelSight Svelte @ MIT 2023 IROS Best Paper**

2023年，Edward Adelson团队提出了一种曲线状、人手指大小、单摄像头触觉传感器GelSight Svelte（Jialiang Zhao and Edward Adelson，2023），能够在大范围内进行触觉和本体感知。GelSight Svelte利用弯曲的镜子来实现所需的形状和感知覆盖范围。本体感知信息，如作用在手指上的总弯曲和扭矩，表现为GelSight Svelte柔软主体上的变形，这些变形也被摄像头捕捉到。作者训练了一个卷积神经网络（CNN），以从捕捉到的图像中估计弯曲和扭矩。这篇文章也获得了2023年IROS Best Paper。

基于手指形状的视触觉传感器GelSight Svelte，作者进一步提出了一种新型的3指2自由度触觉机械手GelSight Svelte Hand。每个手指都配备有一个摄像头，能够获取丰富的触觉信号，其感知区域类似于人手指的全长。GelSight Svelte Hand的每个手指都由半刚性内骨架支撑，并覆盖有柔软的硅胶材料，既提供了刚度又具有一定的柔韧性。

**GelSight 360 @ MIT 2023**

过往的GelSight触觉传感器只能进行单侧感知，Edward Adelson团队于2023年推出了GelSight360，这是一种类似指尖的全向触觉传感器。这个传感器引入了一种新颖的十字LED照明方案。

除了Edward Adelson自己组里关于GelSight的一系列工作，他的团队也和其他团队合作产生了一系列基于GelSight触觉感知的机器人操作工作。比较有代表性的是MIT Tao Chen的两个工作，我们在之前的文章[将机器人的手牵向人类的手：灵巧操作华人论文综述](https://link.zhihu.com/?target=http%3A//mp.weixin.qq.com/s%3F__biz%3DMzkwNDMxMzg1NA%3D%3D%26mid%3D2247484443%26idx%3D1%26sn%3Da4b94a683a41af277a57a6b8c49691c1%26chksm%3Dc089aa8af7fe239c2177db432b8e7fc65d5e6bc21dbe1563b024e4d5a99ebd9246669c7585e7%26scene%3D21%23wechat_redirect)也引用过。

比如TactoFind：在视觉感知缺失、对象形状事先未知且对象可以自由移动的场景中进行对象检索的问题，例如从抽屉中取出物体。



### 世界模型

世界模型能够根据当前输入预测未来观测或潜在表征。其前向预测能力在VLA系统中变得越来越重要，可支持规划、推理和控制功能

（1）**在世界模型中生成动作**：与直接生成动作的模型不同，世界模型会生成未来的视觉观测（如图像或视频序列），然后利用这些观测来指导动作生成。例如，UniPi采用受Video UNet启发的扩散模型，根据初始观测图像和任务指令生成视频序列。随后，逆动力学模型（IDM）将预测的图像序列转换为低层动作。这种视觉预测与基于IDM的控制相结合的方式，是基于模型的VLA中常见的设计模式。类似地，DreamGen和GeVRM通过预测未来的视觉表征来生成动作。HiP通过整合LLM进行子任务分解，进一步扩展了这一思路，能够实现更长周期行为的执行。Dreamitate对Stable Video Diffusion进行微调，以合成人类使用工具完成操作任务的视频。然后，根据生成的视频，利用MegaPose估计工具的6自由度位姿，使机器人能够遵循估计的工具位姿进行操作。与生成完整视频序列不同，SuSIE利用InstructPix2Pix根据初始观测和任务指令生成中间目标图像，从而预测抽象的子目标图像，并将其作为扩散策略的条件约束。CoT-VLA采用类似方法实现思维链推理（详见第4.1节）。LUMOS也会生成目标图像，但其生成过程是通过一个以低层动作指令为输入的世界模型实现的。在LUMOS中，通过与学习到的世界模型交互，训练策略以模仿专家演示。

除了视频和图像生成，近年来许多研究还利用光流或特征点跟踪技术。由于光流和特征跟踪与机器人实体无关，因此为利用人类演示数据提供了更具泛化性的方法。与UniPi类似，AVDC生成视频序列，并利用GMFlow计算每一帧的光流。然后，将目标物体的SE(3)刚体变换估计问题转化为一个优化问题。ATM在训练过程中利用CoTracker预测任意特征点的未来轨迹，并训练一个Transformer，在这些轨迹的指导下生成动作。Track2Act预测初始图像和目标图像之间的特征点轨迹，优化3D刚体变换，并学习残差策略以细化运动。LangToMo根据初始图像和任务指令预测未来光流，利用RAFT进行光流监督，并将该预测映射为机器人动作。MinD采用端到端方法，联合学习视频预测和动作预测。具体而言，MinD将一个低频视频生成器（根据初始图像和指令在潜在空间中预测未来视觉观测）与DiffMatcher（将这些预测转换为时序特征，供高频动作策略用于高效生成动作序列）相结合。PPI接收视觉和语言输入，预测每个关键帧的抓手位姿和物体位移（Pointflow），并将其作为动作生成的中间条件

（2）**通过世界模型生成潜在动作**：这类VLA模型利用世界模型从人类演示中学习潜在动作表征。例如，LAPA（从视频中进行潜在动作预训练）（详见第3节）通过联合学习从当前和未来图像对中预测动作表征，以及根据当前图像和潜在动作生成未来帧，实现了在无显式动作标签的数据集（如人类视频）上的训练。一旦学习到潜在动作，就可以利用这些token训练VLA策略。随后，替换动作头部并进行微调，以输出机器人动作。LAPA已被用于GR00T N1和DreamGen的预训练。此外，GO-1和Moto也采用了类似方法。UniVLA通过语言输入扩展了DINOv2的潜在空间，并采用两阶段训练过程，分离与任务无关和与任务相关的潜在动作token。UniSkill采用基于图像编辑的方法，从RGB-D图像中提取潜在动作，并将其作为扩散策略的条件。

（3）**包含隐式世界模型的感知运动模型**：这类VLA模型同时输出动作和未来观测预测，以提升性能。GR-1整合了预训练的MAE-ViT编码器、CLIP文本编码器和一个Transformer，并在Ego4D数据集上进行训练以预测未来观测图像。随后，通过微调使模型能够根据图像、语言和本体感知输入，同时预测动作和未来帧。通过在标准VLA框架中融入类似视频预测模型的观测预测功能，GR-1在任务成功率方面表现出了提升。GR-2在GR-1的基础上，通过扩大训练数据集和引入架构改进（包括基于VQGAN的图像token化和用于动作生成的条件变分自编码器（VAE）），进一步提升了性能。GR-MG利用基于InstructPix2Pix的模型生成中间目标图像，并将其嵌入到GR-1风格的框架中。此外，GR-3通过整合VLM（Qwen2.5-VL）和带有流匹配功能的扩散Transformer以生成动作，实现了分层结构。3D-VLA通过扩展这一研究方向，利用Stable Diffusion预测RGB-D图像，并利用Point-E预测点云。此外，FLARE、UVA、WorldVLA和ViSA-Flow等多种模型也在感知运动模型中融入了完整的视频预测功能

https://github.com/nvidia-cosmos

https://zhuanlan.zhihu.com/p/19751920966

https://wm-po.github.io/



### 相关论文

https://mp.weixin.qq.com/s/1XK9A5chRFuI2BxxftsiTA 来自东京大学、牛津大学和德克萨斯大学奥斯汀分校的研究人员对机器人领域的视觉-语言-动作 (VLA) 模型进行了全面、全栈式的综述，系统化了它们的演变、架构、训练策略以及实际应用挑战。该综述旨在标准化理解并指导通用机器人系统的发展。https://vla-survey.github.io/

https://serl-robot.github.io/ uc 伯克利 icra论文

https://shengliangd.github.io/StereoVLA-Webpage/



### Franka

#### 控制

https://github.com/facebookresearch/polymetis

https://github.com/iamlab-cmu/frankapy

https://github.com/frankarobotics/franka_ros

https://github.com/frankarobotics/libfranka

https://github.com/TimSchneider42/franky. https://timschneider42.github.io/franky/



数据集：

RSS 2024: https://droid-dataset.github.io/

 https://huggingface.co/KarlP/droid



社区：https://franka.de/community



https://github.com/franzesegiovanni



人机控制franka： https://github.com/franzesegiovanni/franka_human_friendly_controllers?tab=readme-ov-file

### leRobot



#### 可视化

https://github.com/huggingface/lerobot-dataset-visualizer



#### 录制

lerobot record是关键核心流程，其包括了数据的采集和模型推理两部分。

如果是数据采集模式，命令启动如下

```shell
python -m lerobot.record \
    --robot.disable_torque_on_disconnect=true \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0 \
    --robot.id=R12252801 \
    --robot.cameras="{ handeye: {type: opencv, index_or_path: 6, width: 640, height: 480, fps: 30}, fixed: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30}}" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1 \
    --teleop.id=R07252608 \
    --dataset.repo_id=${HF_USER}/record-07271148\
    --dataset.num_episodes=10 \
    --dataset.reset_time_s=5 \
    --dataset.push_to_hub=false \
    --dataset.single_task="Grab the cube" \
    --display_data=true
```

如果是模型推理模式，则命令如下

```shell
python -m lerobot.record  \
  --robot.type=so101_follower \
  --robot.disable_torque_on_disconnect=true \
  --robot.port=/dev/ttyACM0 \
--robot.cameras="{ handeye: {type: opencv, index_or_path: 6, width: 640, height: 480, fps: 30}, fixed: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30}}" \
  --robot.id=R12252801 \
  --display_data=false \
  --dataset.single_task="Put brick into the box" \
  --policy.path=outputs/weigh_07280842/pretrained_model \
  --dataset.episode_time_s=240  \
  --dataset.repo_id=${HF_USER}/eval_so101_07271148
```

主要的区别是如果是采集模式需要使用–teleop参数启动遥控机器，如果是模型推理模式则不需要启动遥控机器，但是需要指定模型路径–policy.path，本质上就是机器人的动作指令来源于哪里，要么来之遥控器的，要么来自模型推理出来的。

可以看出整个录制流程主要围绕机器设备、遥控设备、模型、数据集四个要素进行展开。

- 机器：有SO101Follower、LeKiwi等机器，都继承Robot类。通过命令行参数robot.type调用make_robot_from_config函数选择创建具体的实例设备，函数返回的还是Robot但是指向的是具体的机器实例如SO101Follower，利用了多态的特性做到了解耦，如果要新添机器时，只需要参考SO101Follower添加一个新的设备即可。在创建机器实例时传递RobotConfig参数，这个参数依旧是抽象基类，其继承了draccus.ChoiceRregistry，通过命令行参数robot.type选择注册具体的配置如SO101FollowerConfig。
- 遥控：用于控制机器，常用于数据的采集。这里同样通过命令行参数teleop.type调用make_teleoperator_from_config函数选择创建具体的设备实例，创建实例时需要传递TeleoperatorConfig参数，其也是一个抽象基类，基于命令选择注册实例化的配置类参数，如SO101LeaderConfig。
- 模型：模型用于决策推理动作，其和遥控二选一，如果指定了遥控了，模型就不需要指定了。通用使用了机器、遥控的解耦机制，具体的实例化为ACT或DiffusionPolicy等。
- 数据：通过参数dataset.xxx将参数构建为DataRecordConfig类，然后将其中的信息传递给LeRobotDataset



RecordConfig中有几个关键的成员，分别是RobotConfig，DatasetRrcordConfig、TeleoperatorConfig、PreTrainedConfig。其中除了DatasetRrcordConfig外的其他几个都是继承draccus.ChoiceRegistry 和 abc.ABC，是一个抽象基类，需通过注册的子类（如特定机器人型号的配置类）实例化，种设计既保证了配置的结构化（继承 abc.ABC），又支持灵活的子类选择（通过 draccus.ChoiceRegistry 实现配置注册与解析）。

- RobotConfig 控制硬件接口，确保机器人正确连接和数据采集；
- DatasetRecordConfig 控制数据存储，定义数据集格式和元信息；
- TeleoperatorConfig 和 PreTrainedConfig 控制机器人行为，分别对应手动和自动控制模式。

RobotConfig 是抽象基类（ABC），仅定义所有机器共有的通用配置字段，如id、calibration_dir。其继承了draccus.ChoiceRegistry实现了不同机器人型号的动态注册与选择

DatasetRecordConfig 是数据集录制任务的参数容器，被嵌套在 RecordConfig 中（作为 RecordConfig.dataset 字段），最终通过 parser.wrap() 装饰器从命令行参数解析生成实例

TeleoperatorConfig 是远程遥控操作器（如手柄、键盘、 leader 机器人）的抽象配置基类，用于定义所有遥操作器共有的通用配置字段和动态选择机制。它与RobotConfig类似，同样继承了draccus.ChoiceRegistry 实现了注册机制，其具体的子类又继承TeleoperatorConfig

用户通过-teleop.type指定来实例化具体的操作设备，如–teleop.type=so101_leader时，具体的流程如下。

- 框架通过 draccus.ChoiceRegistry 查找注册名称为 so101_leader 的子类（如 SO101LeaderConfig）；
- 实例化该子类，接收命令行参数（如 –teleop.port=/dev/ttyACM0）并初始化特有字段（如 port）；
- 最终通过 TeleoperatorConfig 基类引用（如 cfg.teleop）传入 make_teleoperator_from_config 函数，创建具体遥操作器实例

PreTrainedConfig是所有策略模型如ACT,TDMPC的抽象配置类，定义了策略训练/推理所需的通用参数，特征规范、设备配置及Hugging Face Hub交互机制。它通过 dataclass、draccus.ChoiceRegistry 和 abc.ABC 实现“配置标准化”“多策略兼容”和“Hub 集成”，是策略初始化的核心参数载体。

- draccus.ChoiceRegistry：通用跟前面的RobotConfig类似，支持动态子类注册，允许子类（如 SACConfig、TDMPCConfig）作为“策略选项”注册，支持通过 –policy.type=sac 动态选择。
- HubMixin：Hugging Face Hub 交互混入类，提供 from_pretrained（从 Hub/本地加载配置）和 _save_pretrained（保存配置到 Hub/本地）方法，实现策略配置的共享与复用。
- abc.ABC：抽象基类，包含未实现的抽象方法（如 get_optimizer_preset），强制子类必须实现核心逻辑，确保策略配置的完整性。

https://www.laumy.tech/2332.html/lerobot%e6%95%b0%e6%8d%ae%e9%87%87%e9%9b%86%e4%b8%8e%e6%a8%a1%e5%9e%8b%e6%b5%8b%e8%af%95/

#### 学习率调度器

学习率调度器（Learning Rate Scheduler）是深度学习训练中动态调整**优化器学习率的工具**（注意是在优化器的基础上动态调整学习率），通过优化收敛过程提升模型性能。

学习率（η）控制梯度更新步长，参数更新量 = -η × 梯度。过大步长导致震荡，过小则陷入局部最优或训练缓慢，固定学习率易导致训练初期震荡或后期收敛缓慢，调度器在训练期间自适应调整初期较高学习率加速收敛，中期稳定探索最优解，后期精细调优避免震荡。

以下是常见的学习率调度器

- StepLR：每隔固定epoch将学习率乘以衰减因子（如step_size=10, gamma=0.5）。
- ExponentialLR：每epoch按指数衰减（lr = lr × gamma）。
- CosineAnnealingLR：按余弦曲线周期下降：η = η_min + 0.5×(η_max – η_min)×(1 + cos(π×t/T_max))。
- OneCycleLR：分两阶段：线性升温至峰值，再退火至极小值。
- PowerLR：基于幂律关系调整，与批次大小和token数量无关。

LRSchedulerConfig是学习率调度器配置的核心抽象，通过抽象接口定义+配置注册机制，实现了学习率调度策略的统一管理与灵活扩展。

其同样继承了abc.ABC标记为抽象基类，强制子类事项build抽象方法，同时继承draccus.ChoiceRegistry提供子类注册机制，通过 @LRSchedulerConfig.register_subclass(“名称”) 将子类与调度器类型绑定（如 “diffuser” → DiffuserSchedulerConfig），支持配置驱动的动态实例化。同时使用了@dataclass 装饰器自动生成构造函数、**repr** 等方法，简化调度器超参数的定义与管理。

其核心属性只有一个num_warmup_steps表示学习率预热步数。预热是深度学习训练的常见技巧（尤其在 Transformer 等模型中），将其作为基类字段可避免子类重复定义。也是几乎所有学习率调度器的基础参数，用于控制“预热阶段”（学习率从低到高线性增长的步数），避免训练初期因高学习率导致的不稳定。

其只有两个方法type和build，type是用于获取子类注册调度器类型名称进而匹配到对应子类，而build的方法是强制子类实现。

lerobot的学习率调度实现了3个DiffuserSchedulerConfig、VQBeTSchedulerConfig、CosineDecayWithWarmupSchedulerConfig子类

在lerobot中，启动训练是可以通过参数来实例化使用哪些调度器，如下。

- –scheduler.type=diffuser指定使用diffuser调度类。
- –scheduler.num_warmup_steps=1000指定预热步数。
- –scheduler.num_warmup_steps=0.5指定余弦衰减周期。

#### 策略优化器

优化器的就是更新计算参数的，根据损失函数的梯度方向调整模型权重和偏置值，公式为：新参数 = 旧参数 – 学习率 × 梯度。通过迭代逐步逼近最优解。在文章https://www.laumy.tech/2050.html我们已经探讨过常用的优化算法。

接下来我们再来从PyTorch使用的角度复习一下。torch.optim 是 PyTorch 官方优化器模块，提供了 SGD、Adam、AdamW 等主流优化算法的实现，所有的优化器都继承自基类 torch.optim.Optimizer。其核心作用是如下：

- 自动化参数更新：根据反向传播计算的梯度，按特定优化策略（如 Adam 的自适应学习率）更新模型参数。
- 统一接口抽象：通过 Optimizer 基类封装不同算法，提供一致的使用流程（zero_grad() 清空梯度 → step() 更新参数）。

在Pytorch中优化器统一封装成torch.optim接口调用，可以有以下优势。

- 避免重复实现复杂算法：无需手动编写 Adam 的动量、二阶矩估计等逻辑，直接调用成熟接口。
- 灵活支持训练需求：支持单/多参数组优化（如不同模块用不同学习率）、学习率调度、梯度清零等核心训练逻辑。
- 工程化与可维护性：通过统一接口管理超参数（lr、weight_decay），便于实验对比与代码复用。



```python
import torch
from torch import nn, optim

# 1. 定义模型（示例：简单线性层）
model = nn.Linear(in_features=10, out_features=2)

# 2. 初始化优化器：传入模型参数 + 超参数
optimizer = optim.Adam(
    params=model.parameters(),  # 待优化参数（PyTorch 参数迭代器）
    lr=1e-3,                    # 学习率（核心超参数）
    betas=(0.9, 0.999),         # Adam 动量参数（控制历史梯度影响）
    weight_decay=0.01           # 权重衰减（L2 正则化，可选）
)

# 模拟输入数据（batch_size=32，特征维度=10）
inputs = torch.randn(32, 10)
targets = torch.randn(32, 2)  # 模拟目标值

# 训练循环
for epoch in range(10):
    # ① 清空过往梯度（必须！否则梯度累积导致更新异常）
    optimizer.zero_grad()

    # ② 前向传播 + 计算损失
    outputs = model(inputs)
    loss = nn.MSELoss()(outputs, targets)  # 均方误差损失

    # ③ 反向传播计算梯度 + 优化器更新参数
    loss.backward()  # 自动计算所有可训练参数的梯度
    optimizer.step()  # 根据梯度更新参数

    print(f"Epoch {epoch}, Loss: {loss.item():.4f}")
    
# 定义参数组（不同模块用不同学习率）
optimizer = optim.Adam([
    {
        "params": model.backbone.parameters(),  # backbone 参数
        "lr": 1e-5  # 小学习率微调
    },
    {
        "params": model.head.parameters(),       # 任务头参数
        "lr": 1e-3  # 大学习率更新
    }
], betas=(0.9, 0.999))  # 公共超参数（所有组共享）
```

loss是损失，通过调用loss.backward()进行反向传播Pytorch就自动把梯度计算好保存了，然后调用关键的一部optimizer.step()就可以执行参数更新了（即新参数=旧参数-学习率*梯度），需要注意的是，在调用loss.backward()进行反向传播计算梯度时，要先调用optimizer.zero_grad()把之前的梯度值情况，因此每计算一次梯度都是被保存，不情况会导致梯度累积



#### ACT算法

https://zhuanlan.zhihu.com/p/1921873980442280840

传统的机器算法过程是观测关节位置J1经过模型预测动作A2然后执行，观测到J2预测数A3，观测到J3遇到A4依次类推，这样就有一个问题，假设预测出的A2跟实际相比偏差就比较大那么对应的观测到的J2就偏离比较大。如果要连续预测K步，就要连续采集K步，缺点就是误差会累积同时预测效率也比较低。那么对于ACT算法是怎么进行优化的了

ACT算法是一下观测连续的K个动作，然后预测出K个动作，这样相对于传统算法效率就提升了K倍。同时也可以解决累积误差，计时K个连续的动作中，有某个动作偏差比较大，但是整体经过模型就会弱化不至于累积。假设K是10 ，简单举个例子理解过程，T0时刻观测到J1数据（开始时只有一个数据），模型直接预测数10个动作序列，等机器按顺序依次执行完这10个动作后，模型下一次就直接把这10个动作当做输入然后预测下一批的10个动作，依次类推

基于transformer的动作分块（ACT）架构。分为训练模式和测试模式。

当为训练模式是，ACT为左图的编码器+右图的解码器。左图可以理解为一个CVAE的编码器，将关节序列、动作序列、CLS经过transformer编码压缩为风格变量Z。然后将Z再加上采集的摄像头数据、关节序列作为输入给到右边的解码器最终输出动作序列。

当为测试模式时，左图丢弃不再，只需要使用右图的部分，输入为摄像头数据、关节序列、Z（被简单设置为0，表示先验的平均值）。可以这么理解Z为CVAE模型中的风格，经过训练后，Z已经让模型的参数定型，在后续的测试过程中就不需要了，因为参数已经固定了就不用了。

动作分块Action Chunking机制，传统机器人每执行一步都要重新观测环境（如拍摄一张照片），走一步采集一步预测一步，而ACT采用的是”分块执行”策略。

具体就是累积到每K步观测一次（如K=100），然后一次性输出K个动作序列，执行这就可以按顺序执行这组K个动作序列。

动作分块也可以理解为决策频率进行了压缩，传统的单步策略需每一步观测环境并生成动作（如T次决策），而ACT是每K步观测一次，一次性生成后续K个动作序列（决策点将至T/K个），例如若K=10,1000步的任务仅需100次决策，效率提升了10倍

将各个动作组合在一起并作为一个单元的执行，从而使起存储和执行更有效率，直观地讲一组动作可以对应抓住糖果包装纸的一角或将电池插入插槽，在实现中将块大小固定为K，每K步agent会收到一次观测然后预测生成K个动作然后机器按顺序执行。如上图所示假设K为4，t=0时刻策略观测到4个动作，然后就会生成4个动作序列，让机器按顺序执行；紧接着到t=4这个时刻，策略又观测到4个动作，生成4个预测动作机器按这个4个动作顺序执行。

#### ACT实现

```python
@PreTrainedConfig.register_subclass("act")
@dataclass
class ACTConfig(PreTrainedConfig):
    # 输入/输出结构
    chunk_size: int = 100  # 动作块长度（每次预测的动作序列长度）
    n_action_steps: int = 100  # 每次策略调用执行的动作步数（≤ chunk_size）
    temporal_ensemble_coeff: float | None = None  # 时序集成系数（None表示禁用）

    # VAE配置
    use_vae: bool = True  # 是否启用VAE（增强动作多样性）
    latent_dim: int = 32  # 潜在空间维度
    kl_weight: float = 10.0  # KL散度损失权重

    # Transformer配置
    dim_model: int = 512  # Transformer隐藏维度
    n_heads: int = 8  # 注意力头数
    n_encoder_layers: int = 4  # 编码器层数
    n_decoder_layers: int = 1  # 解码器层数（原始实现bug，仅用第1层）

    # 视觉Backbone
    vision_backbone: str = "resnet18"  # 图像特征提取网络
    ......
```



#### anytoLerobot

浙大博士生

https://github.com/Tavish9/any4lerobot



### Aloha

https://zhuanlan.zhihu.com/p/707610493



### openpi

https://github.com/Physical-Intelligence/openpi



### robosuite

🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟

https://github.com/ARISE-Initiative/robosuite



#### libero

https://zhuanlan.zhihu.com/p/658683732

https://github.com/Lifelong-Robot-Learning/LIBERO

Libero搭建了一个全新的机器人持续模仿学习环境LIBERO，针对机器人持续学习问题中持续学习算法、网络架构、任务顺序、任务空间/目标/物体变化、语言嵌入、预训练等众多因素耦合导致的难以分析的问题，我们提出使用程序化生成的环境（Procedurally Generated Environments）来解耦地分析各个因素在机器人持续学习训练时带来的影响，为之后的领域内工作提供一个较为全面的实验环境



### robomimic

🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟🌟

https://github.com/ARISE-Initiative/robomimic



### RT1/RT2/RTX

https://robotics-transformer-x.github.io/



### GR1/GR2/GR3/GR-Dexter

https://zhuanlan.zhihu.com/p/1005149740

https://gr2-manipulation.github.io/

https://byte-dexter.github.io/gr-dexter/



### PI-zero

https://www.physicalintelligence.company/blog/pi0

**利用在互联网数据训练的VLM+[action expert](https://zhida.zhihu.com/search?content_id=257894023&content_type=Article&match_order=1&q=action+expert&zhida_source=entity) 组成一个VLA模型，这里结合开源+内部的机器人数据训练得到异构本体foundation model，然后可以在不同的本体/特定的任务上post-training，以完成多任务的泛化或某种复杂任务的灵巧操作**

Pre-training：这里有面向于具身智能的专用数据集，如open x-embodiment dataset和 pi cross-embodiment robot datasets，使用这些数据进行训练，已经包含了大量场景的机器人操作数据（论文把这一步也叫pre-training,其实也没问题），这个阶段后会得到一个foundation model，也就是一个可以统一所有任务/本体的基础模型，这样的模型具备初步的泛化性，但不一定专门用于在任何一项操作任务上实现高性能。

Post-training（fine-tuning）：根据上一个阶段的foundation model，进行ft, 这里分为两类任务的post-traing数据，以提高模型在某种任务表现的专门数据，包含unseen tasks（未见任务）、high dexterity task （高灵巧任务），包括20 多项任务

VLM：视觉语言大模型，这里用的[PaliGemma](https://zhida.zhihu.com/search?content_id=257894023&content_type=Article&match_order=1&q=PaliGemma&zhida_source=entity)，这里主要是指代在使用大量互联网文本图像数据上去预训练VLM

PaliGemma： 2024 年 Google I/O 活动上发布（今年2025I/O刚举办大家感兴趣也可以去看看），它是一种基于两个模型的组合多模态模型：**视觉模型 SigLIP 和大型语言模型 Gemma**，这意味着该模型是 [Transformer 解码器](https://zhida.zhihu.com/search?content_id=257894023&content_type=Article&match_order=1&q=Transformer+解码器&zhida_source=entity)和 Vision Transformer 图像编码器的组合。它将图像和文本作为输入，并生成文本作为输出，支持多种语言。

Action Expert：接受VLM输出，专门输出action的网络，这里使用的flow matching，是VLA中 action的重要组成部分。

Open X-Embodiment dataset :是一个由 DeepMind 创建并开源的超大规模机器人数据集，汇集了来自 22 种不同机器人类型的数据. RT-2也这个数据集上训练的，简称OXE dataset

Pi cross-embodiment robot datasets：pi0公司自己采集的本体数据集，一共7 种不同机器人配置，和 68 个任务的不同数据，长度为1w个小时。cross-embodiment robot 也可以理解为异构本体，不同的机器人类型具有**不同的配置空间和动作表示**，包括**固定基座的单臂和双臂**系统，以及**移动机械手**。自动驾驶中也有类似的事情，不同相机安装角度、型号、个数都算是某种程度的异构。（**在具身智能领域有一个专门的词汇即“通用数据”**）



https://zhuanlan.zhihu.com/p/1907535034941965833

https://zhuanlan.zhihu.com/p/1910755399646287695

https://zhuanlan.zhihu.com/c_1907131586568251035



https://io-ai.tech/platform/guides/Pipeline/LeRobot/Pi0/

https://modelers.csdn.net/680e0968a5baf817cf496e7f.html

https://zhuanlan.zhihu.com/p/1919100548071788918



### 遥操作与模仿学习

https://zhuanlan.zhihu.com/p/1897200096430518846



https://github.com/tonyzhaozh/aloha

#### gello

https://github.com/wuphilipp/gello_software

https://zhuanlan.zhihu.com/p/720266031



### 睿尔曼

#### 开发文档

https://github.com/RealManRobot/ros2_rm_robot

https://develop.realman-robotics.com/robot4th/ros2/gazebo/



#### 数据集

https://huggingface.co/datasets/RealSourceData/RealSource-World



### wandb

https://github.com/wandb/wandb



### rerun

https://github.com/rerun-io/rerun

https://rerun.io/



### lance

https://github.com/lance-format/lance

https://lance.org/



### SMOLVLA

https://huggingface.co/blog/smolvla

https://huggingface.co/lerobot/smolvla_base

https://smolvla.net/index_en

https://www.laumy.tech/2743.html/lerobot-smolvla%e7%ad%96%e7%95%a5/

https://www.laumy.tech/2780.html/smolvla%e7%ad%96%e7%95%a5server%e4%b8%8eclient%e5%88%86%e7%a6%bb%e9%83%a8%e7%bd%b2%e5%ae%9e%e8%b7%b5/







### 长学习

https://www.nature.com/articles/s42256-025-00983-2

