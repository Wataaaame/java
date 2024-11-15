---
title: JavaSE - Note11 Reflect & Path
date: 2022-07-11 10:36:49
categories: JavaSE
tags: [Reflect, Path]
---

# 反射机制

通过 Java 语言中的反射机制，可以操作字节码文件、代码片段（class 文件），让程序更加灵活

`java.lang.reflect.*`

## 相关重要类

### java.lang.Class

代表整个字节码（一个类型，整个类），要操作一个类的字节码，首先需要获取到**这个类的字节码文件**，有以下三种方式：

1. **static** Class<E> forName(String className)

   - 静态方法
   - 方法参数是一个字符串（需要有包路径的完整类名）
   - 方法会导致类加载，若**只想执行**类中 static 静态代码块，可以这么使用

   ```java
   Class c1 = Class.forName("java.lang.String");
   // 代表 java.lang.String.class 文件
   ```

2. Class<E> getClass()

   - Java 中任何一个对象都有 getClass 方法

   ```java
   String s = "abc";
   Class x = s.getClass();
   // x 代表 String.class 字节码文件、String 类型
   System.out.println(c1 == x);	// true（内存地址）
   // c1 与 x 同时指向方法区中的 String.class 字节码文件
   ```

3. Java 语言中任何一种类型（包括基本数据类型），它都有 .class 属性

   ```java
   Class z = int.class;	// z 代表 int 类型
   ```

#### 获取字节文件作用

反射机制获取 Class，通过 Class 的 `newInstance()` 方法来实例化对象

- newInstance() 方法后台调用了无参构造方法，需保证其存在

- 该方法在 JDK 9 之后已过时（此处为了演示），有替代方案

  ```java
  Class c = Class.forName("[package].[className]");
  Object o = c.newInstance();
  System.out.println(o);
  ```

优点：更为灵活

通过 properties 配置文件配置 className，创建对应类时，只需要修改配置文件中的类名，然后通过 io 流读取，再通过反射机制创建类

Java 代码写一遍，在不改变 Java 源码的基础上，可以做到不同对象的实例化（符合 OCP 开放原则：对扩展开放，对修改关闭）

classInfo.properties

```properties
className=java.util.Date
```

Test.java

```java
package io.wataaaame.github.javase.reflect.flex;

import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

public class Test {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InstantiationException, IllegalAccessException {
        // 验证反射机制的灵活性
        // 创建 Properties 集合
        Properties p = new Properties();
        // 通过 IO 流读取配置文件中的 className
        FileReader fr = new FileReader("classInfo.properties");

        // 将读取到的信息存入到 p 集合
        p.load(fr);

        // 关闭流
        fr.close();

        // 通过 key，获取到 className
        String className = p.getProperty("className");
        // 通过反射机制，将获取到的 className 用于实例化对象
        Class c = Class.forName(className);
        Object o = c.newInstance();
        System.out.println(o);	// Mon Jul 11 12:12:00 CST 2022
    }
}
```

### java.lang.reflect.Field

代表字节码中的属性字节码（类中成员变量）

1. Field getField(String name)

   返回一个 Field 对象（仅支持 public 修饰变量）

2. Field[] getFields()

   返回一个 Field 数组（仅支持 public 修饰变量）

   > 若要获取所有类型权限符修饰的变量，使用
   >
   > `Field getDeclaredField()` 或
   >
   > `Field getDeclaredFields()`

3. String getName()

   返回由此 Field 对象表示的字段的名称

4. Class<E> getType()

   返回一个标识字段的类对象

   转换成 Class 便能获取类名/简单类名

   > Class 对象支持以下两种方法：
   >
   > 1. String getName()
   >
   >    返回类名
   >
   > 2. String getSimpleName()
   >
   >    返回简单的类名（不含包名）

5. int getModifiers()

   返回修饰符（可能有多个，即为 int 类型，表示修饰符代号）

   > 通过 `java.lang.reflect.Modifier` 中的
   >
   > `static String toString(int mod)` 方法进行转换
   >
   > > Class 中也有此方法

#### Example

实现成员变量反编译

Student.java

```java
package io.wataaaame.github.javase.reflect.decompilation;

public class Student {
    private int no;
    public String name;
    protected boolean sex;
    int age;
    public static final double MATH_PI = 3.14;
}
```

Test.java

```java
package io.wataaaame.github.javase.reflect.decompilation;

import java.lang.reflect.Field;
import java.lang.reflect.Modifier;

public class Test {
    public static void main(String[] args) throws Exception {
        // 获取反编译 Class 文件
        Class c = Class.forName("io.wataaaame.github.javase.reflect.decompilation.Student");
        // 使用字符串拼接
        StringBuilder sb = new StringBuilder();

        sb.append("package " + c.getPackageName() + ";\n\n");
        sb.append(Modifier.toString(c.getModifiers()) +
                " class " + c.getSimpleName() + " {\n");

        // 通过 Field，配合循环，获取属性信息
        Field[] fields = c.getDeclaredFields();
        for (Field field : fields) {
            sb.append("\t" + Modifier.toString(field.getModifiers()) + " " +
                    field.getType().getSimpleName() + " " +
                    field.getName() + ";\n");
        }

        sb.append("}");

        System.out.println(sb);
    }
}
```

#### 获取对象属性

void set(Object obj, Object value)

将指定对象 obj 上的此 Field 赋一个值 value

Object get(Object obj)

获取指定对象的 Field 值

```java
// 使用反射机制创建一个 Class
Class studentClass = Class.forName("[path]");
// 通过 Class 创建对象 obj
Object obj = studentClass.newInstance();
// 获取 studentClass 中的 no 属性
Field noField = studentClass.getDeclaredField("no");
// 设置 no 值并赋给 obj 对象 中的 no
noField.set(obj, 101);

// 获取值
System.out.println(noField.get(obj));	// 101
```

- 打破封装（反射机制缺点，可能带来安全性问题）

  通过 `Field.setAccessible(true)` 开启私有属性的访问权

### java.lang.reflect.Method

代表字节码中的方法字节码（类中方法）

常用方法类似于 Field

1. Class<E> getReturnType()

   获取方法返回值类型，（调用 Class 的 `getSimpleName()`）

2. Class<E>[] getParameterTypes()

   获取参数列表类型，（循环调用 Class 的 `getSimpleName()`）

#### 调用对象方法

方法 getDeclaredMethod(String name, Class<E>... parameterTypes)

获取对象：对象名；参数列表

**Object invoke(Object obj, Object... args)**

调用方法：对象；Method 方法名（调用函数）；实参列表；返回值

```java
// 使用反射机制创建一个 Class
Class studentClass = Class.forName("[path]");
// 通过 Class 创建对象 obj
Object obj = studentClass.newInstance();
// 获取 studentClass 中的 doSome() 方法
Method doSomeMethod = studentClass.getDeclaredMethod("doSome", String.class, String.class);
// 调用方法
Object retValue = doSomeMethod.invoke(obj, "admin", "123");
```

#### *可变长度参数

`类型... [name]`

可传 0-n 个，且必须位于参数列表中最后一个（只能有一个）

可变长度参数可以当作一个数组来看待（有数组特征）

```java
public static m(int... args) {};

m();
m(10);
m(10, 20);
```

### java.lang.reflect.Constructor

代表字节码中的构造方法字节码（类中构造方法）

类似于 Method

#### 构造方法创建对象

```java
// 使用反射机制创建一个 Class
Class studentClass = Class.forName("[path]");
// 通过 Class 创建对象 obj
Object obj = studentClass.newInstance();
// 获取 studentClass 中的构造方法
Constructor c = studentClass.getDeclaredConstructor(int.class, String.class);
// 无参构造方法可以直接调用
Object o = studentClass.newInstance();
// 通过构造器调用 newInstance() 方法 new 对象
Object newObj = c.newInstance(1, "v");
```

## 获取父类与接口

```java
// 获取 Class 字节码
Class c = Class.forName("java.lang.String");

// 获取父类
System.out.println(c.getSuperclass().getName());
// java.lang.Object

// 获取实现的接口（返回 Class 数组）
Class[] classes = c.getInterfaces();
for (Class temp : classes)
    System.out.println(temp.getSimpleName());
/**
 * Serializable
 * Comparable
 * CharSequence
 * Constable
 * ConstantDesc
 */
```

# 文件路径

## IDEA 路径

```java
new FileReader("chapter25/classInfo.properties");
```

缺点：移植性差，IDEA 中默认的当前路径是 project 的根，离开了 IDEA，路径便无效

## 通用路径

前提：文件必须在**类路径**下（src 是类的根路径）（仅等同于类路径，真正的类路径在 IDEA 的 out 中）

```java
String path = Thread.currentThread().getContextClassLoader().getResource("classInfo.proerties").getPath();
```

- Thread.currentThread()：当前线程对象
- getContextClassLoader()：线程对象方法，可以获取当前线程的类加载器对象
- getResource("classInfo.proerties")：类加载器对象方法，当前线程的类加载器默认从**类的根路径**下加载资源（此为根路径下的 classInfo.properties文件）
- getPath()：获取路径

还可以直接以**流的形式**返回：

```java
InputStream reader = Thread.currentThread.getContextClassLoader().getResourceAsStream("classInfo.properties");
```

## 资源绑定器

`java.util` 包下提供了一个**资源绑定器**，便于获取**属性配置文件**中的内容

```java
ResourceBundle bundle = ResourceBundle.getBundle("classInfo");
String className = bundle.getString("className");
System.out.println(className);
```

- 只能绑定 `xxx.properties`
- 必须放在**类路径下**
- 路径忽略扩展名