---
author: 'Lanxinmob'
title: '暑期日记13'
postSlug: 'summer-diary-13'
featured: false
draft: false
tags:
  - '笔记'
ogImage: ''
description: 'linux'
pubDatetime: 2025-07-18T09:00:00Z
toc: true
---

### less

- `less` 是一个强大的文件查看器，以可交互可滚动的方式查看文本文件。
- 在读取文件时，不会直接读取整个文件，而是只读取屏幕上显示的部分。
- `less a.json` 
- `q` 退出  `/` 搜索

### 其他

- `wc -l`统计行数 `wc -w` 统计单词数
### print

- `print(r, 'is similar to', r.similar(3))` 
- `print()` 接受一个或多个参数，用逗号间隔，打印时自动加上空格分隔。
- 或用 f-string `print(f"{r} is similar to {r.similar(3)}")`
- 或用字符串 `print(str(r) + ' is similar to ' + str(r.similar(3)))`
- 或用format `print("{0} is similar to {1}".format(r, r.similar(3)))`