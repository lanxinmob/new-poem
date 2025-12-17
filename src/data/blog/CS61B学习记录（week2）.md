---
author: 'lanxinmob' 
postSlug: 'CS61B学习记录（week2）'
title: 'CS61B学习记录（week2）'
featured: true
draft: false
tags:
  - '笔记'
  - 'Java'
description: ''
pubDatetime: 2025-10-27T23:00:00Z
---

# CS61B学习记录（week2）

### Public vs. Private

为什么要使用 `private` 限制访问？

- 有些东西是程序设计者需要而使用者不需要知道的细节。
- 同时我们可以修改任何私有变量、方法，因为其他人不会依赖它。
- 另一方面，那些标为 `public` 的意味着永远不会删除他们，其他人永远可以访问这个方法。

### Nested Classes

静态的嵌套类不能访问外部类的任何内容，好处是可以给每个该类实例节省一点内存。

因为如果嵌套类不加 `static`，Java 会默认给每个嵌套类实例，偷偷加一个指向外部类实例的引用。

但既然用不到外部类的东西，这个偷偷加的引用”就是多余的 ，加`static`后，嵌套类变成静态嵌套类，每个实例就不会有这个多余引用，从而节省少量内存。

那么完全不使用外部类（比如`SLList`）的实例成员（实例变量、实例方法）时，就不妨给嵌套类加`static`。

### Generic ALists

Java 有一个令人恼火的特性就是不能使用泛型数组，不能这样写

```java
Glorp[] items = new Glorp[8];
```

而需要使用这样的语法

```java
Glorp[] items = (Glorp []) new Object[8];
```

虽然这样写同样会有 warning ，但是可以忽略。

### Missing  package statement

在专业的 Java 项目中，所有的类都应该被组织在包中。

> 包是 Java 类的集合，这些类协同工作以实现某个共同目标。

当一个类没有 `package` 声明时，它被视为位于默认包中。虽然这在小型测试文件或学习代码中可以接受，但在 IDE 中，通常会触发此警告，因为它不符合标准的 Java 项目结构规范。

通过在文件顶部使用 package 关键字指定包名来表明代码属于某个包。例如，

```java
package AList;
```

