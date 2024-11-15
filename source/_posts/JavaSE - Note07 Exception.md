---
title: JavaSE - Note07 Exception
date: 2022-06-26 20:58:46
categories: JavaSE
tags: Exception
---

# 异常

程序执行过程中的不正常情况

作用：增强程序的健壮性

存在形式：以类的形式存在，每一个异常类都可以创建异常对象

创建一个空指针异常对象

```java
NullPointerException npe = new NullPointerException();
System.out.println(npe);
// java.lang.NullPointerException
```

> JVM 运行中发现异常会自动创建异常对象，然后抛出，打印信息到控制台

![异常的继承结构图](/images/image-java/note07-01-异常的继承结构图.png)

- **编译时异常**（受检/受控异常）必须在编写程序的时候，**预先**对这种异常进行处理，否则编译器就会报错
- **运行时异常**（未受检/受控异常）在编写程序阶段，可以选择处理或不处理
- 所有异常都是发生在**运行阶段**（异常的发生就是 new 对象），编译阶段异常不会发生

## 编译/运行异常区别

编译时异常，异常发生的概率一般比较高，需要在运行前进行预处理

运行时异常，异常发生的概率一般比较低，没必要运行前进行预处理

- 如果不对异常进行划分，虽然安全，但是程序更加繁琐

## 异常处理方式

> 只有一个问题，什么时候应该向上抛出
>
> 1. 传递一个危险信号，需要让调用方知道
> 2. 本方法没有能力处理的异常，调用方有能力处理
> 3. 抛出是框架层面的选择
>
> 打个比方，小孩摔倒了，伤口流血
>
> 1. 告诉爸妈是一个危险信号
> 2. 告诉医生是他们有能力处理
> 3. 告诉身体，是框架层面的，自动去调节身体免疫系统。
>
> > 异常还是一个绝妙的 goto

```java
public static void main(String[] args) {
    // Unhandled exception: java.lang.ClassNotFoundException
    // 因为 doSome() 方法声明时抛出了 ClassNotFoundException
    // 且为编译时异常，调用方法时没有处理，就会报错
    doSome();
}

public static void doSome() throws ClassNotFoundException {
    System.out.println("doSome");
}
```

1. 在方法声明的位置上，使用 `throws` 关键字，抛给上一级

   可持续上抛，若 main() 方法抛给了 JVM，则终止程序运行

   > 类似于推卸责任，继续把异常传给调用者

   ```java
   // 抛给上级 main() 方法
   public static void main(String[] args) throws ClassNotFoundException {
       doSome();
   }
   ```

2. 使用 `try...catch` 语句进行异常的捕捉

   > 类似于把异常拦下，真正解决，调用者不知道

   ```java
   public static void main(String[] args) {
       try {
           doSome();
       } catch (ClassNotFoundException e) {
           // 分支可以使用 e 引用，指向的是异常对象
           e.printStackTrace();
       }
   }
   ```


- 只要异常没有捕捉，采用上报的方式，后续代码不会执行（类似于直接 return）
- try 语句块中某一行出现问题，问题后的代码不会执行（catch 语句后代码继续执行）

> 如果需要告诉调用者，则使用 `throws` 上抛

## try...catch 深入

1. catch 中可以写具体的异常类型，也可以写该异常的父类型（多态机制）
2. catch 可以写多个，建议精确地一个一个处理，有利于程序调试
3. 多个 catch 时，从上到下必须遵循从小到大的原则（子类异常靠上，父类异常靠下）

> JDK 8 中支持：`catch (A | B | C e)` 这种写法

## 异常对象两个重要方法

1. （String）exception.getMessage()

   获取简单的描述信息（构造方法传递的参数）

   ```java
   NullPointerException e = new NullPointerException("空指针异常");
   System.out.println(e.getMessage());
   // 空指针异常
   ```

2. **exception.printStackTrace()**
   打印异常堆栈追踪信息

   ```java
   e.printStackTrace();
   System.out.println("Hello World");
   // java.lang.NullPointerException: 空指针异常...
   // Hello World
   ```

   - 建议实际开发中使用 `exception.printStackTrace`

> 异常信息是异步线程处理，最终顺序可能会不一

## 异常信息处理方法

```java
public static void main(String[] args) {
    try {
        m1();
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }
}

private static void m1() throws FileNotFoundException {
    m2();
}

private static void m2() throws FileNotFoundException {
    m3();
}

private static void m3() throws FileNotFoundException {
    // 文件路径缺少盘符
    new FileInputStream("\\Users\\vv\\Desktop\\jdk api 1.8_google.CHM");
}

/*
	at java.base/java.io.FileInputStream.open0(Native Method)
	at java.base/java.io.FileInputStream.open(FileInputStream.java:213)
	at java.base/java.io.FileInputStream.<init>(FileInputStream.java:155)
	at java.base/java.io.FileInputStream.<init>(FileInputStream.java:110)
	at io.github.wataaaame.javase.Exception.ExceptionTest04.m3(ExceptionTest04.java:24)
	at io.github.wataaaame.javase.Exception.ExceptionTest04.m2(ExceptionTest04.java:20)
	at io.github.wataaaame.javase.Exception.ExceptionTest04.m1(ExceptionTest04.java:16)
	at io.github.wataaaame.javase.Exception.ExceptionTest04.main(ExceptionTest04.java:9)
*/
```

异常追踪信息，从上往下一行一行看

通过包名判断，跳过 SUN 的代码错误（30 - 33）

主要问题出现在自己代码上（34 - 37）

- 上面行数的代码出问题导致下面行数的代码有误

## finally

与 try 语句一起出现

finally 子句中的代码最后执行，并且**一定会执行**，即使 try 语句块中的代码出现了异常

```java
try {
    System.out.println("try");
    return;
    // 退出 JVM 语句可以强制逃过 finally
    // System.exit(0);
} finally {
    System.out.pritnln("finally");
}
// 执行顺序：try -> finally -> return
```

- 通常在 finally 中完成资源的释放/关闭
- try 不能单独使用，但可以仅与 finally 联用

### finally 面试题

```java
public static void main(String[] args) {
    System.out.println(m());	// 100
}

public static int m() {
    int i = 100;
    try {
        // 这行代码出现在 int i = 100; 的下面，所以最终结果必须返回100
        // return 语句必须保证最后执行，一旦执行，整个方法结束
        return i;
    } finally {
        i++;
    }
}
```

Java 中语法规则：

1. 方法体中的代码必须遵循自上而下的顺序依次逐行执行
2. return 语句一旦执行，整个方法必须结束

反编译后的效果：

```java
public static int m() {
    int i = 100;
    int j = i;
    i++;
    return j;
}
```

可以理解为：finally 中的 i++ 只能算是回光返照，结局注定还是要 return 原本的 i

### final、finally、finalize

`final` 是一个关键字，表示最终的、不变的

```java
final int i = 100;
```

`finally` 也是一个关键字，和 try 联用，使用在异常处理机制中，finally 语句块中的代码一定执行

```java
try {
	
} finally {
    System.out.println("finally")
}
```

`finalize()` 是 Object 类中的一个方法名，是一个标识符，GC 负责调用

## 自定义异常

实际业务中，JDK 内置异常类不够用，所以需要自定义异常类

自定义：

1. 编写一个类继承 Exception/RuntimeException
2. 提供两个构造方法，一个无参，一个带有 String 参数

```java
public class MyException {
    public MyException() {
    }

    public MyException(String s ) {
        super(s);
    }
}
```

自定义异常代替 return 终止，通过 `throw new Exception("");` 向外手动抛出，让方法调用者 try...catch

## 异常方法覆盖

父类的方法，子类在重写时不能抛出更宽泛的异常
