---
title: JavaWeb - Note01 BS Framwork
date: 2022-07-26 09:11:13
categories: JavaWeb
tags: BS Framwork
---

# B/S 架构通信原理

## WEB 系统的访问过程

1. 打开浏览器
2. 地址栏输入合法网址
3. 回车
4. 浏览器展示响应结果

## 域名

域名即一个网址，通过域名解析器，被解析成一个具体的 IP 地址和端口号等

`wataaaame.github.io` --解析成--> `185.199.110.153`

### IP 地址

计算机在网络中的一个身份证号，在同一个网络中，IP 地址是唯一的

两台计算机通信，首先需要知道 IP 地址才能建立连接

### 端口号

一个端口对应一个软件，每一个软件启动之后都有一个端口号，端口代表着服务

在同一个计算机上，端口号具有唯一性

## WEB 系统通信原理

1. 用户输入网址（URL，统一资源定位符）
2. 域名解析器进行域名解析（解析成 IP 地址）
3. 浏览器软件在网络中搜索 IP 地址对应服务器
4. 服务器定位到端口对应软件
5. 软件定位浏览器想要的资源名
6. 服务器软件找到资源，并将文件输出响应到浏览器
7. 浏览器接收来自服务器的代码
8. 浏览器执行渲染

## WEB 服务器软件

提前开发好的常用服务器软件

- **Tomcat**（Apache）（WEB 服务器）
- Jetty（WEB 服务器）
- JBOSS（应用服务器）
- WebLogic（应用服务器）
- WebSphere（应用服务器）

应用服务器实现了 JavaEE 所有规范（13个）

WEB 服务器只实现了 JavaEE 中的 Servlet、JSP 两个核心规范

应用服务器包含 WEB 服务器

### Tomcat

[Tomcat](https://tomcat.apache.org)（[Apache](https://www.apache.org/) 开发），是一款开源免费的轻量级 WEB 服务器，体积小、运行速度快，只实现了 Servlet + JSP 规范

Tomcat 是 Java 语言写的，想要运行，必须先有 jre（Java 运行环境）

#### 目录

bin：命令文件（启动关闭 Tomcat）

conf：配置文件（server.xml 配置端口号）

lib：核心程序

logs：日志信息

temp：临时目录

webapps：存放大量 webapp（web application）

work：存放 JSP 翻译之后的 Java 文件以及编译之后的 class 文件

#### 配置

环境变量：

- path 对应的 bin 目录
- CATALINA_HOME
- JAVA_HOME

启动：通过 bin 目录下的 `start.bat` 启动 Tomcat 服务器

> 解决 dos 窗口乱码问题：
>
> 编辑：`%CATALINA_HOME%\conf\logging.properties`，将 `java.util.logging.ConsoleHandler.encoding` 值更改为 GBK

关闭：通过 `shutdown.bat` 关闭 Tomcat 服务器（建议修改为 `stop.bat`，以免同关机命令混淆）

验证：浏览器输入：`http://localhost:8080` 或 `http://120.0.0.1:8080`

> bat 文件是 Windows 操作系统的批处理文件，可以封装 dos 命令执行
>
> sh 文件是 Linux 环境下封装的 shell 命令

分析 start.bat 文件得出，执行该命令，实际上是执行 `catalina.bat` 文件

catalina.bat 文件中有这样一行配置：`MAINCLASS=org.apache.catalina.startup.Bootstrap`

该类就是 main 方法所在的类，启动了 main 方法代表服务器启动

#### 第一个 web 应用

1. 找到 `CATALINA_HOME\webapps` 目录

   所有 webapp 要放在 webapps 目录下

2. 新建一个子目录，起名 `oa`

   oa 就是这个 webapp 的名字

3. oa 目录下新建资源文件，例如 `index.html`

   编写 index.html 文件的内容

4. 启动 Tomcat 服务器

5. 打开浏览器，输入 URL（`http://localhost:8080/oa/index.html`）

超链接中 href 可以省略 IP 与端口号，斜杠开头，从项目名开始写，同样属于绝对路径：

```html
<a href="/oa/login.html">login2</a>
```

## B/S 架构系统的角色和协议

### 角色

浏览器软件开发团队

WEB Server 开发团队

DB Server 开发团队

webapp 开发团队（Java 程序员开发）

### 协议

webapp 开发团队和 WEB Server 的开发团队之间有一套规范：JavaEE 规范之一 Servlet 规范

- Servlet 规范作用是 WebServer 和 webapp 解耦合

Browser 和 WebServer 之间有一套传输协议：HTTP 协议（超文本传输协议）

webapp 开发团队和 DB Server 开发团队之间有一套规范：JDBC规范

![BS架构系统的角色和协议](/images/image-javaweb/note01-01-BS架构系统的角色和协议.PNG)

对于 JavaWeb 程序员来说，只需要做两件事

1. 编写一个类实现 Servlet 接口
2. 将编写的类配置到配置文件中，在配置文件中指定请求路径和类名的关系

> 配置文件的文件名和存放路径遵守 Servlet 规范，不能随意更改