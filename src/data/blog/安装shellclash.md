---
author: 'Lanxinmob'
title: '安装shellclash'
postSlug: 'anzhuangshellcalsh'
featured: false
draft: false
tags:
  - '笔记'
  - 'shellclash'
ogImage: ''
description: ''
pubDatetime: 2025-09-29T09:00:00Z
toc: true
---

`export url='https://raw.githubusercontent.com/juewuy/ShellClash/master'`

- 给shell环境定义环境变量

`bash -c "$(curl -kfsSl $url/install.sh)"`

- 在bash环境下执行命令

`sh -c "$(curl -kfsSl $url/install.sh)"`

- 用/bin/sh来执行命令，而Ubutu/Debian中/bin/sh是dash