---
title: Java - Class Loader
date: 2022-07-11 16:15:31
categories: Java
tags: Class Loader
---

# 关于 JDK 中自带的类加载器

## 类加载器

`ClassLoader`：专门负责加载类的命令/工具

### 启动类加载器

父加载器

`%JAVA_HOME%\jre\lib\rt.jar`

rt.jar 中都是 JDK 最核心的类库

### 扩展类加载器

母加载器

`%JAVA_HOME%\jre\lib\ext\*.jar`

### 应用类加载器

`classpath` 中的 jar 包（class 文件）

### Example

假设有这样一段代码：

```java
String s = "abc";
```

代码在开始执行之前，会将所需要类全部加载到 JVM 当中

通过**类加载器**加载，以上代码类加载器会找 String.class 文件

找到就加载，加载不到会通过**扩展类加载器**加载

若依旧没加载到，会通过**应用类加载器**加载

## 双亲委派机制

父加载器和母加载器称为：双亲委派机制

- 优先从**启动类加载器**中加载，否则从**扩展类加载器**加载，保证了类加载的安全