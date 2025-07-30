---
author: 'lanxinmob' 
postSlug: 'bert'
title: 'BERT'
tags:
  - '笔记'
  - '论文'
  - '自然语言处理'
description: '李沐论文精读 Pre-training of Deep Bidirectional Transformers for Language Understanding'
pubDatetime: 2025-07-27T09:00:00Z
---
[BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding - Jacob Devlin, Ming-Wei Chang, Kenton Lee, Kristina Toutanova](https://www.semanticscholar.org/paper/df2b0e26d0599ce3e70df8a9da02e51594e0e992) (Citations: 95770) [PDF](D:\poem\src\data\blog\download\1810.04805.pdf)

[BERT 论文逐段精读【论文精读】](https://www.bilibili.com/video/BV1PL411M7eQ/)
- BERT出现使我们可以在一个大的数据集上训练一个深的神经网络，训练好后能帮助一大批NLP任务构建自己的神经网络，极简化他们的训练又提升了它们的性能。使NLP出现质的飞跃。
- 但是BERT并不是第一个提出在NLP做预训练的，而是BERT让这个方法出圈了
- 摘要写跟那些工作（ELMO,GPT）有关，和它们的区别是什么
- 展现优点既写绝对精度也写跟别人比的相对精度
- BERT是transformer 编码器的双向表示，使用无标签的左右联合上下文来预训练

## Introduction
>BERT alleviates the previously mentioned unidirectionality constraint by using a “masked language model” (MLM) pre-training objective, in spired by the Cloze task (Taylor, 1953). 

- 这里说采用带掩码语言模型通过随机mask一些token来做完型填空以打破单向限制，受到1953年一篇论文启发(**怎么翻出1953年的论文的？过去这么多年NLP领域没人用过吗**)此外还做了一个“下一个句子预测”的任务，给模型两个句子判断是否相邻，让模型学习句子层面的一些信息。
## Conlusion
- 贡献上在句子层面和词元层面的任务都做到了最好成绩
- 最近一些实验表明用非监督预训练是非常好的，即使资源不多也能享受深度神经网络
- 用大量没有标签数据训练出来的模型可能比稍微小些的有标签数据训练出来的要好
- 把ElMO双向的想法和GPT的transformer的东西结合起来就有了EBRT
- 这篇论文自己认为自己贡献主要在“双向性” （**之前也有人做过双向的模型简单拼接为什么效果不好呢？**）
- 很多时候我们工作是a+b的工作或者把一个技术用在另一个领域的问题，如果你的工作简单好用就是好的，说不定哪天就出圈了。 
### 用预训练模型做特征表示完成下游任务的两种策略：
- feature-based and fine-tuning
- ElMO是预训练的双向LSTM，用于特定任务时将它的输出作为额外特征拼接到原本输入特征一起输入模型，这些额外特征已经有了比较好的表示，模型训练起来就比较容易，也就是feature-based
- GPT是基于transformer解码器部分，用于下游任务时简单微调整个模型预训练参数，也就是fine-tuning
- 这两个都是用相同目标函数，单向的语言模型来学习

### BERT
- 用无标签数据预训练BERT模型，微调时各个下游任务用预训练参数初始化同一个BERT模型，然后用各自有标签数据去训练微调整个模型数据
- 为了处理各种各样下游任务，BERT输入可以是一个句子也可以是一个句子对，这里的句子可以是任意长度一段context而不只是我们平时说的一个句子
- WordPiece是一种分词方法。对于不常见的词，它会将其拆分成更小的“子词”（subword）。例如，embedding 可能会被拆分成 em 和 ##bedding。
- 每个输入序列的第一个Token是一个特殊的分类token [CLS]，在模型处理完所有信息后，这个 [CLS] 标记对应的最终隐藏状态向量，将被用作整个序列的聚合表示，用来进行分类任务。
- 每个sentence用特殊的 [SEP] token将它们隔开。也为每个词添加一个embedding，以表明它属于句子A还是句子B。
- 对每一个词元进入BERT的向量表示是本身词元embedding加上在那个句子的embedding加上在那个位置的embbeding
- 在BERT里不管是属于哪个句子的embedding还是位置的embedding，对应向量表示都是通过学习得来的。
### 总参数量
#### 词嵌入层参数 (V × H)
- 一个巨大的查询表（Lookup Table）。表中有 V 行（对应词典里的每一个词），每一行是一个长度为 H 的向量。将输入的单词（Token）转换成计算机能理解的向量。
#### Transformer Block参数
1. 多头自注意力层 (Multi-Head Attention)
- 它有四个主要的权重矩阵：用于将输入映射成Q（Query）, K（Key）, V（Value）的三个矩阵，以及最后整合所有头信息的输出矩阵 W 
在高效实现中，这四个矩阵的尺寸都是 H × H。
参数量 = 4 × H × H = 4H²
2. 前馈网络层 (Feed-Forward Network)
- 第一个线性层将维度从 H 扩展到 4H。其权重矩阵尺寸为 H × 4H，参数量近似 4H²。
第二个线性层将维度从 4H 再缩减回 H。其权重矩阵尺寸为 4H × H，参数量也近似为 4H²。
参数量 = 4H² + 4H² = 8H²
- 一个完整的Transformer Block的参数量就是：
4H² (注意力) + 8H² (前馈网络) = 12H²
- L层的总参数量就是 L × 12H²。

词表大约36k，可以算得参数数量和论文中一致，BERTBASE (L=12, H=768,Total Parameters=110M) and BERTLARGE (L=24, H=1024,Total   Parameters=340M)
### Masked LM
- 将所有WordPiece token中的百分之15替换成一个掩码，但有个问题是在fine-tuning时数据中是没有[MASK] token的，所以对这15%的token有80%是替换成[MASK]标记,10%替换成一个随机的词元，还有10%什么都不干，通过多样化的替换方式，减少 [MASK] 标记带来的预训练与微调差异，提升模型的适应性。
### Next Sentence Prediction (NSP)
- 这个是为了训练模型理解句子关系的能力，学习一些句子层面的信息。具体来说每个样本中有sentence A and B,B有50%概率在原文确实是在A句子后面(labeled as IsNext), 还有50%是随机的一个句子(labeled as NotNext)。 这个训练能极大提升在Question Answering (QA) and Natural Language Inference (NLI)的效果。
## Experiment
#### SQuADv1.1
- 这个任务是有一个问题给你一段文字，在这段文字中找到答案，其实就是对每一个词元算它是答案开始的概率和答案结束的概率。
> We fine-tune for 3 epochs with a learningrate of 5e-5 and a batchsize of 32.
- 这个误导了很长一段时间，大家发现用BERT同样参数去微调结果很不稳定，问题在于训练得不够，3epoch太少了，BERT用的优化器是Adam的不完全版，在一个较短训练的时间时会有影响。
- 实验表明 BERT 对微调方法和基于特征的方法都有效，但是微调效果更好，所以用BERT的话应该用微调。
- 写作上写了用双向性或者别的带来了什么也要写带来了什么不好的，BERT用了编码器带来了一些好处，但是做机器翻译，生成类的东西可能就没那么方便了，当然NLP领域很大一部分是分类问题，所以在NLP领域有很大影响。
- 现在回头看，BERT展现一个完整解决问题的思路，符合大家对一个深度学习模型的期望，在一个很大数据集上训练好可以用在很多小问题上。
- 另一方面BERT也就是一个更大一点的模型取得一些更好的成绩，但是更好成绩的结局就是被后面的工作所超越。BERT与GPT相比大概是一个同期的工作，虽然BERT结果比GPT好那么一些，但是思路是非常一样的，从引用来看BERT是GPT的十倍(21年的时候)，影响至少10倍以上。（**这是为什么？**）



