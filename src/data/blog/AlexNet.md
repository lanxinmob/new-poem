---
author: 'Lanxinmob'
title: 'AlexNet深度学习奠基作'
postSlug: 'summer-diary-x1'
featured: false
draft: false
tags:
  - '笔记'
  - '论文'
ogImage: ''
description: '李沐论文精读 AlexNet'
pubDatetime: 2025-07-22T09:00:00Z
toc: true
---

## [AlexNet](https://papers.nips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html) 深度学习奠基作

- > We initialized the weights in each layer from a zero-mean Gaussian distribution with standard de
  > viation 0.01. We initialized the neuron biases in the second, fourth, and fifth convolutional layers,
  > as well as in the fully-connected hidden layers, with the constant 1. This initialization accelerates
  > the early stages of learning by **providing the ReLUs with positive inputs.** We initialized the neuron
  > biases in the remaining layers with the constant 0.
  
  偏移本质上来说如果数据比较平衡的话应该偏置初始化为0，这里说发现某些层偏置初始化为1有比较好的效果，李沐老师说大家觉得全部初始化为0效果也不差，所以这个技术后面用的也比较少。(?这里似乎说是初始输入更多为正，避免初期大量神经元失活)
  
- 当时做dropout觉得好像是做多个模型的融合，后面大家发现也不是这样，几年后又写了一篇文章说dropout在现行模型下等价一个L2正则项。

- ImageNet 完整其实是更大不止120万，完整有八百九十万，一万多类，这样训练出来模型效果也会更好点，奇怪的是大家对ImageNet印象好像总是拿120万的图片。

- SGD(随机梯度下降)调参比较难调，但是大家发现sgd的噪音对模型的泛化是有好处的，从这篇文章以后sgd成为了最主流的优化算法。虽然现在有了收敛速度更快的Adam/AdamW，但泛化能力上sgd还是保持着优势.

- > Notice the specialization exhibited by the two GPUs, a result of the restricted connec tivity described in Section 3.5. The kernels on GPU 1 are largely color-agnostic, while the kernels on on GPU 2 are largely color-specific. This kind of specialization occurs during every run and is independent of any particular random weight initialization (modulo a renumbering of the GPUs)

   **未解之谜**，多次训练总是一个GPU学习到的模式与颜色相关，另一个学习到的模式与颜色无关。**(?)**

- > Another way to probe the network’s visual knowledge is to consider the feature activations induced
     > by an image at the last, 4096-dimensional hidden layer. If two images produce feature activation
     >  vectors with a small Euclidean separation, we can say that the **higher levels of the neural network**
     >  **consider them to be similar.** Figure 4 shows five images from the test set and the six images from
     >  the training set that are most similar to each of them according to this measure. Notice that at the
     >  pixel level, the retrieved training images are generally not close in L2 to the query images in the first
     >  column. For example, the retrieved dogs and elephants appear in a variety of poses.  

   虽然很多时候不知道在学什么，但是有些神经元还是很有对应性，一些底层的神经元学到偏局部信息，可解释性为人诟病但也在慢慢研究。

- 读论文主要专注于方法上的东西，工程上的一些细节可以先略过，包括实验上一些东西主要看一下效果，复现时再把那些东西搞懂。

- 每个人都需要有自己的观点，你的观点不对没关系，没有人的观点是对的，但是你一定有自己独特的观点，这样子才能够不一样。如果你跟别人的观点都一样，那么你对这个世界贡献，可能就没那么多了。

  --李沐

  
---

## 后言:

这里是李沐老师21年出的论文精读视频，因为我只是刚接触，这几年的工作并不熟悉，但就凭我刚接触的程度也能感受到其中许多东西在这几年再次发生了变化，有的继续顺延发展有的又回到了最初的方向。

