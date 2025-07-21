---
author: 'Lanxinmob'
title: '暑期日记15'
postSlug: 'summer-diary-15'
featured: false
draft: false
tags:
  - '笔记'
ogImage: ''
description: 'automl'
pubDatetime: 2025-07-21T09:00:00Z
toc: true
---

## automl

- 在尽量不需要人操作下，自动处理输入的数据，提取特征，选取合适的模型，进行训练。

- 大多数 `automl`框架是基于超参数搜索技术，autogluon则是基于融合多个无需基于超参数搜索的模型。

#### 超参数搜索技术

- 超参数是模型训练前设置的参数，该技术指自动寻找这些参数的最优组合。

  | 模型     | 超参数                                        |
  | -------- | --------------------------------------------- |
  | 决策树   | max_depth, min_samples_split                  |
  | 随机森林 | n_estimators, max_features                    |
  | XGBoost  | learning_rate, max_depth, subsample           |
  | 神经网络 | learning_rate, batch_size, hidden_layer_sizes |
- 常见的有：网格搜索，随机搜索，贝叶斯优化，遗传算法，BOHB，Automl等

### autogluon用到的技术

```python
from autogluon.tabular import TabularPredictor

predictor = TabularPredictor(label='target').fit(
    train_data=train_df,
    num_bag_folds=5,       # 5折交叉bagging
    num_stack_levels=1     # 一个堆叠层
)
```
#### Stacking
- 训练多个模型的输出到一个线性模型集成起来最后得到一个输出，也就是加权和，权重是由训练得到的。

#### 多层Stacking

- `num_stack_levels=1 `

- 将多个模型的输出和数据合并起来再做一次或多次 `stacking`
- 通常配合下面的k则交叉bagging使用

#### k-则交叉Bagging

- 来自k-则交叉验证
-  `num_bag_folds=5` 就是将训练集分成5份，每次训练用其中四份，验证用一份，分别去训练5个子模型，分别预测对5个输出取平均得到最终预测结果。
-  防过拟合，增强泛化