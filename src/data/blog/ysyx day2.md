---
author: 'Lanxinmob'
title: 'ysyx day2'
postSlug: 'ysyx day2'
featured: false
draft: false
tags:
  - '笔记'
  - 'ysyx'
ogImage: ''
description: 'f3译码器、编码器'
pubDatetime: 2026-03-12T23:00:00Z
toc: true
---

## 分析门电路

这是或非门，即只有两个输入都为0时输出为1，后面接一个非门就能实现或门。

![image-20260312155922500](./images/image-20260312155922500.png)

## 异或门的几种尝试

![image-20260312131859943](./images/image-20260312131859943.png)

使用16个晶体管的门电路

![image-20260312131901552](./images/image-20260312131901552.png)

使用18个晶体管的门电路

经过两天各种尝试还是没能找到14个晶体管实现的方案，先继续学习 f3 剩余内容。

----

几天后，在ysyx交流群中得到启发，结合学校里同时学习的数电内容中的

**德·摩根定律**：

- $$\overline{X + Y} = \overline{X} \cdot \overline{Y}$$

- $$\overline{X \cdot Y} = \overline{X} + \overline{Y}$$

异或的**最小项**表达式：

-  $$ A \oplus B = A\overline{B} + \overline{A}B $$

首先利用互补律加入为 $0$ 的项，并进行因式分解：
$$
\begin{aligned}
A \oplus B &= A\overline{B} + \overline{A}B \\
&= A\overline{B} + A\overline{A} + \overline{A}B + B\overline{B} \\
&= A(\overline{B} + \overline{A}) + B(\overline{A} + \overline{B}) \\
&= (A + B)(\overline{A} + \overline{B})
\end{aligned}
$$
对右侧括号使用德·摩根定律（$\overline{A} + \overline{B} = \overline{AB}$）：
$$
\begin{aligned}
&= (A + B)\overline{AB} \\
&= \overline{AB}(A + B)
\end{aligned}
$$

对 $(A+B)$ 整体加上双重否定号：
$$
\begin{aligned}
&= \overline{AB} \cdot \overline{\overline{(A+B)}}
\end{aligned}
$$
再次整体逆向使用德·摩根定律（$\overline{X} \cdot \overline{Y} = \overline{X + Y}$）：
$$
\begin{aligned}
&= \overline{AB + \overline{(A+B)}}
\end{aligned}
$$


![image-20260322221553426](./images/image-20260322221553426.png)

## 异或门的全定制电路

A 输入为1 时，B由左侧电路输入，

A 输入为0 时，左侧电路悬空，B由右侧电路输入

![image-20260312160217515](./images/image-20260312160217515.png)

同或门的全定制电路与上图结构类似，改变调换三处位置即可。

## 3-8译码器

使用两个2-4译码器的子电路，和若干个门电路

![image-20260312155604155](./images/image-20260312155604155.png)

## 七段数码管译码器

![image-20260312202916407](./images/image-20260312202916407.png)

## 七段数码管译码器（2）

![image-20260312215858028](./images/image-20260312215858028.png)

## 16-4编码器

![image-20260312220055292](./images/image-20260312220055292.png)

