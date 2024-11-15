---
title: JavaSE - Note04 Static, This & Super
date: 2022-05-12 14:40:45
categories: JavaSE
tags: [Static, This, Super]
---

# Static（静态）

static 修饰的变量：静态变量

static 修饰的方法：静态方法

- 所有 static 修饰的都是类相关的，类级别的

- 类加载时便初始化，无需 `new` 存储在方法区

> 静态变量不会出现空指针异常，因为静态变量不需要对象的存在（实际上运行中 `引用.` 会自动转换为 `类名.`

## *引用规则

**实例一定要使用 `引用.` 来访问，静态建议使用 `类名.` 来访问**

> 如果类中所有对象某个属性值都一样，建议定义为静态变量，在方法区中只保留一份，节省内存开销

## 参考标准

当方法体中访问了实例变量，那么这个方法一定是实例方法

> 一般工具类为了方便使用静态
>
> 多个对象时采用实例

## Example

```java
// TS01_Static.java
public class TS01_Static {
	public static void main(String[] args) {
		System.out.println(Chinese.contry);

		Chinese c1 = new Chinese("101", "vv");
		Chinese c2 = new Chinese("102", "yy");

		System.out.println(c1.idCard);
		System.out.println(c1.name);
		System.out.println(Chinese.contry);

		System.out.println(c2.idCard);
		System.out.println(c2.name);
		System.out.println(Chinese.contry);
	}
}

class Chinese {
	String idCard;
	String name;
	static String contry = "China";

	public Chinese() {}
	public Chinese(String i, String n) {
		idCard = i;
		name = n;
	}
}

// Computational Results:
/*
    China
    101
    vv
    China
    102
    yy
    China
*/
```

![static 静态变量内存图](/images/image-java/note04-01-static静态变量内存图.png)

|          | 栈       | 堆       | 方法区   |
| -------- | -------- | -------- | -------- |
| 存储类型 | 局部变量 | 实例变量 | 静态变量 |
| 级别     | 方法级别 | 对象级别 | 类级别   |

## Static Block（静态代码块）

执行优先级高于非静态的初始化块，**它会在类初始化的时候执行一次**，执行完成便销毁，它仅能初始化类变量，即 static 修饰的数据成员

- 在 `main()` 方法前执行
- 自上而下执行
- 一个类中可以编写多个静态代码块

> 不常用，是一个类加载时机。例如可以存放类加载日志信息

### 语法

```java
static {
    java code...;
    java code...;
}
```

### Example

```java
// TS02_StaticBlock.java
public class TS02_StaticBlock {
	static {
		System.out.println("A");
	}

	static {
		System.out.println("B");
	}

	public static void main(String[] args) {
		System.out.println("main() Start!");
	}

	static {
		System.out.println("C");
	}
}

// Computational Result:
/*
    A
    B
    C
    main Strat!
*/
```

## 补充说明

- `static` 静态变量和静态代码块加载时机相同，靠代码先后次序决定执行顺序

- 目前为止的所有 Java 程序，哪些部分是由顺序要求的？

1. 方法体中的代码，遵循自上而下的原则
2. 静态变量和静态代码块之间
3. 静态代码块彼此之间

## *Instance Block（实例代码块）*

实例语句块，是在构造方法前执行的代码（对象构建时机）

> 可以将构造方法中重复的代码放在实例代码块中

### *语法*

`{java code...;}`

### *执行顺序*

实例语句块在类加载时没有执行：

```java
// TS04_InstanceBlock.java
public class TS04_InstanceBlock {
	public static void main(String[] args) {
		System.out.println("main() go");
	}

	{
		System.out.println("实例语句块 go");
	}

	public TS04_InstanceBlock() {
		System.out.println("无参数实例构造方法 go");
	}
}

/*
    main() go
*/
```

实例化对象，调用无参构造方法后：

```java
// TS04_InstanceBlock.java
public class TS04_InstanceBlock {
	public static void main(String[] args) {
		System.out.println("main() go");
        new TS04_InstanceBlock();
	}

	{
		System.out.println("实例语句块 go");
	}

	public TS04_InstanceBlock() {
		System.out.println("无参数实例构造方法 go");
	}
}

/*
    main() go
    实例语句块 go
*/
```

## Code Order Quiz

```java
// 判断以下程序的执行顺序
public class TS05_CodeOrder {
	static {
		System.out.println('A');
	}

	public static void main(String[] args) {
		System.out.println("main() go");
		new TS05_CodeOrder();
		System.out.println("main() stop");
	}

	public TS05_CodeOrder() {
		System.out.println('B');
	}

	{
		System.out.println('C');
	}

	static {
		System.out.println('x');
	}
}

/*
	A
	X
	main() go
	C
	B
	main() stop
*/
```

# This

this 是一个关键字，保存着当前对象的内存地址，指向自身

- this 是一个变量，一个引用
- 一个对象一个 this
- 存储在堆内存的对象当中

> 谁调用这个实例方法，this 就是谁（代表当前对象）
>
> 访问自身类中实例变量，`this.` 可省略（大部分）
>
> 静态变量无法使用 this

![this 内存图](/images/image-java/note04-02-this关键字.png)

## this 何时不可省略？

在实例方法中，或者构造方法中，为了区分实例变量和局部变量，`this.` 不可省略

根据就近原则，实例方法中局部变量命名重复：

```java
public void setNo(int no) {
    no = no;
}
```

使用 `this.` 来区分实例变量和局部变量：

```java
public void setNo(int no) {
    this.no = no;
}
```

## 构造方法调用构造方法

构造方法中可以通过 `this()` 来调用另一个构造方法

- 构造方法1和构造方法2需要在同一个类中
- `this()` 必须是构造方法中的第一行

```java
public Date() {
    /*
    this.year = year;
    this.month = month;
    this.day = day;
    */
    this(1970, 1, 1);
}

public Date(int year, int month, int day) {
    this.year = year;
    this.month = month;
    this.day = day;
}
```

# Super

是一个关键字，表示通过子类的构造方法调用父类的构造方法，代表了当前对象父类型特征

> 模拟现实中：要想有儿子，需要先有父亲
>
> 父类构造方法连续调用，实际上对象只创建了一个

![super 的原理](/images/image-java/note04-04-super的原理.png)

## Super 的使用

`super.属性名`				【访问父类的属性】

`super.方法名(实参)`	【访问父类的方法】

`super(实参)`				【调用父类的构造方法】

## this & super

| this                                                         | super                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 出现在实例方法和构造方法中                                   | 出现在实例方法和构造方法中                                   |
| 语法是：`this.`、`this()`                                    | 语法是：`super.`、`super()`                                  |
| 不能使用在静态方法中                                         | 不能使用在静态方法中                                         |
| `this.` 大部分情况可以省略                                   | `super.` 大部分情况可以省略                                  |
| `this.` 在区分同名局部变量和实例变量时不能省略               | `super.` 在区分子类和父类同名属性时不能省略                  |
| `this()` 只能出现在构造方法第一行，通过当前构造方法调用“本类”中其他构造方法，目的是“代码复用” | `this()` 只能出现在构造方法第一行，通过当前构造方法调用“父类”中其他构造方法，目的是“代码复用” |
| `this` 可以单独输出，默认调用  `toString()` 打印引用地址     | `super` 不能单独输出，不是引用，不保存内存地址，不指向任何对象，只代表当前对象内部父类型特征 |

## 重要结论

当一个构造方法第一行既没有 this() 也没有 super()，默认会有一个 super()，表示通过当前子类的构造方法调用父类的无参数构造方法，所以**必须保证父类中无参构造方法是存在的**

> this() 和 super() 不能共存，都只能出现在构造方法第一行
>
> 无论如何，父类的构造方法都会先执行
>
> 不管 new 什么对象，最终都会执行 Object 无参构造方法
