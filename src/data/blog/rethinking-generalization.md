---
author: 'Lanxinmob'
title: 'rethinking generalization'
postSlug: 'summer-diary-17x'
tags:
  - '笔记'
  - '论文'
ogImage: ''
description: 'interesting and not well understood'
pubDatetime: 2025-07-24T09:00:00Z
---
[Understanding deep learning requires rethinking generalization - Chiyuan Zhang, Samy Bengio, Moritz Hardt, B. Recht, O. Vinyals](https://www.semanticscholar.org/paper/54ddb00fa691728944fd8becea90a373d21597cf) (Citations: 4641) [PDF](D:\poem\src\data\blog\download\1611.03530.pdf)

大型神经网络的泛化能力，并非源于 “只能学习数据中的真实规律”，而是源于其参数规模带来的超强表达能力—— 它们既能拟合有意义的真实数据（从而在实际中泛化），也能轻松拟合无意义的随机数据（理论上的过拟合能力）。而传统依赖 “正则化限制过拟合” 的解释，无法涵盖这一现象。这一发现推动了对神经网络泛化本质的重新思考（例如从 “归纳偏好”“数据分布特性” 等新角度探索）

优化过程本身的隐式约束（如随机梯度下降的噪声、参数更新路径的特性）；
数据分布的内在结构（真实世界数据并非完全随机，存在可学习的规律）；
模型的归纳偏好（神经网络的架构设计可能使其更倾向于学习 “简单”“平滑” 的模式）

**传统观点中 “模型族特性决定泛化” 的逻辑无法解释：为什么一个能轻松拟合无意义数据（理论上过拟合风险极高）的模型族，在真实数据上反而能表现出好的泛化能力？** 毕竟按照传统认知，能拟合随机数据的模型应该是 “过于复杂” 的，泛化能力会很差。 

pure empirical 
studies that reveal “interesting and not well understood 
behavior”c in the call-for-papers. 

generalization bounds
