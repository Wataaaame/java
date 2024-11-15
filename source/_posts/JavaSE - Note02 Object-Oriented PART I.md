---
title: JavaSE - Note02 Object-Oriented PART I
date: 2022-04-28 15:12:22
tags: Object-Oriented
categories: JavaSE
---

# 面向对象的概念（Object-Oriented）

当我们采用面向对象的方式贯穿整个系统的话，涉及三个术语

- OOA（Object-Oriented Analysis）：面向对象分析
- OOD（Object-Oriented Design）：面向对象设计
- OOP（Object-Oriented Programming）：面向对象编程

## 面向对象三大特征

- 封装（Encapsulation）
- 继承（Inheritance）
- 多态（Polymorphism）

## 类和对象相关的概念

**类**：是一个抽象的概念、模板，现实当中不存在，是我们人类大脑进行思考、总结、抽象的一个结果

**对象（实例）**：现实生活中实际存在的个体

**实例化**：通过类这个模板创建对象的过程

**抽象**：思考总结抽取多个对象共同特征的过程

> 类是对象的抽象，对象是类的具体实现
>
> ```
> 类	--实例化-->	对象（示例）
> 对象	--抽象-->		类
> ```

### 类

类 = 属性 + 方法

- 属性来源于状态（名词）
- 方法来源于动作（动词）

#### 类的定义

语法格式

```java
[修饰符列表] class [类名] {
    // 类体 = 属性 + 方法
    // 属性在代码上以“变量”的形式存在
    // 方法描述动作/行为
}
```

#### 为什么属性是以变量的形式存在的？

因为属性对应的是“数据”，数据在程序中只能放到变量中

> 学生包括：
>
> - sno: 131
> - name: "vv"
> - sex: '1'
> - address: "Wuhan"

#### 变量的分类

根据出现位置划分：

- 方法体当中声明的变量：局部变量
- 方法体外声明的变量：成员变量（即全局变量，包含实例变量、类变量、常量）

根据存储位置划分：

- 栈内存：局部变量
- 堆内存：成员变量

### 对象

类是一个模板，通过一个类可以创建多个对象

#### 对象的创建和使用

语法： `new 类名();`

```java
Student s1 = new Student();
```

> 与 `int i = 10;` 一个道理

#### 默认值

对于成员变量来说，没有手动赋值时，系统默认赋值：

| 类型         | 默认值 |
| ------------ | ------ |
| byte         | 0      |
| short        | 0      |
| int          | 0      |
| long         | 0L     |
| float        | 0.0F   |
| double       | 0.0    |
| boolean      | false  |
| char         | \u0000 |
| 引用数据类型 | null   |

- `null` 是一个 Java 关键字，全部小写，表示空

#### 对象和引用的区别？

- 对象是通过 `new` 出来的，在堆内存中存储
- 引用是保存了对象的内存地址

#### 访问实例对象的语法

`[引用].[实例变量名]`

```java
System.out.println(s1.no);
System.out.println(s2.no);
```

> 实例变量只能通过对象访问

### Example

```java
// Student.java
public class Student {
    int no;
    String name;
    int age;
    boolean sex;
    String addr;
}
```

```java
// StudentTest.java
public class StudentTest {
    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        
        s1.no = 101;
        s2.no = 102;
        
        System.out.println(s1.no, s2.no);
    }
}
```

![对象和引用执行原理](/images/image-java/note02-01-对象和引用执行原理.png)

### 空指针异常

java.lang.NullPointerException

“空引用”访问实例【对象相关】的相关数据时，都会出现空指针异常

# 构造方法

写在类中，用来创建对象，同时给对象的属性赋值以开辟空间

- 当一个类没有提供任何构造方法，系统会默认提供一个无参数的构造方法，这个无参数的构造方法叫做“缺省构造器”
- 实例变量没有手动赋值的时候，系统会赋默认值
- 当一个类中提供了构造方法，系统将不再提供构造方法
- 无参构造方法和有参构造方法都可以调用
- 构造方法支持方法重载

> 建议将无参构造手动写出来，以确保万无一失

## 构造方法的语法结构

`[修饰符列表] 构造方法名(形式参数列表) {构造方法体}`

构造方法体中一般用来对属性进行赋值

e.g. `public Student() {sno=101}`

## 调用构造方法的语法格式

使用 new 运算符来调用构造方法（普通方法是用“ .”来调用）

`new 构造方法名();`

e.g. `Student s1 = new Student();`

