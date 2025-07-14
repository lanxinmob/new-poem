---
author: 'Lanxinmob'
title: '暑期日记9'
postSlug: 'summer-diary-9'
featured: false
draft: false
tags:
  - '笔记'
  - 'MySQL'
ogImage: ''
description: 'MySQL索引原理'
pubDatetime: 2025-07-14T09:00:00Z
toc: true
---

### MySQL

- 关系型数据库
- 一条记录是结构化的二进制数据，以compact的行格式
- **记录类型、下一条记录的偏移地址**、删除标志位、**用户真实数据**、具有唯一性的行ID（**主键ID**）、事务ID、回滚指针等
- record_type为2说明它是当前页最小记录，record_type为3说明是当前页最大记录，存储用户数据的record_type为0，每两个数据页间通过双向链表连接。
#### 页分裂
- 因为插入、更新、删除等操作将记录在不同页间移动的情况就叫页分裂，降低数据库效率。
#### 目录项

- 方便查询时快速定位到所在位置，每页对应一个目录项包括record_type为1，对应页最小主键值，对应页号，随目录项增加给目录项再加一层就形成了一颗B+树。
#### 回表
- 先查二级索引再回主键索引进行查询


---

参考：[MySQL索引](https://b23.tv/6Y9oX6G)

