---
author: 'Lanxinmob'
title: '更换为Napcat框架'
postSlug: '更换为Napcat框架'
featured: false
draft: false
tags:
  - '开发记录'
  - 'napcat'
ogImage: ''
description: ''
pubDatetime: 2025-10-15T09:00:00Z
toc: true
---

### 前言

之前使用的 Lagrange.onebot 如图所示暂时终止了，于是打算更换到 napcat 框架。

![image-20251112193245162](C:\Users\N\AppData\Roaming\Typora\typora-user-images\image-20251112193245162.png)

### 安装napcat

- 我直接使用了官方提供的 NapCat.Installer - Linux 一键使用脚本(支持Ubuntu 20+/Debian 10+/Centos9)
  ```sh
  curl -o \
  napcat.sh \
  https://nclatest.znin.net/NapNeko/NapCat-Installer/main/script/install.sh \
  && bash napcat.sh
  ```

> 其实并没有那么顺利，但是在解决了服务器上网络问题后就很通畅了。

### 接入框架

- 我的 bot 使用的是 Nonebot 框架，所以直接进行一些简单配置
- 修改 NoneBot 配置文件 `.env`，添加 `ONEBOT_ACCESS_TOKEN= 你配置的 token`。
- 进入 NapCat 配置，Websocket 客户端（反向ws）配置添加地址，地址为 `ws://127.0.0.1:8080/onebot/v11/ws`, 这里的 `8080` 是 NoneBot 输出的端口号，`/onebot/v11/ws` 是 NoneBot onebot 适配器默认的路径。添加 token 为 Nonebot 中配置的 token。

### 运行 napcat

后台运行 Napcat ：

- 启动: `screen -dmS napcat bash -c "xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox "`

- 带账号启动: `screen -dmS napcat bash -c "xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox  -q QQ号码"`

前台启动 Napcat :   `xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox`

TUI-CLI 工具用法 ：`napcat`

#### 更换完成。

---

- napcat 提供的 webui 挺有意思。

### 参考

[napcat guide](https://napneko.github.io/guide)