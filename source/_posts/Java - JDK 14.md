---
title: Java - JDK 14
date: 2022-07-13 12:05:45
categories: Java
Tags: JDK 14
---

# JDK 14 新特性

2020年3月17日，正式发布 JDK 14，新增16项新特性

## 新的语言特性

新的语言特性有7项

### 正式功能

1. Switch（JEP 361）

   扩展 switch 表达式主要体现在两个方面

   1. case L -> 标签

      不带有贯穿功能，可以忽略 break，需要大括号

      可使用逗号分割多个常量

      局部变量仅作用于 case（旧版作用于 swtich）

      ```java
      // old
      switch(day) {
          // 贯穿效果
          case MON:
          case TUE:
          case WEN:
              String s = "";
              System.out.println("upper day");
              break;
          case THU:
          case FRI:
              String s = "a";	// 重复定义
              System.out.println("lower day");
          case SAT:
          case SUN:
              System.out.println("Weekend");
      }
      
      // new
      switch(day) {
              // 无贯穿
              case SAT, SUN -> {System.out.println("Weekend"); String s = "v";}
              case MON, TUE, WEN -> {System.out.println("upper part"); String s = "a";}	// 不会报错
              case THU, FRI -> System.out.pritnln("lower part");
      }
      ```

      扩展 switch 作为表达式

      ```java
      System.out.println(switch(day) {
          case MON, TUE, WEN -> "upper day";
      	case THU, FRI -> "lower day";
      	case SAT, SUN -> "Weekend";
      });
      ```

      扩展 switch 赋值给变量

      ```java
      String text = swtch(day) {
          case MON, TUE, WEN -> "upper day";
      	case THU, FRI -> "lower day";
      	case SAT, SUN -> "Weekend";
      };
      System.out.println(text);
      ```

   2. switch 新增 yield 语句

      类似于 return，给这条 case 语句返回一个值

      ```java
      day = Weekday.SAT;
      int x = switch(day) {
      	case MON, TUE, WEN -> "upper day";
      	case THU, FRI -> "lower day";
      	case SAT, SUN -> {
              // 生成[0, 10)范围内的随机整数
              int rnd = (int)(Math.random() * 10);
              System.out.pritnln("rnd" + rnd);
              // 通过 yiedl 产生一个新值
              yield rnd;
          }
      };
      System.out.println("x = " + x);	// 此处的值为 yield 返回的值
      ```

2. 有好的空指针异常（JEP 358）

   早期空指针异常只显示错误行数，无法判断诸如 `car.engine.power` 中哪一个出错

   JDK 14 中可以开启参数：`-XX:+ShowCodeDetailsInExceptionMessages`

   可以更好地指定哪个地方出现异常

3. 非易失性映射字节缓冲区（JEP 352）

   新增特定 JDK 的文件映射模式，可以使用 FileChannel 创建引用非易失性存储器的 MappedByteBuffer

### 预览功能

1. Record（JEP 359）

   提供了一种紧凑的语法来声明类，可以看作新类型，类似于枚举，只需要使用 record 定义名称及它的属性即可

   ```java
   record Person(String name, int age) {}
   ```

   系统会自动生成 Person 的标准成员：

   - Person 中的每一个属性都定义为 `private final`
   - 每个属性都提供公共的 getter 方法
   - 公共的构造方法，参数列表与声明相同
   - 生成 equals()、hashCode() 方法实现
   - 生成 toString() 方法的重写

   注意：record 不能继承；record 类隐含的是 final 类，也不能被继承

2. instanceof 模式匹配（JEP 305）

   通过 instanceof 运算符进行模式匹配

   ```java
   // old
   if (obj instanceof Integer) {
       Integer ii = (Integer)obj;
       System.out.println(ii);
   }
   
   // new
   // 若 obj 判断为 Interger 类型可直接绑定给 ii
   if (obj instanceof Integer ii) {
       System.out.println(ii);
   }
   ```

3. 文本块（JEP 368）

   在 JDK 13 中引入了文本块的预览，在 JDK 14 中进行二次预览，为了更方便的对 HTML、XML、JSON、SQL 这些字符串进行更好的阅读与维护

   使用三个引号开始

   ```java
   String html = """
       <htm>
       	<body>
       		<p> vv </p>
       	</body>
       </html>
   """;
   
   System.out.println(html);
   ```

4. 外部存储器 API（JEP 370）

   外部存储器 API 主要包含三个类：MemorySegment、MemoryAddress、MemoryLayout

   > 该功能作为孵化器模块引用（让更多的 Java 人员参与测试与反馈）
   >
   > 功能发布顺序：孵化 -> 预览 -> 发布

## 垃圾回收修改

1. G1 的 NUMA 内存分配优化（JEP 345）

   NUMA（non-uniform memory access）：非统一的内存访问

   > 内存插槽距离大不相同，实际中传输会有延迟

2. JFR 事件流（JEP 349）

   JDK flight Recorder：对数据进行连续的监视

3. 移除 CMS 垃圾收集器（JEP 363）

4. 弃用 Prllel Scvenge 和 Serial Old 垃圾收集算法（JEP 366）

   > 移除顺序：弃用 -> 移除

5. MacOS 系统上的 ZGC 实验（JEP 364）

6. Windows 系统上的 ZGC 实验（JEP 365）

## 移除包和功能

1. 弃用 Solaris 和 SPARC 端口（JEP 362）
2. 移除 Pack200（JEP 367）

## 新工具

1. 打包工具（JEP 343）

   想创建一个用于打包的独立的 Java 工具，处于孵化阶段