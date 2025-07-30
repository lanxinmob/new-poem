---
author: 'lanxinmob' 
postSlug: 'codex'
title: 'Autopilot'
tags:
  - '笔记'
  - '论文'
description: '李沐论文精读 DeepMind AlphaCode OpenAI Codex'
pubDatetime: 2025-07-29T09:00:00Z
---
## OpenAI Codex
[Evaluating Large Language Models Trained on Code - Mark Chen, Jerry Tworek, Heewoo Jun, Qiming Yuan, Henrique Pondé, Jared Kaplan, Harrison Edwards, Yura Burda, Nicholas Joseph, Greg Brockman, Alex Ray, Raul Puri, Gretchen Krueger, Michael Petrov, Heidy Khlaaf, Girish Sastry, Pamela Mishkin, Brooke Chan, Scott Gray, Nick Ryder, Mikhail Pavlov, Alethea Power, Lukasz Kaiser, Mo Bavarian, Clemens Winter, P. Tillet, F. Such, D. Cummings, Matthias Plappert, Fotios Chantzis, Elizabeth Barnes, Ariel Herbert-Voss, William H. Guss, Alex Nichol, Igor Babuschkin, S. Balaji, Shantanu Jain, A. Carr, Jan Leike, Josh Achiam, Vedant Misra, Evan Morikawa, Alec Radford, M. Knight, Miles Brundage, Mira Murati, Katie Mayer, Peter Welinder, Bob McGrew, Dario Amodei, Sam McCandlish, I. Sutskever, Wojciech Zaremba](https://www.semanticscholar.org/paper/acbdbf49f9bc3f151b93d9ca9a06009f4f6eb269) (Citations: 5892) [PDF](D:\poem\src\data\blog\download\2107.03374.pdf)

[OpenAI Codex 论文精读【论文精读】](https://www.bilibili.com/video/BV1iY41137Zi)
- 基于GPT3的架构，使用Github上的Python文件作为数据集训练使模型能根据文档生成代码。
- 使用的编程问题是自己手动实现的164个问题避免数据泄漏，为了提高精度加入和题库相近的答案来微调。
- 使用预训练的模型进行微调得到的结果可能精度更好，也可能精度不会更好，但通常来说收敛速度都会加快。这里就说到codex从预训练模型开始训练时并没有观察到提升但是确实收敛的更快。
### Results
- 模型首先为下一个可能的词生成一个原始分数列表（logits）。用这些原始分数除以一个设定的温度 t。如果 t < 1.0，高分和低分的差距会被拉大，概率分布变得更尖锐。如果 t > 1.0，高分和低分的差距会被缩小，概率分布变得更平坦。将经过温度调整的分数通过 Softmax 函数，转换成最终的概率分布。
- 在评估结果上，这里使用 pass@k 的评估方式，针对 HumanEval 数据集里的每一个编程问题，模型会独立地生成 k 个不同的代码解决方案，有一个能够成功通过所有的单元测试，我们就认为模型解决了这个问题。最终的 pass@k 分数是 通过的问题数量 / 总问题数量 的百分比。
- 而这里生成每个方案用的是 p=0.95 的核采样的方式。核采样(Nucleus Sampling)不是简单地取各个位置固定数量（Top-k）或概率最高的词组成最终代码，而是根据概率分布动态地选择一个最小的词汇集合，使得这个集合中所有词的累积概率总和超过一个预设的阈值 p。然后在这个“核”中进行随机采样，抽取一个词作为输出。这样每次生成结果不同，包含更多可能性，可以产生更多样化和富有创造性的结果。

- 除了核采样还有一种 beam search，会先选出概率最高的 k 个词，作为 k 个候选序列的开头，然后分别预测下一个最可能的词，再从所有这些新的可能性中，再次选出总概率最高的 k 个序列。重复这个过程，直到生成完整的序列或达到预设的结束标志。

- 在现实世界中没有Oracle（先知），用户也不会提供单元测试，但是必须从生成的 k 个可能方案中，直接挑出最可能是正确的那个作为输出。在这种情况下用一种启发式方法来自动筛选最佳样本非常有效，为 k 个样本计算其平均词元对数概率。那个分数最高（最接近0）的样本，被认为最有可能是正确答案。文章指出，用这个方法来挑选样本，效果比随机挑选要好得多。

### Limitations
- 同样，样本有效性不高。
- 当 docstring 长度增长，生成的代码准确性会指数下降。
- 对一些精确复杂的操作理解有问题，如 binding attributes to variable
- 比较标准的一个用新技术来解决问题，不怎么去改变模型，主要重心放在准备数据，分析模型的结果，如何进一步提高精度。
## DeepMind AlphaCode
[Competition-level code generation with AlphaCode - Yujia Li, David Choi, Junyoung Chung, Nate Kushman, Julian Schrittwieser, Rémi Leblond, Tom, Eccles, James Keeling, Felix Gimeno, A. D. Lago, T. Hubert, Peter Choy, Cyprien de, Masson d’Autume, Igor Babuschkin, Xinyun Chen, Po-Sen Huang, Johannes Welbl, Sven Gowal, Alexey, Cherepanov, James Molloy, D. Mankowitz, Esme Sutherland Robson, Pushmeet Kohli, Nando de, Freitas, K. Kavukcuoglu, O. Vinyals](https://www.semanticscholar.org/paper/5cbe278b65a81602a864184bbca37de91448a5f5) (Citations: 1490) [PDF](D:\poem\src\data\blog\download\2203.07814.pdf)

[DeepMind AlphaCode 论文精读【论文精读】](https://www.bilibili.com/video/BV1ab4y1s7rc)

- 在OpenAI基础上走了一大步，允许有更长的文档，能生成更复杂的代码。
- 数据集使用Github上爬下的语言，包括了各种语言不仅仅是python了。
- 预训练集相比 Codex 更大了，花了更多精力在数据清理，给每个问题生成更多测试样例来避免false positive，降低low rate。
- 架构上采用一个完整的transformer架构进行改进，因为需要理解一个比较长的文本所以需要编码器来理解而不仅是用解码器生成代码。在多头注意机制上，Query的投影比K-V的投影要多一些，减少了计算，内存上好一些。block上，解码器层数是差不多编码器层数的6倍。
- 同时使用了一个非对称的机制，编码器序列长度是1536，基本上能覆盖问题的长度，解码器序列长度是768，因为差不多文本是生成代码的两倍，这里解码器比较深所以尽量优化来减小计算量，transformer计算复杂度和序列平方成正比。
### Fine-tuning
- 微调时使用了额外的修改进行提升，修改如下。
- Tempering t=0.2 , 使分布更尖锐提高了对生成结果的要求，防止过拟合。
- 数据集中一个问题有多个解有的正确有的不正确，为了都利用起来，使用前置conditioning 在每个solution前面加一行 正确就是 correct solution。此外增加一个辅助预测 value 的任务也就是增加一个二分类的损失，预测当前解法正确还是错误。

### Large scale sampling
- 为了确保生成的大量样本不是千篇一律的，采用经过微调的模型，为每个问题生成一半Python和一半C++的解法。在输入给模型的自然语言提示中，随机化题目的标签和评分。采用一个相对较高的采样温度来增加生成过程的随机性，但其实也就0.25。

### Sensitivity
- 实验中发现简单的描述得到正确率更高，也就是模型对比较复杂的文字理解还是相对比较困难。
- 当改变Tag为暴力搜索或数论时模型是可以生成对应代码的，但实际问题中你并不明确知道标签是什么，这里解决方法是每次采样时随机加一些标签进去。
### Clustering
- 经过筛选候选样本数量依然太多，如果从中随机挑选，会非常浪费提交预算，因为很多样本可能只是语法不同，但语义等价，所以这里通过程序的行为进行聚类。
- 首先训练了一个小模型用于对题目描述生成新的测试输入以区分不同代码行为，即输出相同为一类。然后按照聚类的大小，从最小的聚类开始，逐一向最大的聚类挑选，每个聚类中先只选一个解法，来增加多样性。如果聚类的总数少于10个，就从最小的聚类开始循环，（跳过已提交的）直到提交满10个为止。
- AlphaCode 要采样十万到百万级别规模的解，如果纯用解码器就会很贵了