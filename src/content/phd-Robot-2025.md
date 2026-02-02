---
title: 机器人研究进展
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，
toc: true
thumbnail: 
---

​	本文主要研究的是2025年的会议论文

<!--more-->

## 经典论文

LSTM：排队处理的串行机制

**Deep Residual Learning for Image Recognition**：renet。传统深层网络在训练时会遇到 **梯度消失/爆炸** 和 **退化问题**，残差网络，通过引入 **“残差块”（Residual Block）** 和 **“跳跃连接”（Skip Connection）**，成功训练了**极深的神经网络**（如 ResNet-152），并大幅提升了图像识别精度

https://arxiv.org/html/1706.03762v7 Attetion is all you need！ 自注意力机制，并行计算两个词的关联程度

**An Image is Worth 16×16 Words: Transformers for Image Recognition at Scale**：**Vision Transformer（ViT）**多模态transformer的起点

**Denoising Diffusion Probabilistic Models (DDPM)**：diffusion 模型，图像视频新纪元

**Learning Transferable Visual Models From Natural Language Supervision**：Clip，图像编码和文本编码模型，文字和图像互相理解

GAN: GAN 和 DDPM 都属于**生成模型**，但方法截然不同：GAN 是对抗式，DDPM 是扩散式。
而 **CLIP 常与扩散模型（如 Stable Diffusion）结合**，提供文本引导生成的能力

https://arxiv.org/abs/2512.24695 Nested Learning: The Illusion of Deep Learning Architectures

https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf gpt论文

## RSS 2025



## ICRL 2025



## ICRA 2025



## IROS 2025



## 其他

看到一篇文章是pi0团队的成员写的 描述了2025年机器学习的困境和展望 对比了robot learning和LLM 训练的区别： https://vedder.io/misc/state_of_robot_learning_dec_2025.html

https://d197for5662m48.cloudfront.net/documents/publicationstatus/295096/preprint_pdf/e2cb33a1656799982f21da528c242b0a.pdf https://www.techrxiv.org/users/934012/articles/1366553-a-survey-on-reinforcement-learning-of-vision-language-action-models-for-robotic-manipulation?commit=a7c4cf9ff94956e6d3c7f6eb1ff10a6eb2fc05e4 综述 https://zhuanlan.zhihu.com/p/1985283502921490441



https://vla-survey.github.io/ vla综述 VLA论文大全

https://arxiv.org/abs/2512.22983



VLA行业调研 https://pdf.dfcfw.com/pdf/H3_AP202507071704760168_1.pdf?1751961651000.pdf

我们通过对机器人 VLA 与汽车 VLA 进行对比分析以探究机器人VLA数据闭环构建中面临的突出问题，本质上汽车 VLA 与机器人 VLA 所面临的应用场景与任务的不同决定了二者拥有不同的商品属性和标准化程度。汽车可以被视为一种低自由度的特殊机器人，所面临的场景和任务是结构化场景下的单一任务，根本上决定了汽车是感知方式、输出控制、自由度、本体结构都相对标准化的耐用消费品，底层硬件结构的统一性和场景、任务单一性决定了其数据采集方式、所采集的数据、模型设计的标准化；而广义的机器人由于所面临的场景与任务需求多样化，更多偏向于非标的可选消费电子类产品，更需要在模型设计、本体结构标准化、数据采集效率、任务覆盖范围等多个条件中进行权衡。二者场景、任务的不同，决定了二者商品属性和标准化程度不同进而决定了二者上层结构的差异。我们尝试从数据、仿真环境、端侧算力等具体角度对比汽车VLA 与机器人VLA的差异性。整体来看，机器人的数据闭环构建难度远超车端，其工程部署还需要解决标准化、本体交互能力、模型闭环等重要问题，机器人 VLA 所面临的各种问题汇集在一起突出表现为当前还无法进行有效的数据收集进而构建完整数据闭环，而无法Scaling 的具身就无从谈起智能化，因此我们认为机器人 VLA 模型或智能化当前还处于前期探索阶段，相较于汽车VLA专注于工程化，机器人 VLA 更是一个科研问题

机器人 VLA 模型所需的数据更为复杂多样，核心原因在于机器人所面临的场景和任务更为多样化，泛化能力要求远高于汽车。1）应用场景与任务不同，自动驾驶的汽车可以被理解为一个特殊的机器人，其所面临的结构化道路场景和执行的驾驶任务都较为单一；机器人如人形机器人所面临的场景和任务非常丰富，部署场景从家庭、工厂等封闭式场合到公共服务场合等开放式场景，任务从家政服务到工厂务工，几乎能囊括人类日常生活的各方面，因此从训练数据的多样性角度，其数据需求远超汽车；2）模型能力要求不同，从应用场景和任务出发，车端 VLA 重点在于提升端侧推理效率以增强模型的动态博弈能力，更加注重感知数据输入和 2D 轨迹规划输出以及模型实时决策，其数据维度较低（可以理解为一个动作专用模型利用同构低维数据，强化学习反复迭代提升性能）；机器人VLA 则更为注重模型的泛化性，其所需求的数据除感知数据以外，更为关注本体与真实世界的物理交互数据（如力反馈、摩擦力数据等），且更多输出 3D 空间动作规划，所需数据的维度、复杂度更高。因此在假设完美完成一类任务所需数据规模差异不大的前提下，机器人所需的数据从多样性到复杂度相较于车端都有较大提升

Open VLA 为一个 7B 规模的 VLA 模型，能够在接收信息后预测7 维的机器人控制动作，其模型架构主要由输入模块、LLM 主干两部分构成

输入模块由两个视觉编码器、小型两层 MLP 投影器（用于映射）、Llama 语言编码器构成，其中视觉编码器部分包含预训练的 SigLIP 和 DinoV2 模型，输入图像patch分别通过两个编码器得到结果特征向量，相较于 CLIP 或仅 SigLIP 编码器，添加DinoV2 编码器有助于提升模型空间推理能力，更能理解物体间的准确位置关系。LLM 主干部分为一个 7B 规模的 Llama 2 大语言模型并在Open X-Embodiment 机器人数据集上进行了微调，接收图像、文本等多模态信息并进行推理输出action token。模型利用栅格法，将机器人连续的动作离散化分散到 256 个栅格中，每一个栅格可以被视作一个token。当机器人动作被处理为 token 序列后，VLA 模型即能够以标准的自回归方式预测下一个token为目标进行训练

ViLLA 架构的亮点在于对大规模互联网异构视频数据的高效利用。ViLLA架构由VLM＋MOE 组成，主要由 VLM、Latent Planner、Action Expert 三大模块协同工作，相较于传统的VLA，ViLLA 通过预测隐式动作标记，弥合图像-文本输入与机器人执行动作之间的差距，在真实世界的灵巧操作和长时任务方面表现较好，超过了彼时已有的开源SOTA模型；同时LAM的引入，使模型不仅能够从互联网数据中增强语义理解和场景理解能力，还能够直接从人类视频演示中进行轨迹动作知识的迁移，实现对不同互联网数据的高效利用

VLM（多模态大模型）：采用 InternVL-2B，接收多视角图像、力信号、语言输入等，进行通用场景感知和指令理解。 Latent Planner（隐式规划器）：LP 是 MoE 中的一组专家，其基于VLM的输出结果预测隐式动作标记以进行高层级动作规划。为了大规模利用互联网视频数据和训练Latent Planner，ViLLA 架构在云端引入了 LAM 模型来建模互联网人类动作视频当前帧和历史帧之间的隐式变化，然后使 Latent Planner 预测这些变化以进行学习，从而将异构数据源中真实世界的动作知识迁移到模型能力中。 Action Expert（动作专家）：AE 是 MoE 中的另一组专家，通过采用扩散模型，在去噪过程中实现低层级的精细动作序列生成。Action Expert 结构上与Latent Planner 类似，与VLM主干网络共享 Transformer 结构但使用独立 FFN 和投影矩阵



https://www.bilibili.com/video/BV1HorwB7Erd/?spm_id_from=333.1007.tianma.1-1-1.click 具身智能2025论文-tea团队



 https://github.com/jonyzhang2023/awesome-embodied-vla-va-vln



## 创意

灵巧手太复杂，直线电机太简单 能不能有一种比pick-place更灵活的执行器 且可以考虑是否有触觉 是否有软体 再加上泛化VLA的能力

数据采集 scaleup的问题

sim2real的问题

VLA可以做但是需要更灵活的动作，比如跳舞机器人或者摇花手机器人、演员机器人(表情陪伴机器人)

如何准确地抓取物体 识别物体🤔

感觉解决simtoreal的一种方式是realtosim。也就是把实体机械臂的数据通过数据协议发送给仿真环境 https://real-to-sim-to-real.github.io/RialTo/

https://mp.weixin.qq.com/s/SI3IEdjdKCSog7Udqlr_Bg
