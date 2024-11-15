---
title: JavaSE - Note05 Object-Oriented PART II
date: 2022-06-06 15:41:41
categories: JavaSE
tags: [Object-Oriented, Advance]
---

# Final

是一个关键字，表示**最终的、不可变的**，可以修饰变量、方法、类

## Final 修饰类

final 修饰的类无法被继承

```java
final class A {}
class B extends A {}

// 错误: 无法从最终A进行继承
```

## Final 修饰方法

final 修饰的方法无法被覆盖

```java
class C {
	public final void doSome() {
		System.out.println("C do some");
	}
}

class B extends C {
	public void doSome() {
		System.out.println("D do some");
	}
}

// 错误: B中的doSome()无法覆盖C中的doSome()
```

## Final 修饰变量

final 修饰的变量只能**赋一次值**

### 静态变量

```java
public class TS01 {
	public static void main(String[] args) {
		final int j = 100;
		j = 200;
	}
}
// 错误: 无法为最终变量j分配值

public class TS01 {
	public static void main(String[] args) {
		final int k;
		k = 100;
        k = 200;
	}
}
// 错误: 可能已分配变量k
```

### 引用

final 修饰引用，该引用只能且永远指向一个对象，但可以修改对象内部数据

该对象不会被 GC 回收，直到当前方法结束，才会释放空间

### 实例变量

final 修饰的实例变量，系统不会赋默认值，必须手动赋值

定义实例变量时赋值，赋值的时机是执行构造方法时（也就是 new 一个对象时），所以有两个赋值方法：

1. 定义实例变量时赋值
2. 构造方法中赋值

## 常量

`static final` 联合修饰的变量称为常量

区别于静态变量，常量值不可改变。相同的是都存储在方法区，且在类加载时初始化

> 既然 final 修饰的实例变量不可被改变，所以**一般加上 static** 得以节省空间
>
> 因为无法修改，所以常量一般可以公开
>
> 常量名建议全部大写，每个单词之间使用下划线衔接
>
> 记忆方法：常量是静止（static）不变的（final）

# 抽象类 & 接口

## 抽象类

类与类之间的共同特征进一步提取形成**抽象类**，属于引用数据类型

### 语法

`[修饰符列表] abstract class [类名] {类体}`

### 特点

1. 由于类本身不存在，所以抽象类无法创建对象

2. 抽象类无法实例化，所以一般用来继承，且有构造方法


> 抽象类与抽象类还可以继续抽象
>
> 不能联合 final 使用，否则矛盾

### 抽象方法

抽象方法表示没有实现的方法、没有方法体的方法

`public abstract void doSome();`

#### 特点

1. 没有方法体，以分号结尾
2. 用 abstract 关键字修饰
3. 抽象类中不一定有抽象方法，但**抽象方法必须出现在抽象类中**
4. 一个**非抽象类继承抽象类**，必须**将抽象类中的抽象方法实现**（抽象类继承抽象类无需实现）

### 扩展

面试题：Java 语言中凡是没有方法体的方法都是抽象方法。

- 错，Object 类中就有很多方法没有方法体，都是以“；”结尾，但它们都不是抽象方法，例如：

  `public native int hashCode()`

  这个方法底层调用了 C++ 动态链接库程序，且修饰符列表中没有 abstract，使用的是 native 调用 JVM 本地程序

## 接口

一种引用数据类型，编译之后也是一个 “.class” 文件

### 特点

1. 完全抽象（抽象类是半抽象的），或者说是一种特殊的抽象类
2. 支持多继承，一个接口可以继承多个接口
3. 接口中所有元素都是 public 修饰的
4. 类和类之间叫做**继承**，类和接口之间叫做**实现**（可以看作继承）
5. 当一个非抽象的类实现接口的话，**必须将接口中所有的抽象方法全都实现**（覆盖、重写）
6. 一个类可以实现多个接口，这种机制弥补了 Java 中类与类之间的单继承带来的缺陷
7. 接口与接口之间强制类型转换时无需继承关系（编译不报错，区别于类的强转），但运行时会出现 ClassCastException 异常
7. 接口无构造方法

> 接口通常提取（抽象）的是行为动作（如：飞翔、游泳）

### 基础语法

`[修饰符列表] interface 接口名 {}`

接口中只包含两部分：

1. 常量（static、final 修饰符可省略）
2. 抽象方法（abstract 修饰符可省略）

接口的实现使用关键字：`implements`

> public 修饰符可省略
>
> 接口都是抽象方法，所以不能有方法体
>
> 接口支持多态
>
> 与 extends 并存的话，extends 在前，implements 在后

```java
// 特殊的抽象类，完全抽象，叫做接口
interface MyMath {
    // public static final double PI = 3.1415926;
    double PI = 3.1415926;
    
    // public abstract int sum(int a, int b);
    int sum(int a, int b);
    int sub(int a, int b);
}

// 非抽象类，名字随意，使用 implements
class MyMathImpl implements MyMath {
    // 重写、覆盖、实现 接口中的方法（通常叫做实现）
    public int sum(int a, int b) {
        return a + b;
    }
    
    public int sub(int a, int b) {
        return a - b;
    }
}
```

### 开发中的作用

面向接口编程，类似于多态在开发中的作用，接口的使用离不开多态机制，能够降低调用者与使用者之间的耦合

> 多态：面向抽象编程，不要面向具体编程，降低程序耦合度，提高程序扩展力

#### Example

餐厅吃饭

- 菜单是一个接口
- 顾客负责调用接口
- 厨师负责实现这个接口（implements）

餐厅的“菜单”，让“顾客”与“厨师”解耦合，他们之间通过菜单沟通

```java
public class TS01 {
	public static void main(String[] args) {
        // 厨师准备好
		FoodMenu cc1 = new ChinaChef();
		FoodMenu wc1 = new WesternChef();

        // 顾客选择中国厨师
		Customer c1 = new Customer(cc1);
		c1.order();

        // 顾客选择西餐厨师
		Customer c2 = new Customer(wc1);
		c2.order();
	}
}

/*
    Yummy Tomato Omelet
    Not bad Steak
    Can't do this...
    Yummy Steak
*/

interface FoodMenu {
    // 菜单菜品（厨师负责实现）
	void tomatoOmelet();
	void steak();
}

class Customer {
    // 顾客拥有菜单
	private FoodMenu fm;

    // 顾客选择不同厨师
	public Customer() {}
	public Customer(FoodMenu fm) {
		this.fm = fm;
	}

    // 顾客点单
	public void order() {
		fm.tomatoOmelet();
		fm.steak();
	}
}

// 中国厨师做菜
class ChinaChef implements FoodMenu {
	public void tomatoOmelet() {
		System.out.println("Yummy Tomato Omelet");
	}

	public void steak() {
		System.out.println("Not bad Steak");
	}
}

// 西餐厨师做菜
class WesternChef implements FoodMenu {
	public void tomatoOmelet() {
		System.out.println("Can't do this...");
	}
	
	public void steak() {
		System.out.println("Yummy Steak");
	}
}
```

## 区别

| 抽象类                   | 接口                       |
| ------------------------ | -------------------------- |
| 半抽象                   | 完全抽象                   |
| 有构造方法               | 无构造方法                 |
| 类与类之间仅单继承       | 接口与接口之间可多继承     |
| 一个抽象类只能继承一个类 | 一个类可以同时实现多个接口 |

> 以后接口使用的比抽象类多

# Package & Import

## Package

Java 中的包机制，作用是方便程序的管理

不同的功能类分别存放在不同的包下（按照功能划分）

### 使用方法

`package [包名]`

```java
package code.Java.JavaSE.chapter17;
```

- package 语句只允许出现在 java 源代码第一行

### 包名命名规范

一般采用公司域名倒序（具有全球唯一性）

`公司域名倒序 + 项目名 + 模块名 + 功能名`

> 带包名则称完整类名，否则为简类名

### 编译方法

采用之前的编译/运行方法无法通过

类名变成：`[包名].[类名]`

- 编译（带包生成）

  `javac -d . [类名].java`

  其中：

  - javac 负责编译
  - -d 带包编译
  - . 代表编译后生成的文件放在当前目录
  - [类名].java 被编译的 java 文件名

- 运行

  将编译后的 class 文件放在包目录中，然后执行 `java [包名].[类名]`

> 同一包下包名可省略

```java
// TS01.java
package code.java.javase.chapter17;

public class TS01 {
	public static void main(String[] args) {
		System.out.println("Hello World");
	}
}

// TS02.java
package code.java.javase.chapter17;

public class TS02 {
	public static void main(String[] args) {
		// 创建 Hello World 对象
		code.java.javase.chapter17.TS01 ts01 = new code.java.javase.chapter17.TS01();
		System.out.println(ts01);
        
        TS01 ts02 = new TS01();
        System.out.println(ts02);
	}
}
```

## Import

可以导入不同包中的类

### 使用方法

`import [包名].[类名];`

- 只能写在 package 之下，class 声明之上

- 还可以采用星号的方式，代表包下所有类

> 星号只能代表类，不能代表包名
>
> 星号效率不低，编译器在编译时，会自动根据需要，将星号转换为具体类名

### Example

Scanner 书写方式：

```java
java.util.Scanner s = new java.util.Scanner(System.in);
```

其中：`java.util` 是包名，`Scanner` 是类名

可以使用 `import` 导入包：

```java
import java.util.Scanner;
// import java.util.*;

Scanner s = new Scanner(System.in);
```

### 省略 import 的情形

java.lang.* 包下的直接子类无需导入

同包下无需导入

其余一律需要

> import包路径：根据 `CLASSPATH` 配置搜索

# 访问控制权限符

用于限制访问权限，一共有4种

| 关键字    | 访问控制权限符 | 范围                         |
| --------- | -------------- | ---------------------------- |
| private   | 私有的         | 只能在本类中访问             |
| protected | 受保护的       | 只能在本类、同包、子类下访问 |
| public    | 公开的         | 任何位置都可访问             |
|           | 默认           | 只能在本类、同包下访问       |

## 作用范围

| 访问控制修饰符 | 本类 | 同包 | 子类 | 任意位置 |
| -------------- | ---- | ---- | ---- | -------- |
| public         | 可以 | 可以 | 可以 | 可以     |
| protected      | 可以 | 可以 | 可以 | 不行     |
| 默认           | 可以 | 可以 | 不行 | 不行     |
| private        | 可以 | 不行 | 不行 | 不行     |

## 修饰范围

属性（4个都能用）

方法（4个都能用）

类（只有 public 与默认）

接口（只有 public 与默认）

......

> 1. 每个编译单元（文件）都只能有一个 public 类，这表示，每个编译单元都有单一的公共接口，用 public 类来表现。该接口可以按要求包含众多的支持包访问权限的类。如果在某个编译单元内有一个以上的 public 类，编译器就会给出错误信息
> 2. public 类的名称必须完全与含有该编译单元的文件名相同，包含大小写。如果不匹配，同样将得到编译错误
> 3. 虽然不是很常用，但编译单元内完全不带 public 类也是可能的。在这种情况下，可以随意对文件命名
>
> 《Java编程思想（第四版）》6.4 类的访问权限

# 内部类

在一个类的内部定义的类，成为内部类，主要分为：

1. 实例内部类：类似于实例变量
2. 局部内部类：类似于局部变量
   - **匿名内部类**
3. 静态内部类：类似于静态变量

## 匿名内部类

局部内部类的一种，因为这个类没有名字而得名

使用匿名内部类，可以不用写接口的实现类，直接在大括号内实现

```java
// Compute为接口
// 大括号内为功能实现
new Compute() {
    public int sum(int a, int b) {
        return a + b;
    }
}
```

> 不建议使用，无法重复使用，且可读性太差
>
> 学习这个主要为了看懂别人代码 Orz