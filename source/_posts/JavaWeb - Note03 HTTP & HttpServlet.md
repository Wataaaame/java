---
title: JavaWeb - Note03 HTTP & HttpServlet
date: 2022-07-29 18:55:56
categories: JavaWeb
tags: [HTTP, HttpServlet]
---

# HTTP 协议

W3C 制定的一种**超文本传输协议**（通信协议，传输非普通文本，例如流媒体：声音、视频、图片等）

B（Browser）与 S（Server）之间收发数据都遵循此协议，互不依赖（解耦合）

HTTP 协议包括：请求（request）协议、响应（response）协议

## 请求协议

包括：请求行、请求头、空白行、请求体

get 请求报文：

```
// 请求行
GET /servlet04/getServlet?username=vv&password=123 HTTP/1.1
// 请求头
Host: localhost:8080
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: http://localhost:8080/servlet04/index.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
// 空白行

// 请求体

```

post 请求报文：

```
// 请求行
POST /servlet04/postServlet HTTP/1.1
// 请求头
Host: localhost:8080
Connection: keep-alive
Content-Length: 24
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://localhost:8080
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: http://localhost:8080/servlet04/index.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
// 空白行

// 请求体
username=ee&password=456
```

> F12，找到 network，通过这个页面可以查看协议的具体内容响应协议

> 请求行、请求头在 Header - Request Header 中
>
> 请求体在 Header - Form Data 中

### 请求行

包括三部分：

1. 请求方式（7种）

   - **get**

   - **post**
   - delete
   - put
   - head
   - options
   - trace

2. URI

   - URI：统一资源**标识符**，代表网络中某个资源的名称（无法定位）
   - URL：统一资源**定位符**，代表网络中某个资源，通过 URL 可以定位到该资源

   URL 包括 URI，e.g：

   - URL：`http://localhost:8080/servlet/index.html`
   - URI：`servlet/index.html`

3. 协议版本号

### 请求头

作为了解

1. 请求的主机
2. 主机的端口
3. 浏览器信息
4. 平台信息
5. cookie 信息
6. ...

### 空白行

用来区分请求头和请求体

### 请求体

向服务器发送的具体数据

## get & post

### 区别

- get

  1. 发送数据时，在请求行发送数据，发送的数据回显在地址栏

  2. 只能发送普通字符串，不同浏览器有不同长度限制（无明确规范）

  3. get 请求绝对安全，只是为了从服务器获取数据，不会对服务器造成威胁（get 请求不应用在敏感信息）

  4. 支持缓存

     任何一个 get 请求最终的 “响应结果” 都会被浏览器缓存起来

     在浏览器缓存当中，一个 get 请求对应一个资源

     > 实际上，只要发送 get 请求，浏览器就会先从本地缓存找，找不到才会去服务器获取。目的是提高用户体验

     若不想 get 请求走缓存，也就是希望每次都从服务器获取，可以在路径后加一个时间戳，这样资源名每时每刻都在改变，无法从本地缓存找到

- post

  1. 发送数据时，在请求体中发送，不会回显在浏览器地址栏

  2. 可以发送任何类型数据，包括：普通字符串、流媒体（视频、声音、图片...）等，可以发送大数据量，理论上无长度限制

  3. post 请求有危险，向服务器提交数据，数据可能有危险，所以一般情况下拦截数据，大部分都会监听 post 请求

  4. 不支持缓存

     目的是给服务器提交数据，返回的是处理结果，缓存无意义

> 二者发送请求的数据格式相同，只是位置不同
>
> 数据会挂在 URL 后面，且 URL 与数据之间会添加一个“？”
>
> http://[url]?name=value&name=value&name=value...
>
> 其中，以 form 表单为例：
>
> - name：表单中输入域控件的 name
> - value：表单中输入域控件的 value
>
> 所有的 value 皆是字符串

### 发送途径

- post：目前为止，form 表单中，method 设置为 post 则为 post

- get：除 post 以外，其他一律为 get

  如：输入 URL、超链接、form 表单、method 为 get

### 使用情形

- get：

  从服务器端获取数据

- post：

  向服务器端发送数据

  大部分 form 表单中填写大量数据

  form 表单中存在敏感信息

  做文件上传（非普通文本）

## 响应协议

包括：状态行、响应头、空白行、响应体

具体报文：

```
// 状态行
HTTP/1.1 200 ok
// 响应头
Content-Type: text/html;charset=ISO-8859-1
Content-Length: 94
Date: Fri, 29 Jul 2022 09:53:15 GMT
Keep-Alive: timeout=20
Connection: keep-alive
// 空白行

// 响应体
<!doctype html>
<html>
   <head>
       <title>get page</title>
   </head>
   <body>
       <h1>get page</h1>
   </body>
</html>
```

> 状态行、响应头在 Header - Response Header
>
> 响应体在 Response

> 若想响应体好看，可以使用 out.println()，此为源代码换行；网页展示换行需输出 `<br>` 标签

### *状态行

三部分组成：

1. 协议版本号

   HTTP/1.1

2. 状态码（HTTP 协议规定的状态号，对应着不同的响应结果）

   - 200 请求响应成功

   - 404 访问的资源不存在（前端错误）、路径错误或服务器资源没有启动成功
   - 405 前端发送的请求方式与后端请求处理方式不一致，例如：
     - 前端 post 请求，后端按 get 请求处理
     - 前端 get 请求，后端按 post 请求处理
   - 500 服务器端的程序出现异常（后端错误）

   > 4 开头一般为浏览器端错误
   >
   > 5 开头一般为服务器端错误

3. 状态描述信息

   - ok 表示正常成功结束
   - not found 表示资源找不到

### 响应头

作为了解

1. 响应内容类型
2. 响应内容长度
3. 响应时间
4. ...

### 空白行

分隔响应头和响应体

### 响应体

响应正文，是一个长的字符串，被浏览器渲染、解释并运行，最终展示效果

# 模板方法设计模式

在模板类的模板方法中定义核心算法骨架（可以使用 final 修饰，防止修改），具体的实现步骤可以延迟到子类当中完成（可以使用 abstract 关键字）

# HttpServlet

`jakarta.servlet.http.HttpServlet`

HttpServlet 类专门为 HTTP 协议准备，比 GenericServlet 更适合 HTTP 协议下的开发

## 接口

1. jakarta.servlet.http.HttpServlet

   HTTP 协议专用的 Servlet 抽象类

2. jakarta.servlet.http.HttpServletRequest

   HTTP 协议专用的请求对象（简称 request 对象）

   封装了请求协议的全部内容（WEB 服务器解析）

3. jakarta.servlet.http.HttpServletResponse

   HTTP 协议专用的响应对象

## 源码分析

HttpServlet 源码分析（按生命周期）

```java
// 1 创建对象
public class HelloServlet extends HttpServlet {
    // 用户第一次请求，创建对象时，会执行这个无参构造方法
    public HelloServlet {
    }
}

// 2 调用父类 init
public abstract class GenericServlet implements Servlet, ServletConfig, java.io.Serializable {
    // 用户第一次请求，第一次创建对象后，这个 init 方法会被执行
    public void init(ServletConfig config) throws ServletException {
        this.config = config;
        // 3 执行无参 init
        this.init();
    }
    
    // 3 第一次执行时，有参 init 中会执行无参 init
    public void inti() throws ServletException {
        // NULL by default
    }
}

// 4 调用 service 方法
public class HelloServlet extends HttpServlet {
    // 用户只要发送一次请求，service 方法就执行一次
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        HttpServletRequest request;
        HttpServletResponse response;
        try {
            // 将 ServletRequest 和 ServletResponse 向下转型为带有 Http 的 HttpservletRequest 和 HttpServletResponse
            request = (HttpServletRequest)req;
            response = (HttpServletResponse)res;
        } catch (ClassCastException var6) {
            throw new ServletException(lStrings.getString("http.non_http"));
        }

        // 4 调用重载的 service 方法
        this.service(request, response);
    }

    // 4 调用重载的 service 方法
    // 两个参数都带有 Http
    // 这个 service 是一个模板方法，在该方法中定义核心算法骨架，具体的实现步骤延迟到子类中去完成
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求方式（大写），可能是7种请求方式之一
        String method = req.getMethod();
        long lastModified;
        // 如果请求方式是 GET
        if (method.equals("GET")) {
            lastModified = this.getLastModified(req);
            if (lastModified == -1L) {
                // 执行 doGet
                this.doGet(req, resp);
            } else {
                long ifModifiedSince;
                try {
                    ifModifiedSince = req.getDateHeader("If-Modified-Since");
                } catch (IllegalArgumentException var9) {
                    ifModifiedSince = -1L;
                }

                if (ifModifiedSince < lastModified / 1000L * 1000L) {
                    this.maybeSetLastModified(resp, lastModified);
                    this.doGet(req, resp);
                } else {
                    resp.setStatus(304);
                }
            }
        } else if (method.equals("HEAD")) {
            lastModified = this.getLastModified(req);
            this.maybeSetLastModified(resp, lastModified);
            this.doHead(req, resp);
        } else if (method.equals("POST")) {	// 如果请求方式是 POST
            // 执行 doPost
            this.doPost(req, resp);
        } else if (method.equals("PUT")) {
            this.doPut(req, resp);
        } else if (method.equals("DELETE")) {
            this.doDelete(req, resp);
        } else if (method.equals("OPTIONS")) {
            this.doOptions(req, resp);
        } else if (method.equals("TRACE")) {
            this.doTrace(req, resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[]{method};
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(501, errMsg);
        }
    }
}
```

若直接继承 HttpServlet，直接重写 HttpServlet 中的 serice 方法可行，但享受不到 HTTP 协议专属服务（如错误代码提示）

> 未重写 doGet 等处理请求方法，则会执行父类 HttpServlet 中的 doGet 输出错误信息：
>
> ```java
> protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>     String msg = lStrings.getString("http.method_get_not_supported");
>     this.sendMethodNotAllowed(req, resp, msg);
> }
> ```
>
> 避免错误信息的方法：后端重写什么类型的请求，前端就发送什么类型的请求

## 最终开发步骤

1. 编写一个 Servlet 类，直接继承 HttpServlet
2. 重写 doGet/doPost 方法
3. 配置 web.xml 文件
4. 准备前端页面，form 表单中指定路径即可

# web 站点欢迎页面

对于一个 webapp，可以设置它的欢迎页面，当访问这个 webapp 或 访问这个 web 站点时，没有指定任何“资源路径”，这时会访问欢迎页面

之前访问的是：

```
http:localhost:8080/servlet/login.html
```

欢迎页面访问方式：

```
http:localhost:8080/servlet
```

## 设置欢迎页面

1. web 目录下新建一个文件 welcome.html

2. 在 web.xml 中配置 `<welcome-file-list>`

   ```xml
   <welcome-file-list>
       <!-- 不需要“/”，且从 webapp 的根目录开始 -->
       <welcome-file>welcome.html</welcome-file>
       <!-- 可以设置多个，优先级从上往下 -->
   </welcome-file-list>
   ```

3. 启动服务器，地址输入到项目名即可

欢迎页面名为 index 时，可自动访问不需要配置，是因为 Tomcat 已提前配置好

实际上配置欢迎页面有两个方法：

1. 在 webapp 内部的 web.xml 中（局部配置）

2. 在 CATALINA_HOME\conf\web.xml 中（全局配置）

   ```xml
   <welcom-file-list>
   	<welcom-file>index.html</welcom-file>
       <welcom-file>index.htm</welcom-file>
       <welcom-file>index.jsp</welcom-file>
   </welcom-file-list>
   ```

> 欢迎页还可以是一个 Servlet 动态页面，路径直接使用 url-pattern（去掉斜杠）

# *HttpServletRequest 接口详解

`jakarta.servlet.http.HttpServletReques`

该接口是 Servlet 规范中的一员，父接口是 ServletRequest

由 WEB 服务器实现该接口，封装了 **HTTP 请求协议**。用户发送请求时，遵循了 HTTP 协议，WEB 服务器将 HTTP 协议中的信息及数据全部解析出来，然后 WEB 服务器把这些信息封装到 HttpServletRequest 对象中，传递给 Java 程序员

一次请求对应一个 request 和 response

## 常用方法

**获取用户提交的数据**

不建议直接使用 Map 集合存储提交的数据，因为 Map 集合 key 重复则 value 覆盖

采用 `Map<String, String[]>` 的方式存储

1. **String getParameter(String name)**

   根据 Key 获取 value 一维数组中的第一个元素

   ```java
   System.out.println(req.getParameter("username"));	// vv
   System.out.println(req.getParameter("password"));	// 123
   ```

2. Map<String String[]> getParameterMap()

   获取 Map 集合

   ```java
   // 获取 req Map 集合
   Map<String, String[]> parameterMap = req.getParameterMap();
   // 获取 keySet
   Set<String> parameterKeys = parameterMap.keySet();
   for (String key : parameterKeys) {
       System.out.print(key + "=");
       // 通过 key 遍历数组
       for (String value : parameterMap.get(key)) {
           System.out.print(value + " ");
       }
       System.out.println();
   }
   /*
       username=vv 
       password=123 
       habit=sing guitar
   */
   ```

3. Enumeration<java.lang.String> getParameterNmes()

   获取 Map 集合中所有的 Key

   ```java
   // getParameterNames 获取所有的 key
   Enumeration<String> parameterNames = req.getParameterNames();
   while (parameterNames.hasMoreElements()) {
       System.out.print(parameterNames.nextElement() + " ");
   }
   /*
       username password habit
   */
   ```

4. String[] getParameterVlues(String name)

   根据 Key 获取 Map 集合的 value
   
   通常在复选框中使用
   
   ```java
   // getParameterNames 获取所有的 key
   Enumeration<String> parameterNames = req.getParameterNames();
   while (parameterNames.hasMoreElements()) {
       System.out.print(parameterNames.nextElement() + " ");
   }
   /*
       vv 123 sing guitar
   */
   ```
   

## 其他常用方法

1. String getRemoteAddr()

   获取客户端地址

   ```java
   System.out.println(req.getRemoteAdder());	// 127.0.0.1
   ```

2. void setCharacterEncoding(String env)

   设置请求体的字符集，一般用于解决 POST 乱码问题

   ```java
   req.setCharacterEncoding("UTF-8");
   ```

   > Tomcat 10 之后，request 请求体中默认为 UTF-8，不需要设置字符集，不会出现乱码问题
   >
   > Tomcat 9 之前（包括9），如果前端 post 方法提交中文，会出现乱码问题

   > 响应同样会有乱码问题，在 setContentType 中设置字符集
   >
   > ```java
   > resp.setContentType("text/html; charset=UTF-8");
   > ```

   > get 请求不会出现乱码，因为 `CATALINA_HOME\conf\server.xml` 中有个 Connector 标签，其中的 URIEncoding 属性用以配置字符集编码
   >
   > Tomcat 9 之后（包括）默认为 UTF-8 的格式（之前为 ISO-8859-1）

3. **String getContextPath()**

   获取应用根路径

   ```java
   String contextPath = request.getContextPath();
   // /servlet08
   ```

4. String getMethod()

   获取请求方式

5. String getRequestURI()

   获取请求 URI（带项目名）

6. String getServletPath()

   获取请求 URI（不带项目名）

# 请求域对象

请求域对象要比应用域对象范围小很多，生命周期也短很多

请求域只在一次请求内有效（HttpServletRequest request 对象）

请求域也有这三个方法：

```java
// 向域中绑定数据
void setAttribute(String name, Object obj);
// 从域中通过 name 取数据
Object getAttribute(String name);
// 将域中绑定的数据移除
void removeAttribute(String name);
```

与应用域选用原则：

- 尽量使用小的域对象，占用资源较少

## 跨请求域

使用 Servlet 中的转发机制，执行完 A 后跳转到 B，转发为一次跳转

请求转发器：

`RequestDispatcher getRequestDispatcher(String path)`

请求转发器方法：

1. void forward(ServvletRequest req, ServletResponse resp)

请求步骤：

```java
// 第一步：
// A 中获取请求转发器
// 将下一个跳转的资源告诉服务器
RequestDispatcher dispatcher = req.getRequestDispatcher("/b");
// 第二步：
// 调用请求转发器的 forward 方法传递 A 中 request，进行转发
dispatcher.forward(req, resp)
```

- 转发的下一步不一定是 Servlet，支持所有 Tomcat 中的合法资源

  转发路径以 “/” 开始，不加项目名

## 易混淆的两个方法

```java
String username = req.getParameter("username");
Object obj = req.getAttribute("name");
```

第一个方法：获取用户在浏览器上提交的数据

第二个方法：获取请求域中绑定的数据

# Project

[纯 Servlet 实现单表 CRUD（B/S 架构）](https://wataaaame.github.io/2022/08/03/Project%20-%20oa/)

## 资源跳转

在一个 web 应用中通过两种方式，可以完成资源的跳转

1. 转发
2. 重定向

区别：

- 代码上

  1. 转发

     将本 Servlet 中的 request 与 response 通过 foward 方法转发给下一个 Servlet

     ```java
     // 获取请求转发器对象
     RequestDispatcher dispatcher = request.getRequestDispatcher("dept/list");
     // 调用请求转发器对象的 forward 方法转发
     dispatcher.forward(request, response);
     
     // 合并一行代码
     request.getRequestDispatcher("dept/list").forward(request, response);
     ```

  2. 重定向

     调用 response 中的 sendRedirect 方法，将路径响应给浏览器，浏览器又自发地向服务器发送了一次全新的请求

     第一次请求：servlet/a

     第二次请求：servlet/b

     ```java
     // 需要以项目名开始
     response.sendRedirect(request.getContextPath() + "/b");
     ```

- 形式上

  1. 转发（一次请求）

     请求前与请求后浏览器地址栏不变

  2. 重定向（两次请求）

     请求后浏览器地址栏变成最后一次发送的请求

- 本质区别：

  1. 转发是由 WEB 服务器来控制

     ![转发原理](/images/image-javaweb/note03-01-转发原理.PNG)

  2. 重定向是浏览器完成的

     ![重定向原理](/images/image-javaweb/note03-02-重定向原理.PNG)

如何选择：

1. 如果在上一个 Servlet 中向 request 域中绑定了数据，希望从下一个 Servlet 中把 request 域中的数据取出时使用**转发**
2. 剩下所有的请求均使用**重定向**

> 保存转发存在刷新问题，若重复刷新，则导致反复提交；重定向刷新的是最后一次请求

## Servlet 注解

`jakarta.servlet.annotation.WebServlet`

分析 oa 项目中的 web.xml 文件

1. 配置信息过多，对于大项目体量而言不利
2. web.xml 中进行 Servlet 信息配置，开发效率低，每个都需要配置
3. 配置文件很少被修改，可以直接写到 Java 类中

> Servlet 3.0 之后，推出了各种基于注解式开发

### 开发优点

1. 效率高，不需要编写大量配置信息，直接在 Java 类上使用注解进行标注，简化配置
2. web.xml 文件体积变小

一般使用**注解+配置文件**的开发模式

不会经常变化修改的配置使用注解，可能修改的写到配置文件中

### 注解属性

1. name：指定 Servlet 名字，等同于 `<servlet-name>`

2. urlPatterns：指定映射路径，可以指定多个 String

   若只有一个路径，则大括号可以省略

3. value：同 urlPatterns

   若只有一个路径，则大括号可以省略

   如果注解名是 value，则属性名也可以忽略

   ```java
   @WebServlet("/wel")
   ```

   支持模糊匹配：

   ```java
   @WebServlet("/dept/*")
   ```

4. loadOnStartup：启动时是否自动加载，int 决定创建对象顺序

5. initParams：启动时初始化参数

> 注解对象使用格式：
>
> `@注解名(属性名=属性值, 属性名=属性值)`

示例代码：

```java
@WebServlet(
    name = "HellowServlet",
    urlPatterns = {"/hello1", "/hello2"},
    loadOnStartUp = 1,
    initParams = {
        @WebInitParam(name="username", value="vv"),
        @WebInitParam(name="password", value="123")
    },
)
public class HellowServlet extend HttpServlet {}
```

