---
title: SSM - Note04 SpringMVC
date: 2022-09-01 09:45:08
categories: SSM
tags: SpringMVC
---

# SpringMVC

基于 MVC 开发模式的框架，用来优化控制器，是 Spring 家族的一员，也具备 IOC 与 AOP

特点：

1. 轻量级，基于 MVN 框架
2. 易于上手、容易理解、功能强大
3. 具备 IOC 与 AOP
4. 完全基于注解开发

![SpringMVC执行流程](/images/image-ssm/note04-01-SpringMVC执行流程.PNG)

## MVC

一种开发模式，是模型视图控制器的简称，所有的 web 应用都是基于 MVC 开发

- M：模型层，包含实体类、业务逻辑层、数据访问层
- V：视图层，如 html、javascript、vue 等，用来显现数据
- C：控制器，用来接收客户端请求，返回响应到客户端的组件，如 Servlet

![SSM框架优化方向](/images/image-ssm/note04-02-SSM框架优化方向.PNG)

# 注解式开发

1. 新建项目，选择 webapp 模板

2. 修改目录，添加缺失的 test、java、resources（两套），并修改目录属性

3. 修改 pom.xml 文件，添加 SpringMVC 依赖，添加 Servlet 依赖

   ```xml
   <!-- SpringMVC 依赖 -->
   <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-webmvc</artifactId>
     <version>5.2.5.RELEASE</version>
   </dependency>
   <!-- Servlet 依赖 -->
   <dependency>
     <groupId>javax.servlet</groupId>
     <artifactId>javax.servlet-api</artifactId>
     <version>3.1.0</version>
   </dependency>
   ```

4. 添加 springmvc.xml 配置文件，指定包扫描，添加视图解析器

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!-- 添加包扫描 -->
       <context:component-scan base-package="github.wataaaame.controller"></context:component-scan>
       <!-- 添加视图解析器 -->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!-- 配置前缀 -->
           <property name="prefix" value="/admin/"></property>
           <!-- 配置后缀 -->
           <property name="suffix" value=".jsp"></property>
       </bean>
   </beans>
   ```

5. Project Structure 中，点击项目中的 Web，删除旧的 web.xml，点击应用，新建 web.xml

   否则 jsp 中 EL 表达式失效

6. 在 web.xml 文件中注册 springmvc 框架**（所有 web 请求都是基于 Servlet 的，开发控制器只是一个普通的类）**

   于是 web 请求执行流程通过**核心处理器**完成流转

   `index.jsp <--> DispatcherServlet <--> SpringMVC（普通方法）`

   - DispatcherServlet 要在 web.xml 文件中注册才可用

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <!-- 注册 SpringMVC 框架 -->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!-- 初始化 springmvc.xml 到配置文件 -->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc.xml</param-value>
           </init-param>
       </servlet>
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>*.action</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

7. webapp 目录新建 `admin - main.jsp`，删除并添加新 index.jsp 页面，发送请求给服务器

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>index</title>
   </head>
   <body>
       <h1>This is index.jsp</h1>
       <a href="${pageContext.request.contextPath}/demo.action"></a>
   </body>
   </html>
   ```

8. 开发控制器（Servlet）

   ```java
   package github.wataaaame.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   public class DemoAction {
       /**
        * 以前的 Servlet 规范
        * protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {}
        *
        * action 中所有的功能实现都是由方法来完成的
        * action 方法规范：
        *      1. 访问权限是 public
        *      2. 方法返回值任意
        *      3. 方法名称任意
        *      4. 方法可以没有参数，如果有可以是任意类型
        *      5. 要使用 @RequestMapping 注解来声明一个访问的路径（名称）
        */
       @RequestMapping("/demo")
       public String demo() {
           System.out.println("服务器被访问");
           return "main";  // 可以直接跳转到 /admin/main.jsp 上
       }
   }
   ```

   - .action 供 DispatcherServlet 核心处理器解析

9. 添加 tomcat 进行功能测试

## @RequestMapping 详解

此注解用来映射服务器访问路径

1. 可加在方法上，为此方法注册一个可以访问的名称（路径）

2. 可加在类上，提供一个虚拟路径，以**区分不同类中的相同路径**，如：@RequestMapping("/war/demo")

3. 区分 GET 请求和 POST 请求

   ```java
   package github.wataaaame.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   
   @Controller
   public class ReqAction {
       @RequestMapping(value = "/req", method = RequestMethod.GET)
       public String req() {
           System.out.println("I'm do for GET");
           return "main";
       }
       @RequestMapping(value = "/req", method = RequestMethod.POST)
       public String reqp() {
           System.out.println("I'm do for POST");
           return "main";
       }
   }
   ```

## 五种数据提交方式

![SpringMVC优化内容](/images/image-ssm/note04-03-SpringMVC优化内容.PNG)

1. 散提交数据

   只需形参类型与形参名同提交的一致即可

   jsp

   ```jsp
   <h1>单个数据提交</h1>
   <form action="${pageContext.request.contextPath}/one.action" method="post">
       name: <input type="text" name="name">
       <br>
       age: <input type="text" name="age">
       <br>
       <input type="submit">
   </form>
   ```

   Controller

   ```java
   @Controller
   public class DataSubmitAction {
       @RequestMapping("/one")
       public String one(String name, int age) {
           System.out.println("name="+name+", age="+(age+100));
           return "main";
       }
   }
   ```
   
2. 对象封装提交数据

   在提交请求中，保证请求参数的名称与实体类中成员变量的名称一致，则可以自动创建并提交数据，自动类型转换，自动封装数据到对象中

   ```java
   @RequestMapping("/two")
   public String two(User user) {
       System.out.println(user);
       return "main";
   }
   ```

3. 动态占位符提交

   仅限于超链接或地址栏提交数据，`.action` 前，一杠一值，用一杠一大括号取（内填 name），使用 `@PathVariable` 注解解析（支持解析成不同名字）

   ```java
   // <h1>3. 动态占位符提交</h1>
   // <a href="${pageContext.request.contextPath}/three/vv/12.action">go with vv/12</a>
   
   @RequestMapping("/three/{name}/{uage}")
   public String three(
           @PathVariable("name")
           String name,
           @PathVariable("uage")
           int age) {
       System.out.println(name + " " + age);
       return "main";
   }
   ```

4. **映射名称不一致**

   提交请求参数与 action 方法的形参名不一致，使用注解 @RequestParam 来解析

   ```java
   @RequestMapping("/four")
   public String four(
           @RequestParam("name")
           String uname,
           @RequestParam("age")
           int uage
   ) {
       System.out.println("name = " + uname + ", " + "age = " + uage);
       return "main";
   }
   ```

5. 手工提取数据

   ```java
   @RequestMapping("/five")
   public String five(HttpServletRequest request) {
       String name = request.getParameter("name");
       int age = Integer.parseInt(request.getParameter("age"));
       System.out.println(name + " " + age);
   
       return "main";
   }
   ```

## 请求参数中文乱码

配置过滤器，最好放在所有过滤器之前

```xml
<filter>
    <filter-name>encode</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encode</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 处理方法的返回值

1. String：返回客户端资源地址（自动拼接前缀后缀），支持屏蔽自动拼接，从而返回指定路径
2. Object：返回 Json 格式对象，使用 Jackson 工具进行转换（需添加依赖），自动将对象或集合转为 Json，一般用于 AJAX 请求
3. void：无返回值，一般用于 AJAX 请求
4. 8 种基本数据类型：用于 AJAX 请求
5. *ModelAndView*：返回数据和视图对象

### AJAX 请求示例

完成 AJAX 请求访问服务器，返回学生集合：

1. 添加 Jackson 依赖

   ```xml
   <!-- 添加 Jackson 依赖 -->
   <dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
     <version>2.9.8</version>
   </dependency>
   ```

2. springmvc.xml 配置文件

   ```xml
   <!-- 添加包扫描 -->
   <context:component-scan base-package="github.wataaaame.controller"></context:component-scan>
   <!-- 不用添加视图解析器，处理的 AJAX 直接原路返回 -->
   <!-- 必须添加注解驱动，专门用来处理 AJAX 请求 -->
   <mvc:annotation-driven></mvc:annotation-driven>
   ```

   - `<mvc:annotationdriven/>`，用来解析 @ResponseBody 注解

3. 在 webapp 目录下新建 js 目录，添加 jQuery 函数库

4. 在 index.jsp 页面导入函数库

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>AJAX</title>
       <script src="js/jquery-3.6.0.js"></script>
   </head>
   <body>
   <script type="text/javascript">
       function showStu() {
           // 使用 jQuery 封装 AJAX 请求
           $.ajax({
               url: "${pageContext.request.contextPath}/list.action",
               type: "GET",
               dataType: "json",
               success: function (stuList) {
                   var html = "";
                   $.each(stuList, function (i, stu) {
                       html += stu.name + ", " + stu.age + "<br>";
                   })
                   // 回显数据
                   $("#mydiv").html(html);
               }
           })
       }
   </script>
   
   <a href="javascript:showStu()">访问服务器返回学生集合</a><br>
   <div id="mydiv">等待服务器返回数据</div>
   </body>
   </html>
   ```

5. 在 action 上添加注解 @ResponseBody，用来处理 AJAX 请求

   若本类中全都是 AJAX 请求，则类使用 @RestController 注解，方法的 @ResponseBody 可不写
   
   ```java
   package github.wataaaame.controller;
   
   import github.wataaaame.bean.Student;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.ResponseBody;
   
   import java.util.ArrayList;
   import java.util.List;
   
   @Controller
   public class StuListAction {
       @RequestMapping("/list")
       @ResponseBody   // 解析 AJAX 请求，必须在 springmvc.xml 中添加注册驱动
       public List<Student> list() {
           List<Student> stuList = new ArrayList<>();
   
           stuList.add(new Student("vv", 22));
           stuList.add(new Student("ee", 19));
   
           return stuList;
       }
   }
   ```
   
   返回一个集合，SpringMVC 会自动调用 Jackson 转换为 json 格式

## 四种跳转方式

index.jsp

```jsp
<%--
  Created by IntelliJ IDEA.
  User: vv
  Date: 2022/9/1
  Time: 18:17
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<a href="${pageContext.request.contextPath}/one.action">1. 请求转发页面（默认）</a>
<br><br>
<a href="${pageContext.request.contextPath}/two.action">2. 请求转发 action</a>
<br><br>
<a href="${pageContext.request.contextPath}/three.action">3. 重定向页面</a>
<br><br>
<a href="${pageContext.request.contextPath}/four.action">4. 重定向 action</a>
<br><br>
<a href="${pageContext.request.contextPath}/five.action">5. 跳到其他目录</a>
</body>
</html>
```

JumpAction.java

```java
package github.wataaaame.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class JumpAction {
    @RequestMapping("/other")
    public String other() {
        System.out.println("other go");
        return "main";
    }

    @RequestMapping("/one")
    public String one() {
        System.out.println("one go");
        // 默认是请求转发到页面，使用视图解析器拼接
        return "main";
    }

    @RequestMapping("/two")
    public String two() {
        System.out.println("two go");
        // forward 屏蔽前缀后缀拼接，实现请求转发跳转
        return "forward:/other.action";
    }

    @RequestMapping("/three")
    public String three() {
        System.out.println("three go");
        // redirect 屏蔽前缀后缀拼接，实现重定向跳转
        return "redirect:/admin/main.jsp";
    }

    @RequestMapping("/four")
    public String four() {
        System.out.println("four go");
        // redirect 屏蔽前缀后缀拼接，实现重定向跳转
        return "redirect:/other.action";
    }

    @RequestMapping("/five")
    public String five() {
        System.out.println("five go");
        // redirect 屏蔽前缀后缀拼接，实现重定向跳转
        return "redirect:/other/other.jsp";
    }
}
```

## 默认支持的参数类型

不需要创建，直接添加使用

1. HttpServletRequest
2. HttpServletResponse
3. HttpSession
4. Model
5. Map
6. ModelMap

注意：

- Map、Model、ModelMap 同 Request，使用请求作用域进行数据传递，所以服务器端的跳转必须是请求转发；Session 作用域不受此影响
- Response 不能传递数据

index.jsp

```jsp
<a href="${pageContext.request.contextPath}/data.action?name=ee">访问服务器，进行数据携带跳转</a>
```

DataAction.jsp

```java
package github.wataaaame.controller;

import github.wataaaame.bean.User;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.util.Map;

@Controller
public class DataAction {
    @RequestMapping("/data")
    public String data(
            HttpServletRequest request,
            HttpServletResponse response,
            HttpSession session,
            Model model,
            Map map,
            ModelMap modelMap
    ) {
        // 做一个 User 数据，传递到 main.jsp
        User user = new User("vv", 12);

        request.setAttribute("requestUser", user);
        session.setAttribute("sessionUser", user);
        model.addAttribute("modelUser", user);
        map.put("mapUser", user);
        modelMap.addAttribute("modelMapUser", user);

        return ("main");
    }
}
```

main.jsp

```jsp
<%--
  Created by IntelliJ IDEA.
  User: vv
  Date: 2022/9/1
  Time: 18:47
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>main</title>
</head>
<body>
<h1>main.jsp</h1>

<%--
    request.setAttribute("requestUser", user);
    session.setAttribute("sessionUser", user);
    model.addAttribute("modelUser", user);
    map.put("mapUser", user);
    modelMap.addAttribute("modelMapUser", user);
--%>
request: ${requestUser}<br>
session: ${sessionUser}<br>
model: ${modelUser}<br>
map: ${mapUser}<br>
modelMap: ${modelMapUser}<br>
<%-- 使用 param. 的方式取出携带数据 --%>
从 index.jsp 来的数据：${param.name}<br>

<button onclick="window.history.back()">back</button>
</body>
</html>
```

## 日期处理

### 日期的提交处理

日期无法在函数的参数中直接提取，需要特殊处理

1. 单个日期处理

   在方法参数或 set 方法上使用注解 @DataTimeFormat，此注解必须搭配 springmvc.xml 中的 `<mvc:annotationdriver>` 标签（AJAX 请求同样需要）

   index.jsp

   ```jsp
   <%-- 处理日期 --%>
   <form action="${pageContext.request.contextPath}/date.action">
       <input type="date" name="date">
       <input type="submit">
   </form>
   ```

   DateAction.java

   ```java
   package github.wataaaame.controller;
   
   import org.springframework.format.annotation.DateTimeFormat;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   import java.text.SimpleDateFormat;
   import java.util.Date;
   
   @Controller
   public class DateAction {
       SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   
       @RequestMapping("/date")
       public String date(
               @DateTimeFormat(pattern = "yyyy-MM-dd")
               Date date
       ) {
           System.out.println(sdf.format(date));
           return "date";
       }
   }
   ```

2. 类中全局日期处理

   注册一个注解，用来解析本类中所有日期类型，自动转换

   - 注册全局日期注解后无需注册驱动标签

   DateAction.java

   ```java
   // 注册一个全局的日期处理注解
   @InitBinder
   public void initBinder(WebDataBinder webDataBinder) {
       webDataBinder.registerCustomEditor(Date.class, new CustomDateEditor(sdf, true));
   }
   @RequestMapping("/date")
   public String date(Date date) {
       System.out.println(date);
       System.out.println(sdf.format(date));
       return "date";
   }
   ```

### 日期的显示处理

1. 单个日期对象转换

   直接格式化为字符串进行传递显示

   ```java
   // 单个日期传递
   request.setAttribute("date", sdf.format(date));
   ```

2. List 中实体类对象成员变量的日期转换

   添加 JSTL 依赖

   ```xml
   <!-- JSTL 依赖 -->
   <dependency>
     <groupId>jstl</groupId>
     <artifactId>jstl</artifactId>
     <version>1.2</version>
   </dependency>
   ```

   使用 JSTL 标签

   ```jsp
   <%--
     Created by IntelliJ IDEA.
     User: vv
     Date: 2022/9/2
     Time: 17:11
     To change this template use File | Settings | File Templates.
   --%>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%-- 导入 JSTL 核心标签库 --%>
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   <%-- 导入 JSTL 格式化标签库 --%>
   <%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
   
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <h2>学生集合</h2>
   <table width="800px" border="1px">
       <tr>
           <th>name</th>
           <th>birthday</th>
       </tr>
       <c:forEach items="${stuList}" var="stu">
           <tr>
               <td>${stu.name}</td>
               <td>${stu.birthday}-----<fmt:formatDate value="${stu.birthday}" pattern="yyyy-MM-dd"></fmt:formatDate></td>
           </tr>
       </c:forEach>
   </table>
   </body>
   </html>
   ```

返回为 Json 需要在实体类中的成员变量 get 方法上添加注解

```java
@JsonFormat(pattern="yyyy-MM-dd HH:mm:ss")
public Date getDate() {
    return date;
}
```

## mvc:annotation-driven 标签

该标签会自动注册两个 bean，分别为：`DefaultAnnotationHandlerMapping` 和 `AnnotationMethodHandleerAdapter`，是 springmvc 为 @controller 分发请求所必需的，除了注册 bean，还提供很多支持：

1. *支持使用 ConversionService 实例对表单参数进行类型转换*
2. 支持使用 @NumberFormat、@DateTimeFormat
3. 注解完成数据类型的格式化
4. 支持使用 @RequestBody 和 ResponseBody 注解
5. *支持使用静态资源的分流*

## 资源在 WEB-INF 下

此目录下的动态资源，不可直接访问，只能通过请求转发的方式进行访问

springmvc.xml

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
```

WebinfAction.java

```java
@Controller
public class WebinfAction {
    @RequestMapping("/showMain")
    public String showMain() {
        System.out.println("showMain Action go!");

        return ("main");
    }
}
```

## 去掉后缀 action

注册 SpringMVC 框架时修改 url-pattern 标签内容为 `/`

```xml
<!-- 注册 Servlet 框架 -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <!--        <url-pattern>*.action</url-pattern>-->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

如此则 WEB-INF 下的文件也能被访问，需要设置拦截器以提高安全性

# 拦截器

针对请求和响应进行的额外处理，在请求和响应的过程中添加预处理，后处理和最终处理

## 执行时机

1. preHandle()：预处理，请求被处理之前进行操作

   返回布尔类型，用于标识是否继续执行

2. postHandle()：请求被处理之后，但结果还没有渲染之前进行操作，可改变响应结果

3. afterCompletion：最终处理，所有请求响应结束后执行善后工作，清理对象，关闭资源

## 两种实现方式

1. 继承 HandlerInterceptorAdapter 的父类
2. **实现 HandlerInterceptor 接口**

以第二种方式为例，实现步骤如下：

1. 改造登录方法，在 Session 中存储用户信息，用于进行登录验证

2. 开发拦截器的功能，实现 HandlerInterceptor 接口，重写 preHandle() 方法

   ```java
   package com.github.wataaaame.crm.workbench.web.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   public class WorkbenchIndexController {
       @RequestMapping("/workbench/index")
       public String index() { return "workbench/index"; }
   }
   ```

3. 在 springmvc.xml 文件中注册拦截器

   注册示例

   ```xml
   <!-- 配置拦截器 -->
   <mvc:interceptors>
       <mvc:interceptor>
           <!-- 配置拦截请求 -->
           <mvc:mapping path="/settings/**"/>
           <mvc:mapping path="/workbench/**"/>
           <!-- 配置拦截白名单 -->
           <mvc:exclude-mapping path="/settings/qx/user/toLogin"/>
           <mvc:exclude-mapping path="/settings/qx/user/login"/>
           <!-- 配置拦截器 bean -->
           <bean class="com.github.wataaaame.crm.settings.web.interceptor.LoginInterceptor"></bean>
       </mvc:interceptor>
   </mvc:interceptors>
   ```

# SSM 整合

使用 SSM + Vue 整合一个用户表页面

## 后端 SSM

1. 建库建表

   ```mysql
   create database if not exists ssm default character set utf8;
   use ssm;
   
   drop table if exists ssmuser;
   create table ssmuser (
   	user_id varchar(255) not null,
       card_type varchar(255) default null,
       card_no varchar(255) default null,
       user_name varchar(255) default null,
       user_sex varchar(255) default null,
       user_age varchar(255) default null,
       user_role varchar(255) default null,
       primary key(user_id) using btree
   ) engine = innodb character set = utf8;
   
   insert into ssmuser values('101', '身份证', '350201198701146613', '窦民佑', '男', '18', '办事人员和有关人员');
   insert into ssmuser values('102', '护照', 'A32532643', '青海霞', '女', '32', '不便分类的其他从业人员');
   insert into ssmuser values('103', '身份证', '156323198701146613', '王中言', '男', '53', '农、林、牧、渔、水利业生产人员');
   insert into ssmuser values('104', '身份证', '974432198701146613', '方天岚', '男', '48', '未知');
   insert into ssmuser values('105', '军官证', '军34829043', '钱好问', '男', '33', '不便分类的其他从业人员');
   insert into ssmuser values('106', '身份证', '239834198701146613', '肯迪亚', '女', '23', '未知');
   ```

2. 新建 Maven 项目，选择 webapp 模板

3. 修改目录

4. [添加配置文件（模板）](https://wataaaame.github.io/2022/09/04/Project%20-%20Configuration/#SSM)

   - pom.xml

   - jdbc.properties

   - sqlMapConfig.xml

   - applicationContext.xml（mapper、service）

   - springmvc.xml
   - web.xml

5. 实体类

   可以在数据库可视化中，右键选中的表名，点击 `Scripted Extensions -> Generate POJOs.Groovy`，选择目录后自动生成 bean/pojo 文件，可通过 `Alt + Ctrl + L` 修改一下缩进

6. Mapper 层

7. Service 层

8. 测试

   Spring 接管 junit 单元测试

   ```java
   @RunWith(SpringJUnit4ClassRunner.class) // 启动 Spring 容器
   @ContextConfiguration(locations = {"classpath:applicationContext_mapper.xml", "classpath:applicationContext_service.xml"})
   public class MyTest {
       @Autowired
       UserService userService;
       
       @Test
       ...
   }
   ```

9. Controller 层

10. 浏览器测试

## 前端 Vue

Element 框架实现页面

1. 涉及跨域访问，服务器端 Controller 类上添加 ＠CrossOrigin 字段以支持跨域访问

2. 后端端口改为 8082（也可更改前端）

3. [Element UI 框架帮助文档](https://element.eleme.cn/#/zh-CN/component/installation)

4. 安装 nodejs

   使当前计算机使用 vue 框架

   `node-v`：查看 node 版本

   `npm -v`：查看 npm 版本

5. 使用命令行进入到要运行的 **vue 项目目录下**，运行以下命令进行项目构建：

   - `npm i element -ui -S`

     下载 Element UI 框架

   - `npm install`

     打包项目

   - `npm install --save vue-axios`

     下载跨域访问组件 axios
   
6. IDEA 中的 `File -> Settings -> Plugins` 添加 Vue 插件，重启 IDEA

7. 启动服务器，新窗口中打开提供的前端项目

8. 点击 `Add Configuration...`，添加 npm

   `Node interpreter` ：node.exe 安装路径

   `Scripts`（package.json 中规定的当前项目启动方式）：选择 dev

9. 运行即可

### 项目分析

config/index.js

```js
// Paths
assetsSubDirectory: 'static',
assetsPublicPath: '/',
proxyTable: devEnv.OPEN_PROXY === false ? {} : {
    '/api': {
        // 后端服务器地址端口
        target: 'http://localhost:8082',
        changeOrigin: true,
        // 项目中以 /api 开头，统一替换为 / 供服务器识别
        pathRewrite: {
            '^/api': '/'
        }
    }
},
    // Various Dev Server settings
        host: 'localhost', // can be overwritten by process.env.HOST
        // 前端端口号，可修改为 8082 与后端替换
        port: 8080, // can be overwritten by process.env.PORT, if port is in use, a free one will be determined
        autoOpenBrowser: false,
        errorOverlay: true,
        notifyOnErrors: true,
        poll: false, // https://webpack.js.org/configuration/dev-server/#devserver-watchoptions-
```

test/package.json

```json
"scripts": {
    // 项目启动方式
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "unit": "jest --config test/unit/jest.conf.js --coverage",
    "e2e": "node test/e2e/runner.js",
    "test": "npm run unit && npm run e2e",
    "lint": "eslint --ext .js,.vue src test/unit test/e2e/specs",
    "build": "node build/build.js"
},
// 项目依赖使用的版本号
"dependencies": {
    "axios": "^0.21.1",
    "element": "^0.1.4",
    "element-ui": "^2.15.2",
    "vue": "^2.5.2",
    "vue-axios": "^3.4.1",
    "vue-router": "^3.5.2"
},
```

### src

```
src
|___ assets					// 静态资源
|___ componeents			// 组件
	 |___ HelloWorld.vue
	 |___ UserHome.vue
|___ router					// 路由，进行跳转
|___ App.vue				// 入口组件
|___ main.js				// 入口的 JS 文件
```

main.js

```js
// 使用 ElementUI
Vue.use(ElementUI)
// 跨域组件
Vue.prototype.$axios = axios
// 自动转换 JSON
Vue.prototype.qs = qs
/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```

App.vue

```vue
<template>
  <div id="app">
    <router-view/>
     <!-- 路由跳转 -->
     <router-link to="/user">UserHome</router-link> |
  </div>
</template>
```

UserHome.vue

```vue
<template>
  <div>
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item>
        <el-input v-model="formInline.search1" size="mini" placeholder="输入姓名查询" v-on:input="handleSearch()">
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-select size="mini" v-model="formInline.search2" v-on:change="handleSearch()">
          <el-option label="请选择性别" value=""></el-option>
          <el-option label="男" value="男"></el-option>
          <el-option label="女" value="女"></el-option>
        </el-select>
      </el-form-item>
       <el-form-item>
        <el-button icon="el-icon-circle-plus-outline" type="text" @click="search = true">查询</el-button>
      </el-form-item>
      <el-form-item>
        <el-button icon="el-icon-circle-plus-outline" type="text" @click="dialogAdd = true">添加</el-button>
      </el-form-item>
      <el-form-item>
        <el-button icon="el-icon-delete" type="text" @click="handleDeleteList()">删除</el-button>
      </el-form-item>
    </el-form>

    <el-table
      ref="multipleTable"
      :data="tableData"
      border
      highlight-current-row
      style="width: 100%"
      @selection-change="handleSelectionDelete">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column label="用户编号">
        <template slot-scope="scope">
          <span>{{ scope.row.userId }}</span>
        </template>
      </el-table-column>
      <el-table-column label="证件类型" prop="cardType"></el-table-column>
      <el-table-column label="证件号码" prop="cardNo"></el-table-column>
      <el-table-column label="用户姓名">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="right">
            <p>证件类型: {{ scope.row.cardType }}</p>
            <p>证件号码: {{ scope.row.cardNo }}</p>
            <p>用户姓名：{{ scope.row.userName }}</p>
            <p>用户性别: {{ scope.row.userSex }}</p>
            <p>用户年龄: {{ scope.row.userAge }}</p>
            <p>用户角色：{{ scope.row.userRole }}</p>
            <div slot="reference" class="name-wrapper">
              <el-button type="text">{{ scope.row.userName }}</el-button>
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column label="用户性别" prop="userSex"></el-table-column>
      <el-table-column label="用户年龄" prop="userAge"></el-table-column>
      <el-table-column label="用户角色" prop="userRole"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" icon="el-icon-edit" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button type="text" icon="el-icon-delete" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
 <el-dialog title="添加" :append-to-body='true' :visible.sync="dialogAdd" :before-close="handleClose">
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="80px" class="demo-ruleForm" size="medium">
        <el-form-item label="证件类型">
          <el-select v-model="ruleForm.cardType" placeholder="请选择证件类型" prop="cardType">
            <el-option label="身份证" value="身份证"></el-option>
            <el-option label="军官证" value="军官证"></el-option>
            <el-option label="护照" value="护照"></el-option>
            <el-option label="港澳居民通行证" value="港澳居民通行证"></el-option>
            <el-option label="台湾往来大陆通行证" value="台湾往来大陆通行证"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="证件号码">
          <el-input v-model="ruleForm.cardNo"></el-input>
        </el-form-item>
        <el-form-item label="用户姓名">
          <el-input v-model="ruleForm.userName"></el-input>
        </el-form-item>
        <el-form-item label="用户性别">
          <el-radio-group v-model="ruleForm.userSex">
            <el-radio label="男"></el-radio>
            <el-radio label="女"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="用户年龄">
          <el-slider v-model="ruleForm.userAge" show-input></el-slider>
        </el-form-item>
        <el-form-item label="用户角色">
          <el-select v-model="ruleForm.userRole" placeholder="请选择用户角色" prop="userRole">
            <el-option label="国家机关、党群组织、企业、事业单位负责人" value="国家机关、党群组织、企业、事业单位负责人"></el-option>
            <el-option label="专业技术人员" value="专业技术人员"></el-option>
            <el-option label="办事人员和有关人员" value="办事人员和有关人员"></el-option>
            <el-option label="商业、服务业人员" value="商业、服务业人员"></el-option>
            <el-option label="农、林、牧、渔、水利业生产人员" value="农、林、牧、渔、水利业生产人员"></el-option>
            <el-option label="生产、运输设备操作人员及有关人员" value="生产、运输设备操作人员及有关人员"></el-option>
            <el-option label="军人" value="军人"></el-option>
            <el-option label="不便分类的其他从业人员" value="不便分类的其他从业人员"></el-option>
            <el-option label="未知" value="未知"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
        <span slot="footer" class="dialog-footer">       <el-button @click="emptyUserData()" size="medium">取 消</el-button>      <el-button @click="addUser('ruleForm')" type="primary" size="medium">确 定</el-button>
</span>
   </el-dialog>

    <el-dialog title="编辑" :append-to-body='true' :visible.sync="dialogUpdate" :before-close="handleClose">
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="70px" class="demo-ruleForm" size="medium">
        <el-form-item label="证件类型">
          <el-select v-model="ruleForm.cardType" placeholder="请选择证件类型" prop="cardType">
            <el-option label="身份证" value="身份证"></el-option>
            <el-option label="军官证" value="军官证"></el-option>
            <el-option label="护照" value="护照"></el-option>
            <el-option label="港澳居民通行证" value="港澳居民通行证"></el-option>
            <el-option label="台湾往来大陆通行证" value="台湾往来大陆通行证"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="证件号码">
          <el-input v-model="ruleForm.cardNo"></el-input>
        </el-form-item>
        <el-form-item label="用户姓名">
          <el-input v-model="ruleForm.userName"></el-input>
        </el-form-item>
        <el-form-item label="用户性别">
          <el-radio-group v-model="ruleForm.userSex">
            <el-radio label="男"></el-radio>
            <el-radio label="女"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="用户年龄">
          <el-slider v-model="ruleForm.userAge" show-input></el-slider>
        </el-form-item>
        <el-form-item label="用户角色">
          <el-select v-model="ruleForm.userRole" placeholder="请选择用户角色" prop="userRole">
            <el-option label="国家机关、党群组织、企业、事业单位负责人" value="国家机关、党群组织、企业、事业单位负责人"></el-option>
            <el-option label="专业技术人员" value="专业技术人员"></el-option>
            <el-option label="办事人员和有关人员" value="办事人员和有关人员"></el-option>
            <el-option label="商业、服务业人员" value="商业、服务业人员"></el-option>
            <el-option label="农、林、牧、渔、水利业生产人员" value="农、林、牧、渔、水利业生产人员"></el-option>
            <el-option label="生产、运输设备操作人员及有关人员" value="生产、运输设备操作人员及有关人员"></el-option>
            <el-option label="军人" value="军人"></el-option>
            <el-option label="不便分类的其他从业人员" value="不便分类的其他从业人员"></el-option>
            <el-option label="未知" value="未知"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
        <span slot="footer" class="dialog-footer"><el-button @click="emptyUserData()" size="medium">取 消</el-button>        <el-button @click="updateUser()" type="primary" size="medium">确 定</el-button>
    </span>
    </el-dialog>

    <br>

    <el-pagination
      background
      :disabled="disablePage"
      :current-page.sync="currentPage"
      small
      layout="prev, pager, next"
      :page-size="pageSize"
      :total="total"
      @current-change="handlePageChange">
    </el-pagination>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'UserHome',
  data() {
    return {
      ruleForm: {
        userId: null,//用户id
        cardType: null,//证件类型
        cardNo: null,//证件号码
        userName: null,//用户姓名
        userSex: null,//用户性别
        userAge: 25,//用户年龄
        userRole: null//用户角色
      },
      rules: {},
      tableData: [],
      formInline:{
        search1: '',
        search2: ''
      },
      dialogAdd: false,
      dialogUpdate: false,
      pageSize: 5,
      currentPage: 1,
      total: 0,
      disablePage: false,
      multipleSelection: []
    };
  },
  // 钩子函数，在页面打开时自动调用
  created() {
    this.handlePageChange();
    this.getRowCount();
  },
  
  methods: {
    /**
     * 分页
     */
    handlePageChange() {
      // 调用 stringify 进行数据转 json 对象
      let postData=this.qs.stringify({
        page:this.currentPage,
        userName:this.formInline.search1,
        userSex:this.formInline.search2
      });
      // AJAX 跨域访问请求
      this.$axios({
        method:'post',
        url:'/api/user/selectUserPage',
        data:postData
      }).then(response=>{
        this.tableData=response.data;
      }).catch(error=>{
        console.log(error);
      })
    },

    /**
     * 统计用户个数
     */
    getRowCount() {
      let postData=this.qs.stringify({
        userName:this.formInline.search1,
        userSex:this.formInline.search2
      });
      this.$axios({
        method:'post',
        url:'/api/user/getRowCount',
        data:postData
      }).then(response=>{
        this.total=response.data;
      }).catch(error=>{
        console.log(error);
      })
    },

    /**
     * 添加用户
     */
    addUser() {
      if (this.ruleForm.cardType == null || this.ruleForm.cardNo == null || this.ruleForm.userName == null || this.ruleForm.userSex == null || this.ruleForm.userRole == null) {
        this.$alert('用户信息不完整请检查', '温馨提示', {
          confirmButtonText: '确定'
        });
        return;
      }
      let postData = this.qs.stringify({
        cardType: this.ruleForm.cardType,//证件类型
        cardNo: this.ruleForm.cardNo,//证件号码
        userName: this.ruleForm.userName,//用户姓名
        userSex: this.ruleForm.userSex,//用户性别
        userAge: this.ruleForm.userAge,//用户年龄
        userRole: this.ruleForm.userRole,//用户角色
      });
      this.$axios({
        method:'post',
        url:'/api/user/createUser',
        data:postData
      }).then(response=>{
        this.handlePageChange();
        this.getRowCount();
        this.$message({
          type: 'success',
          message: '已添加!'
        });
        this.emptyUserData();
      }).catch(error=>{
        console.log(error);
      })
    },
    search(){
         this.handlePageChange();
      this.getRowCount();
    },

    handleSearch() {
      this.handlePageChange();
      this.getRowCount();
    },

    handleEdit(index, row) {
      this.dialogUpdate = true;
      row.userAge = Number(row.userAge);
      this.ruleForm = Object.assign({}, row, index); //这句是关键！！！
    },

    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done();
          this.emptyUserData();
        })
        .catch(_ => {
        });
    },

    /**
     * 清空绑定数据
     */
    emptyUserData() {
      this.dialogAdd = false;
      this.dialogUpdate = false;
      this.ruleForm = {
        userId: null,//用户id
        cardType: null,//证件类型
        cardNo: null,//证件号码
        userName: null,//用户姓名
        userSex: null,//用户性别
        userAge: 25,//用户年龄
        userRole: null//用户角色
      };
    },

    /**
     * 根据 userId 删除用户
     * @param index
     * @param row
     */
    handleDelete(index, row) {
      //console.log(index, row);
      this.$confirm('删除操作, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let postData = this.qs.stringify({
          userId: row.userId,
        });
        this.$axios({
          method: 'post',
          url: '/api/user/deleteUserById',
          data: postData
        }).then(response => {
          this.getRowCount();
          if (this.total % 5 == 1 && this.currentPage >= 1) {
            if (this.total / 5 < this.currentPage) {
              this.currentPage = this.currentPage - 1;
            }
          }
          this.handlePageChange();

          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          //console.log(response);
        }).catch(error => {
          console.log(error);
        });

      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },

    handleSelectionDelete(val) {
      this.multipleSelection = val;
    },

    /**
     * 根据 userId 批量删除用户
     */
    handleDeleteList() {
      let userIds = "";
      this.multipleSelection.forEach(item => {
        userIds += item.userId + ',';
      })
      console.log(userIds);
      // let userIds= this.multipleSelection.map(item => item.userId).join()

      this.$confirm('删除操作, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let postData = this.qs.stringify({
          userIdList: userIds
        });
        console.log(postData);
        this.$axios({
          method: 'post',
          url: '/api/user/deleteUserByIdList',
          data: postData
        }).then(response => {
          this.getRowCount();
          if (this.total % 5 == 1 && this.currentPage >= 1) {
            if (this.total / 5 < this.currentPage) {
              this.currentPage = this.currentPage - 1;
            }
          }
          this.handlePageChange();

          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          //console.log(response);
        }).catch(error => {
          console.log(error);
        });

      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },


    updateUser() {
      if (this.ruleForm.cardType == null || this.ruleForm.cardNo == null || this.ruleForm.userName == null || this.ruleForm.userSex == null || this.ruleForm.userRole == null) {
        this.$alert('用户信息不完整请检查', '温馨提示', {
          confirmButtonText: '确定'
        });
        return;
      }
      let postData = this.qs.stringify({
        userId: this.ruleForm.userId,//用户 Id
        cardType: this.ruleForm.cardType,//证件类型
        cardNo: this.ruleForm.cardNo,//证件号码
        userName: this.ruleForm.userName,//用户姓名
        userSex: this.ruleForm.userSex,//用户性别
        userAge: this.ruleForm.userAge,//用户年龄
        userRole: this.ruleForm.userRole,//用户角色
      });
      this.$axios({
        method: 'post',
        url: '/api/user/updateUserById',
        data: postData
      }).then(response => {
        this.handlePageChange();
        this.getRowCount();
        this.$message({
          type: 'success',
          message: '已编辑!'
        });
        this.emptyUserData();
        //console.log(response);
      }).catch(error => {
        console.log(error);
      });
    }
  },


}
/* eslint-disable */
</script>
<style scoped>
</style>
```