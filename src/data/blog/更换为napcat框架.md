---
author: 'Lanxinmob'
title: '更换为Napca框架'
postSlug: '更换为Napca框架'
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

[2025-10-15 20:16:01]: 安装位置:
[2025-10-15 20:16:01]:   /root/Napcat
[2025-10-15 20:16:01]:
[2025-10-15 20:16:01]: 启动 Napcat (无需 sudo):
[2025-10-15 20:16:01]:   xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox
[2025-10-15 20:16:01]:
[2025-10-15 20:16:01]: 后台运行 Napcat (使用 screen, 无需 sudo):
[2025-10-15 20:16:01]:   启动: screen -dmS napcat bash -c "xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox "
[2025-10-15 20:16:01]:   带账号启动: screen -dmS napcat bash -c "xvfb-run -a /root/Napcat/opt/QQ/qq --no-sandbox  -q QQ号码"
[2025-10-15 20:16:01]:   附加到会话: screen -r napcat (按 Ctrl+A 然后按 D 分离)
[2025-10-15 20:16:01]:   停止会话: screen -S napcat -X quit
[2025-10-15 20:16:01]:
[2025-10-15 20:16:01]: Napcat 相关信息:
[2025-10-15 20:16:01]:   插件位置: /root/Napcat/opt/QQ/resources/app/app_launcher/napcat
[2025-10-15 20:16:01]:   WebUI Token: 查看 /root/Napcat/opt/QQ/resources/app/app_launcher/napcat/config/webui.json 文件获取
[2025-10-15 20:16:01]:
[2025-10-15 20:16:01]: TUI-CLI 工具用法 (napcat):
[2025-10-15 20:16:01]:   启动: napcat

[2025-10-15 20:16:01]: --
[2025-10-15 20:16:01]: Shell (Rootless) 安装流程完成。