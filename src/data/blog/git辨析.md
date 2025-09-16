---
author: 'lanxinmob' 
postSlug: 'git command analysis'
title: 'git指令辨析'
tags:
  - '笔记'
  - 'markdown'
  - 'git'
description: '一些markdown语法和git指令辨析'
pubDatetime: 2025-09-13T09:00:00Z
---

## markdown语法

<img src="D:\图片\rin.jpg" alt="#" width="400">

<center>
    <img src="D:\图片\rin.jpg" alt="#" width="400">
    <br>
    <div style="color:#C0C0C0;">
        her name is Rin.
    </div>
</center>

- 不知道为什么不能正常使用行内公式的语法`$...$`

## git指令

- `git restore <文件名>` 或`git checkout <文件名>`都是撤回工作区的修改

- `git rm --cached <文件名>`移除暂存区文件但保留工作区文件
- `git rm <文件名>`在`working tree clean`时使用，删除工作区文件，同时将删除文件添加到暂存区
- `git rm -f <文件名>`在文件`commit`一次后时随意使用，删除工作区文件，同时将删除文件添加到暂存区，但如果该文件没有`commit`过，那么不会将删除文件添加到暂存区。
- `git merge <分支名>`将指定分支合并到当前分支，保留分支历史，矛盾集中爆发，生成一个新的合并提交
- `git rebase <分支名>`依次合并，每次合并一个提交，历史更线性，冲突发生时更容易知道是哪个改动引起的

### `commit message`格式

- `[type] [scope] [descriptions]`
- `feat fix docs style refactor test chore` `api login user readme等` `简述内容 动词开头`

- ```
  feat(chat):添加什么功能
  fix(api):修复接口什么错误
  style:调整代码缩进和格式
  docs(readme):更新项目说明文档
  ```

- 一次commit只做一件事，必要时详细说明

---

参考:

- [清华科协暑期培训](https://www.bilibili.com/video/BV1Hh81z9EG2)

