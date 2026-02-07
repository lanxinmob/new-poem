---
author: 'Lanxinmob'
title: 'Unix Programming Learning Note'
postSlug: 'Unix Programming Learning Note'
featured: false
draft: false
tags:
  - '笔记'
  - 'unix'
ogImage: ''
description: ''
pubDatetime: 2026-01-12T09:00:00Z
toc: true
---

## 第二章 程序、线程和进程

#### 进程环境

> 环境列表由指针数组组成，指向“环境变量=环境变量相关的字符串值”形式的字符串。

> 进程开始执行时，外部变量`environ`指向进程环境列表，如果进程是由`execl`，`execlp`，`execvp`初始化的，他就会继承执行exec前那个进程的环境列表。`execle`和`execve`函数则专门设置了环境列表。

`Unix`的设计哲学里，启动新程序通常需要两步走：

1. **fork()**：父进程生一个子进程（此时子进程和父进程一模一样）。
2. **exec()**：子进程调用 exec，把自己变成想要运行的新程序。

**例子：**
在终端里输入 `ls` 并回车时：

1. **Shell (父进程)** 调用 `fork()`，生出一个**子 Shell**。
2. **子 Shell** 调用` exec("ls")`。
3. **子 Shell** 的身体被 `ls` 的代码接管，变成了 **ls 进程**。
4. **ls 进程** 运行结束，退出。

##### exec族

`exec`不是一个函数，而是一组函数（统称` exec `族），它们名字长得很像，区别只在于**参数怎么传**。

名字后缀的含义：

- **l (list)**：参数一个一个列出来（像列表一样）。
- **v (vector)**：参数放在一个数组（向量）里传进去。
- **p (path)**：自动去环境变量 PATH 里找程序（不用写 /bin/ls，直接写 ls 就行）。
- **e (environment)**：不继承老的环境变量，而是由你指定一个新的环境变量数组。

**常见的几个：**

1. **execl** (list):
   
   ```c
   // 需要写全路径，参数一个一个传，最后必须以 NULL 结尾 
   execl("/bin/ls", "ls", "-l", NULL);
   ```
2. **execv** (vector):
   
   ```c
   // 需要写全路径，参数放数组里 
   char *args[] = {"ls", "-l", NULL}; 
   execv("/bin/ls", args);`
   ```
3. **execlp** (list + path):
   
   ```c
   // 文件名写 "ls" 即可，系统会自动去 PATH 里找 /bin/ls 
   execlp("ls", "ls", "-l", NULL);
   ```
4. **execle** (list + environment):
   
   ```c
   // 自定义环境变量（不继承父进程的） 
   char *env[] = {"MYVAR=hello", NULL}; execle("/bin/myprog", "myprog", NULL, env);
   ```

#### 进程终止

> 一个进程终止时，操作系统就会释放进程资源、更新适当的通知消息并向其他进程通知它的死亡。终止可以说是正常的，也可以是非正常的。进程终止期间执行的活动包括取消挂起的定时器和信号、释放虚拟内存资源、释放其他持有的系统资源以及关闭打开的文件。操作系统记录进程状态和资源，通知父进程调用wait函数做出响应。

嗯...微妙的感觉

如果进程终止时父进程没有wait它，这个进程成为僵尸进程。如果父进程先结束了，子进程还在运行，这个进程成为孤儿进程。系统中的 **init 进程**（进程ID为1）会自动成为这些子进程的新父进程，并周期性wait子进程。

正常终止的情况：

- 从main函数返回或隐式返回
  - 调用exit，_exit 或 _Exit 函数

#### `exit` vs `_exit` vs `_Exit`

- **exit()**
  - 执行用户通过 `atexit` 注册的作为退出处理程序的函数。**刷新缓冲区**（比如用 printf 打印的内容可能还在内存缓存里，没写到屏幕或文件，exit 会确保把这些内容写出去），关闭打开的文件流。
  - main 函数里的 return 0 等同于调用 exit(0)。
- **_exit() 和 _Exit() **
  - 直接结束，不会刷新缓冲区（可能导致数据丢失），也不再控制终止前调用用户定义的退出处理程序。
