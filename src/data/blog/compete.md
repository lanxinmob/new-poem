---
author: 'Lanxinmob'
title: '暑期日记8'
postSlug: 'summer-diary-8'
featured: false
draft: false
tags:
  - '笔记'
ogImage: ''
description: 'lstm+mlp tpu'
pubDatetime: 2025-07-13T09:00:00Z
toc: true
---

## Python

- **`if __name__ == "__main__"`** 就像是主程序的“启动按钮”，只有在直接运行那个文件时才会按下，然后它会去**调用**它所需要的所有函数

### TPU

- 全称是 **Tensor Processing Unit (张量处理单元)**
- 可以理解为Google为了人工智能专门设计和制造的芯片（AISC）
- 内部结构称为“脉动阵列” Systolic Array，可以快速完成大型矩阵乘法计算
- 在 `PyTorch` 中使用需要引入 `torch_xla` 库
   ```python
   import torch_xla
   import torch_xla.core.xla_model as xm
   import torch_xla.distributed.parallel_loader as pl
   import torch_xla.distributed.xla_multiprocessing as xmp
   import torch_xla.runtime as xr
   
   device = xm.xla_device()
   
   xm.optimizer_step(optimizer, barrier=True)
   #barrier 阻塞python脚本直到所有TPU核心的所有计算完成
   
   ```
- `torch_xla` 执行模式是延迟执行，`xm.optimizer_step(optimizer)` 告知执行整个计算图包括向前传播、误差计算、向后传播、优化器更新
- `torch_xla` 追踪所有操作最终形成一个静态计算图，XLA 就是获取这个计算图进行深度优化再针对当前硬件编译成极高速度的机器码的编译器。如果模型中有大量无法被编译的 `if/else` 判断或者复杂的 Python 循环，TPU 就需要频繁地和 CPU 通信，反而会变慢。在处理小模型或小批量时编译的开销可能超过带来的优势。
- `torch.utils.data.distributed.DistributedSampler` 是采样器，用于进行多核的分布式训练。



