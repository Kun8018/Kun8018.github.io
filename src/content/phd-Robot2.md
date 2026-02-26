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

https://github.com/JiuTian-VL/Large-VLM-based-VLA-for-Robotic-Manipulation?tab=readme-ov-file

过去，机器人操作的研究总在 “模块化陷阱” 里打转：视觉识别、语言解析、动作控制各成一派，像被割裂的齿轮，很难协同运转。直到大型 VLMs 的出现 —— 这些在海量图文数据上 “吃透” 视觉与语言关联的模型，不仅能看懂图片里的细节、理解人类指令的深意，还能把这种 “理解” 转化为机器人能执行的动作逻辑。于是，基于大型 VLM 的 [VLA 模型](https://zhida.zhihu.com/search?content_id=262266538&content_type=Article&match_order=1&q=VLA+模型&zhida_source=entity)应运而生：它像给机器人装了 “通感大脑”，既能通过视觉捕捉环境动态，又能通过语言锚定任务目标，最终生成连贯、灵活的操作动作。比如让它 “把红色马克杯放到笔记本旁的书架上”，它能自动完成 “找杯子 - 判断位置 - 规划移动路径” 的全流程，甚至应对杯子被书本挡住的突发情况

不过，这个快速发展的领域也藏着不少 “迷雾”：有人把 VLA 模型拆成多个模块，有人追求 “端到端” 的统一架构；数据集要么是模拟环境的 “理想数据”，要么是真实世界的 “零散样本”；更别提大家对 “如何让机器人记住过往动作、完成长期任务”“怎样让模型在不同机器人上通用” 这些问题，还没形成统一答案。

为了理清这些脉络，哈尔滨工业大学（深圳）的研究者们撰写了这篇《Large VLM-based Vision-Language-Action Models for Robotic Manipulation: A Survey》。它不仅是首个系统梳理 “大型 VLM + 机器人操作” 的综述，更像一张 “领域地图”—— 从 VLA 模型的定义、两种核心架构（单体式 / 分层式），到如何结合强化学习、从人类视频里学技能，再到未来要突破的 4D 感知、记忆机制等方向，都被清晰地呈现出来

传统方法依赖分离的视觉编码器、语言模块与规划器（如CLIPort用CLIP做语义接地、RT-1用CNN+独立语言嵌入），泛化性差，难以处理未知物体或模糊指令

以RT-2为代表，将机器人动作离散化为文本token，与VLM（PaLM-E/PaLI-X）共训练，实现“视觉-语言-动作”统一建模；后续OpenVLA（7B参数开源模型）、π₀（流匹配架构）进一步提升泛化性与可访问性。

**现有综述的不足**

- 多单独聚焦VLMs或机器人操作，缺乏对“VLMs与机器人操作交叉领域”的深入分析
- 未聚焦“预训练VLM作为基础组件”的主流范式
- 因覆盖自动驾驶、农业等多领域，稀释了机器人操作特有的实时控制、传感器噪声鲁棒性等挑战
- 仅关注文本LLM的任务规划，未解决VLM在视觉感知与动作接地的核心问题

VLA模型的定义与分类

**定义**：满足两个核心条件的模型：① 利用大型VLM理解视觉观测与自然语言指令；② 执行直接/间接服务于机器人动作生成的推理过程。

**核心分类**：根据“系统整合粒度”与“认知分解显式性”，分为两大类：

- **单体模型（Monolithic）**：将感知、语言理解、动作生成整合于单一/双系统架构，无显式中间表示；
- **分层模型（Hierarchical）**：显式分离“规划”与“执行”，通过人类可解释的中间表示（如子任务、关键点、程序）连接规划器与策略器。

基于VLM的两大类大型VLA模型的比较。单体模型在单系统或双系统架构中集成了感知、语言理解和动作生成，后者包含了一个额外的动作专家。相反，分层模型通过可解释的中间输出（例如，子任务、关键点、程序、功能支持）将规划与策略执行解耦

单体模型（Monolithic Models）

单系统模型（Single-system Models）

**核心思想**：视觉感知、语言指令、机器人状态输入统一模型，通过自回归/并行解码输出动作，架构简洁且无复杂模块通信

**三大研究方向**：

- **经典范式：自回归解码**

**方法：**将连续动作空间离散化为token序列，VLM 自回归生成动作token，再通过解令牌器转换为可执行动作。

**代表技术：**

RT-2 ：以PaLM-E/PaLI-X为VLM骨干，训练互联网视觉语言数据与机器人轨迹，将动作视为语言任务，显著提升语义理解与泛化性；

RT-2-X ：在RT-2基础上，用Open X-Embodiment（OXE）跨机器人数据集微调，提升技能迁移能力；

OpenVLA ：用DINOv2+SigLIP替代大参数视觉编码器，基于LLaMA2-7B，在真实机器人演示数据上微调，开源且性能优异。

- **模型性能增强**

**感知模态扩展**：加入3D（Leo Agent 用PointNet++处理点云、SpatialVLA通过深度估计生成3D坐标）、4D（TraceVLA叠加运动轨迹、4D-VLA 整合3D坐标与历史关键帧）、触觉/听觉（VTLA融合触觉、VLAS 用Whisper提取语音）；

**推理能力提升**：引入思维链（ECoT生成推理链、CoT-VLA 预测像素级子目标观测）、分层闭环控制（LoHoVLA 处理长程任务）

**泛化性优化**：统一动作空间（UniAct定义通用动作码本）、可逆训练（ReVLA恢复视觉编码器预训练状态以减少灾难性遗忘）、多模态融合（FuSe 用自然语言对齐触觉/听觉）。

**代表技术**：Leo Agent 、TraceVLA、CoT-VLA、UniAct、VTLA 。

- **推理效率优化**

**方法：**从架构、参数、解码策略三方面降低推理开销。

**架构优化**：动态层跳过（MoLe-VLA用STAR路由器选择关键LLM层）、早期退出（DeeR-VLA 通过输出一致性判断是否终止推理）、Mamba架构（RoboMamba 实现Transformer的推理速度的三倍）；

**参数优化**：模型压缩（BitVLA 用‘1-bit’权重、NORA 设计小参数模型）；

**解码加速**：并行解码（PD-VLA 将自回归转为不动点迭代、RoboFlamingo 用独立MLP动作头）、投机解码（Spec-VLA速度提升1.42倍）、动作复用（FlashVLA稳定场景下跳过推理）。

**代表技术**：RoboMamba、DeeR-VLA 、BitVLA 、PD-VLA 、FlashVLA

双系统模型（Dual-system Models）

**核心思想**：分离“高层推理（System 2：VLM骨干）”与“低层动作生成（System 1：动作专家）”，平衡推理精度与实时性，无显式中间表示（区别于分层模型）。

**两大研究方向**：

- **级联式（Cascade-based）**

**方法：**System 2处理多模态输入生成 latent 认知表示，再传递给System 1解码为动作，串行执行。

**代表技术**：

DP-VLA ：用OpenVLA为System 2，Behavioral Cloning Transformer为System 1，兼顾效率与性能；

HiRT：System 2低频运行（理解场景），轻量级System 1高频控制，适配动态环境；

TriVLA ：新增“世界动态感知模块”（System 3），补充静态感知的不足

- **并行式（Parallel-based）**

**方法：**System 2与System 1并行运行，通过共享注意力/交叉注意力交互信息。

**代表技术：**

π₀ ：以PaliGemma为预训练VLM（System 2），训练流匹配（Flow Matching）动作专家（System 1），支持零样本泛化；

SmolVLA ：冻结轻量级SmolVLM-2（System 2），仅训练下游流匹配Transformer（System 1），提升效率；

ForceVLA ：在π₀基础上加入MoE结构的力感知模块，优化接触密集型操纵



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

##### 人员

https://www.duyipai.me/



##### 相关论文

https://zhuanlan.zhihu.com/p/1987884045833615002



#### CLIP

2021年见证了vision transformer的大爆发，随着谷歌提出ViT之后，一大批的vision transformer的工作席卷计算机视觉任务。除了vision transformer，另外一个对计算机视觉影响比较大的工作就是Open AI在2021年1月份发布的[DALL-E](https://link.zhihu.com/?target=https%3A//openai.com/blog/dall-e/)和[CLIP](https://link.zhihu.com/?target=https%3A//openai.com/blog/clip/)，这两个都属于结合图像和文本的多模态模型，其中**DALL-E是基于文本来生成模型的模型**，而**CLIP是用文本作为监督信号来训练可迁移的视觉模型**，这两个工作也像ViT一样带动了一波新的研究高潮。这篇文章将首先介绍CLIP的原理以及如何用CLIP实现[zero-shot分类](https://zhida.zhihu.com/search?content_id=197796243&content_type=Article&match_order=1&q=zero-shot分类&zhida_source=entity)，然后我们将讨论CLIP背后的动机，最后文章会介绍CLIP的变种和其它的一些应用场景

CLIP的英文全称是**Contrastive Language-Image Pre-training**，即**一种基于对比文本-图像对的预训练方法或者模型**。CLIP是一种基于对比学习的多模态模型，与CV中的一些对比学习方法如moco和simclr不同的是，CLIP的训练数据是文本-图像对：一张图像和它对应的文本描述，这里希望通过对比学习，模型能够学习到文本-图像对的匹配关系。如下图所示，CLIP包括两个模型：**Text Encoder**和**Image Encoder**，其中Text Encoder用来提取文本的特征，可以采用NLP中常用的text transformer模型；而Image Encoder用来提取图像的特征，可以采用常用CNN模型或者vision transformer

这里对提取的文本特征和图像特征进行对比学习。对于一个包含个文本-图像对的训练batch，将个文本特征和个图像特征两两组合，CLIP模型会预测出个可能的文本-图像对的相似度，这里的相似度直接**计算文本特征和图像特征的余弦相似性（cosine similarity）**，即上图所示的矩阵。这里共有个正样本，即真正属于一对的文本和图像（矩阵中的对角线元素），而剩余的个文本-图像对为负样本，那么CLIP的训练目标就是最大个正样本的相似度，同时最小化个负样本的相似度

为了训练CLIP，OpenAI从互联网收集了共**4个亿的文本-图像对**，论文称之为**WebImageText**，如果按照文本的单词量，它和训练GPT-2的WebText规模类似，如果从数量上对比的话，它还比谷歌的[JFT-300M数据集](https://zhida.zhihu.com/search?content_id=197796243&content_type=Article&match_order=1&q=JFT-300M数据集&zhida_source=entity)多一个亿，所以说这是一个很大规模的数据集。CLIP虽然是多模态模型，但它主要是用来**训练可迁移的视觉模型**。论文中Text Encoder固定选择一个包含63M参数的text transformer模型，而Image Encoder采用了两种的不同的架构，一是常用的CNN架构ResNet，二是基于transformer的ViT，其中ResNet包含5个不同大小的模型：**ResNet50**，**ResNet101**，**RN50x4**，**RN50x16**和**RNx64**（后面三个模型是按照EfficientNet缩放规则对ResNet分别增大4x，16x和64x得到），而ViT选择3个不同大小的模型：**ViT-B/32**，**ViT-B/16**和**ViT-L/14**。所有的模型都训练32个epochs，采用AdamW优化器，而且训练过程采用了一个**较大的batch size：32768**。由于数据量较大，最大的ResNet模型RN50x64需要在592个V100卡上训练18天，而最大ViT模型ViT-L/14需要在256张V100卡上训练12天，可见要训练CLIP需要耗费多大的资源。对于ViT-L/14，还在336的分辨率下额外finetune了一个epoch来增强性能，论文发现这个模型效果最好，记为**ViT-L/14@336**，论文中进行对比实验的CLIP模型也采用这个

**为什么是CLIP，即CLIP这篇工作的motivation**。 在计算机视觉领域，最常采用的迁移学习方式就是先在一个较大规模的数据集如ImageNet上预训练，然后在具体的下游任务上再进行微调。这里的预训练是基于有监督训练的，需要大量的数据标注，因此成本较高。近年来，出现了一些基于自监督的方法，这包括基于对比学习的方法如MoCo和SimCLR，和基于图像掩码的方法如MAE和BeiT，自监督方法的好处是不再需要标注。但是无论是有监督还是自监督方法，它们在迁移到下游任务时，还是需要进行有监督微调，而无法实现zero-shot。对于有监督模型，由于它们在预训练数据集上采用固定类别数的分类器，所以在新的数据集上需要定义新的分类器来重新训练。对于自监督模型，代理任务往往是辅助来进行表征学习，在迁移到其它数据集时也需要加上新的分类器来进行有监督训练。但是NLP领域，基于自回归或者语言掩码的预训练方法已经取得相对成熟，而且预训练模型很容易直接zero-shot迁移到下游任务，比如OpenAI的GPT-3。这种差异一方面是由于文本和图像属于两个完全不同的模态，另外一个原因就是NLP模型可以采用从互联网上收集的大量文本。那么问题来了：**能不能基于互联网上的大量文本来预训练视觉模型？**

其实之前已经有一些工作研究用文本来作为监督信号来训练视觉模型，比如16年的工作[Learning Visual Features from Large Weakly Supervised Data](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1511.02251)将这转化成一个多标签分类任务来预测图像对应的文本的bag of words；17年的工作[Learning Visual N-Grams from Web Data](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1612.09161)进一步扩展了这个方法来预测n-grams。最近的一些工作采用新的模型架构和预训练方法来从文本学习视觉特征，比如[VirTex](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2006.06666)基于transformer的语言模型，[ICMLM](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2008.01392)基于语言掩码的方法，[ConVIRT](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2010.00747)基于对比学习的方法。整体来看，这方面的工作不是太多，这主要是因为这些方法难以实现较高的性能，比如17年的那篇工作只在ImageNet上实现了11.5%的zero-shot性能，这远远低于ImageNet上的SOTA。另外，还有另外的是一个方向，就是基于文本弱监督来提升性能，比如谷歌的[BiT](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1912.11370)和[ViT](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2010.11929)基于JFT-300M数据集来预训练模型在ImageNet上取得SOTA，JFT-300M数据集是谷歌从互联网上收集的，通过一些自动化的手段来将web text来转化成18291个类别，但是存在一定的噪音。虽然谷歌基于JFT-300M数据集取得了较好的结果，但是这些模型依然采用固定类别的softmax分类器进行预训练，这大大限制了它的迁移能力和扩展性

**谷歌的弱监督方法和之前的方法的一个重要的区别在于规模，或者说算力和数据的规模不同**。JFT-300M数据量达到了上亿级别，而且谷歌用了强大的算力来进行预训练。而VirTex，ICMLM和ConVIRT只在10万级别的数据上训练了几天。为了弥补数据上的差异，OpenAI从网上收集了4亿的数据来实验。但是新的问题来了：采用什么样的方法来训练。OpenAI首先尝试了VirTex模型，即联合训练一个CNN和文本transformer来预测图像的文本（image caption），但是发现这种方法的训练效率（用ImageNet数据集上的zero-shot性能来评估）还不如直接预测bag of words，如下图所示，两者的训练效率能相差3倍。如果进一步采用[ConVIRT](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2010.00747)，即基于对比学习的方法，训练效率可以进一步提升4倍。之所出现这个差异，这不难理解，训练数据所包含的文本-图像对是从互联网收集来的，它们存在一定的噪音，就是说文本和图像可能并不完全匹配，这个时候适当的降低训练目标，反而能取得更好的收敛。而从任务难度来看：Transformer Language Model > Bag of Words Prediction > Bag of Words Contrastive (CLIP)。由于训练数据量和模型计算量较大，训练效率成为一个至关重要的因素。这就是作者最终选择对比学习的方法来训练的原因

基于文本来搜索图像是CLIP最能直接实现的一个应用，其实CLIP也是作为DALL-E的排序模型，即从生成的图像中选择和文本相关性较高的

CLIP是基于文本-图像对来做的，但是它可以扩展到文本-视频，比如[VideoCLIP](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2109.14084)就是将CLIP应用在视频领域来实现一些zero-shot视频理解任务。

CLIP可以用在指导图像编辑任务上，[HairCLIP](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2112.05142)这篇工作用CLIP来定制化修改发型

CLIP还可以应用在图像生成上，比如[StyleCLIP](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2103.17249)这篇工作用CLIP实现了文本引导的StyleGAN

华为的工作[MVP](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2203.05175)更是采用CLIP来进行视觉自监督训练

##### siglip

在最近一些知名的开源多模态大模型中，视觉编码器模块有两个重要的身影，一个是**[InternViT-6B](https://zhida.zhihu.com/search?content_id=246959003&content_type=Article&match_order=1&q=InternViT-6B&zhida_source=entity)**（对应的MLLM有**InternVL2、InternVL1.5**等），另一个是**SigLIP-400M**（对应的MLLM有**LLaVA-OneVision、MiniCPM-Llama3-V2.5、LLaVA-Next-Qwen-32B、WeMM、LLaVA-Next-Interleave-7B-DPO**等）

https://github.com/google-research/big_vision

我们提出了**一种简单的两两Sigmoid损失函数用于语言-图像预训练（SigLIP）**。不同于采用softmax归一化的标准对比学习，**Sigmoid损失仅在图像-文本对上操作，并不需要全局视图来对两两相似度进行归一化**。**Sigmoid损失同时允许进一步扩大批量大小，同时在较小的批量大小下表现更佳**。结合锁定图像调优，仅使用四块[TPUv4](https://zhida.zhihu.com/search?content_id=246959003&content_type=Article&match_order=1&q=TPUv4&zhida_source=entity)芯片，我们训练了一个SigLiT模型，在两天内实现了84.5%的ImageNet零样本准确性。**批量大小与损失函数的解耦进一步使我们能够研究示例与对以及负例对正例比率的影响**。最终，**我们将批量大小推向极端，高达一百万，并发现增大批量大小的好处迅速减弱，一个更为合理的32k的批量大小就已足够**

利用从网络上找到的图像-文本对的弱监督进行对比预训练，正逐渐成为获取通用计算机视觉骨干网络的首选方法，慢慢取代在大型标记多类别数据集上的预训练。**其核心思想是使用配对数据同时学习图像和文本的对齐表示空间。开创性工作CLIP [36] 和 ALIGN [23] 在大规模上证实了这种方法的可行性**，随后，许多大型图像-文本数据集私有地 [59, 13, 21, 49] 和公开地 [40, 6, 15, 7, 41] 变得可用。

**预训练这类模型的标准做法利用了图像-文本对比目标。它对匹配（正例）图像-文本对的图像和文本嵌入进行对齐，同时确保不相关（反例）图像-文本对在嵌入空间中不相似**。这是**通过应用基于softmax的批级对比损失实现的，该损失分别对所有图像和所有文本的两两相似度分数进行归一化两次**。softmax的朴素实现在数值上不稳定；通常通过**在应用softmax之前减去最大输入值来稳定它 [18]，这需要在整个批次上再做一次遍历**。

在本文中，我们提出了一个更简单的替代方案：**Sigmoid损失。它不需要在全批上进行任何操作，因此极大地简化了分布式损失的实现并提高了效率**。此外，它**在概念上将批量大小与任务定义解耦**。我们在多种设置下比较了提出的Sigmoid损失与标准softmax损失。具体而言，我们研究了基于Sigmoid的损失与图像-文本学习的两种突出方法：**CLIP** [36] 和 **LiT** [59]，我们分别称之为Sigmoid语言图像预训练（**SigLIP**）和Sigmoid LiT（**SigLiT**）

我们发现，**当批量大小小于16k时，Sigmoid损失的表现显著优于softmax损失。随着训练批量大小的增长，两者差距缩小**。重要的是，**Sigmoid损失是对称的，只需要一次遍历，且典型实现所需的内存比softmax损失少**。这使得在一百万的批量大小下成功训练SigLiT模型成为可能。然而，**我们发现对于softmax和Sigmoid，性能随批量大小增长而饱和**。好消息是，一个合理的批量大小，即**32k，足以用于图像-文本预训练。这一结论同样适用于超过100种语言的多语种SigLIP训练。**

在**表1**中，我们列出了图像-文本预训练的设置，这些设置需要适度数量的TPUv4芯片进行训练。**SigLiT出人意料地高效，在四个芯片上仅一天就能达到ImageNet上79.7%的零样本准确率。SigLIP从头开始的更苛刻训练，使用32个TPUv4芯片在5天内达到73.4%的零样本准确率**。这与先前的工作如FLIP [30] 和 CLIP [36] 相比具有优势，它们分别需要大约5天和10天在256个TPUv3核心上。在SigLIP中微调预训练的视觉骨干网络，**如表1所示，我们发现禁用预训练骨干网络上的权重衰减会导致更好的结果（详情见图4）**。

我们希望我们的工作能为使新兴的语言-图像预训练领域更加普及铺平道路。

https://zhuanlan.zhihu.com/p/714731384



#### DINO-v3

2025年8月14日Meta重磅发布[DINO v3](https://zhida.zhihu.com/search?content_id=261832367&content_type=Article&match_order=1&q=DINO+v3&zhida_source=entity)。官方报道：吞下17亿张图片，Meta最强巨兽DINO-v3 7B参数超级视觉大模型开源

DINO v3跟DINO v2的架构和预训练策略几乎完全一样：MIM（来自于iBOT的特征预测型MIM）+自蒸馏（来自于DINO v1）+多分辨率裁剪数据增强（来自于SwAV）+寄存器token（VitNeedReg）。并且加了一些额外的技术，使得能够有效训练超大模型，且训练得到的密集特征更有效 作者：Dezeming 链接：https://zhuanlan.zhihu.com/p/1940400858836742367 来源：知乎 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

传统深度学习依赖大量人工标注数据，而自监督学习通过从数据本身生成监督信号（如图像块的关系、时序连续性等），彻底摆脱了这一限制。不局限于特定任务或领域，同一算法可处理多样数据（如自然图像、航拍图像等），为通用视觉表征学习铺平道路。模型和数据集规模可自由扩展，无需标注成本，适合大规模训练

**DINOv3的核心创新在于：（1）数据与模型规模的协同优化，**通过精心设计的数据清洗、多样化和增强策略，确保大规模数据的质量。优化模型结构（ViT）以适应超大规模训练，平衡计算效率与表征能力。**（2）[Gram锚定](https://zhida.zhihu.com/search?content_id=261832367&content_type=Article&match_order=1&q=Gram锚定&zhida_source=entity)**（Gram Anchoring，做过卷积网络风格迁移的朋友有没有回想到~）**，**在长周期训练中，密集特征图（dense feature maps）易出现退化（如信息丢失或过度平滑）。通过Gram矩阵（捕捉特征间高阶统计量）锚定特征分布，稳定训练并提升特征质量。这一方法首次系统性解决了该长期存在的问题。**（3）后处理策略（Post-hoc Strategies）。**多分辨率适配支持灵活调整输入图像分辨率，适应不同计算需求。提供不同规模的模型变体（如小型到巨型），适配多样部署场景。**（4）ViT骨干也进行了改进**，使用了axial RoPE位置编码，并且进行了位置编码正则化来避免位置伪影。

DINOv3的性能优势：（1）通用视觉基础模型：在无需微调的情况下，超越以往自监督/弱监督模型（如DINOv2、MoCo等）和领域专用模型（如ImageNet预训练模型）的性能。（2）密集特征质量：生成的高质量密集特征可直接用于分割、检测等任务，显著优于先前方法。（3）开源模型套件：提供不同规模的预训练模型（如Small到Large），推动社区在资源受限或高性能场景中的应用。 

**规模化过程中的三大具体问题：（1）无标注数据集的效用性问题，**如何从无标注数据集中筛选“有用”数据？互联网爬取的原始数据包含噪声（模糊图像、重复内容、无关文本等），直接训练会降低模型效率。**(2) 训练调度的不确定性，[余弦退火调度](https://zhida.zhihu.com/search?content_id=261832367&content_type=Article&match_order=1&q=余弦退火调度&zhida_source=entity)（Cosine Schedule）**需预设总训练步数（优化终点），但在超大规模数据集（如数十亿图像）上难以提前确定最优步数（数据量过大时可能需动态调整），固定调度可能导致欠拟合或过拟合。**(3) 长周期训练中的特征退化，**当模型参数量超过ViT-Large（3亿参数）且训练时间延长时，早期阶段特征质量提升，但后续**相似度图（Patch Similarity Maps）**显示特征逐渐退化（如过度平滑或丢失局部细节）。图像块之间的相似度趋于一致，失去判别性。根本原因在于优化目标与特征密度间的矛盾（如过度依赖全局一致性而忽略局部差异），大规模模型的优化轨迹具有复杂性（梯度噪声累积、损失曲面平坦化）。

这些问题导致单纯扩大DINOv2的规模（数据量、参数量）无法持续提升性能，甚至可能有害

ViT的密集特征天生具有全局感受野，而CNN的密集特征受限于局部感受野。简单来说，你的ViT输出的图像特征是密集特征，以用于下游任务。但有可能学习到的每个patch的特征不是很有“独特性”，即所有patch的特征区分度很小，就不利于后续下游任务。

**余弦退火调度**是一种用于深度学习优化的学习率调整策略，其核心思想是让学习率随着训练过程按余弦函数的形式从初始值平滑衰减到接近零。这种调度方式在训练大规模模型时表现优异，尤其在自监督学习Transformer模型中广泛应用。

**相似性图**（Patch Similarity Maps）就是通过计算图像所有局部块（patch）特征之间的相似性（如余弦相似度），生成一个对称矩阵（或热力图），反映模型对图像内部结构的理解程度。

https://zhuanlan.zhihu.com/p/1940400858836742367



#### 其他先进领域

代表性的VLA方法分为四个高级类别：基于强化学习、无训练、从人类视频中学习和基于世界模型的方法

1.基于强化学习（RL-based Methods）

**核心思想**：通过在线交互（实时优化）或离线轨迹（预收集数据）优化VLA策略，解决奖励稀疏、样本效率低问题。

**代表技术：**

奖励设计：VLA-RL 训练机器人过程奖励模型（RPRM）、ReWiND 以“目标进度”为奖励、Grape用VLM生成反馈奖励；

训练范式：ReWiND（离线IQL+在线SAC）、HIL-SERL（人类在环干预）、ConRFT （离线Cal-ConRFT+在线HIL-ConRFT）；

数据引擎：RLDG （训练专家策略后蒸馏到基础模型）、iRe-VLA（在线RL收集轨迹+SFT迭代优化）

2.无训练方法（Training-Free Methods）

**核心思想**：通过架构/计算优化提升VLA效率，无需重新训练，保留原模型能力。

**代表技术：**

令牌优化：FlashVLA （稳定场景跳过解码）、EfficientVLA （剪枝冗余语言层+过滤视觉令牌）；

解码加速：PD-VLA （并行不动点迭代）、FAST（DCT+字节对编码压缩动作序列）；

调度优化：RTC （监控任务进度调整控制频率）

3.从人类视频学习（Learning from Human Videos）

**核心思想**：利用人类-物体交互与机器人-物体交互的结构相似性，从人类视频中迁移任务知识，缩小embodiment gap。

**代表技术：**

Human-Robot Semantic Alignment ：用人类-机器人配对视频对齐视觉编码器；

UniVLA ：从无标注人类/机器人视频学习任务中心 latent 动作；

LAPA ：VQ-VAE量化 latent 动作，在大规模视频-语言数据上预训练；

3D-VLA ：融合人类交互视频与机器人演示，提升3D推理；

Humanoid-VLA ：从在线视频恢复姿态轨迹，增强运动多样性

4.基于世界模型（World Model-based VLA）

**核心思想**：整合世界模型（预测环境动态的紧凑表示），通过模拟未来状态优化动作规划，提升长程任务能力。

**代表技术：**

WorldVLA ：自回归动作-世界模型，联合预测视觉结果与生成动作；

World4Omni ：大规模世界模型生成子目标图像，指导低层级策略；

3D-VLA ：生成式世界模型预测未来目标图像与点云；

V-JEPA 2-AC ：基于互联网规模视频训练的动作条件世界模型，通过模拟未来 latent 状态做规划

VLA 模型区别于传统机器人操作模型的核心能力：

**多模态融合**：通过统一嵌入与动态整合打破模态壁垒。一是构建 “共享嵌入空间”，依托预训练 VLM 生成视觉与语言的联合嵌入，减少模块间语义偏移；二是实现 “令牌级整合”，将视觉、语言、动作等连续模态离散为令牌，由单个 Transformer 处理以捕捉跨模态依赖；三是具备 “全面模态兼容性”，可无缝整合点云（如 PointVLA）、触觉（如 VTLA）、音频（如 VLAS）等新模态，无需修改核心架构或全量重训。

**指令遵循**：依托语义理解与推理实现灵活指令响应。一是 “语义指令定位”，利用 VLM 世界知识动态解读模糊指令，如 ChatVLA-2 可理解白板上的数学问题并选择数字卡片；二是 “任务分解与协作”，将长期任务拆分为子目标，如 LoHoVLA 生成自然语言子任务指导动作；三是 “思维链推理”，通过预测未来视觉状态（如 CoT-VLA）缓解短视幻觉，提升复杂任务可靠性。

**多维度泛化**：实现跨任务、跨领域、跨载体的广泛适配。一是 “跨任务泛化”，如 DexVLA 无需任务调优即可完成多样操作，性能超越 OpenVLA；二是 “跨领域泛化”，如 π₀ 通过异构数据联合训练，在家庭环境中分布外任务成功率超 90%；三是 “跨载体与模拟到现实泛化”，如 HAMSTER 依托分层架构，在七个泛化维度上成功率比 OpenVLA 高 20%

**真实世界机器人数据集**：

**特点：**捕捉真实环境复杂性，支持语言与动作对齐。但开放世界物体、场景、技能的长尾分布仍未充分覆盖。

**典型案例：**

 OXE（整合 22 个机器人平台的 100 万 + 多模态演示）

 RH20T（支持 147 项任务单样本学习）

 DROID（含 564 项 “自然场景” 远程操作演示）

**模拟数据集与基准测试**：

**特点：**提供可扩展、安全的训练环境，降低真实数据采集成本。但存在物理模拟不完美、视觉伪影等局限。

**典型案例：**

 BEHAVIOR（支持杂乱家庭环境多步骤控制）

 CALVIN（支持无约束语言指令下的长期操作）

 SIMPLER（通过校准环境减少模拟到现实差距）

**人类行为数据集**：

**特点：**提供语义丰富的人类交互先验，辅助 VLA 理解操作逻辑；可帮助 VLA 学习目标识别、动作序列与任务分解。

**典型案例：**

 Ego4D（3000 小时第一视角视频）

 EPIC-Kitchens（细粒度烹饪任务视频）

 EgoDex（829 小时含 3D 手部追踪的第一视角视频）

**具身数据集与基准测试**：

**特点：**聚焦 VLA 的规划与推理能力评估，推动从 “操作” 向 “认知 + 操作” 升级。为高层语义规划提供严格评估标准。

**典型案例：**

 OpenEQA（评估功能与常识推理）

 LoTa-Bench（验证 LLM 生成规划的可执行性）

 MT-EQA（支持多目标推理）

未来方向

1. **数据集与基准测试优化**：需结合大规模真实世界数据采集与复杂任务套件（如长期规划、移动操作），引入子任务成功率、时间效率、抗干扰性等多维度评估指标，解决现有数据 “现实差距” 与基准 “任务单一” 问题。
2. **记忆机制与长期规划**：设计具备情景感知的记忆架构，让 VLA 可利用历史观测信息，从 “逐帧反应” 转向 “目标驱动的长期连贯动作”，解决当前模型短视行为问题。
3. **3D 与 4D 感知升级**：突破静态 2D 视觉输入局限，整合深度、点云数据实现 3D 空间理解，结合时间动态信息构建 4D 感知，提升复杂环境操作稳健性。
4. **移动操作整合**：打破 “移动” 与 “操作” 的阶段分离，学习自适应优先的整合策略，实现机器人同时完成 locomotion 与交互操作，适配真实世界动态场景需求。
5. **多智能体协作**：构建共享世界模型与涌现式对话协议，让多个 VLA 智能体可协商意图、分配子任务（如联合搬运物体、协作使用工具），解决单智能体在复杂协作任务中的能力局限。
6. **开放世界终身学习**：设计增量知识积累机制与抗遗忘记忆结构，让 VLA 可在开放环境中通过探索与反馈持续学习新技能、适应未知物体与交互方式，突破静态数据集训练的局限。
7. **模型效率提升**：通过任务感知动态令牌修剪、异步推理、硬件友好量化，平衡模型容量与实时推理需求，适配资源受限的机器人平台。



### 世界模型

世界模型能够根据当前输入预测未来观测或潜在表征。其前向预测能力在VLA系统中变得越来越重要，可支持规划、推理和控制功能

（1）**在世界模型中生成动作**：与直接生成动作的模型不同，世界模型会生成未来的视觉观测（如图像或视频序列），然后利用这些观测来指导动作生成。例如，UniPi采用受Video UNet启发的扩散模型，根据初始观测图像和任务指令生成视频序列。随后，逆动力学模型（IDM）将预测的图像序列转换为低层动作。这种视觉预测与基于IDM的控制相结合的方式，是基于模型的VLA中常见的设计模式。类似地，DreamGen和GeVRM通过预测未来的视觉表征来生成动作。HiP通过整合LLM进行子任务分解，进一步扩展了这一思路，能够实现更长周期行为的执行。Dreamitate对Stable Video Diffusion进行微调，以合成人类使用工具完成操作任务的视频。然后，根据生成的视频，利用MegaPose估计工具的6自由度位姿，使机器人能够遵循估计的工具位姿进行操作。与生成完整视频序列不同，SuSIE利用InstructPix2Pix根据初始观测和任务指令生成中间目标图像，从而预测抽象的子目标图像，并将其作为扩散策略的条件约束。CoT-VLA采用类似方法实现思维链推理（详见第4.1节）。LUMOS也会生成目标图像，但其生成过程是通过一个以低层动作指令为输入的世界模型实现的。在LUMOS中，通过与学习到的世界模型交互，训练策略以模仿专家演示。

除了视频和图像生成，近年来许多研究还利用光流或特征点跟踪技术。由于光流和特征跟踪与机器人实体无关，因此为利用人类演示数据提供了更具泛化性的方法。与UniPi类似，AVDC生成视频序列，并利用GMFlow计算每一帧的光流。然后，将目标物体的SE(3)刚体变换估计问题转化为一个优化问题。ATM在训练过程中利用CoTracker预测任意特征点的未来轨迹，并训练一个Transformer，在这些轨迹的指导下生成动作。Track2Act预测初始图像和目标图像之间的特征点轨迹，优化3D刚体变换，并学习残差策略以细化运动。LangToMo根据初始图像和任务指令预测未来光流，利用RAFT进行光流监督，并将该预测映射为机器人动作。MinD采用端到端方法，联合学习视频预测和动作预测。具体而言，MinD将一个低频视频生成器（根据初始图像和指令在潜在空间中预测未来视觉观测）与DiffMatcher（将这些预测转换为时序特征，供高频动作策略用于高效生成动作序列）相结合。PPI接收视觉和语言输入，预测每个关键帧的抓手位姿和物体位移（Pointflow），并将其作为动作生成的中间条件

（2）**通过世界模型生成潜在动作**：这类VLA模型利用世界模型从人类演示中学习潜在动作表征。例如，LAPA（从视频中进行潜在动作预训练）（详见第3节）通过联合学习从当前和未来图像对中预测动作表征，以及根据当前图像和潜在动作生成未来帧，实现了在无显式动作标签的数据集（如人类视频）上的训练。一旦学习到潜在动作，就可以利用这些token训练VLA策略。随后，替换动作头部并进行微调，以输出机器人动作。LAPA已被用于GR00T N1和DreamGen的预训练。此外，GO-1和Moto也采用了类似方法。UniVLA通过语言输入扩展了DINOv2的潜在空间，并采用两阶段训练过程，分离与任务无关和与任务相关的潜在动作token。UniSkill采用基于图像编辑的方法，从RGB-D图像中提取潜在动作，并将其作为扩散策略的条件。

（3）**包含隐式世界模型的感知运动模型**：这类VLA模型同时输出动作和未来观测预测，以提升性能。GR-1整合了预训练的MAE-ViT编码器、CLIP文本编码器和一个Transformer，并在Ego4D数据集上进行训练以预测未来观测图像。随后，通过微调使模型能够根据图像、语言和本体感知输入，同时预测动作和未来帧。通过在标准VLA框架中融入类似视频预测模型的观测预测功能，GR-1在任务成功率方面表现出了提升。GR-2在GR-1的基础上，通过扩大训练数据集和引入架构改进（包括基于VQGAN的图像token化和用于动作生成的条件变分自编码器（VAE）），进一步提升了性能。GR-MG利用基于InstructPix2Pix的模型生成中间目标图像，并将其嵌入到GR-1风格的框架中。此外，GR-3通过整合VLM（Qwen2.5-VL）和带有流匹配功能的扩散Transformer以生成动作，实现了分层结构。3D-VLA通过扩展这一研究方向，利用Stable Diffusion预测RGB-D图像，并利用Point-E预测点云。此外，FLARE、UVA、WorldVLA和ViSA-Flow等多种模型也在感知运动模型中融入了完整的视频预测功能

https://github.com/nvidia-cosmos

https://zhuanlan.zhihu.com/p/19751920966

https://wm-po.github.io/



李飞飞：https://vision-x-nyu.github.io/thinking-in-space.github.io/

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

