---
title: JavaSE - Java 字符串比较方法
date: 2022-04-26 21:44:51
tags: Experience
categories: JavaSE
---

# Java字符串比较方法

字符串比较的常用方法有 3 

1. equals()
2. equalsIgnoreCase()
3. compareTo()

## equals()

逐个比较两字符串每个字符是否相同，如果两个字符串具有相同的字符和长度，返回 true，否则返回 false

> 字符的大小写也在检查的范围之内

语法格式

```java
str1.equals(str2);
```

str1 和 str2 可以是字符串变量， 也可以是字符串字面量

```java
"Hello".equals(greeting);
```

## equalsIgnoreCase()

与`equals()`完全相同，唯一不同的是`equalsIgnoreCase()`比较时不区分大小写，当比较两个字符串时，它会认为 A-Z 和 a-z 是一样的

## compareTo()

`compareTo()` 方法用于按字典顺序比较两个字符串的大小，该比较是基于字符串各个字符的 Unicode 值。`compareTo()` 方法的语法格式如下：

```java
str.compareTo(String otherstr);
```

它会按字典顺序将 str 表示的字符序列与 otherstr 参数表示的字符序列进行比较。如果按字典顺序 str 位于 otherster 参数之前，比较结果为一个负整数；如果 str 位于 otherstr 之后，比较结果为一个正整数；如果两个字符串相等，则结果为 0

> 如果两个字符串调用 `equals()` 方法返回 true，那么调用 `compareTo()` 方法会返回 0

# equals()与==的区别

1. `equals()` 是方法，而 `==` 是操作符
2. 对于基本类型的变量来说（如 `short`、 `int`、 `long`、 `float`、 `double`），只能使用 == ，因为这些基本类型的变量没有 equals 方法。对于基本类型变量的比较，使用 == 比较， **一般比较的是它们的值**。
3. `equals()` 方法比较字符串对象中的字符，而 `==` 运算符比较两个对象引用看它们是否引用相同的实例（即指针是否指向同一地址）

> 对于习惯使用 C++的String类的人来说，在进行相等性检测的时候一定要特别小心。C++ 的 String 类重载了`==`运算符以便检测字符串内容的相等性。可惜 Java 没有采用这种方式，它的字符串“看起来、感觉起来”与数值一样，但进行相等性测试时，其操作方式又类似于指针。语言的设计者本应该像对 C++ 那样也进行特殊处理， 即重定义`==`运算符。
