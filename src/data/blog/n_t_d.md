---
author: 'Lanxinmob'
title: '暑期日记4'
postSlug: 'summer-diary-4'
featured: false
draft: false
tags:
  - '笔记'
ogImage: ''
description: 'numpy tensor dataframe'
pubDatetime: 2025-07-10T09:00:00Z
toc: true
---

## 一、库的定位

### Pandas

- 用于**数据分析和预处理**，支持带标签，**异构**数据（每行不同类型），强大数据转换、筛选、聚合、可视化功能
- **核心数据结构**：`DataFrame` （二维表格）`Series` （带标签一维数组） 

### NumPy

- 基于C语言的高性能的**同构**多维数组对象`ndarray`，有丰富数学函数库
- **核心数据结构**：`ndarray`

### PyTorch

- 深度学习引擎，将张量部署到GPU进行大规模并行计算，带有`Autograd`功能

- **核心数据结构**：`Tensor`
## 二、常用方法与转换

#### 1. Pandas

- `iloc` 按整数位置提取想要的行和列

- **转换成NumPy数组**：`.values` `.to_numpy()`

- **Series转换成DataFrame**：`.to_frame()`

- **Pandas to NumPy**

  ```python
  df = pd.DataFrame({'a':[1,2,4],'b':[3,4,6]})
  np_array = df.to_numpy()
  #[[1 3],
  #[2 4],
  #[4 6]]
  np_subset = df.iloc[:,0].to_numpy()
  #[1 2 4]
  ```
-  **NumPy to Tensor**
  
  ```python
  #共享内存
  tensor_share = torch.from_numpy(np_array)
  #创建拷贝
  tensor_copy = torch.tensor(np_array)
  #tensor([[1.0000,3.0000],
  #        [2.0000,4.0000],
  #        [4.0000,6.0000]],dtype=float64)
  np_array[:] = 1
  #tensor_share改变 tensor_copy不变
  np_array = np.ones_like(np_array)#重指向
  #tensor_share也不变
  ```

#### 2. NumPy
- `np.array()` 创建NumPy数组，创建时会尝试找到一种能兼容所有元素的类型
- `np.arange()` 类似Python的range()，但返回一个NumPy数组
- 打印一个 NumPy 数组时，它的元素之间默认用空格分隔，而不是逗号，用于区分Python列表
  ```python
  array = np.array([10, 20, 30, 40])
  #[10 20 30 40]
  array = array + 2 #ndarray可以直接进行向量化操作，python的list不行
  ```
- **Numpy to Pandas**
  ```python
  np_array = np.arange(6).reshape(3,2)
  np_to_pd = pd.DataFrame(np_array,colums=['col1','col2'])
  #   col1  col2
  # 0   0     1
  # 1   2     3
  # 2   4     5
  ```
#### 3. PyTorch

- `torch.cat() `沿一个给定维度将多个tensor拼接
- **Tensor to NumPy**
  
   ```python
   tensor = torch.randn(2,2)
   np_from_tensor = tensor.cpu().numpy()#共享内存
   ```

## 三、数据类型转换

#### 1. NumPy/Pandas

- `.astype('float32')` `/.astype(np.float32) `转换pd的一列或整个numpy数组的元素类型
- `df['col'] = df['col'].astype('float32')`

#### 2. PyTorch

- `.float()` `/.to(torch.float32)`

- `tensor = torch.tensor(data, dtype=torch.float32) `

## 四、数据与计算图

- 一个tensor包含两个部分一个是**数据**，一个是**计算图属性**，如`requires_grad` 和 `grad_fn`，`grad_fn`记录了该tensor如何被计算出来，是自动求导的关键。
#### 1. `.clone()` 

- 分配内存，创建一个新tensor将数据完全复制过去。
- `.requires_grad` 会和原tensor一样， `True`依然是`True`。

  `cloned` 的 `grad_fn` 会记录一个 `CloneBackward` 操作，意味着**梯度可以从 `cloned` 流回 `original`**。

- 用于在模型的不同部分输入相同数据，但是是独立的计算分支。
#### 2. `.copy()`

- `NumPy` 中的深拷贝，创建一个全新的 `ndarray` 对象。

- `Pandas`中同样进行一个对象的深拷贝，避免`SettingWithCopyWarning`，以防修改时影响`original`。

- ```python
  np_copy = array.copy()
  pd_copy = df[df['a'] > 1].copy()
  ```

#### 3. `.detach()`

- `requires_grad`  变成 **`False`**，没有 `grad_fn`，梯度不会回流。
- 用于使用数据绘图等

## 五、绘图

- `.plot()` 默认情况下，`.plot()` 会使用 DataFrame 的 index 作为 X 轴，每一列作为一条独立的 Y 轴。

- ```python
  fig, ax = plt.subplots(figsize=(12, 6))
  df.plot(ax=ax, label='prediction')
  ```
  
  - **`fig`** 代表一个 **Figure 对象**（可理解为一个大画布）， **`ax`** 代表一个 matplotlib 的 **Axes 对象**（可理解为一个子图画布）。
  - 当在一个图中绘制多条来自不同来源的曲线，需要先创建一个 Figure 和一个或多个 Axes 对象。
  
  - **`label` **用于指定这条曲线的名字，在图例（legend）中显示出来。
  
- 下面是把**实际值**和**预测值**画在同一张图上进行对比的例子
  ```python
  import pandas as pd
  import numpy as np
  import matplotlib.pyplot as plt
  
  dates = pd.to_datetime(pd.date_range(start='2025-01-01', periods=100))
  actual_values = np.sin(np.linspace(0, 10, 100)) + np.random.randn(100) * 0.1
  predicted_values = actual_values + np.random.randn(100) * 0.1 - 0.1
  
  df = pd.DataFrame({'Actual': actual_values,'Predicted': predicted_values}, index=dates)
  
  fig, ax = plt.subplots(figsize=(12, 6))
  df['Actual'].plot(ax=ax, label='Actual Values', style='-', color='dodgerblue')
  df['Predicted'].plot(ax=ax, label='Predicted Values', style='--', color='orangered')
  
  ax.set_title('Prediction and Actual Values', fontsize=16)
  ax.set_xlabel('Date')
  ax.set_ylabel('Value')
  ax.grid(True, linestyle='--', alpha=0.6)#透明度
  ax.legend() 
  
  plt.tight_layout()
  plt.show()
  ```

- `np.random.randn(100)` 生成了一个包含 100 个随机数的数组，这些数符合标准正态分布。
- `np.linspace(0, 10, 100)`生成一个0到10的100个数均匀分布的数值序列。
- `pd.date_range(...)` 生成日期范围，返回 Pandas 的 `DatetimeIndex` 对象。
- `pd.to_datetime()`将字符串和其他数据类型转换成日期格式，也返回 `DatetimeIndex` 对象。
- `DatetimeIndex` 是一个索引对象，用于为 `DataFrame` 或 `Series` 提供时间相关的索引。



