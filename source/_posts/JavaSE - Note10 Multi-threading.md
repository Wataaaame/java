---
title: JavaSE - Note10 Multi-threading
date: 2022-07-08 16:22:56
categories: JavaSE
tags: Multi-threading
---

# Multi-threading 多线程

## 进程？线程？

进程：是一个应用程序

线程：是一个进程中的执行场景/单元

- 一个进程可以启动多个线程

- Java 语言中的资源共享：

  进程之间内存独立不共享

  线程之间：**堆内存**和**方法区**内存共享，但是**栈内存**独立，一个线程一个栈

> 对于一个 Java 程序来说，当在 DOS 命令窗口输入：java HelloWorld，执行后：
>
> 会启动 JVM，而 JVM 就是一个进程
>
> JVM 先启动一个主线程调用 main 方法；再启动一个垃圾回收线程负责看护，并回收垃圾
>
> 最起码，现在至少有两个线程并发

> 对于单核 CPU，不能做到真正的多线程并发，但是可以给人一种多线程并发的感觉

## 线程实现

Java 支持多线程机制，并且已经实现，可以直接继承

1. 继承 Thread 方式

   编写一个类，直接继承 `java.lang.Thread`，且必须重写 run 方法

   ```java
   package io.github.wataaaame.javase.thread;
   
   public class ThreadTest01 {
       public static void main(String[] args) {
           // 这里是 main 方法，属于主线程，在主栈中运行
   
           // 新建一个分支线程对象
           MyThread mt = new MyThread();
   
           // 启动线程
           // start() 方法的作用是：启动一个分支线程
           // 在 JVM 中开辟一个新的栈空间
           // 这段代码完成后，瞬间就结束了，线程就启动成功
           // 启动成功的线程会自动调用 rum 方法
           // 并且 run 方法在分支栈的底部（压栈）（run、main 平级）
           mt.start();
   
           // 直接调用 run 方法，并不会开辟新栈
           // 而是在原线程中执行 run 方法
           // mt.run()
   
           // 这里的代码还是运行在主线程中
           for (int i = 0; i < 1000; i++) {
               System.out.println("main -> " + i);
           }
       }
   }
   
   // 定义线程类
   class MyThread extends Thread {
       // run 方法必须重写
       @Override
       public void run() {
           // 编写程序，这段代码运行在分支线程中
           for (int i = 0; i < 1000; i++) {
               System.out.println("branch -> " + i);
           }
       }
   }
   ```

2. **封装接口方式**（使用更多，单独的类还可以方便继承其他类）

   编写一个类，实现 `java.lang.Runnable` 接口，实现 run 方法，并使用 `Thread()` 的构造方法封装

   ```java
   package io.github.wataaaame.javase.thread;
   
   public class ThreadTest02 {
       public static void main(String[] args) {
           // 实例化一个可运行对象
           MyRunnable mr = new MyRunnable();
           // 将 mr 通过 Thread 构造方法封装
           Thread t = new Thread(mr);
           // 代码合并
           // Thread t = new Thread(new MyRunnable());
   
           // 启动线程
           t.start();
   
           for (int i = 0; i < 1000; i++) {
               System.out.println("main -> " + i);
           }
       }
   }
   
   // 此非线程类，是一个“可运行”类
   class MyRunnable implements Runnable {
   
       // 可运行类同样必须重写 run 方法
       @Override
       public void run() {
           for (int i = 0; i < 1000; i++) {
               System.out.println("branch -> " + i);
           }
       }
   }
   ```

   采用匿名内部类实现

   ```java
   package io.github.wataaaame.javase.thread;
   
   public class ThreadTest03 {
       public static void main(String[] args) {
           // 采用匿名内部类方式
           Thread t = new Thread(new Runnable() {
               @Override
               public void run() {
                   for (int i = 0; i < 1000; i++) {
                       System.out.println("Branch -> " + i);
                   }
               }
           });
   
           // 启动线程
           t.start();
   
           // 主线程
           for (int i = 0; i < 1000; i++) {
               System.out.println("main -> " + i);
           }
       }
   }
   ```
   
3. FutureTask 方式，实现 Callable 接口

   `java.util.concurrent.FutureTask`

   JUC包下（并发包），为 JDK 8 新特性，老 JDK 中无此包

   实现的线程可以**获取返回值**

   缺点：效率较低，获取返回值需要等到线程执行完

   ```java
   package io.github.wataaaame.javase.thread;
   
   import java.util.concurrent.Callable;
   import java.util.concurrent.ExecutionException;
   import java.util.concurrent.FutureTask;
   
   public class FutureTaskTest01 {
       public static void main(String[] args) throws ExecutionException, InterruptedException {
           // 第一步，创建一个“未来任务类”对象
           // 参数非常重要，需要给一个 Callable 接口实现对象
           FutureTask ft = new FutureTask(new Callable() {
               @Override
               // call 相当于 run 方法，多了一个返回值
               public Object call() throws Exception {
                   // 线程执行一个任务，执行后可能返回一个结果
                   System.out.println("call begin");
                   Thread.sleep(1000 * 10);
                   System.out.println("call over");
   
                   int a = 100;
                   int b = 200;
                   // 自动装箱变成300
                   return a + b;
               }
           });
   
           // 创建线程对象
           Thread t = new Thread(ft);
   
           // 启动线程
           t.start();
   
           // 通过 get 拿到 FutureTask 的返回结果
           // main 方法会受阻，必须等到 t 线程执行完毕才能得到返回结果
           System.out.println(ft.get());
   
           System.out.println("After crowd");
       }
   }
   /*
       call begin
       call over
       300
       After crowd
   */
   ```

## 线程生命周期

新建状态、就绪状态、运行状态、阻塞状态（锁池）、死亡状态

![线程生命周期](/images/image-java/note10-01-线程生命周期.png)

## 线程常用方法

```java
Thread t = new Thread();
```

1. void setName(String name)

   设置线程名字

   ```java
   t.setName("vThread");
   ```

2. String getName()

   获取线程名称（默认线程名：Thread-0，依次排序）

   ```java
   System.out.println(t.getName());	// vThread
   ```

3. **static** Thread currentThread()

   返回对当前正在执行的线程对象的引用

   ```java
   System.out.println(Thread.currentThread().getName());
   // main
   ```

4. **static** void sleep(long millis)

   使**当前正在执行的线程**以指定的**毫秒数**暂停（暂时停止执行），具体取决于系统定时器和调度程序的精度和准确性

   ```java
   Thread.sleep(1000 * 5);	// 间隔5秒
   ```
   
5. public void interrupt()

   中断这个线程（依靠了 Java 的异常处理机制，直接抛出异常，然后继续运行）

   ```java
   t.start();
   t.interrupt();
   // Exception infomation...
   ```

6. ~~void stop()~~

   已弃用，强制杀死线程，可能造成数据损坏

   ```java
   t.stop();
   ```

   改用 Runnable 类中定义一个 isRun 的 boolean，为 false 时关闭方法

## *线程调度*

### 常见的线程调度模型

#### 抢占式调度模型

哪个线程的优先级比较高，抢到的 CPU 时间片的概率就高一些

> Java 采用的就是抢占式调度模型
>
> 其中：最低优先级1，默认优先级5，最高优先级10

#### 均分式调度模型

平均分配 CPU 时间片，每个线程占有的 CPU 时间片时间长度一样

平均分配，众生平等

### 相关方法

1. void setPriority(int newPriority)

   更改此线程的优先级

2. int getPriority()

   返回此线程的优先级

3. **static** void yield()

   暂停当前正在执行的线程对象，并执行其他线程

   - 并非阻塞，当前线程让位，让给其他线程使用（回到就绪状态）

   > 对调度程序的一个暗示，即当前线程愿意产生当前使用的处理器，调度程序可以自由地忽略这个提示

4. void join()

   合并线程，当前线程进入阻塞，直到 join 线程结束，当前线程才可以继续执行

   > 栈之间协调，并未消失

## *线程安全

存在线程安全问题的三个条件：

1. 多线程并发
2. 有共享数据
3. 共享数据有修改行为

### 两大编程模型

1. 异步编程模型：线程各自执行（多线程并发，效率较高）
2. 同步编程模型：两个线程发生等待关系（线程排队执行，效率较低）

### 线程同步机制

用排队执行解决线程安全问题（不能并发，且牺牲一部分效率）（排他锁）

语法：

```java
// 1. 同步代码块
synchronized(对象) {
	// 线程同步代码块
}
/*
	括号中的数据必须是多线程共享数据（包括类中引用，不包括局部变量），才能令对应线程排队
*/

// 2. 实例方法
public synchronized void doSome() {}
/*
	表示找对象锁
*/

// 3. 静态方法
public static synchronized void doOther() {}
/*
	表示找类锁
*/
```

- 对象锁：一个对象一把锁

  类锁：永远只有一把锁

- synchronized 用在实例方法上一定锁的是 this（不灵活；扩大了同步范围）

- 执行原理是：当线程遇到 `synchronized`，会自动查找并占有小括号中的“对象锁”，直到代码块执行完毕后才释放该锁，以便其他线程继续占用

  ![Lockpool](/images/image-java/note10-02-Lockpool.PNG)

- **局部变量/常量**永远不会存在线程安全问题，一个线程一个栈，局部变量不共享；局部变量字符串拼接，建议使用 StringBuilder（StringBuffer 线程安全，效率较低）

- synchronized 线程同步代码块越小，效率越高

- Vector、Hashtable、Properties 都是线程安全的

### Example

#### 银行取款

模拟一个银行取款操作，若异步编程，则有可能两个账户同时取款而未能正确更新余额

Account.java

```java
package io.github.wataaaame.javase.thread.threadsafe;

// 不使用线程同步机制，多线程对同一账户取款，出现线程安全问题
public class Account {
    private String actNo;
    private double balance;

    public Account(String actNo, double balance) {
        this.actNo = actNo;
        this.balance = balance;
    }

    public String getActNo() {
        return actNo;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    // 取款方法
    public void withdraw(double money) {
        // 取款前余额
        double before = getBalance();
        // 取款后余额
        double after = before - money;
        // 取款操作
        // 若 t1 执行到此，而 t2 同时到此
        // 那么 t1 取款后余额还未更新，导致异步问题
        setBalance(after);
    }
}
```

WithdrawThread.java

```java
package io.github.wataaaame.javase.thread.threadsafe;

public class WithdrawThread extends Thread {
    Account act;

    public WithdrawThread(Account act) {
        this.act = act;
    }

    @Override
    public void run() {
        // 取款5000
        double money = 5000;
        act.withdraw(money);

        // 打印信息
        System.out.println(Thread.currentThread().getName() +
                " get money from " + act.getActNo() +
                " about " + money + ", rest of: " + act.getBalance());
    }
}
```

Test.java

```java
package io.github.wataaaame.javase.thread.threadsafe;

public class Test {
    public static void main(String[] args) {
        // 1个银行账户，2个线程对同一个账户取款
        Account a = new Account("101", 10000);
        Thread t1 = new WithdrawThread(a);
        Thread t2 = new WithdrawThread(a);

        t1.setName("t1");
        t2.setName("t2");

        t1.start();
        t2.start();
        // t2 get money from 101 about 5000.0, rest of: 5000.0
		// t1 get money from 101 about 5000.0, rest of: 5000.0
    }
}
```

使用线程同步代码块解决问题，只需修改 Account.java 中的取款方法：

```java
// 取款方法
public void withdraw(double money) {
    // 使用 synchronized() {} 代码块解决线程安全问题
    synchronized(this) {
        double before = getBalance();
        double after = before - money;
        setBalance(after);
    }
}
```

#### 死锁

t1 线程先锁 o1，再锁 o2；t2 线程先锁 o2，再锁 o1

```java
package io.github.wataaaame.javase.thread;

public class DeadLockTest01 {
    public static void main(String[] args) {
        Object o1 = new Object();
        Object o2 = new Object();
        Thread t1 = new DeadThread1(o1, o2);
        Thread t2 = new DeadThread2(o2, o2);

        t1.start();
        t2.start();
        // 两个线程互相僵持
    }
}

class DeadThread1 extends Thread {
    Object o1;
    Object o2;

    public DeadThread1(Object o1, Object o2) {
        this.o1 = o1;
        this.o2 = o2;
    }

    @Override
    public void run() {
        // 先锁 o1
        synchronized (o1) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // o2 已被 t2 线程锁上
            synchronized (o2) {

            }
        }
    }
}

class DeadThread2 extends Thread {
    Object o1;
    Object o2;

    public DeadThread2(Object o1, Object o2) {
        this.o1 = o1;
        this.o2 = o2;
    }

    @Override
    public void run() {
        // 先锁 o2
        synchronized (o2) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // o1 已被 t2 线程锁上
            synchronized (o1) {

            }
        }
    }
}
```

#### 模拟抢票

TicketGrabbing.java

负责多线程抢票

```java
package io.github.wataaaame.javase.thread.Homework01;

public class TicketGrabbing extends Thread {
    private Station s;

    public TicketGrabbing(Station s) {
        this.s = s;
    }

    @Override
    public void run() {
        // 模拟用户通过平台接口抢票
        while (true) {
            s.grabbing();
            try {
                sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

Station.java

负责提供票，若有票则售卖，无则显示为空

```java
package io.github.wataaaame.javase.thread.Homework01;

public class Station {
    private int ticket = 100;

    public void grabbing() {
        synchronized (this) {
            if (ticket > 0) {
                ticket -= 1;
                System.out.println(Thread.currentThread().getName() +
                                   " got, rest: " + ticket);
            } else {
                System.out.println("rest 0");
            }
        }
    }
}
```

Test.java

```java
package io.github.wataaaame.javase.thread.Homework01;

public class Test {
    public static void main(String[] args) {
        Station s = new Station();
        // 创建3个线程抢票
        for (int i = 0; i < 3; i++) {
            new TicketGrabbing(s).start();
        }
    }
}
```

### 解决方案

1. 尽量使用**局部变量**代替实例变量和静态变量
2. 如果必须是实例变量，那么可以考虑**创建多个对象**，这样实例变量的内存就不共享了（就是一个线程一个对象）
3. 如果**不能使用局部变量，对象也不能创建多个**，才考虑使用 synchronized 线程同步机制

## 守护线程

Java 语言中，线程分为两大类：用户线程与守护线程

eg. 垃圾回收器（守护线程）

> 主线程 main 方法是一个用户线程

### 特点

一般守护线程是一个死循环，所有的用户线程结束，守护线程会自动结束

### 语法

`void setDaemon(boolean on)`

示例代码如下：

```java
package io.github.wataaaame.javase.thread;

public class DaemonThreadTest01 {
    public static void main(String[] args) {
        Thread t = new DaemonThread();

        // 启动之前设置为守护线程
        t.setDaemon(true);
        t.start();

        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + " -> " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
/*
    main -> 0
    Thread-2 -> 0
	...
    main -> 9
    Thread-2 -> 9
    Thread-2 -> 10

    Process finished with exit code 0
*/

class DaemonThread extends Thread {
    @Override
    public void run() {
        int i = 0;

        // 即使是死循环，守护线程依旧会在用户线程结束后自动结束
        while (true) {
            System.out.println(Thread.currentThread().getName() + " -> " + i++);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 定时器

`java.util.Timer`（也很少用，目前高级框架支持定时器）

间隔特定时间执行特定程序

1. void schedule(TimerTask task, Date firstTime, long period)

   从 firstTime 指定的时间开始，对 task 指定的任务执行 period 重复的、固定延迟执行

   - TimerTask 为抽象类，需要自定义继承类

   ```java
   package io.github.wataaaame.javase.thread;
   
   import java.text.ParseException;
   import java.text.SimpleDateFormat;
   import java.util.Date;
   import java.util.Timer;
   import java.util.TimerTask;
   
   public class TimerTaskTest01 {
       public static void main(String[] args) throws ParseException {
           // 使用定时器指定任务
           // 创建定时器对象
           Timer t = new Timer();
           // 守护线程的方式
           // Timer t = new Timer(true);
   
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
           Date firstTime = sdf.parse("2022-07-10 17:16:00");
   
           // 指定定时任务
           // 第一个参数：任务对象；第二个参数：第一次运行时间；第三个参数：间隔时间
           t.schedule(new LogTimerTask(), firstTime, 1000 * 10);
       }
   }
   
   // 编写一个定时任务类
   class LogTimerTask extends TimerTask {
   
       @Override
       public void run() {
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
           System.out.println(sdf.format(new Date()));
       }
   }
   ```

## 生产者和消费者模式

### wait() & notify()

这两个方法不是线程对象的方法，是 Java 中任何一个对象都有的方法（Object 类中的方法）

并非使用线程对象调用，而是通过对象调用

1. void wait()

   让正在 o 对象上活动的线程进入等待状态（释放锁），直到被唤醒为止

   ```java
   Object o = new Object();
   o.wait();
   ```

2. void notify()

   让正在 o 对象上等待的线程进入活动状态（不会释放锁）

   ```java
   o.notify();
   ```

3. void notifyAll()

   唤醒 o 对象上处于等待的所有线程

### 生产者和消费者模式

生产线程负责生产，消费线程负责消费，二者需达到均衡

![生产者与消费者模式](/images/image-java/note10-03-生产者与消费者模式.png)

使用 wait() & notify() 实现生产者消费者模式：

- 二者的使用建立在线程同步的基础之上，因为多线程需要同时操作一个仓库，有线程安全问题
- wait()：让正在 o 对象上活动的 t 线程进入等待状态，并释放之前占有的 o 对象的锁
- notify()：让正在 o 对象上等待的线程被唤醒，只是通知，不会释放 o 对象之前占有的锁

模拟这样的需求：

1. 仓库采用 List 集合，集合中假设只能存一个元素
2. 1个元素表示仓库满，0个元素表示仓库空
3. 保证 List 集合中永远都是最多存储一个元素（生产一个，消费一个）

```java
package io.github.wataaaame.javase.thread;

import java.util.ArrayList;
import java.util.List;

public class ProducerConsumerTest {
    public static void main(String[] args) {
        // 创建 List 仓库
        List list = new ArrayList();
        // 创建生产线程
        Thread p = new Thread(new Producer(list));
        // 创建消费线程
        Thread c = new Thread(new Consumer(list));

        // 设置线程名称
        p.setName("Producer");
        c.setName("Consumer");

        // 开始生产和消费
        p.start();
        c.start();
    }
}

// 生产线程
class Producer implements Runnable {
    // 仓库
    private List list;

    public Producer(List list) {
        this.list = list;
    }

    @Override
    public void run() {
        // 一直生产
        while (true) {
            synchronized (list) {
                // 大于0，说明仓库已经有元素
                if (list.size() > 0) {
                    try {
                        // 当前线程进入等待状态，并且释放占有的 list 锁
                        list.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                // 若执行至此，说明仓库为空，开始生产
                Object o = new Object();
                list.add(o);
                System.out.println(Thread.currentThread().getName() + "->" + o);
                // 唤醒消费者继续消费
                list.notify();
            }
        }
    }
}

// 消费线程
class Consumer implements Runnable {
    // 仓库
    private List list;

    public Consumer(List list) {
        this.list = list;
    }

    @Override
    public void run() {
        // 一直消费
        while (true) {
            synchronized (list) {
                // 如果 list 为0，说明仓库空
                if (list.size() == 0) {
                    // 当前线程进入等待状态，并且释放占有的 list 锁
                    try {
                        list.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                // 执行到此处说明仓库不为空，开始消费
                System.out.println(Thread.currentThread().getName() + "->" + list.remove(0));
                // 唤醒生产者继续生产
                list.notify();
            }
        }
    }
}
```

### Puzzle

使用生产者和消费者模式实现，交替输出：

EvenNum->0
OddNum->1
EvenNum->2
OddNum->3

Num.java

```java
package io.github.wataaaame.javase.thread.homework02;

public class Num {
    private int i = 0;

    public int getI() {
        return i;
    }

    public void setI(int i) {
        this.i = i;
    }
}
```

OddNum.java

```java
package io.github.wataaaame.javase.thread.homework02;

public class OddNum extends Thread {
    private Num n;

    public OddNum(Num n) {
        this.n = n;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (n) {
                // 如果 i+1 对2取模，结果不为0，说明 i 是偶数，则 wait
                if (n.getI() % 2 == 0) {
                    try {
                        n.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                // 执行到此说明 i 为奇数，可以打印
                System.out.println(Thread.currentThread().getName() + "->" + n.getI());
                n.setI(n.getI() + 1);
                // i++ 后，i 为偶数，唤醒 EvenNum 线程
                n.notify();
            }
        }
    }
}
```

EvenNum.java

```java
package io.github.wataaaame.javase.thread.homework02;

public class EvenNum extends Thread {
    private Num n;

    public EvenNum(Num n) {
        this.n = n;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (n) {
                // 如果 i+1 对2取模等于0，则 i 为奇数，需要 wait
                if (n.getI() % 2 != 0) {
                    try {
                        n.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                //执行到此说明 i 为偶数，可以输出
                System.out.println(Thread.currentThread().getName() + "->" + n.getI());
                n.setI(n.getI() + 1);
                // i++ 后 i 为奇数，需要唤醒 OddNum
                n.notify();
            }
        }
    }
}
```

Test.java

```java
package io.github.wataaaame.javase.thread.homework02;

public class Test {
    public static void main(String[] args) {
        Num n = new Num();
        Thread on = new OddNum(n);
        Thread en = new EvenNum(n);

        on.setName("OddNum");
        en.setName("EvenNum");

        on.start();
        en.start();
    }
}
/*
    EvenNum->0
    OddNum->1
    EvenNum->2
    OddNum->3
*/
```
