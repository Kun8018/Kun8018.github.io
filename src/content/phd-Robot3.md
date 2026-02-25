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



#### xlerobot

https://xlerobot.readthedocs.io/en/latest/



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



### 其他数据集

涂鸦数据集：https://github.com/googlecreativelab/quickdraw-dataset
