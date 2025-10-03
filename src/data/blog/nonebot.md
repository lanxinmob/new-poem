---
author: 'lanxinmob' 
postSlug: 'mako-bot pro'
title: '搭建mako-bot的总结'
featured: true
draft: false
tags:
  - '笔记'
  - '计算机网络'
  - 'nonebot'
description: '遇到的问题和收获'
pubDatetime: 2025-07-31T09:00:00Z
modDatetime: 2025-07-31T09:00:00Z
---
# Nonebot2
- 一个跨平台的 `python` 异步机器人框架，所谓异步就是避免被缓慢的操作卡死而可以将其“挂起”先去处理其他任务。
# Lagrange
- Lagrange.Core 是一个开源的 NTQQ 协议实现
## Lagrange.OneBot
- Lagrange.Core 实现了 OneBot V11 的通信协议, 可以和主流 Bot 框架进行通信

## 流程
- 搭建一个`qq bot`的大致流程可以分为，
- 下载框架编写核心插件（在nonbot框架中所有功能都封装成互不干扰的插件），设置事件响应器来监听消息，出现符合的消息时运行处理逻辑，包括用什么模型，什么时候回复是否存储记录等，决定回复内容。
- 下载并运行 lagrange 连接到腾讯服务器后扫码登录qq账号，负责物理上接收消息，与后端nonebot连接，根据程序输出发送消息。
- 由`NoneBot2`启动一个服务，等待客户端（Lagrange）来主动连接它。`NoneBot2`服务地址是固定的（host.docker.internal:8080）,lagrange 通过反向websocket和Nonebot连接，这一步会由 lagrange 自动完成。
- 注意使用小号避免账号冻结。
## 问题排查
### Lagrange
- 检查日志中是否出现了`Login Success`或`Register Status: register success`来判断是否连接到QQ，查看`LAGRANGE_QQ QQ`号是否写错，`LAGRANGE_PASSWORD=""`留空通过扫码登陆。

### Nonebot
- 检查日志中是否明确显示了`Uvicorn running on http://127.0.0.1:8080`。如果出现错误，阅读NoneBot2的启动日志，看是否有[ERROR]信息，并根据错误提示修正代码或配置。
### WebSocket
- 连接上会自动完成，除非想像我一样尝试`docker`启动`lagrange`的话就需要注意检查各种东西（虽然最后我也没能成功连上，直接下了exe启动）。
- 如果日志持续显示`Reconnecting...`，或者明确报`Connection refused`。打开`docker-compose.yml`，检查`environment`块里的`LAGRANGE_REVERSE_WS_URLS`地址是否完全正确：`"ws://host.docker.internal:8080/onebot/v11/"`。检查`Windows Defender`防火墙，确保与Python或Docker Desktop相关的应用权限中，“专用”和“公用”网络都被勾选允许以及对应端口是否被占用。

## 知识小结
#### 127.0.0.1 vs 0.0.0.0：
- 127.0.0.1：仅接受本地进程连接
- 0.0.0.0：接受所有网络接口（包括容器、局域网等）的连接
### Lagrange容器连接到NoneBot2服务有两种方式
#### 破坏容器的隔绝，让容器直接共享主机网络
- `network_mode: host` 使容器共享主机网络栈,容器内访问 127.0.0.1 等同于访问宿主机回环地址。
- **这个方案在 Docker Desktop for Windows/Mac 上不起作用**，主要是为 Linux服务器 环境设计的
#### 网络转换
- `host.docker.internal` 提供主机回环地址让容器可以直接连接而不破坏隔绝。

## strip
- `strip()` 是 `Python` 字符串的方法，用来去除字符串开头和结尾的空白字符（包括空格、换行符\n、制表符\t等）

##  netstat -tuln | grep 8080 
- `netstat -tuln` 的意思是列出本机所有正在监听的TCP和UDP端口，并用数字显示。
- `grep 8080` 是只把包含8080这几个数字的行给找出来
- 合起来就是查看现在服务器上有没有程序正在 8080 端口上监听