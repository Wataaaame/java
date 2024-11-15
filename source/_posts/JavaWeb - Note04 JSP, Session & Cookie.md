---
title: JavaWeb - Note04 JSP, Session & Cookie
date: 2022-08-05 09:51:04
categories: JavaWeb
tags: [JSP, Session, Cookie]
---

# JSP

JSP（JavaServer Pages），基于 Java 语言实现的服务器端的页面，实际上就是一个 Java 程序、一个 Servlet，生命周期与 Servlet 完全相同，都是单例的（假单例）

> `index.jsp` 类继承 `HttpJspBase`，而 `HttpJspBase` 类继承的是 `HttpServlet`

- Servlet 是 JavaEE 的13个子规范之一，那么 JSP 也是 JavaEE 的13个子规范之一
- JSP 是一套规范，所有的 web 容器/服务器都遵守这套规范，按照规范进行“翻译”（它们都内置一个 “JSP 翻译引擎”）

> 程序员主要编写 Servlet 中的”前端代码“，让程序自动将之翻译为 Servlet Java 程序，然后机器再自动将 Java 程序编译成字节码文件，使用 JVM 调用 class 中的方法

## 第一个 JSP

1. 在 WEB-INF 目录之外创建一个 `index.jsp` 文件，不写任何内容

2. 将项目部署后，启动服务器，打开浏览器访问，页面空白

   浏览器访问以上的 `index.jsp`，底层执行的是 `index_jsp.class` 这个 Java 程序

   > 这个 jsp 会被 WEB 服务器翻译生成 `index_jsp.java` 文件，然后又会被编译生成 `index_jsp.class` 文件

- JSP 文件第一次访问比较慢，访问过程流程如下：
  1. 把 JSP 文件翻译成 Java 源文件
  2. Java 源文件编译生成 class 字节码文件
  3. 通过 class 创建 servlet 对象
  4. 调用 init 方法
  5. 调用 service 方法
- 第二次直接调用单例 servlet 对象的 service 方法即可

对 JSP 调试，还是要直接打开 JSP 文件对应的 Java 文件，检查 Java 代码

> 最高境界：眼前是 JSP 代码，闹钟是 Java 代码

## JSP 基础语法

1. 在 JSP 文件中**直接编写文字**

   文字会被翻译到：`Servlet类 -> service方法 -> out.write("here")`，被 Java 程序当作普通字符串打印输出到浏览器

   在 JSP 中编写 前端代码，这些代码对于 JSP 只是普通字符串，但是 JSP 将之输出到浏览器，浏览器就会解释执行，展示出效果

2. JSP 的**配置指令**

   解决响应时的中文乱码问题

   在 JSP 顶部编写如下代码：

   ```jsp
   <!-- 响应内容类型是 text/html，采用的字符集是 UTF-8 -->
   <%@page contentType="text/html; charset=UTF-8" %>
   ```

3. 编写 Java 程序

   使用**脚本块**：`<% [java code]; %>`向浏览器输出一个 Java 变量

   ```jsp
   <%
   	String name = "vv";
   	out.write("name = " + name);
   %>
   ```

   - 以上代码中的 out 是 JSP 九大内置对象之一，可以在 service 中直接使用

   - 普通字符串没必要使用该方法，可直接在 JSP 中编写

   - 如果输出的内容中含有 Java 代码（**动态输出**），这时需要使用以下语法格式：

     `<%= %>`

     ```jsp
     <%= 100 + 200 %>
     <%-- 300 -->
     ```

     翻译成了 `out.print();`

   - 适用于输出的内容含有 Java 变量、是一个动态的内容

   - 在这个符号中编写的被视为 Java 程序，被翻译到 Servlet 类的 service 方法内部
   - 方法内部只能写方法相应格式的语句，诸如静态代码块、方法、成员变量等皆不可定义在脚本块中
   - 编写的代码按自上而下的顺序执行

   *service **方法外**：`<%! %>`*

   - 此符号编写的 Java 程序会自动翻译到 service 方法外

     ```jsp
     <%!
         // 成员变量
         private String name = "vv";
     	// 静态代码块
     	static {
             System.out.println("静态代码块执行");
         }
     	// 方法
     	public static void m1() {
             System.out.println("m1 method execute!");
         }
     %>
     ```

     > 一般少用，
     >
     > JSP 就是 Servlet，是单例的，在 service 方法外写静态变量和实例变量，多线程并发的环境下，一旦有修改操作，必然存在线程安全问题

4. JSP 专用**注释**

   专用注释不会被翻译到源代码中

   ```jsp
   <%--
   	System.out.println("添加横杠即可");
   --%>
   ```

   > HTML 注释 `<!-- -->` 会被翻译

## JSP 与 Servlet 区别

职责不同

- Servlet：逻辑处理、业务处理、连接数据库、获取/收集数据
- JSP：展示数据

## 指令

指导 JSP 翻译引擎如何工作

语法：

```jsp
<%@指令名 属性名=属性值 属性名=属性值...%>
```

指令包括：

1. *include：包含指令，在 JSP 中完成静态包含*

2. taglib：引入标签库指令，JSTL 标签库中学习

3. page：页面指令，用来**定义 JSP 页面的全局属性**，该配置会作用于整个页面

   常用属性：

   1. session

      ```jsp
      <%@page session="[true|false]" %>
      ```

      true 表示一定启用 JSP 内置对象 session，没有 session 对象会创建（默认为 true）

      false 表示不启用内置对象 session，当前 JSP 页面中无法使用内置对象 session

   2. contentType

      contentType 属性用来设置响应的内容类型

      ```jsp
      <%@page contentType="text/html" %>
      ```

      > 其中可以添加响应的字符集：
      >
      > ```jsp
      > <%@page contentType="text/html; charset=UTF-8" %>
      > ```

   3. pageEncoding

      设置响应时采用的字符集

      ```jsp
      <%@page pageEndoding="UTF-8" %>
      ```

      > 可直接写在 contentType 中：
      >
      > ```jsp
      > <%@page contentType="text/html; charset=UTF-8" %>
      > ```

   4. import

      导入包文件

      ```jsp
      <%@page import="java.util.*" %>
      ```

   5. errorPage

      指定当前页面出错时跳转的位置

      ```jsp
      <%@page errorPage="/error.jsp" %>
      ```

   6. isErrorPage

      错误页面可以启用 JSP 九大内置对象中的 exception

      exception 内置对象就是刚刚发生的异常对象，默认值是 false

      ```jsp
      <%@page isErrorPage="true" %>
      ```

      使用方法：

      ```jsp
      <%exception.printStackTrace();%>
      ```

## 九大内置对象

四个域对象：

1. jakarta.servlet.jsp.PageContext pageContext

   页面作用域

2. jakarta.servlet.http.HttpServletRequest request

   请求作用域

3. jakarta.servlet.http.HttpSession session

   会话作用域

4. jakarta.servlet.ServletContext application

   应用作用域

- pageContext < request < session < application
- 以上四个作用域都有：setAttribute、getAttribute、removeAttribute 方法
- 使用原则：尽可能使用小的域

两个响应对象：

1. jakarta.servlet.jsp.jspWriter out

   负责输出

2. jakarta.servlet.http.HttpServletResponse response

   负责响应

其他三个对象：

1. java.lang.Throwable exception

2. jakarta.servlet.ServletConfig config

3. java.lang.Object page

   其实是 this，代表当前的 Servlet 对象

## EL 表达式

Expression Language（表达式语言），可以代替 JSP 中的 Java 代码，让 JSP 文件中的程序看起来更加整洁、美观

> JSP 中夹杂着各种 Java 代码，导致 JSP 文件混乱、不好维护，所以有了 EL 表达式

EL 表达式可以算是 JSP 语法的一部分，归属于 JSP，使用情景主要是：

1. 从某个作用域中**取数据**

   pageContext、request、session、application

2. 将其转换成字符串

   Java 对象也可调用 toString 方法转换为字符串

3. 输出到浏览器

   等同于 <%= %>

基本语法格式：`${表达式}`

```jsp
<%
	// 向 request 作用域当中存储数据
	request.setAttribute("username", "vv");
	// 向 request 作用域中存储对象
	request.setAttribute("userObj", user)
%>

<%-- 使用 Java --%>
<%=request.getAttribute("username")%>

<%-- 使用 EL 表达式 --%>
${username}

<%-- 自动调用对象的 toString 方法 --%>
${userObj}

<%-- 获取对象的私有属性 --%>
<%-- userObj.getUsername()，省略 get 和小括号 --%>
${userObj.username}
```

- EL 表达式中获取对象，与对象的属性变量无关，主要是获取 **get 方法后面的字段**，哪怕实际上无该属性名

  嵌套对象同样可省略 get 与小括号

- EL 表达式优先从小的域中取数据

  可以指定范围来读取数据

  `pageScope`、`requestScope`、`sessionScope`、`applicationScope`

  示例：

  ```jsp
  ${pageScope.date}
  ```

  > 实际开发中，向某个域中存储对象时，name 都是不同的，所以此语法一般无用

- EL 表达式有空值处理（即 null 显示为空白）

  ```jsp
  <%-- EL 表达式 --%>
  ${username}
  
  <%-- 等同于 --%>
  <%=request.getAttribute("username") == null ? "" : request.getAttribute("username")%>
  ```

- EL 表达式底层还是翻译成 Java 代码执行

- 若存储到域中有特殊符号，可以使用**中括号取出数据**（需添加双引号）

  ```jsp
  ${u1.username}
  <%-- 等同于 --%>
  ${u1.["username"]}
  ```

  - 若不加双引号，则找对应变量

    若加上双引号，则找对应属性

  - 适用于极端情况下的读取：

    ```jsp
    <%
    	request.setAttribute("abc.def", "Hello EL");
    %>
    
    <%-- 普通读取方式语法错误 --%>
    ${requestScope.abc.def}
    
    <%-- 使用中括号读取无误 --%>
    ${requestScope["abc.def"]}
    ```

- 取数据：

  1. Map 集合：`${map.key}`
  2. 数组：`${Array[0]}`
  3. List 集合：`${List[0]}`

- *忽略 EL 表达式*

  使用 page 指令忽略所有：`isELIgnored="true"`

  使用反斜杠忽略一条：`\${}`

> 面试题：
>
> ${abc} 与 ${"abc"} 的区别
>
> - 前者表示从某个域中取出数据，并且数据的 name 是 ”abc“
>
>   后者只是将 abc 当成普通字符串输出到浏览器

### 常用隐式对象

1. pageContext

   EL 表达式中没有 request 这个隐式对象

   requestScope 只代表“请求范围”，不等同于 request 对象

   通过 EL 中的隐式对象 **pageContext** 可以获取 request

   ```jsp
   <%=request%>
   
   <%-- 等同于 --%>
   <%=pageContext.getRequest%>
   
   <%-- 等同于 --%>
   ${pageContext.request}
   ```

   > EL 表达式与 JSP 中的 pageContext（九大对象）是同一个对象

   - 使用案例：

     获取 contextPath

     ```jsp
     <%-- pageContext.getRequest() 默认获取的是 ServletRequest，需要强转为 HttpServletRequest 调用 getContextPath --%>
     <%=((HttpServletRequest)pageContext.getRequest()).getContextPath()%>
     
     <%-- 等同于 --%>
     ${pageContext.request.contextPath}
     ```

2. param

   获取浏览器传递参数的第一个值

   ```jsp
   <%=request.getParameter("username")%>
   
   <%-- 等同于 --%>
   ${param.username}
   ```

3. paramValues

   返回所有值的一维数组

   ```jsp
   <%request.getParameterValues("hobit")%>
   
   <%-- 等同于 --%>
   ${paramValues.hobit}
   ```

4. initParam

   获取配置信息

   ServletContext 是 Servlet 上下文对象，对应 JSP 中九大内置对象的 application

   ```jsp
   <%=application.getInitParameter("pageSize");
   
   <%-- 等同于 --%>
   ${initParam.pageSize}
   ```

### *运算符*

1. 算数运算符

   `+ - * / %`

   ```jsp
   ${10 + 20}
   <%-- 30 --%>
   ```

   - 加号运算符只能做求和运算，不会进行字符串拼接操作

     ```jsp
     ${10 + "20"}
     <%-- 30 --%>
     ```

     无法转换成数字则报错

     ```jsp
     ${10 + "abc"}
     ${"abc" + "def"}
     <%-- NumberFormatException --%>
     ```

2. 关系运算符

   `== != > >= < <= eq`

   == 与 != 都调用了 equals 方法

   == 与 eq 作用相同

3. 逻辑运算符

   `! && || not and or`

4. 条件运算符

   `? :`

   可以配合 empty 使用

   ```jsp
   ${empty username ? "yes" : "no"}
   ```

5. 取值运算符

   `[] .`

6. empty 运算符

   运算结果为 boolean 类型

## JSTL 标签库

Java Standard Tag Lib（Java 标准标签库），通常结合 EL 表达式一起使用，目的是让 JSP 中的 Java 代码消失

标签中的变量默认存储到 pageContext 域

使用步骤：

1. 引入 JSTL 标签库对应的 jar 包

   > tomcat 10 之前的 jar 包是：
   >
   > - [taglibs-standard-impl-1.2.5.jar](https://dlcdn.apache.org/tomcat/taglibs/taglibs-standard-1.2.5/taglibs-standard-impl-1.2.5.jar)
   > - [taglibs-standard-spec-1.2.5.jar](https://dlcdn.apache.org/tomcat/taglibs/taglibs-standard-1.2.5/taglibs-standard-spec-1.2.5.jar)
   >
   > tomcat 10 之后（含）的 jar 包是：
   >
   > - jakarta.servlet.jsp.jstl-2.0.0.jar
   > - jakarta.servlet.jsp.jstl-api-2.0.0.jar

2. 在 JSP 中指定要使用的标签库

   JSTL 提供了很多种标签，使用 taglib 引入想要使用的标签

   ```jsp
   <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
   ```

   - prefix 为标识，名称随意，标签使用时的名称，核心标签库一般命名为 c

     uri 为标签地址，来自于引用的 jstl.jar 中 c.tld 中的 uri 地址，此处标签为核心标签库

     - url 路径实际上指向了一个 .tld 文件，这是一个 xml 配置文件，其中描述了**”标签“和 “Java 类”**之间的关系

       以上核心标签库对应了 c.tld 文件，位于 `jakarta.servlet.jsp.jstl-2.0.0.jar -> META-INF/c.tld`

     - tld 解析

       ```jsp
       <tag>
       	<description>标签描述</description>
           <name>catch</name>	标签名
           <tag-class>org.apache.taaglibs.standard.tag.common.core.CatchTag</tag-class>	标签对应的 Java 类
           <body-content>JSP</body-content>	标签体中支持的语法
           <attribute>	属性
           	<description>属性描述</description>
               <name>var</name>	属性名
               <required>false</required>	属性是否为必须值
               <rtexprvalue>false</rtexprvalue>	属性值是否支持 EL 表达式
           </attribute>
       </tag>
       ```

3. 使用标签

   语法：`<[prefix]:[tag] [name="value"]>body</c:[tag]>`

   表面使用的是标签，底层还是 Java 程序

   e.g. 遍历 Student List 中的学生 id 和 学生 name，request 域中有一个 stuList

   ```jsp
   <c:forEach items="${stuList}" var="s">
   	id: ${s.id}, name: ${s.name}<br>
   </c:forEach>
   ```

   - items 为遍历的集合，支持 EL 表达式
   - var 为当前的迭代项

### 常用标签

1. if

   test 为 boolean 类型，支持 EL 表达式

   ```jsp
   <c:if test="${empty param.username}">Sorry</c:if>
   <c:if test="${not empty param.username}">Welcome</c:if>
   
   <%--
       if 标签中还有：
           var：声明一个 boolean变量，保存 test 属性值
           scope：将 var 的变量存储到指定域中
               page（pageContext 域）
               request（request 域）
               session（session 域）
               application（application 域）
   --%>
   <c:if test="empty param.username" var="v" scope="request">Welcome</c:if>
   ```

2. forEach

   - fori 循环

     var：指定循环中的变量

     begin：开始

     end：结束

     step：步长

     ```jsp
     <c:forEach var="i" begin="1" end="4" step="1">
         <%-- i 默认存储到 pageContext 域中才得以被 EL 表达式读取 --%>
         ${i}<br>	<%-- 1 2 3 4 --%>
     </c:forEach>
     ```

   - foreach 循环

     items：循环容器

     var：循环项

     ```jsp
     <c:forEach items="${students}" var="s">
         id: ${s.id}, name: ${s.name}<br>
     </c:forEach>
     ```

   **varStatus 属性**：表示 var 状态对象，代表了 var 状态

   1. 名称随意

   2. 状态对象有 count 属性

      从1开始，以1递增，主要用于**项目序号**

      ```
      <c:forEach items="${students}" var="s" varStatus="stuStatus">
          no: ${stuStatus.count}, id: ${s.id}, name: ${s.name}<br>
      </c:forEach>
      ```

   3. choose, weh, otherwise
   
      类似于 if, else if 或 switch 语句
   
      ```jsp
      <c:choose>
      	<c:when test="${param.age < 18}">child</c:when>
          <c:when test="${param.age < 35}">adult</c:when>
          <c:when test="${param.age < 55}">middle</c:when>
          <c:otherwise>elder</c:otherwise>
      </c:choose>
      ```

## Filter 过滤器

每一个 Servlet 处理各自相关的业务，在执行前可能都需要判断用户的登录状态，而判断的代码是固定的；包括解决中文乱码的公共代码，也是重复编写，显然没有达到复用。此时可以使用 Filter 解决该问题

Filter 是过滤器，属于 Servlet 规范中的一员，可以在目标 Servlet 之前或之后添加代码，以实现过滤规则

一般情况下都是在过滤器中编写公共代码

![Filter原理](/images/image-javaweb/note04-01-Filter原理.PNG)

- 过滤器的调用遵循栈数据结构

> Filter 与 Servlet 对比：
>
> 相同：
>
> - Filter 与 Servlet 生命周期同
>
> - Servlet 与 Filter 皆为单例
>
> 不同：
>
> - Filter 优先级高于 Servlet
>
> - Servlet 对象默认在服务器启动时不会新建对象
>
>   Filter 对象默认在服务器启动时会新建对象
>
> - Filter 偏向于 web.xml 中配置
>
>   Servlet 偏向于注解配置

### 实现步骤

1. 编写一个 Java 类实现一个接口：`jakarta.servlet.Filter`，并且实现这个类中所有方法

   1. init()：在 Filter 对象第一次被创建之后调用，只调用一次
   2. doFilter()：此方法中编写过滤规则，只要用户发送一次请求，则执行一次
      - 传递来的是 HttpServletRequest 与 HttpServletResponse，在 Filter 中使用需做强制类型转换
   3. destroy()：在 Filter 对象被释放/销毁之前调用，只调用一次

2. 对 Filter 进行配置（类似于 Servlet）

   - 在 web.xml 文件中配置：

     ```xml
     <filter>
     	<filter-name>myFilter</filter-name>
         <filter-class>io.github.wataaaame.javaweb.serlet.MyFilter</filter-class>
     </filter>
     <filter-mapping>
     	<filter-name>myFilter</filter-name>
         <url-pattern>*.do</url-pattern>
     </filter-mapping>
     ```

     > `<filter.mapping>` 标签位置越靠上，优先级越高

   - 使用注解

     1. 精确匹配

        ```java
        @WebFilter("/a.do")
        ```

     2. 后缀匹配（模糊匹配中的扩展匹配）

        ```java
        @WebFilter("*.do")
        ```

     3. 前缀匹配

        ```java
        @WebFilter("/dept/*")
        ```

     4. 匹配全部

        ```java
        @WebFilter("/*")
        ```

     > Filter 类名在字典中越靠前，优先级越高

3. 方法中添加以下代码以**跳转到下一个过滤器**，若不是过滤器则**跳转到下一个 Servlet**

   ```java
   filterChain.doFilter(request, response);
   ```

### 责任链设计模式

过滤器最大的优点：在程序编译阶段不会确定调用顺序，是通过 web.xml 配置文件调整，于程序的运行阶段动态组合

## Listener 监听器

Listener 是 Servlet 规范中的一员，是一个特殊时机启动的代码

- 所有的监听器接口都是以 Listener 结尾
- 所有的监听器都是由服务负责调用

### Servlet 规范提供的监听器

`Jakarta.servlet` 包下：

1. ServletContextListener

   application 域的创建与销毁时触发

   1. 编写一个类实现 ServletContextListener 接口，并实现其中的方法

      ```java
      // 对象被创建时调用
      @Override
      public void contextInitialized(ServletContextEvent sce);
      
      // 对象被销毁时调用
      @Override
      public void contextDestroyed(ServletContextEvent sce);
      ```

   2. 在 web.xml 文件中对 ServletContextListener 进行如下配置

      ```xml
      <listener>
      	<listener-class>io.github.wataaaame.javaweb.listener.MyServletContextListener</listener-class>
      </listener>
      ```

      也可以使用注解

      ```java
      @WebListener
      ```

2. ServletContextAttributeListener

3. ServletRequestListener

4. ServletRequestAttributeListener

`jakarta.servlet.http` 包下：

1. HttpSessionListener

2. HttpSessionAttributeListener

   监听 **Session 域中数据的变化**

   - 需要进行 xml 文件配置或注解标注

   ```java
   @WebListener
   public class MyHttpSessionAttributeListener implements HttpSessionAttributeListener {
       // 向 session 中存储数据时
       @Override
       public void attributeAdded(HttpSessionBindingEvent se) {}
       
       // 将 session 中数据删除时
       @Override
       public void attributeRemoved(HttpSessionBindingEvent se) {}
       
       // session 中某个数据替换时
       @Override
       public void attributeReplaced(HttpSessionBindingEvent se) {}
   }
   ```

3. HttpSessionBindingListener

   实现该接口的**特定对象**绑定到 Session 时触发

   - 一般为个体类对象的监听（如 JavaBean）
   - 不需要 xml 文件配置或注解标注

   ```java
   // 绑定数据时
   @Override
   public void valueBound(HttpSessionBindingEvent event) {}
   
   // 解绑数据时
   @Override
   public void valueUnbound(HttpSessionBindingEvent event) {}
   ```

   - 通过 event.getSession().getServletContext() 获取 application 对象

4. *HttpSessionIdListenner*

   检测 Session id 状态

5. *HttpSessionActivationListener*

   监听 Session 对象的钝化和活化

   - 钝化：Session 对象从内存存储到硬盘文件
   - 活化：从硬盘文件把 Session 恢复到内存

### Example

统计网站实时在线人数

- 服务器通过浏览器的 Session 对象判断用户登录状态，一个 Session 代表一个用户，通过 HttpSessionListener 监听器获取 Session 对象的个数

统计登录的用户的在线数量

- 通过 HttpSessionBindingListener，统计名为 “name” 的 Session 对象个数

  ```java
  @Override
  public void valueBound(HttpSessionBindingEvent event) {
      // 用户登录
      // 获取 ServletContext
      ServletContext application = event.getSession().getServletContext();
      // 获取在线人数变量
      Integer onlineCount = (Integer)application.getAttribute("onlineCount");
      // 第一个人登录时，onlineCount 为空，需手动指定为1
      if (onlineCount == null) {
          application.setAttribute("onlineCount", 1);
      } else {
          // 否则在线人数加一，并存储到域中
          application.setAttribute("onlineCount", ++onlineCount);
      }
  }
  
  @Override
  public void valueUnbound(HttpSessionBindingEvent event) {
      // 用户退出
      // 获取 application 域
      ServletContext application = event.getSession().getServletContext();
      // 获取在线用户人数
      Integer onlineCount = (Integer)application.getAttribute("onlineCount");
      // 在线人数减1后存储到 application 域
      application.setAttribute("onlineCount", --onlineCount);
  }
  ```

# Session

`jakarta.servlet.http.HttpSession`

B/S 架构系统的会话机制，从浏览器打开到关闭的整个过程叫做**一次会话**，最主要的作用是**保存会话状态**

session 机制属于 B/S 结构的一部分，属于一种规范，其他语言同样实现了该机制

> 在浏览器中点击一个页面，到页面加载完毕，可以粗略的认为是**一次请求**（request），一次会话对应 n 次请求

> 域对象：
>
> - request（HttpServletRequest）：请求域
> - session（HttpSession）：会话域
> - application（ServletContext）：应用域
>
> **request < session < application**
>
> 都有以下三个公共方法：
>
> 1. setAttribute
> 2. getAttribute
> 3. removeAttribute
>
> 尽量使用小的域

HTTP 协议是一种无状态协议，向服务器请求时，B 和 S 是连接的；请求之后连接中断，且服务器无从得知

如此可以降低服务器压力，因而需要判断用户登录信息则使用 session 机制

## Session 语法

### 对象获取

通过 `request.getSession()` 获取当前 session 对象

```java
// 若未获取到，则新建
HttpSession session = request.getSession();
// 若未获取到，返回 null
HttpSession session = request.getSession(false);
```

session 对象同样拥有 setAttribute/getAttribute 方法

### 配置超时时间

可以通过 web.xml 配置文件配置超时时间

```xml
<session-config>
	<session-timeout>30</session-timeout>
</session-config>
```

- session 的默认超时时长是 30 分钟

> JSP 启动时默认有创建 Session 对象，可以通过以下配置禁用：
>
> ```jsp
> <%@page session="false" %>
> ```

> Session 在 JSP 中可以直接使用（九大内置对象之一）

### 对象销毁

通过 `session.invalidate()` 销毁 Session 对象

## 实现原理

WEB 服务器中有一个 session 列表，类似于 Map 集合

Map 集合的 key 存储的是 SESSIONID；Map 集合的 value 存储的是对应的 session 对象

用户发送第一次请求时：服务器创建新的 session 对象，同时该对象生成一个 id。WEB 服务器会将 session 的 id 发送给浏览器，浏览器将之放入缓存中

用户发送第二次请求时：浏览器自动将缓存中的 SESSIONID 发送给服务器，服务器将获取到的 SESSIONID 检索并找出对应的 session 对象

- 缓存中的 SESSIONID=XXXX 是以 Cookie 的形式保存在浏览器内存中，浏览器一旦关闭，Cookie 即销毁

- 浏览器设置中可以禁用 Cookie，服务器正常发送 Cookie 给浏览器，但是浏览器拒收，如此每次都会新建一个 Session，直到服务器 Session 超时

- Cookie 禁用后 Session 机制依然能够实现，使用 URL 重写机制访问：

  ```url
  http://localhost:8080/servlet;sessionid=xxxx
  ```

  由于此番举措导致任何请求路径都需要加上 sessionid，提高了开发者的成本，所以禁用了 Cookie 直接不予优化

而 Session 的销毁有两种方式：

1. 超时销毁

   服务器无法监测到浏览器关闭，若浏览器超时，则服务器关闭 该浏览器 session

2. 手动销毁

   用户点击安全退出，服务器立即销毁该 session

# Cookie

HTTP 协议是一种无状态、无连接的协议，需要会话机制保存状态

Cookie 是将会话状态保存在**浏览器客户端**上

Session 是将会话状态保存在**服务器端**上

> Cookie 与 Session 机制都是为了保存会话状态

Session 的实现原理中，每一个 Session 对象都会关联一个 sessionid，例如：

`JSESSIONID=4C8483BE4C8782582D06B8420D209528`

以上这个键值对数据其实就是 Cookie 对象

对于 Session 关联的 Cookie 来说，这个 Cookie 是被保存在浏览器的**运行内存**中

只要浏览器不关闭，用户再次发送请求时，会自动将内存中的 Cookie 发送给服务器

服务器就是根据这个 Cookie 找到 Session 对象

> Cookie 与 Session 属于 HTTP 协议的一部分，并非 Java 中的机制

## 存储位置

Cookie 最终存储在浏览器客户端上

- 运行内存（随浏览器关闭而消失）
- 硬盘文件（永久保存）

## 经典案例

在未登录的情况下，向购物车存放商品，重启浏览器后商品还在？

- 将购物车中的商品编号放到 Cookie 中，Cookie 保存在硬盘文件中，即使关闭浏览器，硬盘中的 Cookie 依旧保存

  下一次再打开商城时，从硬盘中的 Cookie 文件中读取商品编号，并展示到浏览器

  > 硬盘中的 Cookie 若被清除则失效（缓存中的 Cookie 随浏览器关闭而失效）

十天内免登录

- 用户输入了正确的用户名和密码，并且选中了十天免登录，登录成功后，浏览器会在硬盘中保存一个 Cookie，其中存储了用户名和密码等信息，十天有效
- 十天内用户再次访问时，浏览器自动提交关联的 Cookie 给服务器，服务器接收到 Cookie 之后，获取用户名和密码并验证，通过后，自动登录

## 组成

HTTP 协议规定：任何一个 Cookie 都是由 name 和 value 组成的，name 和 value 都是 String 类型

当浏览器发送请求时，会自动携带该 URL 请求路径下的 Cookie 数据给服务器

## 常用方法

Java 的 Servlet 中提供了一个 Cookie 类专门表示 Cookie 数据

`jakarta.servlet.http.Cookie`

1. Cookie(String name, String value)

   Cookie 的构造方法，需要传递一个键值对

   > Cookie 没有无参构造

2. setMaxAge(int expiry)

   设置 Cookie **失效时间**，秒为单位

   - 没有设置有效时间，则默认保存在浏览器运行内存中，随浏览器关闭而消失

   - 有效时间大于0，表示将 Cookie 存储到硬盘文件

     有效时间等于0，表示该 Cookie 被删除，主要应用在删除浏览器上的同名 Cookie

     有效时间小于0，表示该 Cookie 不会被存储到硬盘文件中，与不调用该方法没有区别

3. setPath(String uri)

   设置 Cookie 请求路径

   - 关联路径

     假设生成 Cookie 发送的请求路径是 `http://localhost:8080/servlet/cookie/generate`

     若 Cookie 没有设置 path，默认的 path 是 `http://localhost:8080/servlet/cookie`，以及该路径的子路径

     以后的请求路径若是默认 path 或其子路径，Cookie 都会被自动携带到浏览器

收发 Cookie：

1. void response.addCookie(Cookie cookie)

   Java 程序将 Cookie 数据发送给浏览器

2. Cookie[] request.getCookie()

   Java 程序获取浏览器携带的 Cookie

   - 返回值是一个数组，因为浏览器可能包含多个 Cookie

   - 若浏览器没有提交 Cookie，则返回 null

   ```java
   Cookie[] cookies = request.getCookie();
   if (cookies != null) {
       for (Cookie cookie : cookies) {
           // 获取 Cookie 的 name
           String name = cookie.getName();
           // 获取 Cookie 的 value
           String value = cookie.getValue();
       }
   }
   ```

## 删除

1. 获取浏览器的 Cookie
2. 遍历寻找想要销毁的 Cookie
3. cookie.setMaxAge 设置失效
4. cookie.setPath 保持与原 Cookie 路径一致
5. response.addCookie 告知浏览器该 Cookie 已失效

```java
// 获取浏览器传递的 Cookie
Cookie[] cookies = request.getCookies();
// 遍历寻找用户名与密码 Cookie
if (cookies != null) {
    for (Cookie cookie : cookies) {
        if ("username".equals(cookie.getName()) || "password".equals(cookie.getName())) {
            // 令 Cookie 失效
            cookie.setMaxAge(0);
            // 设置相同路径
            cookie.setPath(request.getContextPath());
            // 告知浏览器
            response.addCookie(cookie);
        }
    }
}
```

