---
title: JavaSE - Note09 IO Stream, File, IO & Properties
date: 2022-07-06 10:30:56
categories: JavaSE
tags: [IO Stream, File, IO & Properties]
---

# IO Stream

`java.io.*`

硬盘文件到内存的读（Input）和写（Output）

## 分类

1. 流的方向（以内存为参照物）

   往内存中去：输入流（读）；从内存中出：输出流（写）

2. 读取数据方式

   - 字节方式（字节流）：一次读1个字节 byte，等同于一次读取8个二进制位（万能读取）
   - 字符方式（字符流）：一次读取一个字符（方便读取纯文本文件）

## Stream 通用规则

- 类名以 Stream 结尾的都是**字节流**

- 所有流都实现了 `java.io.Closeable` 接口，都有 close 方法，都是可关闭的
  使用完流记得关闭以节约资源

- 所有**输出流**都实现了 `java.io.Flushable` 接口，都有 flush 方法，都是可刷新的

  输出流在最终输出后，要记得 flush 刷新（清空流中未输出的数据），否则有可能丢失数据

- 文件路径：

  支持相对路径或绝对路径

  反斜杠 `\` 需要两个，否则会被识别为转义字符

  可用正斜杠 `/` 代替
  
  > IDEA 默认当前路径为工程 project 的根路径

## 常用 Stream（16个）

### File

文件流

1. **java.io.FileInputStream**

   文件字节输入流，万能输入，任何类型的文件都可以采用这个流读取

   ```java
   // 创建流引用
   // 设置为 null 方便在 finally 中使用
   FileInputStream fis = null;
   
   try {
       fis = new FileInputStream("C:\\vv\\code\\Java\\JavaSE\\chapter23\\src\\io\\github\\wataaaame\\javase\\io\\test.txt");
       // 读取数据 read 方法（IOException 异常包含掉第一个异常）
       // 循环读取文件，返回-1代表读到末尾
       int readData = 0;
       // read() 方法返回读取到的数据
       while ((readData = fis.read()) != -1)
           System.out.println(readData);
   } catch (FileNotFoundException e) {
       e.printStackTrace();
   } catch (IOException e) {
       e.printStackTrace();
   } finally { // 使用后一定记得关闭流
       // 若流为空，则没必要关闭；也为了避免空指针异常
       if (fis != null) {
           try {
               fis.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   缺点：一次读取一个字节 byte，内存和硬盘交互太频繁，资源都耗费在交互上（使用 byte[] 数组改进）

   ```java
   /*
   int read(byte[] b)
   一次最多往 byte[] 数组中读取 b.length 个字节
   */
   // a b c d e f
   
   byte[] bytes = new byte[4];
   // 此方法返回的是读取到的字节数量
   int readCount = fis.read(bytes);
   System.out.println(readCount);	// 4
   // 数组转换成 String 输出
   System.out.println(new String(bytes));	// a b c d
   
   readCount = fis.read(bytes);
   System.out.println(readCount);	// 2
   // 应该是读了多少个转多少个
   //System.out.println(new String(bytes));	// e f c d
   System.out.println(new String(bytes, 0, readCount));	// e f
   
   System.out.println(fis.read(bytes));	// -1
   ```

   最终版：

   ```java
   FileInputStream fis = null;
   byte[] bytes = new byte[4];
   int readCount;
   
   try {
       fis = new FileInputStream("chapter23/src/io/github/wataaaame/javase/io/test.txt");
       /*
       while (true) {
           // 按照数组容量读取字节
           readCount = fis.read(bytes);
           // 如果无数据则退出
           if (readCount == -1)
               break;
           // 如果有数据则按读取量转换为数组输出
           System.out.print(new String(bytes, 0, readCount));	// a b c d e f
       }
       */
       while ((readCount = fis.read(bytes)) != -1) {
           System.out.print(new String(bytes, 0, readCount));
       }
   } catch (FileNotFoundException e) {
       e.printStackTrace();
   } catch (IOException e) {
       e.printStackTrace();
   } finally {
       if (fis != null) {
           try {
               fis.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   其他常用方法：

   1. int available()

      返回流中未读到的剩余字节数

      ```java
      // a b c d e f
      fis.read();
      System.out.println(fis.available());	// 5
      ```

      - 可以直接通过该方法指定 byte[] 数组长度（不适合大文件，byte[] 数组不能太大）

   2. long skip(long n)

      跳过 n 个字节不读

      ```java
      // a b c d e f
      fis.skip(3);
      System.out.println(fis.read);	// 100
      ```

2. **java.io.FileOutputStream**

   文件字节输出流，负责写

   - 输出路径文件不存在时会自动创建，若存在则自动追加，每次执行会**清空源文件再重新写入**
   - 追加写入只需在构造方法中路径之后加上 `true` 即可

   ```java
   FileOutputStream fos = null;
   byte[] bytes = {86, 65, 89};
   
   try {
       // 追加写入：new FileOutputStream("path", true);
       fos = new FileOutputStream("chapter23/src/io/github/wataaaame/javase/io/Output");
       // 将 byte[] 数组全部写出
       // fos.write(byte[] b);
       // 将 byte[] 数组全部写出
       // fos.write(byte[] b, int off, int len);
       fos.write(bytes);
   
       // 文件写完需要刷新
       fos.flush();
   } catch (FileNotFoundException e) {
       e.printStackTrace();
   } catch (IOException e) {
       e.printStackTrace();
   } finally {
       if (fos != null) {
           try {
               fos.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   - 可将 String 字符串，通过 `.getBytes()` 方法转换为 byte[] 数组传入输出流进行写入操作

     通过 FileInputStream 与 FileOutputStream 流实现文件的拷贝：

   ```java
   package io.github.wataaaame.javase.io;
   
   import java.io.FileInputStream;
   import java.io.FileNotFoundException;
   import java.io.FileOutputStream;
   import java.io.IOException;
   
   public class CopyTest01 {
       public static void main(String[] args) {
           FileInputStream fis = null;
           FileOutputStream fos = null;
           // 一次拷贝 10B
           byte[] bytes = new byte[10];
           // 每次读取计数
           int readCount;
   
           try {
               fis = new FileInputStream("chapter23/src/io/github/wataaaame/javase/io/Output");
               fos = new FileOutputStream("Z:/CopyTest");
   
               // 读多少，写多少
               while ((readCount = fis.read(bytes)) != -1)
                   fos.write(bytes, 0, readCount);
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           } finally {
               // 分别进行 try/catch，避免第一个抛出异常后第二个无法执行
               if (fis != null) {
                   try {
                       fis.close();
                   } catch (IOException e) {
                       e.printStackTrace();
                   }
               }
               if (fos != null) {
                   try {
                       fos.close();
                   } catch (IOException e) {
                       e.printStackTrace();
                   }
               }
           }
       }
   }
   
   ```

3. java.io.FileReader

   文件字符输入流，只能读取普通文本

   通过 char[] 数组读取

4. java.io.FileWriter

   文件字符输出流，只能输出普通文本

   支持直接写入字符串

### Stream

转换流（将字节流转换成字符流）

1. java.io.InputStreamReader

   将 InputStream 输入字节流转换为 Reader 字符流

   ```java
   InputStreamReader isr = new InputStreamReader(fis);
   ```

2. java.io.OutputStreamWriter

   将 OutputStream 输出字节流转换为 Writer 字符流

   ```java
   OutputStreamReader osr = new OutputStreamReader(fos);
   ```

### Buffered

缓冲流

自带缓冲区的字符缓冲流，不需要指定数组

1. java.io.BufferedReader

   ```java
   public static void main(String[] args) throws IOException {
       FileReader reader = new FileReader("chapter23/src/io/github/wataaaame/javase/io/test.txt");
       // 当一个流的构造方法中需要一个流的时候，被传进来的流叫做：节点流
       // 外部负责包装的流叫做：包装流/处理流
       // new BufferedReader(Reader in) 构造方法只能传递字符流
       BufferedReader br = new BufferedReader(reader);
   
       // 该方法支持读取一行，读到末尾时返回 null
       // 行尾不会自带换行符
       String s;
       while ((s = br.readLine()) != null)
           System.out.println(s);
       /*
           abcdef
           ghijkl
           mnopqr
           stuvwx
           yzvvvv
       */
   
       // 对于包装流，只需要关闭最外层流，里面的节点流会自动关闭
       br.close();
   }
   ```

    将字节流通过转换流转换为字符流，再变成缓冲流

   ```java
   public static void main(String[] args) throws IOException {
       // 字节流
       FileInputStream fis = new FileInputStream("chapter23/src/io/github/wataaaame/javase/io/test.txt");
   
       // 通过转换流转换为字符流
       InputStreamReader isr = new InputStreamReader(fis);
   
       // 构造方法只能传递字符流
       BufferedReader br = new BufferedReader(isr);
   
       // 合并
       // BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream("path")));
   
       // 读取一整行
       String line;
       while ((line = br.readLine()) != null)
           System.out.println(line);
       /*
           abcdef
           ghijkl
           mnopqr
           stuvwx
           yzvvvv
       */
   
       // 关闭最外层流
       br.close();
   }
   ```

2. java.io.BufferedWriter

   ```java
   public static void main(String[] args) throws IOException {
       // 合并字节输出流、字节输出流转字符输出流、字符输出流
       BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("test")));
       // 写入文档
       bw.write("Hello");
       bw.write("\n");
       bw.write("World");
       // 记得刷新
       bw.flush();
       // 记得关闭
       bw.close();
   }
   ```

3. java.io.BufferedInputStream

4. java.io.BufferedOutputStream

### Data

数据流

将数据连同数据类型写入文件（非普通文档，无法用记事本打开）

1. java.io.DataInputStream

   读取 DataOutputStream 的文件时需提前知道写入的顺序

   ```java
   DataInputStream dis = new DataInputStream(new FileInputStream("output"));
   
   // 开始读
   System.out.println(dis.readByte());
   System.out.println(dis.readShort());
   System.out.println(dis.readInt());
   System.out.println(dis.readLong());
   System.out.println(dis.readFloat());
   System.out.println(dis.readDouble());
   System.out.println(dis.readBoolean());
   System.out.println(dis.readChar());
   
   // 关闭流
   dis.close();
   
   /*
       99
       1
       2
       3
       4.4
       5.5
       false
       b
   */
   ```

2. java.io.DataOutputStream

   DataOutputStream 写的文件，只能通过 DataInputStream 去读

   ```java
   // 创建数据专属的字节输出流
   DataOutputStream dos = new DataOutputStream(new FileOutputStream("output"));
   
   // 创建数据
   byte b = 99;
   short s = 1;
   int i = 2;
   long l = 3;
   float f = 4.4F;
   double d = 5.5;
   boolean bl = false;
   char c = 'b';
   
   // 写入
   dos.writeByte(b);
   dos.writeShort(s);
   dos.writeInt(i);
   dos.writeLong(l);
   dos.writeFloat(f);
   dos.writeDouble(d);
   dos.writeBoolean(bl);
   dos.writeChar(c);
   
   // 刷新流
   dos.flush();
   
   // 关闭流
   dos.close();
   ```

### Print

标准输出流

1. java.io.PrintWriter

2. **java.io.PrintStream**

   ```java
   // 联合起来写
   System.out.println("Hello World");
   
   // 分开写
   PrintStream ps = System.out;
   ps.println("Hello vv");
   ps.println("Hello ee");
   
   // 标准输出流不需要手动关闭
   ```

   标准输出流可以改变输出方向：`System.setOut`

   ```java
   // 设置节点流
   PrintStream psf = new PrintStream(new FileOutputStream("log"));
   // 更改输出方向
   // 可设置为 System.out 更改回
   System.setOut(psf);
   System.out.println("hello vv");
   System.out.println("hello ee");
   ```

   记录日志工具：

   ```java
   // 记录日志工具，先将标准输出流导向 log 文件
   // 再记录当前日期以及传来的日志文本
   public static void log(String msg) {
       try {
           // 设置一根指向一个日志文件的管子
           PrintStream ps = new PrintStream(new FileOutputStream("chapter23/src/io/github/wataaaame/javase/io/logger/log", true));
           // 改变输出器方向为该管
           System.setOut(ps);
           // 可以理解为：第一步创建一根指向某文件的新管子，第二步将输出口指向该管子入口
   
           // 获取当前日期
           Date nowDate = new Date();
           // 更改日期格式
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss sss");
           // 将更改格式后的日期转换为 String 类型
           String strNowDate = sdf.format(nowDate);
   
           // 写入日志
           System.out.println(strNowDate + ": " + msg);
       } catch (FileNotFoundException e) {
           e.printStackTrace();
       }
   }
   ```

### Object

对象流

1. **java.io.ObjectInputStream**

   **Deserialize 反序列化**

   将硬盘数据恢复为 Java 对象的过程

   ```java
   package io.github.wataaaame.javase.io;
   
   import java.io.FileInputStream;
   import java.io.IOException;
   import java.io.ObjectInputStream;
   
   public class ObjectInputStreamTest01 {
       public static void main(String[] args) throws IOException, ClassNotFoundException {
           ObjectInputStream ois = new ObjectInputStream(new FileInputStream("student"));
   
           System.out.println(ois.readObject());
           // Student{no=101, name='vv'}
   
           ois.close();
       }
   }
   ```

   反序列化 ArrayList 集合

   ```java
   public class ObjectInputTest02 {
       public static void main(String[] args) throws Exception {
           ObjectInputStream ois = new ObjectInputStream(new FileInputStream("student"));
   
   //        输出为 Object 集合，底层属于 List，需要强转为 List
   		System.out.println(ois.readObject() instanceof List);	// true
           List<Student> studentList = (List<Student>)ois.readObject();
           for (Student student : studentList) {
               System.out.println(student);
           }
           /*
           	Student{no=101, name='vv'}
               Student{no=102, name='ee'}
               Student{no=103, name='xx'}
               Student{no=104, name='yy'}
           */
   
           ois.close();
       }
   }
   ```

2. **java.io.ObjectOutputStream**

   **Serialize 序列化**

   将 Java 对象的状态保存下来的过程（对象拆成碎片从内存一块块传输到硬盘）

   ```java
   package io.github.wataaaame.javase.io;
   
   import java.io.*;
   
   public class ObjectOutputStreamTest01 {
       public static void main(String[] args) throws IOException {
           Student s1 = new Student(101, "vv");
           ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("student"));
   
           oos.writeObject(s1);
   
           oos.flush();
           oos.close();
       }
   }
   
   class Student implements Serializable {
       private int no;
       private String name;
   
       public Student() {
       }
   
       public Student(int no, String name) {
           this.no = no;
           this.name = name;
       }
   
       @Override
       public String toString() {
           return "Student{" +
                   "no=" + no +
                   ", name='" + name + '\'' +
                   '}';
       }
   }
   
   ```
   
   存多个对象需序列化 ArrayList 集合
   
   ```java
   public class ObjectOutputTest02 {
       public static void main(String[] args) throws IOException {
           List<Student> studentList = new ArrayList<>();
           ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("student"));
   
           studentList.add(new Student(101, "vv"));
           studentList.add(new Student(102, "ee"));
           studentList.add(new Student(103, "xx"));
           studentList.add(new Student(104, "yy"));
   
           oos.writeObject(studentList);
   
           oos.flush();
           oos.close();
       }
   }
   ```

#### 序列化版本号

参与序列化的对象必须实现 `Serializable` 接口（标志接口，起到标识作用）

Java 虚拟机看到 Serializable 接口之后，会自动生成一个序列化版本号，用于区分相同类名的类

区分方式：

1. 首先通过类名比对，如果类名不一样，肯定不是同一个类
2. 如果类名一样，靠实现序列化接口生成的版本号区分

缺陷：一旦代码确定后不能进行后续的修改

结论：凡是一个类实现了 `Serializable` 接口，建议给该类提供一个固定不变的序列化版本号：

```java
private static final long serialVersionUID = 1L;
```

#### transient 关键字

transient 关键字表示游离的，可以将某个属性不参与序列化

```java
public class Student {
    private int no;
    private transient String name;	// 不参与序列化
    // Student{no=101, name='null'}
}
```

# File 类

File 对象代表文件和目录路径的抽象表示形式，可能是目录，也可能是文件

File 类不能完成读和写

## File 中常用的方法

`java.io.File`

创建 File 对象（构造方法传入目录）：

```java
File f1 = new File("Z:/vv");
```

1. boolean exists()

   判断该文件/目录是否存在

   ```java
   System.out.println(f1.exists());	// false
   ```

2. boolean createNewFile()  

   当且仅当该名称文件不存在时，创建一个由该抽象路径名命名的新空文件

   ```java
   f1.createNewFile();
   ```

3. boolean mkdir()

   创建由此抽象路径名命名的目录

   ```java
   f1.mkdir();
   ```

4. boolean mkdirs()

   以多重目录形式新建

   ```java
   File f2 = new File("Z:/a/b/c");
   f2.mkdirs();
   ```

5. String getParent()

   获取当前文件的父路径

   ```java
   File f3 = new File("Z:\\Java\\src\\java.base\\java\\io\\File.java");
   System.out.println(f3.getParent());
   // Z:\Java\src\java.base\java\io
   ```

6. File getParentFile()

   返回此抽象路径名的 File 对象

   1. public String getAbsolutePath()

      返回此 File 对象抽象路径名的绝对路径名字符串

      ```java
      File f4 = new File("Z:\\\\Java\\\\src\\\\java.base\\\\java\\\\io\\\\File.java");
      File parentFile = f4.getParentFile();
      System.out.println(parentFile.getAbsolutePath());
      ```

7. String getName()

   返回由此抽象路径名表示的文件或目录的名称

   ```java
   System.out.println(f4.getName());	// File.java
   ```

8. boolean isDirectory()

   判断是否为目录

   ```java
   System.out.println(f4.isDirectory());	// true
   ```

9. boolean isFile()

   判断是否为文件

   ```java
   System.out.println(f4.isFile());	// true
   ```

10. long lastModified()

    获取最后一次修改的时间（1970年到现在的总毫秒数）

    ```java
    System.out.println(f4.lastModified());	// 1562023764000
    ```

    将毫秒数转换成日期：

    ```java
    Date date = new Date(f4.lastModified());
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    System.out.println(sdf.format(date));	// 2019-07-02 07:29:24
    ```

11. long length()

    获取文件大小

    ```java
    System.out.println(f4.length());	// 98503
    ```

12. File[] listFiles()

    获取当前目录下所有子文件

    ```java
    File[] files = f4.listFiles();
    for (File file : files)
        System.out.println(file.getAbsoluteFile());
    ```

## Puzzle

拷贝目录所有文件到另一个目录

```java
package io.github.wataaaame.javase.io.copydirectory;

import java.io.*;

public class CopyDirectory {
    public static void copyDir(File srcDir, File destDir) {
        // 如果 file 是一个文件，则终止递归，并拷贝到目标目录
        if (srcDir.isFile()) {
            FileInputStream fis = null;
            FileOutputStream fos = null;

            try {
                fis = new FileInputStream(srcDir);

                String strDestDir = destDir + "\\" + srcDir.getAbsolutePath().substring(3);
                fos = new FileOutputStream(strDestDir);

                byte[] bytes = new byte[1024];
                int readCount;
                while ((readCount = fis.read(bytes)) != -1)
                    fos.write(bytes, 0, readCount);

                fos.flush();
            } catch (java.io.IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (fis != null) {
                        fis.close();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
                try {
                    if (fos != null) {
                        fos.close();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            return;
        }
        // 获取源文件当前路径所有 file
        File[] files = srcDir.listFiles();
        for (File file : files) {
            // 如果 file 是一个文件夹，则在目标目录新建对应文件夹
            if (file.isDirectory()) {
                String strSrcDir = file.getAbsolutePath();
                String strDestDir = destDir + "\\" + strSrcDir.substring(3);
                File newFile = new File(strDestDir);
                newFile.mkdirs();
            }
            // 递归，将当前 file 再通过 copyDir 方法遍历
            copyDir(file, destDir);
        }
    }
}
```

# IO & Properties

IO流：文件的读和写

Properties：是一个 Map 集合，key 和 value 都是 String 类型

通过 `void load(InputStream inStream)` 方法，从输入字节流读取属性列表（键和元素对）到 Properties 集合

```java
public static void main(String[] args) throws IOException {
    // 将 userinfo 中的数据加载到 Properties 对象中
    // 新建一个输入流对象
    FileInputStream fis = new FileInputStream("chapter23/src/io/github/wataaaame/javase/io/properties/userinfo");

    // 新建一个 Map 集合
    Properties p = new Properties();

    // 调用 Properties 对象的 load 方法将文件中的数据加载到 Map 集合中
    // 文件中的数据顺着管道加载到 Map 集合中，其中等号左边做 key，右边做 value
    p.load(fis);

    System.out.println(p.getProperty("username"));
    System.out.println(p.getProperty("password"));
    
    fis.close();
}
```

> 经常改变的信息可以放在文件当中，可使程序动态修改信息而代码无需改动或编译
>
> 类似于以上机制的文件称为配置文件，且内容格式为：key=value 形式的叫做属性配置文件
>
> Java 规范中要求：属性配置文件建议以 .properties 结尾，但这不是必须的（Properties 类便是专门存放属性配置文件的一个类）
>
> 属性配置文件重复时，value 会自动覆盖
>
> 建议：等号之间不要有空格；不用冒号代替等号