---
author: 'Lanxinmob'
title: 'Java的学习'
postSlug: 'java learning'
featured: false
draft: false
tags:
  - '笔记'
  - 'Java'
ogImage: ''
description: '初识Java'
pubDatetime: 2025-09-17T09:00:00Z
toc: true
---

# Java

- 通过 Java 虚拟机（ JVM ）实现跨平台运行，一次编写，到处运行
- 内置多线程机制，自动垃圾回收，面向对象编程

### 环境准备

- **JDK**: Java development kit
- **Gradle DSL**( domain specific language 用于通用语言如c、Java的补充): Kotlin /Groovy，gradle的dsl用于配置构建过程
- 常使用 JetBrains 的 IntelliJ IDEA 作为开发环境，功能强大

### 语法

#### 注释

- ```java
    //单行注释
    
    /*
    多行注释
    */
    
    /**
    *文档注释
    *后续可用javadoc提取
    */
    ```

- 必须有一个包含 main( ) 函数的类作为程序起点

#### 数据类型

- ```java
  //数组
  int[]  score = {1,2,4};
  ```

- byte 类型 8bit，有符号整数，char 类型为 16 bit，可表示 Unicode 字符

- Java 中的字符串为不可变对象，一旦创建，值就不会变，若修改则会创建一个新对象。

#### 运算符

- Java 中逻辑右移运算符 `>>>>`，右移后高位补零，若为负数不保留符号

-  在 Java 用 `.equals()` 比较内容，`==` 比较的是对象地址

#### 输入输出

- ```java
  import java.util.Scanner;//导入标准库
  
  public class Hello {
      public static void main(String[] args) {
          System.out.println("Hello world!");
  
          Scanner sc = new Scanner(System.in);
          //创建了一个 Scanner 对象，从标准输入读取数据
         
          System.out.print("What is your name? ");//print输出完不换行
          String name = sc.nextLine();//读取一整行
          System.out.println("Hello " + name);//println输出完自动换行
  
          sc.close(); // 关闭输入流
      }
  }
  ```

### 接口

- 定义接口
  ```java
  public interface Animal {
      void eat();
      void sleep();
  }
  
  public interface Swimmable {
      void swim();
  }
  ```
  
-  实现接口，且必须重写接口中方法
  ```java
  public class Dog implements Animal {
      @Override
      public void eat() {
          System.out.println("Dog is eating");
      }
  
      @Override
      public void sleep() {
          System.out.println("Dog is sleeping");
      }
  }
  
  public class Duck implements Animal, Swimmable {
      @Override
      public void eat() {
          System.out.println("Duck is eating");
      }
  
      @Override
      public void sleep() {
          System.out.println("Duck is sleeping");
      }
  
      @Override
      public void swim() {
          System.out.println("Duck is swimming");
      }
  }
  
  
- 不同类实现相同接口时，可以通过接口引用统一调用。
   ```java
   public class Test {
      public static void main(String[] args) {
          Animal dog = new Dog();
          dog.eat();
          dog.sleep();
    
          Animal duck = new Duck();
          duck.eat();
          duck.sleep();
      }
  }
  ```
  
- Java 中一个类不能同时继承多个抽象类，但可以实现多个接口，让 Java 类可以获得多种行为。

#### ？

- 但是为什么不直接分别在 Dog 类和 Duck 类实现 eat 和 sleep 方法：

- 编译器会强制要求 `Dog` 和 `Duck` 必须实现接口里的所有方法，这样当 `Animal` 接口的方法需要调整时，比如新增一个 `move()` 方法：

   - 有接口的情况下，编译器会帮你检查所有实现类，让你有序修改，否则就算有遗漏或其他问题也不会报错

- 同时接口可以实现多态调用

- ```java
   public void feed(Animal animal) {
       animal.eat();
   }
   public static void main(String[] args) {
           Animal dog = new Dog();
           Animal duck = new Duck();
           Animal cat = new Cat();
   
           feed(dog);   // Dog eats bones
           feed(duck);  // Duck eats grains
           feed(cat);   // Cat eats fish
   }
   ```

- 无论是 `Dog`、`Duck`、`Cat` 还是其他动物，只要实现了 `Animal`，都能用同一个方法处理。实际运行的行为由对象的类型决定，调用的是不同类型各自重写的实现方法，从而实现多态。

### 标准库

- 和C++中的STL一样，Java 也提供了功能强大的标准类库，如Math、BigInteger、Arrays、 LinkedList、HashMap、HashSet



---

参考：

- [酒井科协暑培25 java](https://summer25.net9.org/frontend/java/Java_introduction/)

- 示例代码来自chatgpt