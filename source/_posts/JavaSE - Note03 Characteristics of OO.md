---
title: JavaSE - Note03 Characteristics of OO
date: 2022-05-12 09:17:25
categories: JavaSE
tags: [Encapsulation, Inheritance, Mixins]
---

# 面向对象的三大特征

封装（Encapsulation）

继承（Inheritance）

多态（Mixins）

> 封装 -> 继承 -> 多态

# 封装（面向对象的首要特征）

1. 保证内部结构安全
2. 屏蔽复杂，暴露简单

> 在代码级别上，一个类体中的数据，对于调用者来说，不需要关心代码的复杂实现，只需要通过一个简单的入口就可以访问。另外类体中安全级别较高的数据封装起来，外部人员不能随意访问，以保证数据的安全性

## 封装实现

1. 属性私有化（使用 `private` 关键字进行修饰）
2. 对外提供简单的操作入口（都不带 `static`，都是实例方法/对象方法）

## 方法调用

1. 带有 static 的方法

   通过“类名.”的方式访问

2. 没有 static 的方法（实例方法/对象方法，对象级别的方法）

   先 `new` 对象，通过“引用.”的方式去访问

> 对象被称为实例
>
> 实例相关的有：实例变量、实例方法
>
> 实例变量是对象变量，实例方法是对象方法

# 继承 Extends

基本作用：子类继承父类，代码可以得到复用

主要作用：有了继承关系，才有了后期的方法覆盖和多态机制

## 继承语法

`class B extends A`

A 为父类，B 为子类，A 类继承给 B 类，B 类继承 A 类

## 继承的相关特性

1. B 类继承 A 类，则 A 类称为超类（superclass）、父类、基类

   B 类则称为子类（subclass）、派生类、扩展类

2. Java 中只支持单继承，而 C++ 中才支持多继承（体现简单性）

   eg. `class C extend A, B {}`

3. 虽然 Java 不支持多继承，但是可以采用间接继承的效果，例如：

   ```java
   class A {}
   class B extends A {}
   class C extends B {}
   ```

   其中：B 直接继承 A，C 直接继承 B、间接继承 A

4. 子类继承父类，仅构造方法无法继承，且父类私有属性无法在子类中直接访问

5. Java 中的类没有显式继承任何类，则默认继承 Object 类，这是 Java 语言提供的根类，一个对象与生俱来就有 Object 类型中所有特征

6. 继承缺点：代码耦合度高，父类修改，子类受牵连

## 继承使用时机

凡是能用 "is a" 描述的，都可以继承

eg. Cat is a Animal

> 凡是能用 “have/has a” 描述的，属于关联关系，统一以属性的方式存在
>
> eg. Customer has a food menu
>
> 凡是能用 “like a” 描述的，表示实现关系，通常是类实现接口
>
> eg. Chef like a menu

## Object.class

目录：`%JAVA_HOME%/lib/src.zip`

- `toString()`

  将“Java 对象”转换成“字符串”的形式，返回 `[class_name]@[hash_address]`

  eg. `System.out.println(引用.toString);`

  > 当直接输出一个“引用”时，println() 方法会自动调用 `.toString()`
  >
  > 可以进行方法覆盖，类名可以直接返回想要结果


# 方法覆盖 & 多态

## 方法覆盖/方法重写（Override/Overwrite）

子类继承父类之后，当继承过来的方法无法满足当前子类的业务需求时，有必要进行“方法覆盖”

当子类对父类继承过来的方法进行“方法覆盖“之后，子类对象调用该方法的时候，**一定执行覆盖之后的方法**

### 构成条件

1. 两个类必须有继承关系
2. 重写之后的方法与之前的方法具有相同的**返回值类型、方法名、参数列表**
3. 访问权限不能更低，只能更高（public > protected） 
4. 重写之后的方法不能比之前的方法抛出更多的异常，可以更少

> 注意事项：
>
> 1. 方法覆盖只针对方法，和属性无关
> 2. 私有方法无法覆盖
> 3. 构造方法不能被继承（也不能被覆盖）
> 4. 方法覆盖只针对实例方法，静态方法覆盖无意义
> 4. 基本返回值类型必须相同，应用返回值类型可以更小（子类）

### 方法重载与方法覆盖区别

- 重载发生在同一类中；覆盖发生在具有继承关系的父子类中
- 重载方法名相同，参数列表不同；覆盖除方法体外皆相同

## *多态

多种形态，多种状态

父类型引用指向子类型对象，过程包括编译阶段和运行阶段

- 编译阶段：绑定父类的方法
- 运行阶段：动态绑定子类型对象的方法

> 编译的时候一种形态，运行的时候另一种形态

### 基础语法

1. 向上转型（upcasting）

   `Animal a1 = new Cat();`

   子 转 父 （自动类型转换）

2. 向下转型（downcasting）

   `Cat c1 = (Cat)a1;`
   
   父 转 子 （强制类型转换，需要加强制类型转换符）
   
   > 当需要访问子类对象中特有的方法时才需要向下转型
   >
   > 向下转型（强制）存在风险

***大前提是有继承关系**

> 多态转型使用向上/向下类型转换术语，自动/强制类型转换使用于基本数据类型

### Example

父类 Animal，拥有 move() 方法；子类 Cat，拥有 move() 方法、catchMouse() 方法

```java
// 向上转型（自动）
Animal a1 = new Cat();
a1.move();

// 向下转型（强制）
Animal a2 = new Cat();
Cat c1 = (Cat)a2;
c1.catchMouse()
```

Java 程序分为编译阶段和运行阶段

#### 编译阶段

对于编译器而言，编译器只知道 a1 的类型是 Animal，所以编译器在检查语法的时候，会去 Animal.class 字节码文件中找 move() 方法，找到后绑定上 move() 方法，编译通过，静态绑定成功（编译器阶段属于静态绑定）

#### 运行阶段

运行阶段的时候，实际上在堆内存中创建的 Java 对象是 Cat 对象，所以 move 的时候，真正参与 move 的对象是一只猫，所以运行阶段会动态执行 cat 对象的 move 方法，这个过程属于运行阶段绑定（运行阶段绑定属于动态绑定）

### 类型转换异常

java.lang.ClassCastException

父类 Animal，子类 Cat 与 Bird

```java
// Bird 向上转型为 Animal a1
Animal a1 = new Bird;
// a1 向下转型为 Cat c1
Cat c1 = (Cat)a1;
c1.catchMouse();

// 编译通过，运行报错
```

编译器检测 a6 这个引用是 Animal 类型，与 Cat 存在继承关系，可以通过

而运行阶段，堆内存中 a1 实际创建的是 Bird 对象，Bird 向下转换为 Cat 时，由于无继承关系，导致出现类型转换异常

#### 如何避免？

使用 `instanceof` 运算符，可以在**运行阶段动态判断**引用指向的对象类型

##### 语法

`引用 instanceof 类型`

##### 运算结果

只能是：true/false

假设 `a1 instanceof Cat`

- true：a1 引用指向的堆内存中的 Java 对象是一个 Cat
- false：a1 引用指向的堆内存中的 Java 对象不是一个 Cat

以上代码可修改为：

```java
// 如果 a1 是一只 Cat，则进行向下转换
if (a1 instanceof Cat) {
    Cat c1 = (Cat)a1;
    a1.catchMouse();
}
```

> Java 规范中要求：凡是对类型进行向下转型，一定要使用 `instanceof` 运算符进行判断

### *开发中的作用

降低程序的耦合度，提高程序的扩展力（面向父类型编程、面向更加抽象进行编程）

```java
public class Master {
    public void feed(Dog d1) {}
    public void feed(Cat c1) {}
}
```

以上代码中 Master 与 Dog 及 Cat 关系很紧密（耦合度高），导致扩展力差

```java
public class Master {
    public void feed(Pet p1) {}
}
```

以上代码中 Master 与 Dog 及 Cat 关系就脱离了，Master 关注 Pet 类，这样就降低了耦合度，提高扩展性

> 软件开发有七大原则，其中 OCP（开闭原则）：对扩展开放，对修改关闭
>
> 软件扩展过程中，修改的越少越好

# 总结

封装、继承、多态一环扣一环

有了封装后，对象和对象之间产生了继承

有了继承后，才有方法的覆盖和多态

> 私有方法不能覆盖
>
> 静态方法不考虑覆盖
