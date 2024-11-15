---
title: SSM - Note01 Maven
date: 2022-08-19 08:50:13
categories: SSM
tags: Maven
---

# Maven

Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理，具有以下功能：

1. 管理 jar 文件
2. 自动下载 jar 和 doc、src 源代码
3. 管理 jar 的直接依赖（若 a 需要 b，则自动下载 b）
4. 管理需要的 jar 版本
5. 把 java 编译为 class
6. 测试程序代码
7. 打包为 jar/war 文件
8. 部署项目

## 构建

构建是面向过程的，完成项目代码的编译、测试、运行、打包、部署等

Maven 支持的构建包括：

1. 清理：把之前项目编译的东西删除，为新的编译代码做准备

2. 编译：把程序源代码批量编译为可执行代码

3. 测试：批量测试程序代码，可以同时测试多个功能

4. 报告：生成测试结果文件

5. 打包：把项目中所有的 class、配置等所有资源放到一个压缩文件中，这个压缩文件就是项目的结果文件

   > 通常 Java 程序，压缩文件扩展名为 jar
   >
   > web 应用，压缩文件扩展名为 war

6. 安装：将打包生成的 jar/war 安装到本机仓库

7. 部署：把程序安装好后执行

## 第一次使用

### 安装和配置

1. 从 Maven 官网下载 Maven 安装包：[apache-maven-3.3.9-bin.zip](https://archive.apache.org/dist/maven/maven-3/3.3.9/binaries/)

2. 解压安装包到非中文目录

   bin：Maven 执行程序，主要使用 mvn.cmd

   conf：Maven 配置文件（settings.xml）

3. 配置环境变量

   在环境变量中，指定 M2_HOME 值为 Maven 安装目录

   将 `%M2_HOME%\bin` 添加到 path 环境变量中

4. 验证安装，执行 `mvn -v`

#### 国内镜像源

Maven 执行需下载很多 jar 包，可以配置国内镜像源

打开：`[Maven安装目录]/conf/settings.xml`

在 mirror 节点中添加以下内容（包括 mirror 标签）：

```xml
<mirror>
    <id>huaweicloud</id>
    <mirrorOf>*</mirrorOf>
    <url>https://repo.huaweicloud.com/repository/maven/</url>
</mirror>
```

#### 本机仓库目录

设置本机存储 jar 包目录

打开：`[Maven安装目录]/conf/settings.xml`

修改 `settings标签 - localRepository` 来指定目录（勿使用中文）

```xml
<localRepository>/path/to/local/repo</localRepository>
```

### Start

1. 在 project/src/main/java 中编写 Java 程序

2. 在 project 下配置 pom.xml 文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apche.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   	<modelVersion>4.0.0</modelVersion>
   	<groupId>io.github.wataaaame.maven</groupId>
   	<artifactId>maven01</artifactId>
   	<version>1.0-SNAPSHOT</version>
   </project>
   ```

3. 命令行项目路径下运行 `mvn compile` 指令，编译所有 main 下的 java 代码

   Maven 工具执行操作需要下载很多插件，执行该指令后会下载需要的 jar 包到以下目录：`C:\Users\[name]\.m2\repository`

   出现 `[INFO] BUILD SUCCESS` 代表完成，会在项目根目录下生成一个 `target` 结果目录

   > [ERROR] 不再支持源选项 5。请使用 7 或更高版本。
   >
   > - **方法一**
   >
   >   在项目 pom.xml 的 project 标签中插入以下配置信息：
   >
   >   ```xml
   >   <properties>
   >       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   >       <maven.compiler.source>13</maven.compiler.source>
   >       <maven.compiler.target>13</maven.compiler.target>
   >   </properties>
   >   ```
   >
   > - 方法二
   >
   >   配置 `[Maven安装目录]/conf/settings.xml` 中的 profile 节点
   >
   >   ```xml
   >   <profile>
   >       <id>jdk-13</id>
   >       <activation>
   >           <jdk>13</jdk>
   >           <activeByDefault>true</activeByDefault>
   >       </activation>
   >       <properties>
   >           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   >           <maven.compiler.source>13</maven.compiler.source>
   >           <maven.compiler.target>13</maven.compiler.target>
   >       </properties>
   >   </profile>
   >   ```

## Maven 核心概念

1. POM：一个文件，名称是 `pom.xml`，pom 翻译过来叫做项目对象模型
   - Maven 把一个项目当作一个模型使用
   - 控制 Maven 构建项目过程，管理 jar 依赖
2. 约定的目录结构：Maven 项目的目录和文件位置都是固定的
3. 坐标：唯一的字符串，用来表示资源
4. 依赖管理：管理项目可使用的 jar 文件
5. *仓库管理*：资源存放的位置
6. *生命周期*：Maven 工具构建项目的过程
7. *插件和目标*：执行 Maven 构建时用的工具是插件
8. 继承
9. 聚合

### *约定目录结构

每一个 Maven 项目在磁盘中都是一个文件夹

目录结构：

```
Project
|___ src					# 源代码
|    |___ main				# 主程序和配置文件
|    |    |___ java			# 程序包和包中的 Java 文件
|    |    |___ resources	# 程序中使用的配置文件
|    |___ test				# 测试程序代码和文件
|         |___ java			# 测试包 Java 文件
|         |___ resources	# 测试配置文件
|___ pom.xml				# Maven 核心文件
```

### 仓库

仓库存放 Maven 和项目使用的 jar 包

仓库的分类：

- 本地仓库：个人计算机上的文件夹，存放各种 jar
- 远程仓库：互联网上的仓库，使用互联网访问
  - 中央仓库：最权威的，所有开发人员共享使用的集中仓库
  - 镜像仓库：中央仓库的备份，各大洲重要城市建立
  - 私服仓库：一般在公司内部局域网中使用

Maven 的使用中不需要人为参与，调用顺序为：

`本地仓库 -> 私服仓库 -> 镜像仓库 -> 中央仓库`

### *pom

即 Project Object Model 项目对象模型。Maven 把一个项目结构和内容抽象成一个模型，在 xml 文件中进行声明，以方便进行构建和描述

#### 基本信息

| tag            | descript                                                     |
| -------------- | ------------------------------------------------------------ |
| modelVersion   | Maven 模型的版本，对于 Maven 2 和 3 来说，只能是 4.0.0       |
| **groupId**    | 组织 id，一般是公司域名倒写，类似于 package 命名             |
| **artifactId** | 项目名称（模块名称），对应 groupId 中项目中的子项目          |
| **version**    | 版本号，若项目还在开发中，为不稳定版本，通常版本号后带 “-SNAPSHOT-” 标识 |
| packaging      | 项目打包类型，可使用 jar、war、rar、ear、pom，默认为 jar     |

- groupId、artifactId、varsion 三个元素生成了一个 Maven 项目的基本坐标，在众多 Maven 项目中可以唯一定位到某一个项目

  坐标也决定将来项目在仓库中的路径及名称

  ```xml
  <modelVersion>4.0.0</modelVersion>
  <groupId>io.github.wataaaame.maven</groupId>
  <artifactId>maven01</artifactId>
  <version>1.0-SNAPSHOT</version>
  ```

- [搜索中央仓库](www.mvnrepository.com)：搜索使用的中央仓库，使用 groupId 或 artifactId 作为搜索条件，查找

#### 依赖

| tag                          | descript                                             |
| ---------------------------- | ---------------------------------------------------- |
| dependencies 及子 dependency | 管理其他 jar 包，这些 jar 就被称为依赖，通过坐标定位 |

```xml
<dependencies>
    <!-- 依赖 -->
	<dependency>
    	<groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.9</version>
    </dependency>
</dependencies>
```

#### 配置属性

| tag        | descript     |
| ---------- | ------------ |
| properties | 定义配置属性 |

1. 属性设置

   properties 设置 Maven 的常用属性

   ```xml
   <properties>
       <!-- Maven 项目构建使用的是 UTF-8，避免中文乱码 -->
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <!-- 编译项目使用的 JDK 版本 -->
       <maven.compiler.source>13</maven.compiler.source>
       <!-- 运行项目使用的 JDK 版本 -->
       <maven.compiler.target>13</maven.compiler.target>
   </properties>
   ```

2. 全局变量

   properties 自定义属性：一般是用于定义依赖的版本号

   1. 在 properties 通过自定义标签声明变量（标签名就是变量名）
   2. 在 pom.xml 中其他位置，使用 ${标签名} 使用变量的值

#### 构建（build）

| tag   | descript                   |
| ----- | -------------------------- |
| build | 与构建相关的配置，配置插件 |

```xml
<!-- 控制配置 Maven 构建项目的参数设置 -->
<build>
	<plugins>
    	<plusin>
        	<groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
            	<source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plusin>
    </plugins>
</build>
```

##### 资源插件

没有使用 resources 时，编译默认会将 `src/main/resources` 下的文件编译到 `target/classes` 中，且对于 `src/main/java` 目录下的非 Java 文件不处理

若执行 Java 需要在 `target/classes` 目录中使用 `src/main/java` 中的非 Java 文件，需要添加以下 build 配置信息

```xml
<build>
	<resources>
    	<resource>
        	<!-- 所在的目录 -->
            <directory>src/main/java</directory>
            <includes>
            	<!-- 包括目录下的 .properties, .xml -->
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
        </resource>
        
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.xml</include>
                <include>**/*.properties</include>
            </includes>
        </resource>
    </resources>
</build>
```

#### 继承

| tag    | descript                                                     |
| ------ | ------------------------------------------------------------ |
| parent | 若多模块需声明相同配置，拥有类似 Java 继承机制，用 parent 声明要继承的父工程的 pom 配置 |

#### 聚合

| tag     | descript                                                     |
| ------- | ------------------------------------------------------------ |
| modules | 为了统一构建整个项目的所有模块，可以提供一个额外的模块，打包方式为 pom，在其中使用 modules 聚合的其他模块，这样即可通过本模块自动识别模块间的依赖关系来构建所有模块 |

### *坐标（gav）

Maven 把任何一个插件都作为仓库中的一个项目进行管理，用一组（三个）向量组成的坐标来表示，坐标在仓库中可以唯一定位一个 Maven 项目

groupId：组织名，通常是公司或组织域名倒序+项目名

artifactId：模块名，通常是工程名

version：版本号

- 坐标决定项目在仓库中的路径
- artifactId 和 version 决定 jar 包名

### *依赖（dependency）

一个 Maven 项目正常运行需要其他项目的支持，Maven 会根据坐标自动到本地仓库中进行查找。对于程序员自己的 Maven 项目需要进行安装，才能保存到仓库中

> 不用 Maven 时所有 jar 都不属于你，需要到各个地方下载拷贝
>
> 用了 Maven 所有 jar 包都是你的，想要谁，叫谁名字就行，maven 帮你下载

pom.xml 加入依赖方式：

```xml
<!-- log4j 日志依赖 -->
<dependency>
	<groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

<!-- 单元测试依赖 -->
<dependency>
	<groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
</dependency>
```

#### 依赖范围

使用 scope 表示依赖使用的范围（生效阶段）

|              | compile（默认） | test（测试） | provided（提供者） |
| ------------ | --------------- | ------------ | ------------------ |
| 主程序有效   | 是              | 否           | 是                 |
| 测试程序有效 | 是              | 是           | 是                 |
| 参与打包     | 是              | 否           | 否                 |
| 参与部署     | 是              | 否           | 否                 |

### 生命周期（lifecycle）

Maven 构建项目的过程：清理、编译、测试、报告、打包、安装、部署

### 常用命令

独立使用，通过命令完成生命周期的执行

Maven 的命令对应着生命周期的各个阶段，执行命令需进入 pom.xml 所在目录，命令如下：

1. `mvn clean`：清理（会**删除原来编译和测试的目录**（target 目录），已经 install 到仓库的包不会删除）

2. `mvn compile`：编译主程序及配置文件（当前目录下生成 target，里面存放**主程序**生成的字节码文件）

3. `mvn test-compile`：编译测试程序及配置文件（当前目录下生成 target，里面存放**测试程序**生成的字节码文件）

4. `mvn test`：测试（生成一个目录 **surefire-reports** 保存测试结果）

   用的是 junit，一个专门测试的框架（工具）

   junit 测试内容：类中的方法，每一个方法都是独立测试的（方法是测试的基本单元，Maven 借助单元测试批量测试类中的大量方法是否符合预期）

   使用步骤：

   1. 引用坐标，在 pom.xml 中配置依赖：

      ```xml
      <!-- 单元测试依赖 -->
      <dependencies>
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.11</version>
              <scope>test</scope>
          </dependency>
      </dependencies>
      ```

   2. 在 Maven 项目中的 `src/test/java` 目录下创建测试程序

      包名保持一致

      - 测试类：

        推荐名称：`Test[ClassName]`

      - 测试方法：

        1. public 方法
        2. 没有返回值
        3. 推荐名称：`test[MethodName]`
        4. 注解：@Test

5. `mvn package`：打包主程序（编译、编译测试、测试，并按照 pom.xml 配置把主程序打包**生成 jar/war 包**）

6. `mvn install`：安装主程序（工程打包，**按照坐标保存到本地仓库**）

7. `mvn deploy`：部署主程序（工程打包，按照坐标保存到本地仓库、**私服仓库，自动部署到 web 容器中**）

### 插件

Maven 命令执行时，真正完成功能的是插件（插件就是一些 jar 文件）

```xml
<!-- 控制配置 Maven 构建项目的参数设置 -->
<build>
    <!-- 配置插件 -->
	<plugins>
        <!-- 配置具体插件 -->
    	<plusin>
            <!-- 插件组织名 -->
        	<groupId>org.apache.maven.plugins</groupId>
            <!-- 插件项目名 -->
            <artifactId>maven-compiler-plugin</artifactId>
            <!-- 插件版本 -->
            <version>3.8.1</version>
            <!-- 插件的配置信息 -->
            <configuration>
                <!-- 程序编译于 jdk13 -->
            	<source>13</source>
                <!-- 程序运行在 jdk13 -->
                <target>13</target>
            </configuration>
        </plusin>
    </plugins>
</build>
```

## 在 IDEA 中使用

IDEA 中内置了一个 Maven，一般不使用，因为修改配置不方便

### 配置

使用自己的 Maven，需要覆盖 IDEA 中的默认设置、修改指定 Maven 路径等

1. 修改 Settings

   修改当前 Project 配置

   1. 点击 `File -> Settings -> Build, Execution, Deployment -> Build Tools -> Maven`

      - Maven Home directory：Maven 安装目录
      - User settings file：Maven 安装目录下的 conf/settings.xml 配置文件
      - Local repository：本机仓库目录路径

   2. 点击 `Maven -> Runner`

      - VM Options：填写 `-DarchetypeCatalog=internal`

        避免联网下载模板文件，增加 Maven 项目创建速度

      - JRE：选择自己的 JDK 版本

2. 修改 Other Settings

   修改以后新建 Project 配置

   点击 `File -> Other Settings -> Settings for New Projects`，其余与 Settings 设置相同

以后创建项目都是基于 Maven 的形式

### 传统 Maven

#### 创建

创建普通 Java 项目

1. 新建一个空工程

2. 新建 Module，类型选择 Maven

   - Create from archetype：通过模板创建项目

     勾选后选中：`org.apache.maven.archetypes:maven-archetype-quickstart` 以创建普通 Java 项目

3. 下一步

   - Parent：父项目（暂无）

   输入项目名与项目路径

4. 下一步

   配置 groupId、artifactId、version 等相关配置信息

5. 点击完成

   弹出 `Maven project need to be imported`，点击 `Enable Auto-Import` 启用自动导入功能

   > 该选项在 pom 中改动后 IDEA 会自动刷新

由于快速创建模板默认无 resources 文件夹，可手动创建文件夹后，右键选择：`Mark Directory as -> Resources Root`，即可设置为 Maven 规范中的资源文件夹（其他文件夹类型类似）

至此项目创建完成，pom 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- 项目坐标（gav） -->
  <groupId>io.github.wataaaame</groupId>
  <artifactId>maven-test01</artifactId>
  <version>1.0-SNAPSHOT</version>

  <!-- 生成网页时的名称和地址 -->
  <name>maven-test01</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <!-- Maven 项目构建使用的是 UTF-8，避免中文乱码 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- 编译项目使用的 JDK 版本 -->
    <maven.compiler.source>13</maven.compiler.source>
    <!-- 运行项目使用的 JDK 版本 -->
    <maven.compiler.target>13</maven.compiler.target>
  </properties>

  <!-- 依赖 -->
  <dependencies>
    <!-- 单元测试 -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <!-- 自动生成，方便配置，若不需要可删除 -->
  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- default lifecycle, jar packaging: see https://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_jar_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-jar-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
        <!-- site lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#site_Lifecycle -->
        <plugin>
          <artifactId>maven-site-plugin</artifactId>
          <version>3.7.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-project-info-reports-plugin</artifactId>
          <version>3.0.0</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

#### 运行

主程序与测试程序中的 App/AppTest 为自动生成的演示文件

1. src/main/java package 中编写

   HelloMaven.java

   ```java
   package io.github.wataaaame;
   
   public class HelloMaven {
       public static void main(String[] args) {
           HelloMaven hm = new HelloMaven();
           int res = hm.add(10, 20);
           System.out.println("a + b = " + res);
       }
   
       public int add(int a, int b) {
           return a + b;
       }
   }
   
   ```

   点击方法旁绿色开始键执行该程序

   点击类旁绿色开始键执行所有程序

2. src/test/java package 中编写

   TestHelloMaven.java

   ```java
   package io.github.wataaaame;
   
   import org.junit.Assert;
   import org.junit.Test;
   
   public class TestHelloMaven {
       @Test
       public void testAdd() {
           System.out.println("testAdd() executed!");
           HelloMaven hm = new HelloMaven();
           int res = hm.add(10, 20);
           Assert.assertEquals(30, res);
       }
   
       @Test
       public void testAdd2() {
           System.out.println("testAdd2() executed!");
           HelloMaven hm = new HelloMaven();
           int res = hm.add(30, 20);
           Assert.assertEquals(50, res);
       }
   }
   
   ```

   点击方法旁绿色开始键执行该测试

   点击类旁绿色开始键执行所有测试

3. 点击右侧 “Maven” 菜单栏，选中项目下的 Lifecycle，双击执行对应的生命周期方法

   Plugins：对应的插件信息

   Dependencies：pom.xml 中的依赖信息

### webapp Maven

#### 创建

创建 Web 项目：

1. 同普通项目，创建时模板选择：`org.apache.maven.archetypes:maven-archetype-webapp`

2. 更改目录结构

   带星号表示需要手动创建

   ```
   Project
   |___ src
   |    |___ main
   |    |    |___ *java			# Sources Root
   |    |    |___ *resources		# Resources Root
   |    |    |___ webapp
   |    |         |___ WEB-INF
   |    |         |    |___ web.xml
   |    |         |___ index.jsp
   |    |___ *test
   |         |___ *java			# Test Source Root
   |         |___ *resources		# Test Resource Root
   |___ pom.xml
   ```

pom.xml 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>io.github.wataaaame</groupId>
  <artifactId>maven02-webapp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!-- web 项目是 war -->
  <packaging>war</packaging>

  <name>maven02-webapp Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>13</maven.compiler.source>
    <maven.compiler.target>13</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <finalName>maven02-webapp</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

#### 运行

web 项目运行无需添加 jar 包，直接在 pom.xml 中配置依赖即可：

```xml
<!-- Tomcat 9 -->
<!-- Servlet 依赖 -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
<!-- JSP 依赖 -->
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.1</version>
    <scope>provided</scope>
</dependency>
```

> IDEA 可在 dependency 中 alt+insert 查找依赖并添加

然后正常写 Servlet 代码即可

package 后的 war 文件可直接放在服务器的 webapps 下，服务器运行时会自动解压，并支持用户访问该 webapp
