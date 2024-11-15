---
title: JavaWeb - Note02 Servlet & GenericServlet
date: 2022-07-26 16:13:52
categories: JavaWeb
tags: [Servlet, GenericServlet]
---

# Servlet

Servlet（Server Applet ），服务器端的 Java 小程序，JavaWeb 开发最核心的规范

## 合法的 webapp 目录结构

- WEB-INF
  - classes
  - lib
  - web.xml
- html
- css
- javascript
- image
- ...

## 第一个 Servlet webapp

步骤：

1. 在 webapps 目录下新建 `项目目录`

   > vv：不能包含子目录

2. 项目目录下新建一个 `WEB-INF` 目录

   Servlet 规范，目录内的资源受保护，在浏览器上不能通过路径直接访问

   > 像 HTML、CSS、JS、image 等静态资源需放在 WEB-INF 目录之外

3. WEB-INF 目录下新建一个 `classes` 目录

   Servlet 规范，存放 Java 程序编译后的 class 文件

4. WEB-INF 目录下新建一个 `lib` 目录

   *非必须，存放第三方 jar 包*

5. WEB-INF 目录下新建一个 `web.xml` 文件

   描述请求路径与 Servlet 类之间对照关系

   > 建议直接复制
   >
   > ```xml
   > <?xml version="1.0" encoding="UTF-8"?>
   > 
   > <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
   >   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   >   xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
   >                       https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
   >   version="5.0"
   >   metadata-complete="true">
   > 
   > </web-app>
   > 
   > ```

6. 编写一个 Java 程序

   必须实现 Servlet 接口，该接口不在 JDK 中，属于 JavaEE 中的类库

   Tomcat 实现了 Servlet 规范，所以拥有该接口

   > 关于 JavaEE 版本：
   >
   > JavaEE 目前最高版本是 JavaEE 8，被 Oracle 捐献给了 Apache，升级后更名为 JakartaEE 9
   >
   > JavaEE 8 对应的 Servlet 类名为：javax.servlet.Servlet
   >
   > JakartaEE 9 以后对应的 Servlet 类名为：**jakarta.servlet.Servler**

   ```java
   package io.github.wtaaaame.servlet;
   
   import jakarta.servlet.Servlet;
   import jakarta.servlet.ServletException;
   import jakarta.servlet.ServletRequest;
   import jakarta.servlet.ServletResponse;
   import jakarta.servlet.ServletConfig;
   import java.io.IOException;
   import java.io.PrintWriter;
   
   public class HelloServlet implements Servlet {
   	// 5个实现方法
   	public void init(ServletConfig config) throws ServletException {
   		
   	}
   
   	public void service(ServletRequest request, ServletResponse response)
   		throws ServletException, IOException {
   		// 向控制台打印输出
   		System.out.println("Hello Servlet");
   
   		// 设置流对象响应类型（普通文本或 html 代码）
   		// 在流对象之前设置有效
   		response.setContentType("text/html");
   		// 如果出现中文乱码，可以加上：
   		// response.setContentType("text/html; charset=UTF-8");
   
   		// 创建流对象，将信息直接输出到浏览器
   		// 使用 ServletResponse 接口：response
   		// 输出流，负责输出字符串到浏览器，不需要刷新和关闭，由 Tomcat 管理
   		PrintWriter out = response.getWriter();
   		out.println("Response Servlet");
   		// 未设置响应类型，无格式输出，需在开启流对象之前设置
   		out.println("<h1>This is a h1，测试乱码问题</h1>");
   	}
   
   	public void destroy() {}
   
   	public String getServletInfo() {
   		return "";
   	}
   
   	public ServletConfig getServletConfig() {
   		return null;
   	}
   }
   ```

7. 编译

   配置环境变量：`CLASSPATH=.;[servlet-aip].jar`

8. 将编译后的包放到 `tomcat/webapps/[project]/WEB-INF/classes` 中

9. 注册 Servlet 类

   在 web.xml 中编写配置信息，让“请求路径”和 ”Servlet 类名“关联

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   
   <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
                         https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
     version="5.0"
     metadata-complete="true">
   
   	<!-- Servlet 描述信息 -->
   	<!-- 任何一个 servlet 都对应一个 servlet-mappint -->
   	<servlet>
   		<servlet-name>vv</servlet-name>
   		<!-- 这个位置必须是带有包名的全限定类名-->
   		<servlet-class>io.github.wtaaaame.servlet.HelloServlet</servlet-class>
   	</servlet>
   
   	<!-- Servlet 映射信息 -->
   	<servlet-mapping>
   		<!-- 这里随便，但需要与上面的 name 相同 -->
   		<servlet-name>vv</servlet-name>
   		<!-- 
   			这里需要一个路径
   			唯一的要求就是以 / 开始
   			当前随便写
   		-->
   		<url-pattern>/vv/vv</url-pattern>
   	</servlet-mapping>
   
   </web-app>
   
   ```

10. 启动 Tomcat 服务器

11. 打开浏览器

    地址栏输入 URL：`http://localhost:8080/[project_name][url-pattern]`

    浏览器路径与 url-pattern 路径唯一区别是：浏览器路径带 /[project_name]

## 超链接

若浏览器上编写的路径太复杂，可以使用超链接（html 页面只能放到 WEB-INF 目录上一层）

```html
<!doctype html>
<html>
	<head>
		<title>Hello Servlet</title>
	</head>
	<body>
		<a href="/crm/vv/vv" />Hello Servlet
	</body>
</html>
```

访问时只需访问该 html 页面（`http://localhost:8080/crm/index.html`），通过页面中的超链接跳转

## Servlet 连接数据库

Servlet 是 Java 程序，所以在 Servlet 中可直接编写 JDBC 代码连接数据库

Servlet 连接数据库需新写一个 class，在 service() 方法中编写程序，并将数据库驱动 jar 包放到 webapp 中的 lib 下

## 在 IDEA 中开发

- 让 Module 变成 JavaEE 模块（符合 Servlet 规范的 Module）：

  `模块右键 -> Add Frameworks Support -> Java EE - Web Application`

  - 生成的 web 目录，就是 webapp 的根

- 配置 IEAD 的 classpath：

  将 `CATALINA_HOME\lib` 下的 `servlet-api.jar` 添加到 `Libraries` 中（`IDEA -> File -> Project Structure -> Project Settings - Libraries`）

  - 也可以在 `Project Settings - Modules - [Module] - Dependencies` 中点击 `+` 号单独添加 jar 包

- IEAD 工具关联 Tomcat 服务器

  关联的过程中将 webapp 部署到 Tomcat 服务器

  1. IDEA 工具右上角，Build 按钮右边，点击 `Add Configuration...`
  2. 点击左上角 `+` 号，找到 `Tomcat Server`，选择 `local`
  3. 配置 Tomcat 路径、服务器、JRE 等
  4. 当前窗口点击 `Deployment` 部署 webapp
     1. 点击 `+` 配置`Artifact`
     2. 修改 `Application context`，重命名为项目名
  5. 建议以 Debug 模式启动服务器

## Servlet 对象生命周期

Servlet 对象的创建、方法调用、销毁，JavaWeb 程序员无权干预

Servlet 对象的生命周期由 Tomcat 服务器（WEB Server）全权负责

Tomcat 服务器通常又称为：WEB 容器（WEB Container）

自己 new 的 Servlet 对象不受 Tomcat 服务器管理，WEB 服务器创建的 Servlet 对象都会被放到一个 HashMap 中，只有放在这个 HashMap 集合中的 Servlet 才能够被 Tomcat 管理

### 实例化时机

在 Servlet 中提供一个无参数的构造方法，启动服务器时看看构造方法是否执行？

默认情况下，服务器在启动时 Servlet 对象并不会被实例化

- 用户没有发送请求之前，如果提前创建出所有 Servlet 对象，必然要耗费大量内存

- 若想在服务器启动时创建对象，只需在 `web.xml` 中的 `servlet` 标签中加上：

  ```xml
  <load-on-startup>0</load-on-startup>
  ```

  - 中间整数决定创建顺序（从小到大）

### 生命周期

1. 默认用户发送第一次请求时，实例化 Servlet 对象
2. 对象被创建出来后，Tomcat 服务器马上调用对象 init 方法
3. 以后每次发起请求都只调用 service 方法，不新建对象
4. 关闭服务器时调用 destroy 方法

说明：

1. Servlet 对象是单实例的（假单例，并不符合单例模式）

   程序员无法管理 Servlet 对象，由于 Tomcat 只创建了一个，所以导致了单例，但是属于假单例，真单例构造方法是私有化的

2. 无参数构造方法、init 方法只在第一次用户发送请求时执行

3. 只要用户发送一次请求，service 方法必然被调用一次

4. Servlet 的 destroy 方法只被 Tomcat 服务器调用一次

5. destroy 方法属于实例方法，方法结束后才会释放

### 5个对象方法

```java
// 无参数构造方法
public [Servlet name]() {}

@Override
// init 方法通常完成初始化操作
// 在对象第一次被创建后执行
public void init(ServletConfig servletConfig) throws ServletException {
}

@Override
// 处理用户请求的核心方法
// 用户发送一次请求，则执行一次 service 方法
public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
}

@Override
// 只执行一次，Tomcat 服务器销毁对象之前调用
public void destroy() {

}

@Override
public String getServletInfo() {
    return null;
}

@Override
public ServletConfig getServletConfig() {
    return null;
}
```

- 当 Servlet 类中编写有参数构造方法，如果**没有手动编写无参数构造方法**会出现 `500 错误`（HTTP 协议的错误状态码，500一般属于服务器的 Java 程序出现了异常，服务器内部错误）

  > Servlet 开发中不建议定义构造方法，容易导致无参构造方法消失，可能无法实例化对象，所以 init 方法有存在的必要

## *GenericServlet*

> *注意：以后编写 Servlet 类，不会直接继承 GenericServlet，因为 B/S 架构系统基于 HTTP 超文本传输协议，在 Servlet 规范中提供了一个类叫做 `HttpServlet`，它是专门为 HTTP 协议准备的一个 Servlet 类（HTTP 协议专用）*
>
> > jakarta.servlet.Servlet（接口）
> >
> > jakarta.servlet.GenericServlet implement Servlet（接口的抽象类）
> >
> > jakarta.servlet.http.HttpServlet implement GenericServlet（抽象类的抽象类）

Servlet 中需要实现 Servlet 接口所有方法，包含可能用不上的方法，所以不够美观

可以利用适配器设计模式（Adapter），编写一个 GenericServlet 抽象类，类中实现 Servlet 接口所有方法，并把想要重写的方法定义为抽象方法，用以继承实现

- 子类依旧会调用父类 init 方法

### ServletConfig

ServletConfig 是一个接口，**Tomcat 先创建了 ServletConfig 对象，然后调用 init 方法**，将 ServletConfig 对象传给 init 方法

若 service 中想要使用 ServletConfig 对象，可以在 Servlet 类中创建一个私有的成员变量，然后在 init 方法中初始化赋值

若 GenericServlet 中重写的 init 方法不想让子类覆盖，可以使用 final 关键字，然后重载一个无参数的 init 方法调用，并让子类重写；与此同时，通过 getServletConfig 对象返回 config

示例代码如下：

```java
package io.github.wataaaame.generic;

import javax.servlet.*;
import java.io.IOException;

public abstract class GenericServlet implements Servlet {
    // 定义一个私有成员变量
    private ServletConfig servletConfig;

    @Override
    // 不想子类重写方法导致影响成员变量的赋值，可以使用 final 修饰
    public final void init(ServletConfig servletConfig) throws ServletException {
        // 成员变量赋值
        this.servletConfig = servletConfig;
        // 原方法调用重载的 init 方法
        init();
    }

    // 重载一个无参的 init 方法供子类重写，从而避免影响
    public void init() {

    }

    @Override
    public ServletConfig getServletConfig() {
//        return null;
        // 此处为成员变量的 get 方法，可返回成员变量
        return servletConfig;
    }

    @Override
    public abstract void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException;

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}

```

> GenericServlet 已有现成抽象类，可以直接继承

### 扩展：关联源码

IDEA 可以反编译 class 文件，可以在顶部提示框点击 `Choose Source` 选择源码文件，以展示注释信息

### ServletConfig 详解

ServletConfig 是 Servlet 对象的配置信息对象

Tomcat 解析 web.xml 文件，包装了文件中 `<servlet></servlet>` 标签的配置信息

Servlet 规范中的一员，是一个接口，由以下类实现

```java
public class org.apache.catalina.core.StandardWrapperFacade implements ServletConfig {}
```

- 若换作其他服务器，则包名、类名会不同，但都实现了 ServletConfig 接口

一个 Servlet 对象中有一个 ServletConfig 对象（一对一）

在 Servlet 对象创建的时候，同时创建了 ServletConfig 对象，然后传参到 init 方法

#### 方法

```xml
<servlet>
    <servlet-name>config</servlet-name>
    <servlet-class>io.github.wataaaame.config.ConfigTest</servlet-class>
    <!-- 这里可以配置一个 Servlet 对象的初始化信息 -->
    <!-- 局部配置信息时使用  -->
    <init-param>
        <param-name>driver</param-name>
        <param-value>com.mysql.jdbc.Driver</param-value>
    </init-param>
    <init-param>
        <param-name>url</param-name>
        <param-value>jdbc:mysql://localhost:3306/learning</param-value>
    </init-param>
    <init-param>
        <param-name>user</param-name>
        <param-value>root</param-value>
    </init-param>
    <init-param>
        <param-name>password</param-name>
        <param-value>0.0</param-value>
    </init-param>
</servlet>
```

通过 ServletConfig 对象的前两个方法，可以获取到 web.xml 中的初始化参数配置信息

1. Enumeration<java.lang.String> getInitParameterNames()

   获取所有初始化参数的 name

   hasMoreElements() 判断是否有下一个元素

   nextElement() 获取下一个元素

   ```java
   // 获取所有初始化参数名称
   Enumeration<String> parameterNames = getServletConfig().getInitParameterNames();
   // 遍历集合
   while (parameterNames.hasMoreElements()) {  // 是否有更多元素
       // 取出元素并打印
       out.print(parameterNames.nextElement() + "<br>");
   }
   ```

2. String getInitParameter(String name)

   获取 name 对应的 value

   ```java
   // 获取 name 对应的 value
   out.print(getServletConfig().getInitParameter("driver"));
   ```

3. ServletContext getServletContext()

   获取 ServletContext 对象

   ```java
   out.print(getServletContext() + "<br>");
   // org.apache.catalina.core.ApplicationContextFacade
   ```

4. String getServletName()

   获取 servlet 配置中的 servlet-name

   ```java
   out.print(getServletConfig().getServletName());	// config
   ```

> 以上方法可以通过 ServletConfig 调用，也可通过继承 GenericSerlvet 直接调用

### ServletContext 详解

Servlet 的环境（上下文）对象，是一个接口，是 Servlet 规范中的一员，**包含 web.xml 中所有配置信息**（属于公用配置）

- Tomcat 是一个容器，可以存放多个 webapp，**一个 webapp 对应一个 ServletContext 对象**

由 Tomcat 服务器实现：

```java
public class org.apache.catalina.core.ApplicationContextFacade implements ServletContext {}
```

在 WEB 服务器启动时创建，关闭时结束，属于**应用级对象**（全局配置）

> 全局配置信息使用 ServletContext 的 `<context-param>`
>
> 局部 servlet 配置信息使用 ServletConfig 的 `<init-param>`

ServletContext 对象还有另一个名字：**应用域**（以后还有请求域、会话域）

如果所有用户**共享一份数据**（对象只有一个，不共享没有意义），并且这个数据**很少被修改**（共享数据修改涉及线程安全问题，所以这里数据一般只读）、**数据量很少**（过大占用太多堆内存，且这个数据生命周期长，会影响服务器性能），则可以考虑放到 ServletContext 这个应用域中

#### 重要方法

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 此处配置上下文的初始化参数 -->
    <!-- 全局配置信息时使用 -->
    <context-param>
        <param-name>pageSize</param-name>
        <param-value>10</param-value>
    </context-param>
    <context-param>
        <param-name>startIndex</param-name>
        <param-value>0</param-value>
    </context-param>
    
    <servlet>
        <servlet-name>context01</servlet-name>
        <servlet-class>io.github.wataaaame.context.ServletContextTest01</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>context01</servlet-name>
        <url-pattern>/context01</url-pattern>
    </servlet-mapping>
</web-app>
```

1. String getInitParameter(String name)

   通过初始化参数的 name 获取 value

2. Enumeration<java.lang.String> getInitParameterNames()

   获取所有的初始化参数的 name

   hasMoreElements() 判断是否有下一个元素

   nextElement() 获取下一个元素

   ```java
   // 获取 ServletContext 上下文的初始化参数
   Enumeration<String> initParameterNames = application.getInitParameterNames();
   while (initParameterNames.hasMoreElements()) {
       String name = initParameterNames.nextElement();
       String value = application.getInitParameter(name);
       out.print(name + " " + value + "<br>");
   }
   ```

   > vv：一定需要调用 ServletContext 的1、2方法，否则调用的是 ServletConfig 中的 init-parameter

3. **String getContextPath()**

   获取应用地址的根路径

   ```java
   out.print(application.getContextPath() + "<br>");	// /context
   ```

4. **String getRealPath(String path)**

   获取 webapp 文件真实路径

   ```java
   // 获取文件真实路径
   out.print(application.getRealPath("/html.html") + "<br>");
   // C:\vv\code\Java\JavaWeb\out\artifacts\servlet03_war_exploded\html.html
   ```

   路径为 webapp 的根路径为起点

5. void log(String msg)

   记录日志，记录到 `CATALINA_HOME\ligs` 下

   ```java
   // 输出日志文件
   application.log("It's VV");
   // 29-Jul-2022 12:11:32.764 信息 [http-nio-8080-exec-7]
   // org.apache.catalina.core.ApplicationContext.log It's VV
   ```

   void log(String message, Throwable throwable)

   ```java
   // 输出日志文件（Throwable）
   int age = 12;
   if (age < 18) {
       application.log("未成年", new RuntimeException("FBI WARNING"));
   }
   // 29-Jul-2022 12:11:32.764 严重 [http-nio-8080-exec-7]
   // org.apache.catalina.core.ApplicationContext.log 未成年
   // java.lang.RuntimeException: FBI WARNING
   ```

   > 使用原始方法肯定会直接操作 `CATALINA_HOME`，而 IDEA 中只会生成到 IDEA 指定的目录，须在命令台找到存放路径

   Tomcat 日志文件：

   - localhost_access_log：服务器访问日志
   - catalina：服务器启动日志
   - localhost：方法记录的日志

#### 应用域方法

```java
// ServletContext 应用域
User u1 = new User("vv", 22);
User u2 = new User("ee", 21);
```

1. void setAttribute(String name, object value)

   存数据到 ServletContext 应用域

   ```java
   // 存
   application.setAttribute("user1", u1);
   application.setAttribute("user2", u2);
   ```

2. Object getAttribute(String name)

   从 ServletContext 应用域取数据

   ```java
   // 取
   out.print(application.getAttribute("user1") + "<br>");	// User{name='vv', age=22}
   ```

3. void removeAttribute(String name)

   从 ServletContext 应用域删数据

   ```java
   // 删
   application.removeAttribute("user1");
   ```

## 扩展：缓存机制

- 堆内存中的字符串缓存机制

  “abc” 先在字符串常量池中查找，如果有，直接拿来用；如果没有则新建，然后放入字符串常量池

- 堆内存中的整数型常量池

  [-128, 127] 一共256个 Integer 类型的引用，放在整数型常量池中，没有超出范围则直接从常量池中取

- 连接池（Connection Cache）

  这里说的连接池中的连接是 Java 语言连接数据库的连接对象：java.sql.Connection 对象

  JVM 与 MySQL 皆是进程，进程之间建立连接，打开通道很费资源

  可以提前创建好 n 个 Connection 连接对象，将连接对象放到一个集合中，我们把这个放有 Connection 对象的集合称为连接池。这样，每次用户连接不需要新建连接对象，直接从连接池中获取连接对象，大大提升访问效率

  连接池可以提高用户的访问效率，也可以保证数据库的安全性，实际开发中需考虑：

  - 最小连接数
  - 最大连接数

- 线程池

  Tomcat 服务器本身支持多线程

  Tomcat 服务器启动时，会先创建好 n 多个线程对象，然后将线程对象放到集合中，称为线程池

  用户发送请求后，需要有对应的线程来处理请求，此时线程对象就会直接从线程池中获取，效率较高

  - 所有的 WEB 服务器或应用服务器都有线程池机制

- Redis

  NoSQL 数据库，非关系型数据库、缓存数据库

- 向 ServletContext 应用域中存储数据，也等于是将数据存放到缓存 cache 中了