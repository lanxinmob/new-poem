---
author: 'Lanxinmob'
title: 'Redis note'
postSlug: 'redis note'
featured: false
draft: false
tags:
  - '笔记'
  - 'Redis'
ogImage: ''
description: '常用redis指令'
pubDatetime: 2025-10-03T09:00:00Z
toc: true
---

# Redis

#### 查看所有 key 及 key 的类型

- `KEYS *`
- `TYPE key`

#### 获取各种类型的 key

| 类型   | 命令示例                     |
| ------ | ---------------------------- |
| string | `GET key`                    |
| hash   | `HGET key field`             |
| list   | `LRANGE key 0 -1`            |
| set    | `SMEMBERS key`               |
| zset   | `ZRANGE key 0 -1 WITHSCORES` |

#### 删除各种类型的 key 

##### 删除整个key

- 如果你想将 `"memory ae2a"` 这个键（以及它包含的所有字段）完全删除，使用 **`DEL`** 命令。

**命令格式:** `DEL key_name`

##### 只删除 Hash 中的一个字段 (Field)

- 如果你只想删除 `"point_text"` 这个字段，而保留 `memory ae2a` 这个键及其它可能的字段，则使用 **`HDEL`** 命令。

**命令格式:** `HDEL key field`

**List (列表)**

- 删除指定数量的元素`LREM key count value` `LREM mylist 2 "apple"` (从头开始删除 2 个 "apple")

**Set (集合)**

- 删除一个或多个成员 (Member)`SREM key member` `SREM myset "user_a" "user_b"`

**ZSet (有序集合)**

- 删除一个或多个成员 (Member)`ZREM key member` `ZREM myzset "member1" "member2"`

**String (字符串)**

- N/A*(String 内部没有字段或元素的概念，删除就是 `DEL key`)

#### 读取 key 值

- `redis-cli GET key_name | jq .`

- `redis-cli --raw GET your_key_name`

---

参考：

[Redis command](https://redis.io/docs/latest/commands//)

[Redis as a vector database quick start guide](https://redis.io/docs/latest/develop/get-started/vector-database/)