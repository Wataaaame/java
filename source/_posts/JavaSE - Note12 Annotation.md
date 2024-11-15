---
title: JavaSE - Note12 Annotation
date: 2022-07-12 16:03:48
categories: JavaSE
tasg: Annotation
---

# 注解/注释类型

是一种引用数据类型，编译之后也是生成 .class 文件

等同于一种标记，可以在编译，类加载，运行时被读取，并执行相应的处理

通过注解开发人员可以在不改变原有代码和逻辑的情况下在源代码中嵌入补充信息

注解可以出现在类上、属性上、方法上、变量上等任意位置（支持套娃注解）

定义：`[修饰符列表] @interface 注解类型名 {}`

```java
public @interface MyAnnotation {
}
```

使用：`@注解类型名`

```java
@MyAnnotation
public class Annotation {
}
```

## JDK 内置注解

`java.lang`

1. **Deprecated**

   此注解标注的类型已过时，不鼓励其他程序员使用，通常是因为它很危险或存在更好的选择

2. **Override**

   重写方法，标识性注解，表示需重写父类中的方法

   - 只能注解方法
   - 给编译器参考，若方法有误则编译器报错
   - 和运行阶段无关

3. SuppressWarnings

   表示在注释元素（以及注释元素中包含的所有程序元素）中应该抑制命名的编译器警告

## 元注解

用来标注“注解类型”的注解被称为**元注解**

常见的元注解有：

1. **Target**

   用来标注”被标注的注解“**出现**的位置

2. **Retention**

   用来标注“被标注的注解”**保存**的位置

   | 注解名                  | 保存位置                       |
   | ----------------------- | ------------------------------ |
   | RetentionPolicy.SOURCE  | Java 源文件                    |
   | RetentionPolicy.CLASS   | class 文件                     |
   | RetentionPolicy.RUNTIME | class 文件（支持反射机制读取） |

元注解实例：

```java
// 被标注的注解 Override 只能出现在方法上
@Target(ElementType.METHOD)
// 被标注的注解 Override 保存在源文件当中
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

## 自定义注解

注解中可以定义属性，类似于

```java
// 以下属性代码与属性的使用代码一一对应
public @interface MyAnnotation {
    // f1()
    String name();	// 属性看起来像一个方法
    // f2()
    int age() default 22;	// 属性默认值
    // f3()
    String value();	// 只有 value 作为属性名
    // f4()
    String[] email();	// 数组类型
}
```

属性的使用：

```java
public class MyAnnotationTest {
    // 若注解中有属性，那么必须给属性赋值
    @MyAnnotation(name = "vv")
    public void f1() {}
    
    // 若属性已使用 default 指定了默认值，使用时可以不用再指定值
    @MyAnnotation(age)
    public void f2() {}
    
    // 若使用 value 作为属性名，且仅有该属性，则使用时可省略
    @MyAnnotation("ee")
    public void f3() {}
    
    // 若使用数组，则使用大括号
    // 若数组只有一个元素，则大括号可以省略
    @MyAnnotation(email = {"1@vv.com", "2@vv.com"})
    public void f4() {}
}
```

### 属性的类型

基本数据类型 + String + Class + Enum

包括以上每种类型的数组形式

## 反射注解

Retention 元注解中使用 “RetentionPolicy.RUNTIME”，即可被反射获取

```java
// 获取这个 class
Class c = Class.forName([package].[class]);
// 判断该 class 是否有 @MyAnnotation
if (c.isAnnotationPresent(MyAnnotation.class)) {
    // 获取该注解对象
    MyAnnotation ma = (MyAnnotation)c.getAnnotation(MyAnnotation.class);
    System.out.println(ma);
    // 获取注解对象的属性，和调接口没区别
    System.out.println(myAnnotation.value());
}

```

