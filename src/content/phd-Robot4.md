---
title: 机器人研究(二)
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web
toc: true
thumbnail:
---

<!--more-->

## 具身智能

### tensorflow

#### 初始化

tf.compat.v1 里“初始化”相关的东西主要分两类：会话中一次性执行的初始化器（返回一个 op，需要 sess.run），以及为变量提供初始值的“初始器”（传给 get_variable/Variable 的 initializer 参数）

会话级初始化器（运行一次以初始化图中的对象）

- global_variables_initializer() / initializers.global_variables()
  初始化所有“全局变量”（模型参数等，Variable 被加入 GLOBAL_VARIABLES 集合）。

- local_variables_initializer() / initializers.local_variables()
  初始化所有“局部变量”（常用于指标、临时计数等，加入 LOCAL_VARIABLES 集合）。

- variables_initializer(var_list) / initializers.variables(var_list)
  按需初始化指定变量列表。

- tables_initializer() / initializers.tables()
  初始化查找表（tf.lookup 等），不初始化变量。

- 兼容别名（已弃用，但在 compat.v1 仍可用）

  - initialize_all_variables() ≡ global_variables_initializer()
  - initialize_local_variables() ≡ local_variables_initializer()
  - initialize_variables(var_list) ≡ variables_initializer(var_list)
  - initialize_all_tables() ≡ tables_initializer()

- 辅助检查

  - report_uninitialized_variables(var_list=None)
    返回未初始化变量的名字（sess.run 后得到字符串列表），用于排查。

  - assert_variables_initialized(var_list=None)
    断言变量已初始化，未初始化则抛错

    sess.run(tf.compat.v1.global_variables_initializer())

  - sess.run([tf.compat.v1.local_variables_initializer(), tf.compat.v1.tables_initializer()])

变量初始值“初始器”（传给变量创建时的 initializer）

- constant_initializer(value=0)
  常量初值。
- zeros_initializer() / ones_initializer()
  全 0 / 全 1。
- random_uniform_initializer(minval=0.0, maxval=1.0, seed=None, dtype=None)
  区间均匀分布。
- random_normal_initializer(mean=0.0, stddev=1.0, seed=None, dtype=None)
  正态分布。
- truncated_normal_initializer(mean=0.0, stddev=1.0, seed=None, dtype=None)
  截断正态（去除远离均值的尾部）。
- orthogonal_initializer(gain=1.0, seed=None)
  正交矩阵初始化（常用于线性层/卷积核）。
- identity_initializer(gain=1.0)
  单位矩阵（仅适用于方阵形状）。
- uniform_unit_scaling_initializer(factor=1.0, seed=None, dtype=tf.float32)
  单位尺度均匀分布（v1 经典初始化器）
- 方差缩放初始化，把权重按 fan_in/fan_out 计算出尺度，再用均匀或正态分布采样。它们的等价关系与公式如下（fan_in=输入通道数，fan_out=输出通道数；Dense 的 [in, out]，Conv2D 的 [kh, kw, inC, outC] 则 fan_in=kh*kw*inC，fan_out=kh*kw*outC）
  - tf.compat.v1.glorot_normal_initializer()（Xavier/Glorot Normal）
    - 分布：截断正态（truncated normal）
    - 标准差：stddev = sqrt(2 / (fan_in + fan_out))
    - 适用：tanh、sigmoid、线性等非 ReLU 激活常用
  - tf.compat.v1.glorot_uniform_initializer()（Xavier/Glorot Uniform）
    - 分布：均匀
    - 范围：[-limit, limit]，limit = sqrt(6 / (fan_in + fan_out))
    - 适用：同上
  - tf.compat.v1.keras.initializers.he_normal()（Kaiming/He Normal）
    - 分布：正态（Keras实现为未截断的 normal）
    - 标准差：stddev = sqrt(2 / fan_in)
    - 适用：ReLU/LeakyReLU/ELU 等 ReLU 系列激活
  - tf.compat.v1.keras.initializers.he_uniform()（Kaiming/He Uniform）
    - 分布：均匀
    - 范围：[-limit, limit]，limit = sqrt(6 / fan_in)
    - 适用：同上
  - tf.compat.v1.keras.initializers.lecun_normal()
    - 分布：正态
    - 标准差：stddev = sqrt(1 / fan_in)
    - 适用：SELU（自归一化网络）推荐；也可用于线性/softsign 等
  - tf.compat.v1.keras.initializers.lecun_uniform()
    - 分布：均匀
    - 范围：[-limit, limit]，limit = sqrt(3 / fan_in)
    - 适用：同上

- “xavier_*”就是“glorot_*”的别称；TF 提供的是 glorot_*。
- TF v1 的 glorot_normal 使用截断正态（更稳一些）；Keras 的 he/lecun normal 用未截断正态。
- 选择建议：
  - ReLU 系列：he_normal / he_uniform
  - tanh/sigmoid/线性：glorot_normal / glorot_uniform
  - SELU：lecun_normal（配合 AlphaDropout）

#### layer

tf.compat.v1.layers 是 TF1 的“简易层”API（已弃用，TF2 请用 tf.keras.layers）。里面主要是两类：函数式层（返回张量）和对应的层类

tf.keras.layers

核心与形状

- Input/InputLayer：定义输入张量
- Dense：全连接
- Activation：独立激活
- Lambda：自定义运算
- Flatten、Reshape、Permute、RepeatVector
- Dropout、SpatialDropout1D/2D/3D、GaussianDropout、GaussianNoise、AlphaDropout
- ActivityRegularization
- Masking（基于掩码的序列填充忽略）

卷积与转置卷积

- Conv1D/2D/3D
- SeparableConv2D（深度可分离卷积）
- DepthwiseConv2D（纯深度卷积）
- Conv2DTranspose/Conv3DTranspose（反卷积/上采样）
- Conv1DTranspose（部分 TF2 版本提供，2.9+）

池化

- MaxPooling1D/2D/3D、AveragePooling1D/2D/3D
- GlobalMaxPooling1D/2D/3D、GlobalAveragePooling1D/2D/3D

归一化与标准化

- BatchNormalization
- LayerNormalization
- 说明：Group/Instance Norm 不在核心 Keras，通常用 tensorflow_addons.layers.GroupNormalization/InstanceNormalization

嵌入与注意力

- Embedding
- Attention、AdditiveAttention
- MultiHeadAttention（Transformer 常用）

序列与循环网络

- RNN（通用容器）
- SimpleRNN、GRU、LSTM
- Bidirectional、TimeDistributed（包装器）
- ConvLSTM2D（时空卷积 LSTM）

合并/运算层

- Add、Subtract、Multiply、Average、Maximum、Minimum、Concatenate、Dot

采样/几何变换/边界

- UpSampling1D/2D/3D
- ZeroPadding1D/2D/3D、Cropping1D/2D/3D
- Resizing（尺寸缩放，插值）、Rescaling（数值缩放）

预处理（可在模型内端到端使用）

- Numeric: Normalization、Discretization、CategoryEncoding
- Lookup: StringLookup、IntegerLookup、Hashing
- 图像: Rescaling、Resizing、CenterCrop、RandomFlip、RandomRotation、RandomZoom、RandomTranslation、RandomContrast
- 文本: TextVectorization

### pytorch

https://datawhalechina.github.io/thorough-pytorch/%E7%AC%AC%E4%BA%8C%E7%AB%A0/2.1%20%E5%BC%A0%E9%87%8F.html

- torch：张量与基础运算、设备管理（CPU/GPU）、随机数、保存/加载
- torch.nn：模型构建（Module、层、Loss）
- torch.nn.functional：函数式 API（无状态的激活/卷积/损失等）
- torch.optim：优化器（SGD、Adam 等）
- torch.utils.data：Dataset、DataLoader
- torch.cuda：CUDA 相关（设备查询、显存等）



### rlhf

https://huggingface.co/blog/zh/rlhf

过去几年里各种 LLM 根据人类输入提示 (prompt) 生成多样化文本的能力令人印象深刻。然而，对生成结果的评估是主观和依赖上下文的，例如，我们希望模型生成一个有创意的故事、一段真实的信息性文本，或者是可执行的代码片段，这些结果难以用现有的基于规则的文本生成指标 (如 [BLEU](https://en.wikipedia.org/wiki/BLEU) 和 [ROUGE](https://en.wikipedia.org/wiki/ROUGE_(metric))) 来衡量。除了评估指标，现有的模型通常以预测下一个单词的方式和简单的损失函数 (如交叉熵) 来建模，没有显式地引入人的偏好和主观意见。

如果我们 **用生成文本的人工反馈作为性能衡量标准，或者更进一步用该反馈作为损失来优化模型**，那不是更好吗？这就是 RLHF 的思想：使用强化学习的方式直接优化带有人类反馈的语言模型。RLHF 使得在一般文本数据语料库上训练的语言模型能和复杂的人类价值观对齐

RLHF 是一项涉及多个模型和不同训练阶段的复杂概念，这里我们按三个步骤分解：

1. 预训练一个语言模型 (LM) ；
2. 聚合问答数据并训练一个奖励模型 (Reward Model，RM) ；
3. 用强化学习 (RL) 方式微调 LM

RLHF的训练分为两个关键阶段：

1. **奖励模型训练**：根据人类提供的偏好数据构建奖励函数，这个奖励函数为每个生成的响应分配一个分数。
2. **策略优化**：通过强化学习算法优化语言模型，使生成的内容能够最大化奖励函数的输出。

在这种背景下，RLHF有以下几个核心需求：

- **稳定性和效率**：语言模型参数通常高达数十亿甚至更多，训练中的不稳定性会导致训练崩溃或结果不可用。
- **对[KL散度](https://zhida.zhihu.com/search?content_id=251181114&content_type=Article&match_order=1&q=KL散度&zhida_source=entity)的控制**：语言模型需要在优化奖励函数的同时，保持与初始参考策略的相似性，以避免生成质量的退化。
- **高效的采样和更新**：算法需要在有限的计算资源下，对复杂策略进行高效更新

https://github.com/huggingface/trl

https://github.com/CarperAI/trlx

https://github.com/allenai/RL4LMs

https://github.com/facebookresearch/Pearl

https://github.com/BVLC/caffe    https://caffe.berkeleyvision.org/

https://github.com/Farama-Foundation/Gymnasium



#### RL算法

https://zhuanlan.zhihu.com/p/10791831521

DQN,

DQN适用于离散动作空间的强化学习问题，其目标是学习一个近似的Q值函数。Q值函数的更新公式为：

在DQN中，动作是离散的，通常使用-贪婪策略进行选择。然而，LLM（Large Language Models）的输出是连续概率分布，无法直接适配离散动作的Q学习方法。要应用DQN，需要对动作空间进行离散化，但这会引入近似误差，导致生成质量下降。

同时，DQN存在样本效率较低和收敛性不稳定的问题，在RLHF的大规模复杂任务中会放大这些缺点

PPO, 

SAC **Soft Actor-Critic**

- Off-policy算法
- 最大化熵（鼓励探索）
- 双Critic网络（减少过估计）
- 连续动作空间

DDPG

PPO是一种基于信任区域的强化学习算法，它通过限制策略更新的幅度，在稳定性和效率之间找到了平衡。以下是PPO在RLHF中的核心优势：

1. **策略更新的稳定性** PPO通过引入裁剪的目标函数（clipped objective），限制新旧策略的相对熵（KL散度）。这一机制能够有效防止策略更新过大导致的训练不稳定性。在RLHF中，这一点尤为重要，因为奖励模型本身可能带有噪声或不确定性

MC、TD、SARSA、Q-learning、DQN、REINFORCE、PPO、SAC、TD3……这些名字听起来像是完全不同的东西。但其实，它们都在回答同一个问题：**怎么让智能体学会做决策**。

后来我发现，如果你用”三个问题”来看这些算法，它们就不再是一堆孤立的名字，而是有清晰脉络的一个体系：

1. **它在学什么？**（状态价值、动作价值、还是策略）
2. **它的学习目标从哪来？**（真实回报 vs 估计值）
3. **数据能不能重复用？**（on-policy vs off-policy）

https://www.infyai.cn/2025/12/28/rl-algorithms-unified-framework/#DQN%EF%BC%9AQ-learning-%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C-%E7%A8%B3%E5%AE%9A%E5%99%A8

#### tianshou

https://github.com/thu-ml/tianshou



#### rlinf

https://github.com/RLinf/RLinf/tree/main



#### trl

https://github.com/huggingface/trl

### SFT

精选博主： https://www.zhihu.com/people/yilan-zhong-shan-xiao-29-98/posts

现在的大模型在进行预训练时大部分都采用了[GPT](https://zhida.zhihu.com/search?content_id=232733945&content_type=Article&match_order=1&q=GPT&zhida_source=entity)的预训练任务，即 [Next token prediction](https://zhida.zhihu.com/search?content_id=232733945&content_type=Article&match_order=1&q=Next+token+prediction&zhida_source=entity)。

- **Token**：在NLP中，一个“token”可以是一个词、一个字或一个标点符号。例如，句子“我爱北京”被切分成三个tokens: “我”, “爱” 和 “北京”。
- **Prediction**：预测是指根据模型的当前输入，猜测接下来应该出现的token是什么。

在训练过程中，模型通过大量的文本数据来学习文本之间的模式和结构。例如，模型会看到成千上万次的“我爱X”这样的模式，其中X可以是各种不同的词。通过这样的训练，模型会学会哪些词最有可能出现在“我爱”之后。

要理解清楚这一训练过程，最主要的就是搞清楚**预训练的数据怎么构造，数据怎么喂给模型，模型输出的是什么，以及如何计算loss**。

假设现在有一个用来预训练的数据集

```text
你知道什么是预训练吗？
```

假设经过分词后

```text
你: 9
知道: 3
什么: 6
是: 4
预训练: 2
吗: 1
？: 5
```

原来的数据变为如下序列，后面补了三个0（假设我们希望最大序列长度是10）

```text
9 3 6 4 2 1 5 0 0 0
```

预测下一个token就类似于9预测3，93预测6，以此类推，但是如果这样拆成很多个数据段其实比较低效，因此就可以考虑**移位**来构造数据，即

- 模型输入X为 `9 3 6 4 2 1 5 0 0 0`
- 模型输出targets为 `3 6 4 2 1 5 0 0 0 0`

这样就可以一次性把整条序列喂给模型，计算一次就包含了个预测下一个token的损失了。注意这里模型的设计是有讲究的，我们**不能让输入看到后面的词**（如果看得到的话就没必要进行预测了），也就是“你”在模型内看不到“知道”，“你 知道”在模型内看不到“什么”，这个可以通过注意力机制实现，不是本文的关注点，这里就不展开了

现在模型的输入的维度为，第一维为batch_size，然后经过embedding层后变为，这里假设embedding的维度为768。

记住**进入transfomer前后数据的维度不会发生变化，把transfomer当作一个黑盒**，也就是[transformer](https://zhida.zhihu.com/search?content_id=232733945&content_type=Article&match_order=1&q=transformer&zhida_source=entity)(X)的维度还是，接下来就是基于它来进行预测了，因为要预测哪个词，词的可能情况就是词表的大小，所以做的就是一个**分类任务**，预测下一个token是词表中的哪一个（词表中的每一个词当作一个类别）

为了完成分类任务，需要对transformer的输出做一个映射，**映射到跟词表一样大**，也就需要定义这样一个线性变换

```shell
output_layer = nn.Linear(768, vocab_size, bias=False)
```

然后`logits=output_layer(transformer(X))`的维度就是`(1,10,vocab_size)`，接下来就可以计算loss了，具体来说就是计算logits（模型预测）与targets（真实标签）之间的[交叉熵损失](https://zhida.zhihu.com/search?content_id=232733945&content_type=Article&match_order=1&q=交叉熵损失&zhida_source=entity)，同时忽略了填充值对应的损失。

```shell
loss = F.cross_entropy(logits.view(-1, logits.size(-1)), targets.view(-1), ignore_index=0)
```

Supervised fine-tuning

“有监督微调”意味着使用有标签的数据来调整一个已预训练好的语言模型（LLM），使其更适应某一特定任务。通常LLM的预训练是无监督的，但微调过程往往是有监督的

当进行有监督微调时，模型权重会根据与真实标签的差异进行调整。通过这个微调过程，模型能够捕捉到标签数据中特定于某一任务的模式和特点。使得模型更加精确，更好地适应某一特定任务。

以一个简单的例子来说，你有一个已经预训练好的LLM。当输入“我不能登录我的账号，我该怎么办？”时，它可能简单地回答：“尝试使用‘忘记密码’功能来重置你的密码。”

这个回答很直接，适用于一般问题，但如果是客服场景，可能就不太合适了。一个好的客服回答应该更有同情心，并且可能不会这么直接，甚至可能包含联系信息或其他细节。这时候，有监督微调就显得非常重要了。

经过有监督微调后，你的模型可以提供更加符合特定指导原则的答案。例如，经过一系列专业的培训示例后，你的模型可以更有同情心地回答客服问题。

接下来我们还是从数据到模型输出，计算loss的步骤来看看SFT的实现原理。

首先还是来看看数据怎么构造，SFT的每一条样本一般由两部分组成，也就是prompt（instruction）+ answer，比如：

- prompt: `翻译以下句子: What is pretrain`
- answer: `什么是预训练`

也就是我们要给模型提供一些类似于问答形式的答案来学习，有了前面预训练的经验后，SFT其实就很好理解的，它本质上也在做next token prediction，只是我们更希望模型关注answer部分的预测，这可以通过生成一个`mask`向量来屏蔽不希望计算loss的部分，下面就是数据构造的一个示意：做的事情就是拼接prompt和answer，并在answer两侧添加一个开始和结束的符号，算一下prompt/instruction的长度，以及后面需要pad的长度，然后生成一个mask向量，answer部分为1，其他部分为0

```python
input_id=prompt+[bos]+answer+[eos]
context_length = input_id.index(bos)
mask_position = context_length - 1
pad_len = max_length - len(input_id)
input_id = input_id + [pad] * pad_len
loss_mask = [0]*context_length+[1]*(len(input_id[mask_position+1:])) + [0]*pad_len
```

构造好输入后，**token转为embedding，经过transformer的过程跟之前预训练完全一样**，也就是我们又得到了一个维度是`(1,10,vocab_size)`的输出`logits=output_layer(transformer(X))`，进一步就可以**计算answer部分的loss**了，其实就是通过mask把不希望考虑的地方乘以0，保留answer部分loss

```python
loss = F.cross_entropy(logits.view(-1, logits.size(-1)), Y.view(-1), ignore_index=0,reduce=False)
loss_mask = loss_mask.view(-1)
loss = torch.sum(loss*loss_mask)/loss_mask.sum()
```

有了loss，进行反向传播更新模型参数就OK啦

#### 什么情况下需要微调

语言风格有要求 比如ai恋爱 ai面试

#### 微调方式

**全模型微调（Full Fine-Tuning）**

- **含义**：在预训练模型的基础上，**对所有参数** 进行进一步训练，使其适应特定任务。
- **优点**：通常效果最好，模型能充分适应新数据。
- **缺点**：计算量大、容易过拟合、需要大量标注数据。

**冻结部分参数微调（Partial Fine-Tuning / Layer Freezing）**

- **含义**：只训练模型的部分层（通常是靠近输出的几层），而**冻结（不更新）** 其他层的参数。
- **优点**：减少计算量、降低过拟合风险、适合数据量较小的场景。
- **缺点**：可能不如全模型微调灵活

**轻量化微调（Parameter-Efficient Fine-Tuning, PEFT）**

- **含义**：通过引入少量可训练参数（如适配器模块、LoRA、Prefix Tuning等），在保持预训练参数不变的情况下进行微调。
- **优点**：大大减少训练参数和内存占用，适合大模型微调。
- **缺点**：效果可能略低于全模型微调。

**渐进微调（Progressive Fine-Tuning）**

- **含义**：分阶段、渐进地解冻并训练模型的不同部分（例如从顶层到底层逐步解冻）。
- **优点**：平衡训练效率和模型性能，避免一次性全参数训练带来的过拟合。
- **缺点**：训练流程更复杂，需要更多调优。

**多任务微调（Multi-Task Fine-Tuning）**

- **含义**：同时使用多个相关任务的数据进行微调，使模型能同时适应多个任务。
- **优点**：提升模型的泛化能力和鲁棒性，避免单任务过拟合。
- **缺点**：任务之间可能存在冲突，需要精心设计任务权重或架构。

科研用1就行

https://github.com/huggingface/peft

```
from peft import PeftModel

device = torch.accelerator.current_accelerator().type if hasattr(torch, "accelerator") else "cuda"
model_id = "Qwen/Qwen2.5-3B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(model_id, device_map=device)
model = PeftModel.from_pretrained(model, "qwen2.5-3b-lora")

inputs = tokenizer("Preheat the oven to 350 degrees and place the cookie dough", return_tensors="pt")
outputs = model.generate(**inputs.to(device), max_new_tokens=50)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

| 你的情况             | 推荐方法           | 理由                         |
| :------------------- | :----------------- | :--------------------------- |
| **数据量小（<1k）**  | 冻结+LoRA          | 防止过拟合，高效利用参数     |
| **数据量大（>10k）** | 全微调或LoRA       | 全微调效果最好，LoRA性价比高 |
| **计算资源有限**     | QLoRA              | 单卡微调大模型的唯一选择     |
| **需要多任务能力**   | 多任务微调+LoRA    | 共享表示，任务特定适配       |
| **快速实验迭代**     | LoRA/Prompt Tuning | 训练快，切换方便             |
| **生产部署**         | LoRA（合并后）     | 无推理开销，性能接近全微调   |

#### transformers

目前各种Pretraining的Transformer模型层出不穷，虽然这些模型都有开源代码，但是它们的实现各不相同，我们在对比不同模型时也会很麻烦。[Huggingface Transformer](https://huggingface.co/transformers/)能够帮我们跟踪流行的新模型，并且提供统一的代码风格来使用BERT、XLNet和GPT等等各种不同的模型。而且它有一个[模型仓库](https://huggingface.co/models)，所有常见的预训练模型和不同任务上fine-tuning的模型都可以在这里方便的下载。截止目前，最新的版本是4.5.0

Huggingface Transformer 4.5.0需要安装Tensorflow 2.0+ **或者** PyTorch 1.1.0+

Transformers的目的是为了：

- 帮助NLP研究者进行大规模的transformer模型
- 帮助工业界的使用者微调模型并且不是到生产环境
- 帮助工程师下载预训练模型并且解决实际问题

使用预训练模型最简单的方法就是使用pipeline函数，它支持如下的任务：

- 情感分析(Sentiment analysis)：一段文本是正面还是负面的情感倾向
- 文本生成(Text generation)：给定一段文本，让模型补充后面的内容
- 命名实体识别(Name entity recognition)：识别文字中出现的人名地名的命名实体
- 问答(Question answering)：给定一段文本以及针对它的一个问题，从文本中抽取答案
- 填词(Filling masked text)：把一段文字的某些部分mask住，然后让模型填空
- 摘要(Summarization)：根据一段长文本中生成简短的摘要
- 翻译(Translation)：把一种语言的文字翻译成另一种语言
- 特征提取(Feature extraction)：把一段文字用一个向量来表示

https://fancyerii.github.io/2021/05/11/huggingface-transformers-1/



### parquet

Parquet 是一种开源的列式存储文件格式，用于高效存储和分析大数据。与传统的行式存储格式（如 CSV 或 JSON）不同，Parquet 将同一列的数据存储在一起，从而提高了查询性能，并能通过先进的压缩和编码技术降低存储成本。它在 Hadoop 生态系统中被广泛使用，并被 [Apache Spark](https://www.ibm.com/cn-zh/think/topics/parquet)、[Apache Hive](https://www.infoq.cn/article/tsp7pghp8dcbhsdhaxds) 等多种大数据处理框架所支持和采用

随着大模型AI的进一步发展，我们需要存储和处理的数据量呈指数级增长，寻找存储各种数据风格的最佳方式依然是最大的挑战之一。

相信现在几乎已经没有人还会认为关系数据库是依然是唯一数据存储处理方式。

比如说抖音的视频和直播等信息，其原始数据通常是无法实现以传统（关系）数据库方式存储的，或者以传统方式存储它们需要大量的精力和时间，同时会增加总体数据的分析时间。

即使我们还在以某种方式坚持传统方法，结构化方式存储数据，但我们需要设计复杂且耗时的 ETL 工作负载来将这些数据移动到企业数据仓库中。

这种架构方式使得数据分析从业人员，可能被分为两类，一类人主要每天接触 Python负责处理转换数据到关系型数仓，一类主要接触SQL针对关系型数据库进行分析

可以说最近几年 Parquet 已经被认为是当今存储数据的事实上的标准了，它主要有以下几个优势：

1. **数据压缩：**通过应用各种编码和压缩算法，Parquet 文件可减少内存消耗，减少存储数据的体积。
2. **[列式存储](https://zhida.zhihu.com/search?content_id=239272919&content_type=Article&match_order=1&q=列式存储&zhida_source=entity)：**快速数据读取操作在数据分析工作负载中至关重要，列式存储是快速读取的关键要求。
3. **与语言无关：**开发人员可以使用不同的编程语言来操作 Parquet 文件中的数据。
4. **开源格式：**这意味着您不会被特定供应商锁定
5. **支持复杂数据类型**

我们已经提到过 Parquet 是一种基于列的存储格式。

然而，要了解使用 Parquet 文件格式的好处，我们首先需要区分基于行和基于列的数据存储方式。

在传统的基于行的存储中，数据存储为行序列

现在我们举例OLAP数据分析中的一个场景，用户可能会问的一些常见问题：

- 我们卖了多少个球？
- 有多少美国用户购买了 T 恤？
- 客户 Maria Adams 的总消费金额是多少？
- 1 月 2 日我们有多少销售额？

为了能够回答这些问题，引擎必须从头到尾扫描每一行！

因此，为了回答这个问题：有多少美国用户购买了 T 恤，引擎必须执行以下操作

本质上，我们只需要两列中的信息：产品（T 恤）和国家/地区（美国），但引擎将会扫描所有五列数据！

这不是我们想要的最有效的解决方案。

现在让我们看看列存储是如何工作的，是否如您可能猜想的那样

如上图所示，在这种情况下，每一列都是一个单独的实体 - 这意味着，每一列在物理上都与其他列分开的！

回到我们之前的业务问题：引擎现在可以只扫描查询所需的列（产品和国家/地区），同时跳过不必要扫描的列。

在大多数情况下，这种数据跳过应该会提高分析查询的性能。

好的，但在Parquet诞生之前，列式存储早已经出现

实际上我们在OLAP场景中，主要关心两个概念：

1. **投影**
2. **谓词**

投影是指SQL 语言中的**SELECT**语句 - 查询需要哪些列。回到之前的示例，我们只需要“产品”和“国家/地区”列，因此引擎可以跳过扫描其余的列。

谓词是指SQL 语言中的**WHERE**子句 – 哪些行满足查询中定义的条件。在我们的例子中，我们只对 T 恤感兴趣，因此引擎可以完全跳过扫描第 2 行组，其中“产品”列中的所有值都等于袜子！

让我们暂停总结分析一下，因为我希望您认识到各种类型的存储在引擎需要执行的工作方面的差异：

- 行存储——引擎需要扫描所有 5 列和所有 6 行
- 列存储 – 引擎需要扫描 2 列和所有 6 行
- 具有行组的列存储 – 引擎需要扫描 2 列和 4 行

显然，这是一个过于简化的示例，只有 6 行和 5 列，您绝对看不到这三种存储选项之间的性能差异。然而，在现实生活中，当您处理大量数据时，差异就会变得更加明显。

**现在，最急切的问题是：Parquet 是如何“知道”要跳过/扫描哪个行组？**

**Parquet 文件包含元数据**

这意味着，每个 Parquet 文件都包含“有关数据的数据”，例如特定行组内特定列中的最小值和最大值等信息。

此外，每个 Parquet 文件都包含一个页脚，其中保存有关格式版本、架构信息、列元数据等信息。[您可以在此处](https://link.zhihu.com/?target=https%3A//parquet.apache.org/docs/file-format/metadata/)找到有关 Parquet 元数据类型的更多详细信息。

为了优化性能并消除不必要的数据结构（行组和列），引擎首先需要“熟悉”数据，因此它首先读取元数据。

虽然这个操作不算慢，但还是需要一定的时间，因此，如果您从多个小型 Parquet 文件中查询数据，查询性能可能会降低，因为引擎必须从每个文件中读取元数据。

因此，您最好将多个较小的文件合并为一个较大的文件（但仍然不要太大)

那么什么是“大”，什么是“小”呢，这里没有单一的“黄金”数字，因为这和任务的处理瓶颈是相关的，一般我们建议[单个 Parquet 文件的大小至少应为几百 MB](https://link.zhihu.com/?target=https%3A//www.youtube.com/watch%3Fv%3DRxjMibOx__A)。

当说数据湖文件组织格式所提供能力时，主要是 Parquet 文件的版本控制。它还存储事务日志，以便跟踪应用于 Parquet 文件的所有更改。这也称为[ACID 兼容事务](https://link.zhihu.com/?target=https%3A//www.ibm.com/docs/en/cics-ts/5.4%3Ftopic%3Dprocessing-acid-properties-transactions)。

由于它不仅支持 ACID 事务，还支持时间旅行（回滚、审计跟踪等）和 DML（数据操作语言）语句，例如 INSERT、UPDATE 和 DELETE，等数据仓库的行为，所以又被叫做“data warehouse on the data lake”

https://zhuanlan.zhihu.com/p/680143641



### CUDA

cuda

[CUDA](https://zhida.zhihu.com/search?content_id=236817005&content_type=Article&match_order=1&q=CUDA&zhida_source=entity)（Compute Unified Device Architecture）是由[NVIDIA](https://zhida.zhihu.com/search?content_id=236817005&content_type=Article&match_order=1&q=NVIDIA&zhida_source=entity)推出的一种并行计算平台和编程模型。它允许开发者使用通用编程语言（如C、C++）来利用NVIDIA GPU进行并行计算。CUDA提供了一组库、编译器、运行时系统和开发工具，使开发者能够更轻松地利用GPU的计算能力

cuda-toolkit

[CUDA Toolkit](https://zhida.zhihu.com/search?content_id=236817005&content_type=Article&match_order=1&q=CUDA+Toolkit&zhida_source=entity)是用于CUDA开发的软件包，包括了CUDA编译器、运行时库、GPU驱动程序、开发工具等。CUDA Toolkit提供了一整套用于开发GPU加速应用程序的工具和库，以及与NVIDIA硬件和驱动程序的兼容性。

CUDA Toolkit 完整和不完整的区别：在安装了CUDA Toolkit (Pytorch)后，只要系统上存在与当前的 cudatoolkit 所兼容的 Nvidia 驱动，则已经编译好的 CUDA 相关的程序就可以直接运行，不需要重新进行编译过程。如需要为 Pytorch 框架添加 CUDA 相关的拓展时（Custom C++ and CUDA Extensions），需要对编写的 CUDA 相关的程序进行编译等操作，则需安装完整的 Nvidia 官方提供的 CUDA Toolkit

[nvidia-driver](https://zhida.zhihu.com/search?content_id=236817005&content_type=Article&match_order=1&q=nvidia-driver&zhida_source=entity)

nvidia-driver(NVIDIA驱动程序)是操作系统与NVIDIA GPU硬件之间的软件接口。它负责管理GPU硬件的操作、资源分配、性能优化等任务。CUDA依赖于NVIDIA的驱动程序，因为它需要与GPU硬件进行通信以执行并行计算任务。在使用CUDA进行开发时，确保安装了与CUDA兼容的NVIDIA驱动程序是很重要的。

理解驱动的定义，有助于理解nvidia-driver

[cuDNN](https://zhida.zhihu.com/search?content_id=236817005&content_type=Article&match_order=1&q=cuDNN&zhida_source=entity)

CuDNN（CUDA Deep Neural Network library）是NVIDIA提供的用于**深度学习任务的GPU加速库**。它是专门为使用CUDA（Compute Unified Device Architecture）平台的深度学习框架而设计的，旨在优化深度神经网络的计算性能。

CuDNN提供了一系列**高度优化的基本操作和算法，例如卷积、池化、归一化等**，这些操作是深度学习中常见的基本构建块。通过使用CuDNN，深度学习框架可以利用NVIDIA GPU的并行计算能力，加速神经网络的训练和推理过程。



