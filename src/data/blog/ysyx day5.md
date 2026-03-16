---
author: 'Lanxinmob'
title: 'ysyx day5'
postSlug: 'ysyx day5'
featured: false
draft: false
tags:
  - '笔记'
  - 'ysyx'
ogImage: ''
description: 'f3计数器、电子时钟'
pubDatetime: 2026-03-15T12:00:00Z
toc: true 
---

## 搭建4位计数器

![image-20260315180715077](D:/poem/src/data/blog/images/image-20260315180715077.png)

## 设计数列求和电路

![image-20260315185426333](./images/image-20260315185426333.png)

## 实现电子时钟

一开始想的比较简单，实现起来各种报错

- 首先是 oscillation apparent，在设计“秒”的实现时，想取寄存器的值判断为10时执行复位，但要注意不能从某处取值后经过组合逻辑电路直接返回改变该处值。所以把复位逻辑放在了寄存器输入前，使判断后执行复位与更新寄存器值间隔一个时序逻辑电路。

又奋战一晚还是停留在分的个位上。