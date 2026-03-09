---
author: 'Lanxinmob'
title: 'CS188项目记录（1）'
postSlug: 'CS188项目记录（1）'
featured: false
draft: false
tags:
  - '笔记'
  - 'CS188'
  - '算法'
ogImage: ''
description: ''
pubDatetime: 2026-02-19T01:32:00Z
toc: true
---

所有 factor 共享同一个“大 assignment”

枚举：
$$
(V,W,X,Y,Z)
$$
的所有组合。

assignment = 所有变量的一种完整组合



question 4

**边缘叶子节点**无条件变量就只有 1 个，进行无关叶子节点剪枝，直接从图上把它删掉