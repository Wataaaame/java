---
title: JavaSE - Note01 OverLoad & Recursion
date: 2022-04-26 12:21:36
tags: [Overload, Recursion]
categories: JavaSE
---

# 方法重载

## 什么情况下我们考虑使用方法重载机制？

当功能相似的时候，建议将方法名定义为一致的，这样代码美观，又方便编程

> 如果功能不相似，坚决要让方法名不一致。

## 代码满足什么条件的时候构成了方法重载？

1. 在同一个类当中
2. 方法名相同
3. 形式参数列表不同（类型、个数、顺序）

> 方法重载和返回值类型无关，和修饰符列表无关。
>

## 方法重载的优点？

- 代码美观
- 方便代码的编写

## Example

```java
// 重载System.out.print，将其改为print()
public class Overload {
	// 换行的方法
	public static void print() {
		System.out.println();
	}

	// 输出byte
	public static void print(byte b) {
		System.out.println(b);
	}

	// 输出short
	public static void print(short s) {
		System.out.println(s);
	}

	// 输出int
	public static void print(int i) {
		System.out.println(i);
	}

	// 输出long
	public static void print(long l) {
		System.out.println(l);
	}

	// 输出float
	public static void print(float f) {
		System.out.println(f);
	}

	// 输出double
	public static void print(double d) {
		System.out.println(d);
	}

	// 输出boolean
	public static void print(boolean b) {
		System.out.println(b);
	}

	// 输出char
	public static void print(char c) {
		System.out.println(c);
	}

	// 输出String
	public static void print(String s) {
		System.out.println(s);
	}
}
```

# 方法递归

方法自身调用自身

> 使用递归的时候，必须添加结束条件，否则发生栈内存溢出错误
>
> 能够使用循环代替递归的尽量使用循环，循环的执行耗费内存少一些
>
> 递归使用不当很容易内存溢出，JVM停止工作，只有极少数情况下只能用递归
>
> 即使结束条件合法，递归太深也会导致内存溢出

## 递归经验

### 栈内存溢出错误怎么办？

1. 先检查结束条件是否正确
2. 如果正确，可以调整JVM的栈内存大小`java -X`

## Example

```java
// 使用递归的方式计算N的阶乘
public class HW01_FacRec {
	public static void main(String[] args) {
		java.util.Scanner s = new java.util.Scanner(System.in);
		
		System.out.println("Factorial of it: " + facRec(s.nextInt()));
	}

	public static long facRec(int n) {
		if (n == 1)	return 1;

		return n * facRec(n-1);
	}
}
/*****************
3
Factorial of it: 6
*****************/
```

