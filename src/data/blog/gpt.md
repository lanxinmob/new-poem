---
author: 'lanxinmob' 
postSlug: 'gpt'
title: 'GPT'
tags:
  - '笔记'
  - '论文'
  - '自然语言处理'
description: '李沐论文精读 GPT,-2,-3'
pubDatetime: 2025-07-28T09:00:00Z
---
[GPT，GPT-2，GPT-3 论文精读【论文精读】](https://www.bilibili.com/video/BV1AF411b7xQ)
- GPT的核心技术是用transformer的解码器部分训练大量没有标号的文本数据获得一个预训练的语言模型，再用它在子任务上微调。
- GPT是2018年6月出现，10月BERT出现，在差不多的都是一亿参数量下BERT base效果比GPT好，BERT large就更甩了一条街。同一批人吸取教训继续沿着原来路线走，隔了一年零四个月有了GPT2，训练了一个更大的模型，发掘了它的zero-shoot能力，但是走得比较远效果没那么惊艳。又隔一年零三个月，推出了数据和模型是GPT2的100倍的GPT3，也就是一千亿的量级，效果非常惊艳。
- GPT系列是OpenAI的团队完成，Transformer和BERT都是Google的研究组完成的，在学术界影响力GPT系列似乎不如BERT。
- OpenAI选择去做更大的问题，技术上实现更难一些，出效果更难一些，要出惊艳的效果如GPT3几乎没有别的团队能复现这个结果。因为整个公司还是想做强人工智能所以必须选择大一点的问题。而Google的这些独立研究组想解决的问题都比较小，如Transformer一开始就是想做一个机器翻译，BERT就是想把计算机视觉成熟的那一套先训练一个预训练模型再微调出子模型的思路套用到NLP。

[Improving Language Understanding by Generative Pre-Training](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf)
### Unsupervised pre-training
- 目标是最大化根据前k个词预测到第k+1个词的对数似然，是用于无监督训练的语言建模目标
###  Supervised fine-tuning
- 有监督任务的目标（最大化根据前k个词预测到对应标签的对数似然）加上语言建模目标作为辅助，提升有监督模型的泛化能力，加快收敛速度
### Task-specific input transformations
- 通过构造输入格式和输出层设计运用到NLP中四大常见应用
#### Classification
- 输入格式：[Start] + Text + [Extract]
- Transformer 编码整个序列  
- 提取 [Extract] 的 hidden state，送入 Linear 分类器 → 输出分类结果  
   
#### Entailment

- 输入格式：[Start] + Premise + [Delim] + Hypothesis + [Extract]
- [Delim] 是分隔符（如 [SEP]）    
- Transformer 编码整个序列  
- [Extract] 的hidden state → Linear → 判断 entailment / contradiction / neutral     
#### Similarity
- 输入格式（两路）： 
 -  路1: [Start] + Text1 + [Delim] + Text2 + [Extract]  
 -  路2: [Start] + Text2 + [Delim] + Text1 + [Extract]    
- 分别用 Transformer 编码两次  
- 两个 [Extract] 位的输出拼接（或其他融合），送入 Linear 层 → 相似度回归或分类     
#### Multiple Choice  
- 对每个选项独立构造输入序列：[Start] + Context + [Delim] + Answer_i + [Extract]

- 每个组合单独过一遍 Transformer  
- 提取 [Extract] 的输出 → Linear  
- 最后所有答案得分softmax，取最大值作为最终答案

[Language Models are Unsupervised Multitask Learners](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)
- GPT2继续沿着原路线走，扩大数据和模型到48层，维度1600，15亿的参数，把卖点放在了zero-shoot的能力。
- 使用reddit上找到的链接和文本来训练，在这些样本中就已经有很多类似翻译问答的对话，模型就可能学习到这种能力。GPT2和已有的一些zero-shoot工作相比在一些任务上表现的不错，在一些任务上还有待提升。
- 在GPT的基础上初始化权重时按层数缩放，使得更深的网络更容易训练。
- GPT 的 LayerNorm 在 Transformer 的输出之后，GPT-2 改成了 LayerNorm 在输入之前。
- GPT 用的是基本的 Byte-level BPE（不可逆），GPT-2 使用了一种 Byte-level 可逆 BPE，所有输入作为 UTF-8 字节处理（每个字符都可映射），输出也可以完美还原成原始文本，支持任意语言符号、标点、特殊字符。

[Language Models are Few-Shot Learners - Tom B. Brown, Benjamin Mann, Nick Ryder, Melanie Subbiah, J. Kaplan, Prafulla Dhariwal, Arvind Neelakantan, Pranav Shyam, Girish Sastry, Amanda Askell, Sandhini Agarwal, Ariel Herbert-Voss, Gretchen Krueger, T. Henighan, R. Child, A. Ramesh, Daniel M. Ziegler, Jeff Wu, Clemens Winter, Christopher Hesse, Mark Chen, Eric Sigler, Ma-teusz Litwin, Scott Gray, Benjamin Chess, Jack Clark, Christopher Berner, Sam McCandlish, Alec Radford, I. Sutskever, Dario Amodei](https://www.semanticscholar.org/paper/90abbc2cf38462b954ae1b772fac9532e2ccd8b0) (Citations: 43115) [PDF](D:\poem\src\data\blog\download\language_understanding_paper.pdf)
- GPT3尝试采用对比三种approach：few-shoot,zero-shoot,one-shoot
- 其中特别强调少样本学习的结果，因为其中许多结果仅略落后于最先进的微调模型。但是最终来看，one-shoot even zero-shoot，似乎是与人类表现最公平的比较方式，也是未来研究的重要目标。
### Model and Architectures
- 架构上使用与 GPT-2 相同的模型和架构，但在 Transformer 层类似于稀疏  Transformer。
- 尝试8种不同参数规模，最大的是1750亿的参数量，是GPT2的100倍
### Training Dataset
- 对应数据集必须使用更大的 Common Crawl，用了三个步骤来清洗。
- 基于与一系列高质量参考语料库相似性进行筛选，用lsh算法在文章层面进行模糊去重，加入已知高质量参考语料库增加多样性。

### Limitations
- 在输出大段文字写到后面可能会重复前面的语义，在物理常识方面的问题存在困难。
- 对所有目标token权重相同，文本有大量常见但没什么意义的词，导致模型花费大量时间去学习了那些不重要的虚词。
- 样本有效性不够，训练模型几乎用光了网上所有的文章。
- 不清楚少样本学习在推理时究竟是从零开始学习新任务，还是仅仅识别并确认它在训练期间已经学过的任务。
- 决策不容易解释，不知道是哪些权重起到关键性的作用

#### LSH
```python
from datasketch import MinHash, MinHashLSH
from sklearn.feature_extraction.text import TfidfVectorizer
import re

documents = {
    "doc1": "Machine learning is a subfield of artificial intelligence.",
    "doc2": "Artificial intelligence and machine learning are closely related.",
    "doc3": "Bananas are yellow and sweet.",
    "doc4": "This paper discusses deep learning, a field within machine learning.",
}

def preprocess(text):
    text = re.sub(r'[^\w\s]', '', text.lower())#替换
    tokens = text.split()
    return set(tokens) 

def get_minhash(tokens, num_perm=128):
    m = MinHash(num_perm=num_perm)
    for token in tokens:
        m.update(token.encode('utf8'))
    return m

lsh = MinHashLSH(threshold=0.5, num_perm=128)
minhashes = {}
for doc_id, text in documents.items():
    tokens = preprocess(text)
    m = get_minhash(tokens)
    lsh.insert(doc_id, m)
    minhashes[doc_id] = m

query_doc = "Deep learning is a subfield of artificial intelligence."
query_tokens = preprocess(query_doc)
query_m = get_minhash(query_tokens)

print("Query:", query_doc)
for match in lsh.query(query_m):
    print(f"相似文档: {match} | Jaccard 估计 = {query_m.jaccard(minhashes[match]):.3f}")

```