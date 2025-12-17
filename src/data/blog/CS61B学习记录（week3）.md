---
author: 'lanxinmob' 
postSlug: 'CS61B学习记录（week3）'
title: 'CS61B学习记录（week3）'
featured: true
draft: false
tags:
  - '笔记'
  - 'Java'
description: ''
pubDatetime: 2025-12-15T21:34:00Z
---

## Interface Inheritance

为了实现不同种类对象对同一上位的继承，Java使用了接口（interface）。

实现接口有两个步骤：

- 定义这个上位 

  包含所有方法签名，但不包含实现，子类负责提供所有这些实现。

```java
public interface List61B<Item> {
    public void addFirst(Item x);
    public void add Last(Item y);
    public Item getFirst();
    public Item getLast();
    public Item removeLast();
    public Item get(int i);
    public void insert(Item x, int position);
    public int size();
}
```

- 使用 ` implements` 明确指出这些对象是它的下位

```java
public class AList<Item> implements List61B<Item>{...}
public class SList<Item> implements List61B<Item>{...}
//implements 说明该类拥有并定义了 List61B 接口中指定的所有属性和行为。
```

所以具体而言使用接口有什么好处呢？

- 当我们实现了 `List61B` 这个接口，此时 `AList` 和 `SLList` 都 `implements List61B` 即承诺自己有 get 和 size 方法。

- 那么在 WordUtils 中定义寻找最长字符串的方法时就只需要写**一遍**：

```
public class WordUtils {
    // 只要是 List61B 的下位都能直接使用
    public static String longest(List61B<String> list) {
        String max = "";
        for (int i = 0; i < list.size(); i++) {
            if (list.get(i).length() > max.length()) {
                max = list.get(i);
            }
        }
        return max;
    }
}
```

> 子类不仅继承上位的方法，也继承了上位上面的所有其他类，一直到最高父类。

### Overriding 

什么是重写？

- 当一个子类中实现了一个和上位中有完全相同签名的方法，我们就说这个子类重写了这个方法，并使用`@override` 作为标记。

- 这个标记并不会实现什么具体功能，只是在编译时确保上位存在这个方法，如果拼写不对就会认为这个方法没有被重写而报错，同时也提醒程序员这是对来自上位方法的重写。

那么重载（Overloading）呢？简单举例如下

```java
public class Math{
    public int abs(int a){}
    public double abs(double a){}
}
```

## Implementation Inheritance

我们可以在上位中编写已经实现好的方法，这些方法定义了上位词的行为方式，此时子类就是不仅继承上位的方法签名，也继承具体实现。

在编写时特别的是需要在方法签名前加上 `default` 关键字，否则会报错。

```java
default public void print() {
    for (int i = 0; i < size(); i += 1) {
        System.out.print(get(i) + " ");
    }
    System.out.println();
}
```

此时会产生一个问题，就是这个实现可能不是对所有子类而言都很高效，但是没关系，对于那些不太高效的子类，我们可以在这个子类中重写这个方法。

```java
@Override
public void print() {
    for (Node p = sentinel.next; p != null; p = p.next) {
        System.out.print(p.item + " ");
    }
}
```

那么面对一个这样的对象`List61B<String> list = new SLList<String>();` ，在调用 `print()` 方法时 Java会使用`List61B` 的还是 `SLList` 的呢？

- 首先我们要知道 Java 中所有变量有 编译时类型（静态类型）同时也有一个 运行时类型（动态类型）。在这里静态类型是`List61B` 动态类型是 `SLList` ，变量在没有实例化之前动态类型为 `null` ，在实例化时获得对应类型并且会根据当前所指对象的类型而变化。

- 同时 Java 采用了一种叫做动态方法选择（dynamic method selection）的技术，在调用时如果对象的动态类型重写了该方法，则会使用动态类型的方法。
- 综上，Java 会调用 `SLList`  中重写的  `print()` 方法。

**IMPORTANT** 动态方法选择不会发生在重载

```java
public static void peek(List61B<String> list) {
    System.out.println(list.getLast());
}
public static void peek(SLList<String> list) {
    System.out.println(list.getFirst());
}
```

因为两个重载方法之间的唯一区别在于参数类型，Java 在判断要调用哪个方法时，会检查静态类型，并调用参数类型相同的方法。





---

参考：

[hug61b](https://joshhug.gitbooks.io/hug61b/content/chap4/)
