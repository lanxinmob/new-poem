---
author: 'Lanxinmob'
title: 'Attetion is all you need'
postSlug: 'summer-diary-18'
tags:
  - '笔记'
ogImage: ''
description: '李沐论文精读 Transformer'
pubDatetime: 2025-07-25T09:00:00Z
toc: true
---

[Attention is All you Need - Ashish Vaswani, Noam M. Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Lukasz Kaiser, I. Polosukhin](https://www.semanticscholar.org/paper/204e3073870fae3d05bcbc2f6a8e263d9b72e776) (Citations: 133599) [PDF](D:\poem\src\data\blog\download\1706.03762.pdf)
- 第一个只依赖自注意来实现 `encoder decoder`架构的转录模型，没有使用循环或卷积。
- `BlEU score`是机器翻译的一个衡量指标，越高越好
- 通常把代码放在摘要的最后，因为神经网络中细节很多，有代码最好第一时间公布代码。
- 注意机制已经被成功应用在`RNN`，主要用在如何让有效把编码器东西传递给解码器，不用考虑输入或输出序列中距离依赖。
- `transformer`纯用`attention`就有一个更高的并行度
- 卷积神经网络对长的序列难以建模，而attention可以一次看到整个序列。卷积的好是可以通过多种核做多个输出通道，不同输出通道可以认为识别了不同模式，所以这里就用多头注意力机制来模拟卷积神经网络多输出通道的效果。
- 这里有残差连接，所以就直接设置每层维度都是512，调参就比较简单，包括`BERT,GPT`就是总共有多少层，每层的维度是多少。
- 这里用`layernorm`是每个样本自己算均值和方差，不用去存全局的均值和方差，避免使用`batchnorm`时计算整体的均值和方差会因为样本不同长度的影响而导致的不稳定。
- 许多东西在文章中解释可能会和后来人们的解释不太一样，后面有一篇文章解释layernorm的有效更多是从梯度，对事物的normalization，提升常数来解释。

- 解码器比编码器多的一个子层是masked多头注意，让它不看到t时间以后的那些输入，因为这里训练时输入就是已知的目标句子。从而保证训练时和预测时行为是一致的。

## **Attention**
> An attention function can be described as mapping a query and a set of key-value pairs to an output,where the **query, keys, values**, and output are all vectors. The output is computed as a weighted sumof the values, where the weight assigned to each value is computed by a compatibility function of the query with the corresponding key.
### Scaled dot-product attention
- 看着名字长其实是最简单的注意力机制，这里使用相同维度的Q,K,V,通过分别计算Q和每个K的点乘来得到相似度，内积越大相似度越高，内积为0也就是正交相似度为0。 
- 计算上就是Q矩阵乘K矩阵，然后除以根号下dimension dk，如果这里是目标序列作为输入就要做一个mask把kt后的值变为极大的负数这样经过softmax就会接近0避免看到需要预测的序列，再对每行做softmax得到权重，最后乘矩阵value，得到的每一行就是我们想要的输出了。
- 当dk比较小时除不除根号下dk都没关系，但是当dk比较大时候两个向量比较长，点乘结果的量级可能很大，softamx以后大的更大小的更小接近0或1，这样得到的梯度就会很小，就会跑不动
- 加型注意力用一层隐藏层的前馈网络计算相关性函数，两个在理论复杂度上差不多。在实践上，点乘比加型更快更高效，因为就是两次矩阵乘法。
- AI的PyTorch实现
```python
import torch
import torch.nn as nn
import math

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, num_heads):
        super().__init__()
        assert d_model % num_heads == 0, "d_model must be divisible by num_heads"
        
        self.d_model = d_model
        self.num_heads = num_heads
        self.head_dim = d_model // num_heads
        
        # 步骤1 & 2: 创建一个大的线性层，一次性完成所有头的投影
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)
        
    def scaled_dot_product_attention(self, Q, K, V, mask=None):
        attn_scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.head_dim)
        if mask is not None:
            attn_scores = attn_scores.masked_fill(mask == 0, -1e9)
        attn_probs = torch.softmax(attn_scores, dim=-1)
        output = torch.matmul(attn_probs, V)
        return output

    def forward(self, Q, K, V, mask=None):
        batch_size = Q.size(0)
        
        # 1. 通过大的线性层进行投影
        # 输入 Q, K, V: [batch_size, seq_len, d_model]
        Q_proj = self.W_q(Q) # -> [batch_size, seq_len, d_model]
        K_proj = self.W_k(K) # -> [batch_size, seq_len, d_model]
        V_proj = self.W_v(V) # -> [batch_size, seq_len, d_model]
        
        # 2. 变形和转置，分离出各个头
        # view: [batch_size, seq_len, num_heads, head_dim]
        # transpose: [batch_size, num_heads, seq_len, head_dim]
        Q_proj = Q_proj.view(batch_size, -1, self.num_heads, self.head_dim).transpose(1, 2)
        K_proj = K_proj.view(batch_size, -1, self.num_heads, self.head_dim).transpose(1, 2)
        V_proj = V_proj.view(batch_size, -1, self.num_heads, self.head_dim).transpose(1, 2)
        
        # 3. 进行批量的注意力计算
        context = self.scaled_dot_product_attention(Q_proj, K_proj, V_proj, mask)
        
        # 4. 合并各个头的结果
        # transpose: [batch_size, seq_len, num_heads, head_dim]
        # view (reshape): [batch_size, seq_len, d_model]
        context = context.transpose(1, 2).contiguous().view(batch_size, -1, self.d_model)
        
        # 5. 通过最后的线性层
        output = self.W_o(context)
        
        return output
```
### Application of attention 
- 编码器和解码器的第一个`attention`k q v都是输入本身，解码器的第二个注意块 k-v来自编码器的输出，解码器第一个attention的输出作为它的query。
### Feedforward NetWorks
### 小结
- attention这里作用就是把整个序列的信息抓取出来，做一个汇聚aggregation，每个点都有了序列中所有感兴趣的信息，后面跟着投影做mlp映射我想要的语义空间的时候就可以每个点单独去做，因为已经有了序列信息。
### Embedding and softmax
- embedding是任何一个词学习一个长为d向量来表示它

- 编码器一个embedding解码器输入一个embedding最后softmax前一个embedding三个共用一个权重训练起来比较简单，把这些权重都除以根号下dmodel
### Positional Encoding
- posditional embedding 就是对应位置的向量加到各自embedding
## Training
- 一个工作是训练英语到德语，德语和英语共用一个37000 tokens的vocabulary
## Regularization
### dropout
- 在每个子层输出之后，在与原始输入进行残差连接（相加）和层归一化之前进行0.1 dropout。
- 另外在词嵌入（embeddings）和位置编码（positional encodings）相加后，对这个最终生成的输入向量施加Dropout。
- dropout 0.1是随机对百分之九十的数据除0.9，对百分之十置零。
### label smoothing
>During training,we employed label smoothing of value ϵ ls =0.1.This hurts perplexity,as the model learns to be more unsure,but improves accuracy and BLEU score.
- 不再对标签绝对相信，不同于通常正确标签为1，其他标签为0，这里正确标签为0.9，其他标签均分均分0.1，也就是不只是记住正确标签，提高困惑度，也要去了解其他标签也有正确的可能，认识到微小的不确定性，从而防过拟，增强泛化。
- 自注意看上去好像对长数据处理更好而且算的也不慢，但实际上不是这样，实际上attention对整个模型的假设做得更少，导致说需要更多的数据和更大的模型才能训练出来做得跟rnn cnn同样效果，导致现在基于transformer的模型都特别大特别贵

- 对一篇文章来说，你需要在讲一个故事，让读者有代入感说服读者，为什么做这个事，设计理念，你的对整个文章的一些思考是什么样的

- 如果把残差连接说明的去掉只有attention基本上什么都训练不出来，所以这个模型并不是只需要attention

- transformer在不止翻译各个领域，语言视频图片都表现出很好的效果

- attention并没有对数据的顺序做建模为什么能跑赢rnn呢，rnn显示序列信息理论上应该比mlp要好，现在大家认为它使用了一个更广泛的归纳偏置使得他能处理一些更一般化的信息 

- 这也就是说attention并没有做任何空间上的一些假设，他也能取得cnn甚至比cnn更好的结果，代价是假设更一般，对数据抓取能力变差，需要更多的数据更大模型才能训练出想要的效果

- 也给研究者鼓励在cnn rnn之外也有模型能打败他们，所以现在人们尝试就用mlp或一些简单的架构在图片文本表现出好的效果

---

参考:[transformer](https://www.bilibili.com/video/BV1pu411o7BE)