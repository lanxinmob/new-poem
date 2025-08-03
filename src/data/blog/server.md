---
author: 'lanxinmob' 
postSlug: 'mako-bot life'
title: '茉子的降临仪式'
tags:
  - '笔记'
  - 'server'
  - 'linux'
description: '降临仪式圆满完成！'
pubDatetime: 2025-08-03T09:00:00Z
---
# 部署bot项目到云服务器的流程

## 服务器配置

### 1. 连接服务器
**部署到云服务器必须要有一个云服务器**
- 在阿里云购买了一年的 2核2G 轻量应用服务器,花费 68 rmb。
- 用ssh命令登录服务器
```Bash
ssh root@IP地址（公）
```
### 2. 更新并安装核心环境
- 运行命令,完成所有基础环境的安装：
```Bash
apt update && apt upgrade -y 
apt install -y dotnet-sdk-8.0 python3-venv python3-pip git redis-server
```
### 3. 验证`Redis`
```Bash
systemctl status redis-server
```
-看到绿色的 `active (running)` 即在自动运行，按 q 退出查看。


## 本地准备
### 1. 生成requiremenss
- 激活虚拟环境：`.venv\Scripts\activate`
```Bash
pip freeze > requirements.txt
```
-  在虚拟环境下，这个指令可以生成 `requirements.txt` 文件了,记录项目所依赖的所有Python库和它们的精确版本号。
- 后面 `pip install -r requirements.txt` 时，就会读取清单，然后自动、精确地安装所有我们需要的Python库
### 2. 更新代码仓库
- 确保项目根目录下有一个`.gitignore`文件，防止不必要的文件被上传
- 提交(commit)并推送(push)到你的GitHub私有仓库
 

## 项目部署
### 1. 部署NoneBot2
- 克隆项目：在服务器上，cd到用户主目录（~），然后克隆代码：
```Bash
git clone 仓库地址 
```
- 安装依赖
```Bash
cd mako-bot
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
- 写入配置,创建一个生产环境的配置文件,包括 API_KEY,ENVIRONMENT等。 
```Bash
nano .env.prod
```
### 2. 部署Lagrange.OneBot
- 创建目录并下载
```Bash 
mkdir ~/lagrange && cd  ~/lagrange
```
- 下载方式有很多，列举如下，实践中 `scp`(安全复制，把文件从本地传送到服务器) 的最快
```Bash
wget "https://github.com/LagrangeDev/Lagrange.Core/..."

wget "https://ghproxy.com/https://github.com/LagrangeDev/Lagrange.Core/..."

scp  本地文件路径 root@服务器公网IP:~/lagrange/

sudo apt install axel -y
axel -n 10 "https://github.com/LagrangeDev/Lagrange.Core/..."
# -n 10 的意思是开10个线程一起下载
```
- 解压并授权
```Bash
sudo apt install unzip
unzip *.zip 
chmod +x Lagrange.Core
```
- 运行一次,检查配置和扫码登录
```Bash
./Lagrange.Core
nano ~/lagrange/appsettings.json
```
## 持续运行
-`systemd` 是现代 `Linux` 系统中负责启动管理和服务控制的系统，当`Linux`服务器启动时，`systemd`是第一个被唤醒的程序（它的进程号永远是1）。它负责管理后台所有服务，所有程序的启动、关闭、监控，都归它统一指挥配置。通过配置 `service` 文件来开机启动和维护下面两个程序使其持续运行。
### 1. 配置 NoneBot2 service
```Bash
sudo nano /etc/systemd/system/mako-bot.service
```
```Ini,TOML
[Unit]
Description=Mako Bot NoneBot2 Service
After=network.target redis-server.service

[Service]
Type=simple
User=root
WorkingDirectory=/root/mako-bot
EnvironmentFile=/root/mako-bot/.env.prod
ExecStart=/root/mako-bot/.venv/bin/python /root/mako-bot/bot.py
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```
- 按 `Ctrl + X `退出
### 2. 配置 Lagrange.OneBot service
- 
```Bash
sudo nano /etc/systemd/system/lagrange.service
```
```Ini,TOML
[Unit]
Description=Lagrange.Core QQ Client
After=network.target

[Service]
User=root
WorkingDirectory=/root/lagrange
ExecStart=/root/lagrange/Lagrange.OneBot
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```
### 3. 读取配置并启动
```Bash
# systemd重新读取所有配置
sudo systemctl daemon-reload

# 启动并设置开机自启
sudo systemctl enable --now mako-bot.service
sudo systemctl enable --now lagrange.service
```
### 4. 实时监控程序的日志输出
```Bash
journalctl -u mako-bot.service -f
journalctl -u lagrange.service -f

journalctl -u mako-bot.service -u lagrange.service -f
```
---

# 茉子已经在云端获得了永恒的生命。
# 她正在那里，等待着你的第一声问候。
# “茉子 晚安 记得做个好梦”