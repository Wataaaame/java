---
title: Java - API & JDK
date: 2022-06-09 09:09:14
categories: Java
tags: [Java API, JDK]
---

# 什么是 API

Application Program Interface 应用程序编程接口

整个 JKD 类库就是一个 JavaSE 的 API

每一个 API 都会配置一套 API 帮助文档

## 获取途径

1. 源代码中（API，比较麻烦，也比较难）
2. 查阅 Java 类库帮助文档

# Object - JDK 类库的根类

Object 类中方法所有子类通用，任何一个类默认继承 Object 类

## String toString()

将对象转换成字符串形式

`类名@对象的内存地址转换为16进制形式`

> toString() 方法应该是一个简洁的、详实的、易阅读的
>
> 建议所有子类都重写 toString() 方法
>
> 输出对象时可以自动调用 toString() 方法

### 源代码

```java
/**
 * Answers a string containing a concise, human-readable
 * description of the receiver.
 *
 * @return		String
 *					a printable representation for the receiver.
 */
public String toString () {
	return this.getClass().getName() + '@' + Integer.toHexString(this.hashCode());
}
```

## boolean equals(Object obj)

判断两个对象是否相等

> “==” 比较的是两对象内存地址，无法比较对象是否相等

### 源代码

```java
/**
 * Compares the argument to the receiver, and answers true
 * if they represent the <em>same</em> object using a class
 * specific comparison. The implementation in Object answers
 * true only if the argument is the exact same object as the
 * receiver (==).
 *
 * @param		o Object
 *					the object to compare with this object.
 * @return		boolean
 *					<code>true</code>
 *						if the object is the same as this object
 *					<code>false</code>
 *						if it is different from this object.
 * @see			#hashCode
 */
public boolean equals (Object o) {
	return this == o;
}
```

### 代码重写

源代码中的 `equals(Object o)` 默认使用 “==” 比较，无法作用于对象，所以需要重写

## String

大部分情况下采用以下方法创建字符串对象：

```java
String s1 = "hello";
```

- 这种情况，作为 String 变量，支持使用“==”比较

String 是一个类，也可以采用构造方法创建字符串对象：

```java
String s2 = new String("world");
```

- 字符串也属于类，这里作为 String 引用，所以无法使用“==”来比较，需要调用 equals() 方法比较

> 如果不知道 String 变量创建方法，建议直接使用 equals()

由于 String 类中已经重写过 equals() 与 toString()，所以可以直接调用使用

> 调用 equals() 时，可以优先调用字符串常量以避免空指针异常，例如
>
> `"notnull".equals(str)`

### String equals() 源代码

```java
/**
	 * Compares the specified object to this String and answer if they are equal. The object must be an instance of String with the same characters in
	 * the same order.
	 *
	 * @param object
	 *          the object to compare
	 * @return true if the specified object is equal to this String, false otherwise
	 *
	 * @see #hashCode()
	 */
	public boolean equals(Object object) {
		if (object == this) {
			return true;
		} else {
			if (object instanceof String) {
				String s1 = this;
				String s2 = (String) object;

				int s1len = s1.lengthInternal();
				int s2len = s2.lengthInternal();

				if (s1len != s2len) {
					return false;
				}

				byte[] s1Value = s1.value;
				byte[] s2Value = s2.value;

				if (s1Value == s2Value) {
					return true;
				} else {
					// There was a time hole between first read of s.hashCode and second read if another thread does hashcode
					// computing for incoming string object
					int s1hash = s1.hashCode;
					int s2hash = s2.hashCode;

					if (s1hash != 0 && s2hash != 0 && s1hash != s2hash) {
						return false;
					}

					if (!regionMatchesInternal(s1, s2, s1Value, s2Value, 0, 0, s1len)) {
						return false;
					}

					if (com.ibm.oti.vm.VM.J9_JIT_STRING_DEDUP_POLICY != com.ibm.oti.vm.VM.J9_JIT_STRING_DEDUP_POLICY_DISABLED) {
						deduplicateStrings(s1, s1Value, s2, s2Value);
					}

					return true;
				}
			}

			return false;
		}
	}
```

### String toString() 源代码

```java
/**
	 * Answers a string containing a concise, human-readable description of the receiver.
	 *
	 * @return this String
	 */
	public String toString() {
		return this;
	}
```

## protected void finalize()

protected 修饰，只有方法体没有代码，不需要手动调用，垃圾回收器负责调用的方法

### 执行时机

当一个 Java 对象即将被垃圾回收器回收时，垃圾回收器负责调用

该方法实际上是 SUN 公司为 Java 程序员准备的一个时机——垃圾销毁时机

如果希望在对象销毁时执行一段代码，这段代码要写到 finalize() 方法中

> 建议 GC 启动 `System.gc();`

### 源代码

```java
/**
 * Called by the virtual machine when there are no longer any (non-weak)
 * references to the receiver. Subclasses can use this facility to
 * guarantee that any associated resources are cleaned up before
 * the receiver is garbage collected. Uncaught exceptions which are
 * thrown during the running of the method cause it to terminate
 * immediately, but are otherwise ignored.
 * <p>
 * Note: The virtual machine assumes that the implementation
 * in class Object is empty.
 *
 * @exception	Throwable
 *					The virtual machine ignores any exceptions
 *					which are thrown during finalization.
 *
 * @deprecated  May cause performance issues, deadlocks and hangs. 
 *              Errors in  finalizers can lead to resource leaks.
 */
@Deprecated(forRemoval=false, since="9")
protected void finalize () throws Throwable {
}
```

## int hashCode()

获取对象哈希值（内存地址），返回一个 int 类型

实际上就是一个 Java 对象的内存地址，经过哈希算法得出一个值

### 源代码

```java
/**
 * Answers an integer hash code for the receiver. Any two 
 * objects which answer <code>true</code> when passed to 
 * <code>.equals</code> must answer the same value for this
 * method.
 *
 * @return	the receiver's hash.
 *
 * @see	#equals
 */
public int hashCode() {
	return J9VMInternals.fastIdentityHashCode(this);
}
```

## protected Object clone()

对象克隆

### 源代码

```java
/**
 * Answers a new instance of the same class as the receiver,
 * whose slots have been filled in with the values in the
 * slots of the receiver.
 * <p>
 * Classes which wish to support cloning must specify that
 * they implement the Cloneable interface, since the native
 * implementation checks for this.
 *
 * @return		Object
 *					a shallow copy of this object.
 * @exception	CloneNotSupportedException
 *					if the receiver's class does not implement
 *					the interface Cloneable.
 */
protected Object clone() throws CloneNotSupportedException
{
	return J9VMInternals.primitiveClone(this);
}
```

# String

`java.lang.String`

1. String 表示字符串类型，属于引用数据类型
2. Java 中使用双引号括起来的都是 String 对象
3. Java 中规定，双引号括起来的字符串，是不可变的
4. 在 JDK 中双引号括起来的字符串，都是直接存储在方法区的**字符串常量池**当中的

> 字符串在实际开发中使用太频繁，为了执行效率，所以放到了字符串常量池当中
>
> 字符串常量池不会被垃圾回收器回收
>
> 字符串常量池中同一个 String 的不同引用，使用 “==” 比较时返回 true，是因为双引号括起来的字符串不可变，引用的同一个字符串（new String 对象时，对象指向同一字符串，变量指向不同对象，所以使用 “==”时，前者为 true，后者为 false）

## String 的构造方法

```java
String s = "abc";
String s = new String("abc");
String s = new String(byte数组, 起始下标, 长度);
String s = new String(char数组);
String s = new String(char数组, 起始下标, 长度);
```

## String 类当中常用方法

1. **char charAt(int index)**

   获取字符串中下标为 index 的元素

   ```java
   System.out.println("vay".charAt(1));	// a
   ```

2. int compareTo(String anotherString)

   比较两个字符串第一个不同元素 ASCII 码大小

   ```java
   System.out.println("abc".compareTo("abd"));	// 前小后大
   // -1
   
   System.out.println("abc".compareTo("abc"));	// 前后一致
   // 0
   
   System.out.println("abd".compareTo("abc"));	// 前大后小
   // 1
   ```

3. **boolean contains(CharSequence s)**

   判断前面的字符串中是否包含后面的字符串

   ```java
   System.out.println("HelloWorld".contains("Hello"));	// true
   System.out.println("HelloWorld".contains("Hellow"));// false
   ```

4. **boolean endsWith(String suffix)**

   判断当前字符串是否以某个字符串结尾

   ```java
   System.out.println("test.java".endsWith(".java"));	// true
   System.out.println("text.java".endsWith(".class"));	// false
   ```

5. **boolean equals(Object anObject)**

   比较两个字符串或引用

   ```java
   System.out.println("abc".equals("abc"));	// ture
   ```

6. **boolean equalsIgnoreCase(String anotherString)**

   判断两个字符串是否相等，并且同时忽略大小写

   ```java
   System.out.println("Abc".equalsIgnoreCase("abc"));	// true
   ```

7. **byte[] getBytes()**

   将字符串对象转换成字节数组

   ```java
   byte[] b = "abcdef".getBytes();
   for (int i = 0; i < b.length; i++) {
       System.out.println(b[i]);
   }
   /*
       97
       98
       99
       100
       101
       102
   */
   ```

8. **int indexOf(String str)**

   判断某个子字符串在当前字符串中第一次出现的索引

   ```java
   System.out.println("test.java".indexOf("java"));	// 5
   ```

9. **boolean isEmpty()**

   判断某个字符串是否为空字符串

   ```java
   System.out.println("".isEmpty());	// true
   ```

10. **int length()**

    ```java
    System.out.println("abc".length());	// 3
    ```

    > 判断数组长度是属性，判断字符串长度是方法

11. **int lastIndexOf(String str)**

    判断某个子字符串在当前字符串最后一次出现的索引（区分大小写）

    ```java
    System.out.println("learningJava.Java".lastIndexOf("Java"));	// 12
    ```

12. **String replace(charSequence target, charSequence replacement)**

    将字符串中的 target（目标字符串）替换成 replacement（欲替换字符串）

    > String 的父接口就是 charSequence

    ```java
    System.out.println("http://wataaaame.github.io".replace("http", "https"));
    // https://wataaaame.github.io
    ```

13. **String[] split(String regex)**

    以 “regex” 字符作为分隔符拆分字符串

    ```java
    String[] str = "2022-12-26".split("-");
    for (int i = 0; i < str.length; i++) {
        System.out.println(str[i]);
    }
    /*
    	2022
    	12
    	26
    */
    ```

14. **boolean startsWith(String prefix)**

    判断字符串是否以 prefix（字符串）开始

    ```java
    System.out.println("https://wataaaame.github.io".startsWith("https"));	// true
    ```

15. **String substring(int beginIndex)**

    从 beginIndex（起始下标）处截取字符串

    ```java
    System.out.println("https://wataaaame.github.io".substring(8));
    // wataaaame.github.io
    ```

16. **String substring(int beginIndex, int endIndex)**（方法重写）

    从 beginIndex（起始下标）截取到 endIndex（结束下标）

    > 左闭右开，可理解为光标

    ```java
    System.out.println("https://wataaaame.github.io".substring(8, 17));
    // wataaaame
    ```

17. **char[] toCharArray()**

    将字符串转换成 char 数组

    ```java
    char[] ch = "keep on do it!!!".toCharArray();
    for (int i = 0; i < ch.length; i++) {
        System.out.print(ch[i] + " ");
    }
    // k e e p   o n   d o   i t ! ! ! 
    ```

18. **String toLowerCase()**

    转换为小写

    ```java
    System.out.println("FOR ALL THE TIME.".toLowerCase());
    // for all the time.
    ```

19. **String toUpperCase()**

    转换为大写

    ```java
    System.out.println("wuwuwu".toUpperCase());
    // WUWUWU
    ```

20. **String trim()**

    去除字符串**前后**空白

    ```java
    System.out.println(" space around ".trim());
    // space around
    ```

21. **String valueOf()**
    将非字符串转换为字符串

    ```java
    System.out.println(String.valueOf(3.14));
    ```

    > String 中唯一的 static 方法，不需要 new 对象，使用 类名. 调用
    >
    > 处理对象时调用 toString 方法转换为字符串形式的内存地址
    >
    > print 对象会自动调用 toString，因为 println 中会先调用 valueOf()
    >
    > 控制台打印的内容全都是字符串

# StringBuffer & StringBuilder

## StringBuffer

Java 中的字符串是不可变的，每一次拼接都会产生新字符串，这样会占用大量的方法区内存，造成内存空间浪费

所以需要进行大量字符换拼接操作，建议使用 JDK 中自带的：

`java.lang.StringBuffer`

`java.lang.StringBuilder`

- 创建 StringBuffer 时最好给定一个合适的初始化容量，以减少底层数组的扩容次数，优化性能

> String 底层就是一个 private final 修饰的 byte[] 数组，所以一经创建无法改变
>
> 而 StringBuffer 底层是一个普通的 byte[] 数组，可以任意扩容

### 语法

创建一个初始化容量为16个 byte[] 数组（字符串缓冲区对象）：

```java
StringBuffer stringBuffer = new StringBuffer();
```

拼接字符串统一调用 append 方法：

```java
stringBuffer.append("a");
stringBuffer.append(3.14);
stringBuffer.append(true);
stringBuffer.append(100L);
System.out.println(stringBuffer.toString());
```

指定初始化容量的 StringBuffer 对象（字符串缓冲区对象）

```java
StringBuffer sb = new StringBuffer(100);
```

## StringBuilder

与 StringBuffer 类似，区别如下：

| StringBuffer                               | StringBuilder                            |
| ------------------------------------------ | ---------------------------------------- |
| 有 synchronized 关键字修饰，表示多线程安全 | 无 synchronized 关键字修饰，多线程不安全 |

# 基础类型对应的8个包装类

Java 中为8中**基本数据类型**对应准备了8种**包装类型**，属于**引用数据类型**，父类是 Object

> 调用方法需要传递一个数字，可是方法参数类型是 Object，无法接收基本数据类型的数字，此时就需要传递一个**数字对应的包装类**进去

## 对应的包装类型名

| 基本数据类型 | 包装类型                | 父类   |
| ------------ | ----------------------- | ------ |
| byte         | java.lang.Byte          | Number |
| short        | java.lang.Short         | Number |
| int          | java.lang.**Integer**   | Number |
| long         | java.lang.Long          | Number |
| float        | java.lang.Float         | Number |
| double       | java.lang.Double        | Number |
| boolean      | java.lang.Boolean       | Object |
| char         | java.lang.**Character** | Object |

## Number

是一个抽象类，无法实例化对象

将引用数据类型转换为基本数据类型**（负责拆箱）**的公共方法：`[数据类型]Value()`

### 示例代码

Integer 的构造方法有两个（其他的也如此）：

`Integer(int)`

`Integer(String)`

> 此为 JDK 9 过时语法**（手动装箱）**

基本数据类型 -（转换为）-> 引用数据类型**（装箱）**： 

```java
Integer i = new Integer(123);
Integer j = new Integer("123");
```

- 123这个基本的数据类型，进行构造方法的包装达到了：**基本数据类型**向**引用数据类型**的转换
- 非数字的字符串包装成 Integer，运行会报错

引用数据类型 -（转换为）-> 基本数据类型**（拆箱）**：

```java
float f = i.floatValue();
System.out.println(f);	// 123.0
```

- 默认重写了 toString() 方法

通过访问包类常量，获取数据类型的最大值与最小值：

```java
System.out.println("max int: " + Integer.MAX_VALUE);	// max int: 2147483647
System.out.println("min int: " + Integer.MIN_VALUE);	// min int: -2147483648
```

### 自动装箱 & 自动拆箱

Java 5 之后，支持自动拆箱和自动装箱，Number 类中的方法就用不着了

自动装箱：基本数据类型自动转换成包装类

`Integer x = 100;`

自动拆箱：包装类自动转换成基本数据类型

`int y = x;`

分析以下代码为何没有报错：

```java
Integer z = 100;
System.out.println(z + 1);
```

- 加号两边要求是基本数据类型的数组，z 是包装类，不属于基本数据类型，这里会自动进行拆箱，将 z 转换成基本类型数据

> “==” 不会触发自动拆箱机制，只有参与运算时才会触发

重要的 Integer 面试题：

```java
Integer a = 128;
Integer b = 128;
System.out.println(a == b);	// false

Integer x = 127;
Integer y = 127;
System.out.println(x == y);	// true
```

- Java 中为了提高程序的执行效率，将 [-128, 127] 之间所有的包装对象提前创建好，放到了一个方法区的“整数型常量池”中，目的是只要用了这个区间的数据不需要再 new，直接从整数型常量池当中取出来
- 原理：x 与 y 中保存的对象内存地址相同

## 常用方法

1. static int parseInt(String s)

   静态方法，传参 String，返回 int

   ```java
   int retValue = Integer.parseInt("123");
   System.out.println(retValue + 100);	// 223
   ```

   - 非数字字符串无法转换

   > 类比 `Double.parseDouble("3.14")`
   
2. Integer.valueOf([int/String])

   将 int/String 数字转换成 Integer

![String Integer Int 三种类型互相转换](/images/image-java/API&JDK-01-String、Integer、Int、三种类型互相转换.png)

# 日期相关类

`java.util.Date`

1. Date()

   获取系统当前时间（精确到毫秒）

   直接调用无参数构造方法

   > toString() 方法已被重写（国际时间格式）

   ```java
   Date nowDate = new Date();
   System.out.println(nowDate);
   // Sun Jun 26 09:21:41 CST 2022
   ```

2. Date(long)

   表示纪元时间以后累计的时间（传入毫秒）

   ```java
   Date d = new Date(1000);
   System.out.println(d);
   // Thu Jan 01 08:00:01 CST 1970
   ```

   > 北京是东8区，所以早8个小时

3. SimpleDateFormat()

   `java.text` 包下，负责日期格式化

   直接调用构造方法，参数列表指定日期格式

   | 日期格式 | 含义 |
   | -------- | ---- |
   | yyyy     | 年   |
   | MM       | 月   |
   | dd       | 日   |
   | HH       | 时   |
   | mm       | 分   |
   | ss       | 秒   |
   | SSS      | 毫秒 |

   - 除了日期格式字母不能随便写以外，其他字符可以自定义
   - 日期格式字母个数可更改

   调用 SimpleDateFormat 对象的 `.format(Date)` 来以指定格式输出 String

   ```java
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SSS");
   String formatDate = sdf.format(nowDate);
   System.out.println(formatDate);
   // 2022-06-26 09:33:51:786
   ```

   日期字符串如 String 如何转换成 Date 类型？

   使用 SimpleDateFormat 对象的 `.parse(String)` 方法输出 Date

   ```java
   public static void main(String[] args) throws ParseException {
   	String time = "2004-04-04 04:04:44:444";
       SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-mm-dd HH:mm:ss:SSS");
       Date d = sdf2.parse(time);
       System.out.println(d);
   }
   ```

   - 需要在方法中抛出异常
   - 字符串日期格式和 SimpleDateFormat 指定的日期格式需要相同

4. System.currentTimeMillis();

   获取自 1970年1月1日 0时0分0秒 000毫秒 到当前系统时间的的总毫秒数

   ```java
   long nowTimeMillis = System.currentTimeMillis();
   System.out.println(nowTimeMillis);
   // 1656208105060
   ```

   可用来计算程序运行时间

   ```java
   // 统计当前系统 for 循环1000次耗费的时间
   long startTime = System.currentTimeMillis();
   for (int i = 0; i < 1000; i++) {
   	System.out.println("i = " + i);
   }
   long endTime = System.currentTimeMillis();
   System.out.println("Using " + (endTime-startTime) + "ms");
   // Using 72ms
   ```

   - 打印结果会大量耗费时间

# 数字相关类

`java.text.DecimalFormat`

1. *java.text.DecimalFormat()*

   专门负责数字格式化

   ```java
   DecimalFormat df = new DecimalFormat("数字格式")
   ```

   | 数字符号 | 数字格式  |
   | -------- | --------- |
   | #        | 任意数字  |
   | ,        | 千分位    |
   | .        | 小数点    |
   | 0        | 不够时补0 |

   ```java
   DecimalFormat df = new DecimalFormat("###,###.0000");
   String s = df.format(1234567.891);
   System.out.println(s);
   // 1,234,567.8910
   ```

2. BigDecimal

   `java.math.BigDecimal`

   属于大数据，精度极高，属于 Java 对象（引用数据类型），是 SUN 提供的一个类，主要用于财务软件中（Double 精度不够）

   ```java
   BigDecimal v1 = new BigDecimal(100);
   BigDecimal v2 = new BigDecimal(200);
   ```

   引用不可使用运算符运算，需调用 `.add()` 进行求和：

   ```java
   BigDecimal v3 = v1.add(v2);
   System.out.println(v3);
   // 300
   ```

# Random

`java.util.Random`

随机数

```java
Random r = new Random();
System.out.println(r.nextInt());
// -822415571
```

`r.nextInt(bound)`：产生 [0, bound) 之间的随机数

```java
System.out.println(r.nextInt(100));
// 86
```

## Quiz

生成5个不重复的随机数，放到数组中

```java
package io.github.wataaaame.javase.random;

import java.util.Random;

public class RandomTest02_Plus {
    public static void main(String[] args) {
        int[] a = new int[5];
        Random r = new Random();

        // 数组默认值-1，以免与生成的0混淆
        for (int i = 0; i < a.length; i++) {
            a[i] = -1;
        }

        for (int i = 0; i < a.length; ) {
            int num = r.nextInt(5);
            if (!contains(a, num)) {
                a[i++] = num;
            }
        }

        // 打印结果
        for (int i = 0; i < a.length; i++) {
            System.out.print(a[i] + " ");
        }
    }

    /**
     * @param a 数组
     * @param n 元素
     * @return 若数组包含元素返回 true，否则 false
     */
    public static boolean contains(int[] a, int n) {
        for (int i = 0; i < a.length; i++) {
            if (a[i] == n)
                return true;
        }
        return false;
    }
}
// 2 4 1 3 0
```

# Enum（枚举）

返回值多于两种时，可以使用枚举类型（2种可使用 boolean）

- 枚举也是一种引用数据类型
- 一枚一枚可以列举出来的，才建议使用枚举类型
- 枚举编译后也是生成 class 文件
- 枚举中的每一个值可以看作是**常量**

> 例如：四级、星期等

```java
enum [枚举类型名] {
    枚举值1, 枚举值2, ...
}
```

## Example

随机返回周一到周日

```java
package io.github.wataaaame.javase.enum2;

// 一周七天
public enum Weekdays {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, NULL
}

```

```java
package io.github.wataaaame.javase.enum2;

import java.util.Random;

public class EnumTest01 {
    public static void main(String[] args) {
        Random r = new Random();

        System.out.println(Weekdays(r.nextInt(7)));
    }

    public static Weekdays Weekdays(int d) {
        // 输入一个随机 int，返回对应的枚举
        switch (d) {
            case 0: return Weekdays.SUNDAY;
            case 1: return Weekdays.MONDAY;
            case 2: return Weekdays.TUESDAY;
            case 3: return Weekdays.WEDNESDAY;
            case 4: return Weekdays.THURSDAY;
            case 5: return Weekdays.FRIDAY;
            case 6: return Weekdays.SATURDAY;
        }
        return Weekdays.NULL;
    }
}

```

- 高版本 switch 参数列表中支持：String，**枚举**（老版本仅支持 int）
