---
author: 'Lanxinmob'
title: '暑期日记4'
postSlug: 'summer-diary-4'
featured: false
draft: false
tags:
  - '笔记'
  - 'css'
ogImage: ''
description: '练习rnn,lstm,完善网站'
pubDatetime: 2025-07-09T09:00:00Z
---

# data processing

 - 读取用`[]`, 写入用`.loc[]`

## 内容居中

```html
<div class="relative px-4 pb-12 max-w-screen-xl mx-auto">
      <main
        id="main-content"
        class="w-[720px] min-w-0 mx-auto"
        data-pagefind-body
      >...</main>
```
- `mx-auto` 让main在父容器中居中，`w-[720px]` 保证内容宽度固定
- 外层`<div class="relative px-4 pb-12 max-w-screen-xl mx-auto">` 只做最大宽度和内边距，不用flex，不让aside运行主内容的流式布局。
- 如果外层用了 flex，主内容和 aside 会在一行，主内容会被挤到左侧。

## 目录

```html
<aside class="hidden lg:block w-64 fixed right-8 top-24" style="z-index:20;">
```

- `top right z-index `让目录浮在右上角合适位置。
- `fixed` 定位让目录脱离文档流不影响布局。
## Tailwind css
- 一种流行的css框架，“功能类优先”框架，用简短的类名来快速应用特定的css样式。

- **`px`** **p**adding-**x**-axis 水平内边距

- **`pb`** **p**adding-**b**ottom 底部内边距

- **`mx`**  **m**argin-**x**-axis 水平外边距 
  - `mx-auto`  css中一个标准技巧使固定宽度的块级元素在父级容器中水平居中。
  
- `t`, `b`, `l`, `r` 分别代表 top, bottom, left, right