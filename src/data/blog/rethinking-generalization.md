---
author: 'Lanxinmob'
title: 'rethinking generalization'
postSlug: 'summer-diary-17x'
featured: true
draft: false
tags:
  - '笔记'
  - '论文'
ogImage: ''
description: 'interesting and not well understood'
pubDatetime: 2025-07-24T09:00:00Z
---
## 重新思考泛化

[Understanding deep learning requires rethinking generalization - Chiyuan Zhang, Samy Bengio, Moritz Hardt, B. Recht, O. Vinyals](https://www.semanticscholar.org/paper/54ddb00fa691728944fd8becea90a373d21597cf) (Citations: 4641) [PDF](D:\poem\src\data\blog\download\1611.03530.pdf)

>  The effective capacity of neural networks is sufficient for memorizing the entire data set. 2. Even optimization on random labels remains easy. In fact, training time increases only by a small constant factor compared with training on the true labels. 3. Randomizing labels is solely a data transformation, leaving all other properties of the learn ing problem unchanged.
>
>  Deep neural networks easily fit random labels.

- 大型神经网络的泛化能力，并非源于 “只能学习数据中的真实规律”，而是源于其参数规模带来的超强表达能力—— 它们既能拟合有意义的真实数据（从而在实际中泛化），也能轻松拟合无意义的随机数据（理论上的过拟合能力）。而传统依赖 “正则化限制过拟合” 的解释，无法涵盖这一现象。

- 优化过程本身的隐式约束和正则，如随机梯度下降的噪声、参数更新路径的特性
- 数据分布具有内在结构，真实世界数据或某些”随机“数据并非完全随机，其实存在可学习的规律，保证了泛化的可能。
- 不同模型有不同归纳偏好，神经网络的架构设计可能使其更倾向于学习 “简单”“平滑” 的模式，从而实现较强泛化。

- 传统观点中 “**模型族特性决定泛化**” 的逻辑同样无法解释：为什么一个能轻松拟合无意义数据（理论上过拟合风险极高）的模型族，在真实数据上反而能表现出好的泛化能力？毕竟按照传统认知，能拟合随机数据的模型应该是 “过于复杂” 的，泛化能力会很差。 

> pure empirical studies that reveal “interesting and not well understood behavior”c in the call-for-papers. 

- 呼吁纯粹的实证研究来揭示 “有趣且尚未被充分理解的行为”。
- 泛化界限generalization bounds，泛化误差≤训练误差+一个与模型和数据相关的复杂项，试图找到泛化误差的上界。对tansformer因其极高的复杂度使其界限十分宽泛，但其具有极强泛化能力，也就是需要重新思考泛化的原因。

