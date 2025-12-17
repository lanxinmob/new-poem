---
author: 'lanxinmob' 
postSlug: 'CS61B学习记录（week1）'
title: 'CS61B学习记录（week1）'
featured: false
draft: false
tags:
  - '笔记'
  - 'Java'
description: ''
pubDatetime: 2025-10-19T23:00:00Z
---

# CS61B学习记录（week1）

![image-20251016161901606](C:\Users\N\AppData\Roaming\Typora\typora-user-images\image-20251016161901606.png)

还能说什么呢，直接泪目。

### Java Workflow

在 Java 中，将程序从` .java `文件转换为可执行文件主要有两个步骤：编译和解释。

![img](https://cs61b-2.gitbook.io/cs61b-textbook/~gitbook/image?url=https%3A%2F%2F2316889115-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCLYj7ccqvV6l4Pt9R0w5%252Fuploads%252FhedhIwHW0XpvKamJ52mc%252F1-2-compile-interpret.svg%3Falt%3Dmedia%26token%3Da953a222-d3a8-4f12-9053-5d01e0ec67c8&width=768&dpr=4&quality=100&sign=8f8415b2&sv=2)

在终端中的执行指令

```bash
$ javac HelloWorld.java
$ java HelloWorld
Hello World!
```

#### class files

为什么使用`.class` files

- `.class`文件经过了类型检查，这使得分发的代码更安全，对源码处理后执行效率也更高，并且在涉及知识产权的情况下能保护实际的源代码。

### Static Typing

Java 是一种静态类型语言，这意味着所有变量、参数和方法都必须具有声明类型。声明后，类型将永远无法更改。表达式也具有隐式类型。

静态类型的优点:

- 在编码过程中更早地发现类型错误，减轻程序员的调试负担。

- 避免给终端用户带来类型错误。

- 使代码更易于阅读和推理。

- 避免耗时的运行时类型检查，提高代码效率。
- 能让程序员确切知道自己正在处理的是哪种对象

缺点：

- 代码更冗长，通用性更差

与其形成对比的是 Python 等动态类型语言，在动态类型语言中，用户则可能会在**运行时**遇到类型错误。因为Python 不限制类型，所以它不能假设你想要什么类型。

### Static vs. Non-static

静态方法使用类名来调用，不能访问实例变量。

非静态（实例）方法使用实例名来调用。

有一些类从不实例化比如，Math。

```java
x = Math.round(5.6)
```

静态方法要使用实例变量就要指定实例，

```java
public static Dog maxdog(Dog d1,Dog d2){
       if (d1.weight>d2.weight){
           return d1;
       }return d2;
}
Dog.maxdog(d1,d2);
public Dog maxdog(Dog d2){
       if (this.weight>d2.weight){
           return this;
       }return d2;
}
d1.maxdog(d2);
```

非 static 变量：属于某个类的实例，每个实例都有自己的一份副本。
static 变量：属于类本身，整个类共享一份，所有实例都访问同一个变量。

### Declaring a Variable

不同于 c/cpp，Java 向程序员隐藏内存位置会降低控制力，这会使你无法进行某些类型的优化。不过，这也能避免一大类非常棘手的编程错误。在计算成本极低的现代，这种权衡通常是非常值得的。

> As the wise Donald Knuth once said: "We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil".



---

参考：

[cs61B textbook](https://joshhug.gitbooks.io/hug61b/content)





