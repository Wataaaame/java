---
title: JavaSE - Note00 Mechanism
date: 2022-05-26 14:23:05
categories: JavaSE
tags: Mechanism
---

# cmd 命令行编译方法

- 第一种方式

  ```
  javac Student.java
  javac StudentTest.java
  ```

- 第二种方式

  ```
  javac StudentTest.java
  ```

- 第三种方式

  ```
  javac *.java
  ```


# 关于垃圾回收器（GC）

在 Java 语言中，垃圾回收器主要针对的是堆内存，当一个 Java 对象没有任何引用指向该对象时，GC 会考虑将该垃圾数据释放回收掉

# JVM 内存中存储类型

Java 中的三大变量

![Java 中的三大变量](/images/image-java/note04-03-java中的三大变量.png)

# 退出 JVM

`System.exit(0);`

其中：

- 0 代表正常退出
- 任意数字（例如 -1）代表异常状态退出

# == && equals

基本数据类型使用 `==`比较是否相等

引用数据类型统一使用 `equals()` 方法比较是否相等

