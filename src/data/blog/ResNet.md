---
author: 'Lanxinmob'
title: 'ResNet'
postSlug: 'summer-diary-17'
tags:
  - '笔记'
ogImage: ''
description: '李沐论文精读 ResNet'
pubDatetime: 2025-07-24T09:00:00Z
toc: true
---

[Deep Residual Learning for Image Recognition - Kaiming He, X. Zhang, Shaoqing Ren, Jian Sun](https://www.semanticscholar.org/paper/2c03df8b48bf3fa39054345bafabfeff15bfd11d) (Citations: 195310) [PDF](D:\poem\src\data\blog\download\1512.03385.pdf)

## ja

那些经典的文章不一定要原创地提出什么东西或技术，很可能就是将以前的一些东西巧妙组合起来，解决一个现在比较难的问题。

bottleneck极大地减少了参数数量和计算复杂度（FLOPs）。

1. 第一个卷积层参数：`(3×3 核大小) × 256 (输入通道) × 256 (输出通道) = 589,824`
2. 第二个卷积层参数：`(3×3 核大小) × 256 (输入通道) × 256 (输出通道) = 589,824`

- **总参数量 ≈ 1,180,000**

**降维/压缩 (Squeeze)**: 先用一个 **`1x1` 的卷积**，将通道数从 256 **减少**到 64。

**核心卷积 (Convolve)**: 然后用一个 `3x3` 的卷积来处理这个“变瘦”了的特征图，输入和输出通道都是 64。

**升维/扩张 (Expand)**: 最后再用一个 **`1x1` 的卷积**，将通道数从 64 **恢复**到原来的 256。

**Shortcut（快捷连接）**：它的主要作用是解决“梯度消失”

这里的梯度相比没有残差只是一个连乘，还加上了一个本身的梯度这样就能保持梯度一直够大，就能一直训练下去

只要保证梯度一直够大，其实你最后的结果就会比较好，那些其实不算收敛只是梯度没了跑不动了

加上这些东西其实是降低了模型的复杂度，比没加这些东西更容易走到那个想要的道路

回到重新思考过拟合、泛化的问题，为什么像现在transformer 上千亿参数没有过拟合

这里是在feature维度，gradiant boosting的residual是在标号上做residual

