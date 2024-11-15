---
title: JavaWeb - Note05 AJAX
date: 2022-08-13 16:37:32
categories: JavaWeb
tags: AJAX
---

传统请求有：地址栏输入 URL、点击超链接、提交 form 表单、使用 JS 代码四种

页面全部刷新导致用户体验较差，浏览器处理完才能显示后面的页面，有空档期

# AJAX

AJAX（Asynchronous Java and XML）可以在浏览器中发送异步请求，请求之间不需要等待，不会出现整个页面清空后再展示新页面的效果，类似于多线程开发

- 并非一种技术，而是多种技术的综合产物
- 代码属于 WEB 前端代码，与后端无关
- 使用 XML/JSON 传输数据
- 可以只更新网页部分内容，而不需要重新加载整个页面（局部刷新）
- 同一个网页中启动多个请求

## XMLHttpRequest

该对象是 AJAX 的核心对象，发送请求以及接受服务器数据的返回，现代浏览器支持，且内置该对象

![AJAX原理](/images/image-javaweb/note05-01-AJAX原理.PNG)

创建对象：

```java
var xhr = new XMLHttpRequest();
```

对象方法

| 方法                                    | 描述                                                         |
| :-------------------------------------- | :----------------------------------------------------------- |
| abort()                                 | 取消当前请求                                                 |
| getAllResponseHeaders()                 | 返回头部信息                                                 |
| getResponseHeader()                     | 返回特定的头部信息                                           |
| **open(method, url, async, user, psw)** | 开启通道。请求参数 **method**：请求类型 GET 或 POST；**url**：文件位置；**async**：true（异步）或 false（同步）；**user**：可选的用户名称；**psw**：可选的密码（e.g.FTP 服务器） |
| **send()**                              | 将请求发送到服务器，用于 GET 请求                            |
| send(*string*)                          | 将请求发送到服务器，用于 POST 请求                           |
| setRequestHeader()                      | 向要发送的报头添加标签/值对                                  |

对象属性

| 属性               | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| onreadystatechange | 定义当 readyState 属性发生变化时被调用的函数                 |
| **readyState**     | 保存 XMLHttpRequest 的状态。**0**：请求未初始化；**1**：服务器连接已建立；**2**：请求已收到；**3**：正在处理请求；**4**：请求已完成且响应已就绪 |
| responseText       | 以字符串返回响应数据                                         |
| responseXML        | 以 XML 数据返回响应数据                                      |
| status             | 返回请求的状态号。**200**："OK"；**403**："Forbidden"；**404**："Not Found" |
| statusText         | 返回状态文本（比如 "OK" 或 "Not Found"）                     |

## GET

发送 AJAX get 请求

前端代码：

ajax01.html

```html
<!-- First AJAX -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>AJAX GET</title>
</head>
<body>
<script type="text/javascript">
    window.onload = function() {
        // 在函数中绑定鼠标单击事件
        document.getElementById("myBtn").onclick = function() {
            // console.log("Hello AJAX!!");

            // 发送 AJAX GET 请求
            // 1. 创建 AJAX 核心对象 XMLHttpRequest
            var xhr = new XMLHttpRequest();
            // 2. 注册回调函数
            xhr.onreadystatechange = function() {
                // console.log(xhr.readyState);
                if (xhr.readyState == 4) {
                    // 响应状态为4，响应完毕后执行
                    if (xhr.status == 404) {
                        // 若返回的状态号为404
                        alert("Sorry, page not fount");
                    } else if (xhr.status == 200) {
                        // 将响应信息放到 div 图层中渲染
                        // 此处的 this 为 xhr 对象
                        document.getElementById("myDiv").innerText = this.responseText;
                    }
                }
            }
            // 3. 开启通道
            xhr.open("get", "/ajax01/ajaxrequest01", true);
            // 4. 发送请求
            xhr.send();
        }
    }
</script>

<!-- 给一个按钮，用户点击这个按钮时发送 get 请求 -->
<input type="button" value="hello ajax" id="myBtn">

<!-- 给一个 div 图层，ajax 接受到响应的数据之后，在 div 中进行渲染 -->
<div id="myDiv"></div>
</body>
</html>
```

后端代码：

AjaxRequest01Servlet.java

```java
package io.github.wataaaame.javaweb.ajax.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/ajaxrequest01")
public class AjaxRequest01Servlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();

        // 响应代码相同，只不过不响应到页面，而是响应到 XmlHttpRequest 对象中
        out.println("You wiiiin!!");
    }
}

```

### 提交数据

```javascript
<!-- set value in get request -->
// 动态获取用户输入的 value
var username = document.getElementById("username").value;
xhr.open("get", "/ajax01/ajaxrequest02?username="+username, true);
```

### GET 请求缓存问题

HTTP 协议中规定 get 会被缓存起来（post 不会），直接从浏览器缓存中加载已获取过的资源，速度较快，用户体验好

但对于低版本的 IE 浏览器来说，AJAX 的 get 请求可能会走缓存，无法实时获取最新的服务器资源

解决方法：在请求路径后添加一个时间戳或随机数，如此请求的资源路径就各不相同，从而重新向浏览器发起请求

## Post

发送 AJAX post 请求

仅前端代码有区别：

```javascript
xhr.open("post", "/ajax01/AjaxRequest03Servlet", true);

// *模拟 form 表单，将 value 放到请求体中
// 固定写法，集成工具输入 "<form enctype>" 以自动提示
xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

// 放在 send 形参中的数据会通过请求体提交
// 获取输入的 username 与 password
var username = document.getElementById("username").value;
var password = document.getElementById("password").value;
// 注意格式
xhr.send("username="+username+"&password="+password);
```

### Project

使用 AJAX POST 请求检测用户注册时，用户名是否可用

- 前端：用户输入用户名后，点击查询按钮，然后发送 AJAX POST 请求，提交 username

- 后端：接收到用户名，连接数据库，根据用户名去表中检索

  若用户名存在：响应红色错误信息

  若用户名不存在：响应绿色合法信息

前端代码：

ex01.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Logon</title>
</head>
<body>
    <script>
        window.onload = function () {
            document.getElementById("checkUsernameBtn").onclick = function () {
                var xhr = new XMLHttpRequest();
                xhr.onreadystatechange = function () {
                    if (this.readyState == 4) {
                        if (this.status == 200) {
                            document.getElementById("usernameCheckingResult").innerHTML = this.responseText;
                        } else {
                            alert(this.statusText);
                        }
                    }
                }
                xhr.open("post", "/ex01/exRequest01Servlet", true);
                xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");
                xhr.send("username=" + document.getElementById("username").value);
            }
        }
    </script>

    用户名：<input type="text" id="username">
    <button id="checkUsernameBtn">checking availability</button>
    <span id="usernameCheckingResult"></span>
</body>
</html>
```

后端代码：

exRequest01Servlet.java

```java
package io.github.wataaaame.javaweb.ajax.web.action;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;

@WebServlet("/exRequest01Servlet")
public class exRequest01Servlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 默认用户名不存在
        boolean exist = false;
        // 获取需要检查的 username
        String username = request.getParameter("username");

        // 连接数据库
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");
            String sql = "select username from juser where username=?";
            ps = conn.prepareStatement(sql);
            ps.setString(1, username);
            rs = ps.executeQuery();
            exist = rs.next() ? true : false;
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }

        // 响应结果到浏览器
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        if (exist) {
            // 若有同名，则返回错误信息
            out.println("<text style='color:red'>Sorry, it's exist</text>");
        } else {
            out.println("<text style='color:lightgreen'>Great! It's available</text>");
        }
    }
}
```

## 数据交换

JSON 与 XML 都是常用的数据交换格式

JSON 体积小，解析简单，较常用

XML 体积大，解析麻烦，较少用

### JSON

回顾 JavaScript JSON 对象

```javascript
// 创建 JSON 对象
var jsonobj = {
	"name1": value1,
    "name2": value2
}

// 访问 JSON 对象
// 第一种方式
user.name1;
// 第二种方式
user["name1"];
```

- 数字不用加引号，但不可以0开始

#### 前端 JSON 字符串转 JSON

从服务器返回的不是一个 JSON 对象，而是一个 JSON 格式的字符串，需要将其转换为 JSON 对象方可使用

有两种转换方式：

1. [window.eval 函数](https://wataaaame.github.io/2022/07/25/JavaScript%20-%20Note03%20JSON/#window-eval)

   将字符串当作一段 JS 代码解释并运行

2. parse 方法

   调用 JS 语言中的内置对象，JSON 的 parse 方法

   ```javascript
   var jsonObj = JSON.parse(jsonStr);
   ```

#### Fastjson

阿里巴巴的 Fastjson，组件中的 JSON 类可以将对象转换成 json 格式的字符串

下载地址：[fastjson-1.2.2.jar](https://repo.maven.apache.org/maven2/com/alibaba/fastjson/1.2.2/fastjson-1.2.2.jar)

示例代码：

```java
// 创建一个 User 对象
User user = new User("101", "vv", 20);

// 使用 Fastjson
String jsonStr = JSON.toJSONString(user);
```

#### Example

点击按钮展示学生信息

前端代码：

ex02.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Student List</title>
</head>
<body>
    <script>
        window.onload = function () {
            document.getElementById("showBtn").onclick = function () {
                var xhr = new XMLHttpRequest();
                xhr.onreadystatechange = function () {
                     if (this.readyState == 4) {
                         if (this.status == 200) {
                             // 此为数组
                             var stuJson = JSON.parse(this.responseText);
                             var html = "";
                             for (var i=0; i<stuJson.length; i++) {
                                 html += "<tr>";
                                 html += "<td>"+stuJson[i].classno+"</td>";
                                 html += "<td>"+stuJson[i].sname+"</td>";
                                 html += "</tr>";
                             }
                             document.getElementById("list").innerHTML = html;
                         } else {
                             alert(this.statusText);
                         }
                     }
                }
                xhr.open("get", "/ex01/exRequest02Servlet", true);
                xhr.send();
            }
        }
    </script>

    <h1>Student list</h1>

    <button id="showBtn">Show</button>
    <br>

    <table width="50%" border="1px black solid">
        <tr>
            <th>no</th>
            <th>name</th>
        </tr>
        <tbody align="center" id="list">
        </tbody>
    </table>
</body>
</html>
```

后端代码：

exRequest02Serlet.java

```java
package io.github.wataaaame.javaweb.ajax.web.action;

import com.alibaba.fastjson.JSON;
import io.github.wataaaame.javaweb.ajax.bean.Student;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

@WebServlet("/exRequest02Servlet")
public class exRequest02Servlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 连接数据库查询学生信息
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        // 保存 student bean 的集合
        List<Student> students = new ArrayList<>();

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");
            stmt = conn.createStatement();
            String sql = "select classno, sname from t_student";
            rs = stmt.executeQuery(sql);
            while (rs.next()) {
                int classno = rs.getInt("classno");
                String sname = rs.getString("sname");
                // 将 Student bean 存储到集合
                students.add(new Student(classno, sname));
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }

        // 将拼接好的字符串响应到浏览器
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        // 通过 fastjson 将集合转换为字符串响应
        out.print(JSON.toJSONString(students));
    }
}
```

### XML

与 JSON 类似，修改处见下

前端代码：

```js
// 获取 xml
var xmlDoc = this.responseXML;
// 拼串
var html = "";

// 获取所有 <student> 元素，返回了多个对象，是一个数组
// 通过 getElementsByTagName 获取元素
var students = xmlDoc.getElementsByTagName("student");
for (var i=0; i<students.length; i++) {
    html += "<tr>";
    // 获取 <student> 元素下所有的子元素
    var children = students[i].childNodes;
    for (var j=0; j<children.length; j++) {
        // 获取子元素名
        if ("classno".equals(children[j].nodeName)) {
            // 获取子元素值
            html += "<td>"+children[j].textContent+"</td>";
        } else if ("sname".equals(children[j].nodename)) {
            html += "<td>"+children[j].textContent+"</td>";
        }
    }
    html += "</tr>";
}

// 设置到 tbody
document.getElementsById("list").innerHTML = html;
```

后端代码：

```java
response.setContentType("text/xml; charset=UTF-8");
```

拼串格式如下：

```xml
<students>
	<student>
        <classno>101</classno>
    	<sname>vv</sname>
    </student>
    <student>
        <classno>102</classno>
    	<sname>ee</sname>
    </student>
</students>
```

## 乱码问题

Tomcat 10 程序员无需干涉

Tomcat 9 及以前：

- 响应中文

  ```java
  response.setContentType("text/html; charset=UTF-8");
  ```

- 接受参数

  ```java
  request.setCharacterEncoding("UTF-8");
  ```

## 异步与同步

请求并发为异步

```js
xhr.open("[method]", "[URL]", true);
```

请求等待为同步

```js
xhr.open("[method]", "[RUL]", false);
```

- 发送的同步请求，需执行完后才能发送其他请求

  e.g. 注册时，类似字段检查需发送同步请求

## 封装 jQuery

AJAX 请求相关的代码都是类似的，可以封装成一个工具类，使用时调用即可

封装示例如下：

jQuery-1.0.0.js

```js
function jQuery(selector) {
    if (typeof selector == "string") {
        // 传递字符串
        if (selector.charAt(0) == "#") {
            // 声明全局变量
            domObj = document.getElementById(selector.substring(1));
            // 返回一个 jQuery 对象，得以调用 html 方法
            return new jQuery();
        }
    } else if (typeof selector == "function") {
        // 传递函数
        window.onload = selector;
    }

    // 替代 innerHTML
    this.html = function (htmlStr) {
        domObj.innerHTML = htmlStr;
    }

    // 替代 onclick
    this.click = function (fun) {
        domObj.onclick = fun;
    }

    // 替代 .value
    this.val = function (v) {
        if (v == undefined) {
            return domObj.value;
        } else {
            domObj.value = v;
        }
    }

    // 定义一个静态方法发送 AJAX 请求
    jQuery.ajax = function (jsonConfig) {
        var xhr = new XMLHttpRequest();
        xhr.onreadystatechange = function () {
            if (this.readyState == 4) {
                if (this.status == 200) {
                    // document.getElementById("postDiv").innerText = xhr.responseText;
                    jsonConfig.success(JSON.parse(xhr.responseText));
                } else {
                    alert(this.statusText)
                }
            }
        }
        if (jsonConfig.type.toUpperCase() == "GET") {
            xhr.open("GET", jsonConfig.url+"?"+jsonConfig.data, jsonConfig.async);
            xhr.send();
        } else if (jsonConfig.type.toUpperCase() == "POST") {
            xhr.open("POST", jsonConfig.url, jsonConfig.async);
            xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
            xhr.send(jsonConfig.data);
        }
    }
}
$ = jQuery;
// 让静态 ajax 生效
new jQuery()
```

前端代码：

jQueryTest01.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script type="text/javascript" src="/ajax01/js/jQuery-1.0.0.js"></script>

    <script>
        $(function () {
            $("#btn").click(function () {
                $("#div").html("Hello jQuery");

                // 调用 ajax
                $.ajax({
                    type : "post",
                    url : "/ajax01/jqueryrequest01",
                    data : "username="+$("#username").val(),
                    async : true,
                    success : function (respJson) {
                        alert(respJson.username);
                    }
                })
            })
        })
    </script>

    username:<input type="text" id="username">
    <br>
    <button id="btn">click</button>
    <div id="div"></div>
</body>
</html>
```

后端代码：

jQueryRequest01.java

```java
package io.github.wataaaame.javaweb.ajax.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/jqueryrequest01")
public class jQueryRequest01 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html; charset=UTF-8");
        response.getWriter().print("{\"username\":\""+request.getParameter("username")+"\"}");
    }
}

```

## Project

选择对应省份后，动态关联出对应的市；选择对应市后，动态关联出对应的区

数据库表设计

```sql
id	code	name	pcode
-------------------------
1	001		湖北省	
2	002		浙江省	
3	003		武汉市		001
4	004		黄冈市		001
5	005		杭州市		002
6	006		金华市		002
7	007		江苏省	
8	008		南京市		007
```

前端：

ex03.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>省市联动</title>
</head>
<body>
    <script type="text/javascript" src="/ex01/js/jQuery-1.0.0.js"></script>
    <script type="text/javascript">
        $(function () {
            $("#province").html("<option value=''>--请选择省份--</option>");
            $("#city").html("<option value=''>--请选择市区--</option>");

            $.ajax({
                type: "get",
                url: "/ex01/listArea",
                data: "t="+new Date().getTime(),
                async: "true",
                success: function (json) {
                    var selectHtml = "<option value=''>--请选择省份--</option>";
                    for (var i = 0; i < json.length; i++) {
                        selectHtml += "<option value='"+json[i].code+"'>"+json[i].name+"</option>"
                    }
                    $("#province").html(selectHtml);
                }
            })
            $("#province").change(function () {
                $.ajax({
                    type: "get",
                    url: "/ex01/listArea",
                    data: "pcode="+this.value+"&t="+new Date().getTime(),
                    async: "true",
                    success: function (json) {
                        var selectHtml = "<option value=''>--请选择市区--</option>";
                        for (var i = 0; i < json.length; i++) {
                            selectHtml += "<option value='"+json[i].code+"'>"+json[i].name+"</option>"
                        }
                        $("#city").html(selectHtml);
                    }
                })
            })
        })
    </script>

    <select id="province">
    </select>

    <select id="city">
    </select>
</body>
</html>
```

后端：

ListAreaServlet.java

```java
package io.github.wataaaame.javaweb.ajax.web.action;


import com.alibaba.fastjson.JSON;
import io.github.wataaaame.javaweb.ajax.bean.Area;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

/**
 * 动态获取所有省或市
 */
@WebServlet("/listArea")
public class ListAreaServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 获取查询的市
        String pcode = request.getParameter("pcode");
        // 连接数据库，动态获取所有区域，最终响应一个 JSON 格式字符串
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        List<Area> areaList = new ArrayList<>();

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");
            if (pcode == null) {
                String sql = "select code, name from t_area where pcode is null";
                ps = conn.prepareStatement(sql);
            } else {
                String sql = "select code, name from t_area where pcode=?";
                ps = conn.prepareStatement(sql);
                ps.setString(1, pcode);
            }
            rs = ps.executeQuery();
            while (rs.next()) {
                String code = rs.getString("code");
                String name = rs.getString("name");
                areaList.add(new Area(code, name));
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }

        response.setContentType("text/html; charset=UTF-8");
        response.getWriter().print(JSON.toJSONString(areaList));
    }
}
```

## 跨域问题

跨域是指从一个域名网页请求另一个域名资源，比如京东页面去请求淘宝资源

通过超链接或 from 表单、window.location.href、script 与 image 标签中 src 的方式不存在该问题

但发送 AJAX 请求访问另一域名资源，由于**同源策略**（浏览器的一种安全策略，协议、域名、端口号皆一致称为同源）的存在，导致无法跨域访问

根本问题：跨域时共享同一个 XMLHttpRequest 是不安全的

### 解决方案

1. 设置响应头

   在响应的 Servlet 中设置响应头，允许指定的同源策略访问

   ```java
   // 允许某个
   response.setHeader("Access-Control-Allow-Origin", "http://localhost:8080");
   // 允许所有
   response.setHeader("Access-Control-Allow-Origin", "*");
   ```

2. jsonp

   json with padding（带填充的 json），并非一个真正的 AJAX 请求，只是可以完成局部刷新的效果，是一种类似 AJAX 请求的机制

   通过 script 标签的 src 发送请求**（只支持 get）**：

   ```html
   <body>
       <script type="text/javascript">
           window.onload = function () {
               document.getElementById("btn").onclick = function () {
                   // 加载 script 元素
                   // 创建 script 元素对象
                   const htmlScriptElt = document.createElement("script");
                   // 设置 script 的 type 属性
                   htmlScriptElt.type = "text/javascript";
                   // 设置 script 的 src 属性
                   htmlScriptElt.src = "http://localhost:8081/b/jsonp?fun=sayHello";
                   // 将 script 对象添加到 body 标签中（加载 script）
                   document.getElementsByTagName("body")[0].appendChild(htmlScriptElt);
               }
           }
           
           // 后端响应回数据给函数（json 格式）
           function sayHello(data) {
               document.getElementById("mydiv").innerHTML = data.username;
           }
       </script>
       
       <button id="btn">jsonp解决跨域问题，达到 ajax 局部刷新效果</button>
       <div id="mydiv"></div>
   </body>
   ```

   jsonp Servlet：

   ```java
   // 获取函数名
   String fun = request.getParameter("fun");
   // 响应一段 js 代码
   response.getWriter.print(fun+"({\"username\":\"vv\"})");
   ```

3. JQuery

   jQuery 库已经对 jsonp 进行了封装，使用前需引入 jQuery 库的 js 文件

   ```html
   <body>
       <script type="text/javascript" src="/a/js/jquery-3.6.0.min.js"></script>
       <script type="text/javascript">
       	$(function () {
               $("#btn").click(function () {
                   // 发送所谓的 ajax 请求，本质上是一个 jsonp
                   $.ajax({
                       type: "GET",	// jsonp 只支持 get
                       // 实际 url 是：/b/jsonp?callback=...
                       // callback 即 fun，值为之前的 success，自动生成
                       url: "http://localhost:8081/b/jsonp",
                       dataType: "jsonp",	// 指定数据类型
                       success: function (data) {
                           $("#mydiv").html("Welcome, "+data.username);
                       }
                   })
               })
           })
       </script>
       
       <button id="btn">jQuery 封装的 jsonp</button>
       <div id="mydiv"></div>
   </body>
   ```

   Servlet：

   ```java
   // 获取函数名
   String callback = request.getParameter("callback");
   // 响应一段 js 代码，调用函数
   response.getWriter().print(callback+"({\"username\":\"vv\"})");
   ```

   其中，ajax 中的配置可以更换为以下形式：

   ```js
   $.ajax({
       type: "GET",
       url: "http://localhost:8081/b/jsonp",
       dataType: "jsonp",
       jsonp: "fun",	// 指定具体参数名传参，不采用默认参数 callback
       jsonpCallback: "sayHello"	// 指定具体的回调函数，不采用默认回调函数（默认回调函数会自动调用 success 的回调函数）
   })
   
   // 自定义函数
   function sayHello(data) {
       $("#mydiv").html("Welcome, "+data.username);
   }
   ```

   后端参数对应修改即可

4. 代理机制（httpclient）

   令 ajax 请求本域中的 Servlet，然后让该 Servlet 跨域访问

   使用 Java 程序发送 get/post 请求：

   1. *使用 JDK 内置的 API*：`java.net.URL`，发送 HTTP 请求

   2. 使用第三方的开源组件，如：apache 的 httpclient

      示例代码：

      ```java
      public class void main(String[] args) throws Exception {
          // 使用 java 代码发送 HTTP get 请求
          // 目标地址
          String url = "http://localhost:8081/b/hello";
          HttpGet httpGet = new HttpGet(url);
          
          // 设置类型
          httpGet.setHeader("Content-Type", "application/x-www-form-urlencoded");
          System.out.println("调用 URL："+httpGet.getURI());
          
          // httpClient 实例化
          CloseableHttpClient httpClient = HttpClients.createDefault();
          // 执行请求并获取返回
          HttpResponse response = httpClient.execute(httpGet);
          HttpEntity entity = response.getEntity();
          System.out.println("返回状态码：" + reponse.getStatusLine());
          
          // 显示结果
          BufferedReader reader = new BufferedReader(new InputStreamReader(entity.getContent(), "UTF-8"));
          String line = null;
          StringBuffer responseSB = new StringBuffer();
          while ((line = reader.readLine()) != null) {
              responseSB.append(line);
          }
          System.out.println("服务器响应的数据：" + responseSB);
          reader.close();
          
          httpClient.close();
      }
      ```

5. nginx 反向代理

   同样使用了代理机制完成 AJAX 的跨域，只需修改 nginx 的配置即可

## Project

实现搜索联想，自动补全

- 搜索联想：用户输入单词后，自动联想出用户要搜索的信息，给一个提示
- 自动补全：当联想出一些内容后，用户点击某个联想的单词，将该单词自动补全到搜索框中

核心原理：

1. 当键盘事件发生时，发送 ajax 请求，请求中提交用户输入的搜索内容
2. 后端接收到 ajax 请求，接收到请求内容，执行 select 语句进行模糊查询，返回查询结果
3. 将查询结果封装成 json 格式的字符串响应到前端
4. 前端接收到 json 格式字符串后，解析这个 json，动态展示页面

数据库：

hotSearch.sql

```sql
drop table if exists t_hsearch;

create table t_hsearch (
    id int primary key auto_increment,
    content varchar(255)
);

insert into t_hsearch(content) values("武汉疫情");
insert into t_hsearch(content) values("武汉天气");
insert into t_hsearch(content) values("武汉购房");
insert into t_hsearch(content) values("武汉大学生落户");
insert into t_hsearch(content) values("武汉补贴");
insert into t_hsearch(content) values("武汉住房公积金");
commit;

select * from t_hsearch;
```

前端代码：

autocomplete.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AJAX 实现自动补全</title>
    <style>
        .searchInput {
            width: 300px;
            height: 25px;
            padding-left: 5px;
        }

        .showList {
            width: 309px;
            border: 1px solid black;
            background-color: #fafafa;
            display: none;
        }

        .showList p {
            padding-left: 5px;
            margin: 5px;
        }

        .showList p:hover {
            cursor: pointer;
            border: 1px solid black;
            background-color: gray;
        }

        .searchBtn {
            background-color: #c1f0fc;
            width: 60px;
            height: 30px;
        }
    </style>
</head>
<body>
    <script type="text/javascript">
        window.onload = function () {
            document.getElementById("searchInput").onkeyup = function () {
                if (this.value == "") {
                    document.getElementById("showList").style.display = "none";
                } else {
                    // 发送 ajax 请求
                    var xhr = new XMLHttpRequest();
                    xhr.onreadystatechange = function () {
                        if (this.readyState == 4) {
                            if (this.status >= 200 && this.status < 300) {
                                var json = JSON.parse(this.responseText);
                                var html = "";
                                for (var i = 0; i < json.length; i++) {
                                    var content = json[i].content;
                                    html += "<p onclick='setInput(\""+content+"\")'>"+content+"</p>";
                                }
                                document.getElementById("showList").innerHTML = html;
                                // 显示 div
                                document.getElementById("showList").style.display = "block";
                            }
                        }
                    }
                    xhr.open("GET", "/ex02/hsearch?input=" + this.value + "&t=" + new Date().getTime(), true);
                    xhr.send();
                }
            }
        }

        // 自动补全函数
        function setInput(content) {
            document.getElementById("searchInput").value = content;
            document.getElementById("showList").style.display = "none";
        }
    </script>

    <input type="text" class="searchInput" id="searchInput">
    <button class="searchBtn">Search</button>
    <div class="showList" id="showList"></div>
</body>
</html>
```

后端代码：

HotSearchServlet.java

```java
package io.github.wataaaame.javaweb.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.*;

@WebServlet("/hsearch")
public class HotSearchServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 获取传递的关键字
        String input = request.getParameter("input");
        // 连接数据库
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        StringBuilder sb = new StringBuilder();

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");
            String sql = "select content from t_hsearch where content like ?";
            ps = conn.prepareStatement(sql);
            // 模糊查询不建议前后都加 %，会让索引失效，从而效率降低
            ps.setString(1, input+"%");
            rs = ps.executeQuery();
            sb.append("[");
            while (rs.next()) {
                sb.append("{\"content\":\""+rs.getString("content")+"\"},");
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }

        response.setContentType("text/html; charset=UTF-8");
        response.getWriter().print(sb.subSequence(0, sb.length()-1) + "]");
        sb.append("]");
    }
}
```

依赖 jar 包：

`servlet-api.jar`

`mysql-connector-java-5.1.23.jar`

# 附录：HTTP状态信息

## 1xx: 信息

| 消息:                   | 描述:                                                        |
| :---------------------- | :----------------------------------------------------------- |
| 100 Continue            | 服务器仅接收到部分请求，但是一旦服务器并没有拒绝该请求，客户端应该继续发送其余的请求 |
| 101 Switching Protocols | 服务器转换协议：服务器将遵从客户的请求转换到另外一种协议     |

## 2xx: 成功

| 消息:                             | 描述:                                                        |
| :-------------------------------- | :----------------------------------------------------------- |
| 200 OK                            | 请求成功（其后是对GET和POST请求的应答文档）                  |
| 201 Created                       | 请求被创建完成，同时新的资源被创建                           |
| 202 Accepted                      | 供处理的请求已被接受，但是处理未完成                         |
| 203 Non-authoritative Information | 文档已经正常地返回，但一些应答头可能不正确，因为使用的是文档的拷贝 |
| 204 No Content                    | 没有新文档。浏览器应该继续显示原来的文档。如果用户定期地刷新页面，而Servlet可以确定用户文档足够新，这个状态代码是很有用的 |
| 205 Reset Content                 | 没有新文档。但浏览器应该重置它所显示的内容。用来强制浏览器清除表单输入内容 |
| 206 Partial Content               | 客户发送了一个带有Range头的GET请求，服务器完成了它           |

## 3xx: 重定向

| 消息:                  | 描述:                                                        |
| :--------------------- | :----------------------------------------------------------- |
| 300 Multiple Choices   | 多重选择。链接列表。用户可以选择某链接到达目的地。最多允许五个地址 |
| 301 Moved Permanently  | 所请求的页面已经转移至新的url                                |
| 302 Found              | 所请求的页面已经临时转移至新的url                            |
| 303 See Other          | 所请求的页面可在别的url下被找到                              |
| 304 Not Modified       | 未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用 |
| 305 Use Proxy          | 客户请求的文档应该通过Location头所指明的代理服务器提取       |
| 306 *Unused*           | 此代码被用于前一版本。目前已不再使用，但是代码依然被保留     |
| 307 Temporary Redirect | 被请求的页面已经临时移至新的url                              |

## 4xx: 客户端错误

| 消息:                             | 描述:                                                        |
| :-------------------------------- | :----------------------------------------------------------- |
| 400 Bad Request                   | 服务器未能理解请求                                           |
| 401 Unauthorized                  | 被请求的页面需要用户名和密码                                 |
| 402 Payment Required              | 此代码尚无法使用                                             |
| 403 Forbidden                     | 对被请求页面的访问被禁止                                     |
| 404 Not Found                     | 服务器无法找到被请求的页面                                   |
| 405 Method Not Allowed            | 请求中指定的方法不被允许                                     |
| 406 Not Acceptable                | 服务器生成的响应无法被客户端所接受                           |
| 407 Proxy Authentication Required | 用户必须首先使用代理服务器进行验证，这样请求才会被处理       |
| 408 Request Timeout               | 请求超出了服务器的等待时间                                   |
| 409 Conflict                      | 由于冲突，请求无法被完成                                     |
| 410 Gone                          | 被请求的页面不可用                                           |
| 411 Length Required               | "Content-Length" 未被定义。如果无此内容，服务器不会接受请求  |
| 412 Precondition Failed           | 请求中的前提条件被服务器评估为失败                           |
| 413 Request Entity Too Large      | 由于所请求的实体的太大，服务器不会接受请求                   |
| 414 Request-url Too Long          | 由于url太长，服务器不会接受请求。当post请求被转换为带有很长的查询信息的get请求时，就会发生这种情况 |
| 415 Unsupported Media Type        | 由于媒介类型不被支持，服务器不会接受请求                     |
| 416                               | 服务器不能满足客户在请求中指定的Range头                      |
| 417 Expectation Failed            |                                                              |

## 5xx: 服务器错误

| 消息:                          | 描述:                                            |
| :----------------------------- | :----------------------------------------------- |
| 500 Internal Server Error      | 请求未完成。服务器遇到不可预知的情况             |
| 501 Not Implemented            | 请求未完成。服务器不支持所请求的功能             |
| 502 Bad Gateway                | 请求未完成。服务器从上游服务器收到一个无效的响应 |
| 503 Service Unavailable        | 请求未完成。服务器临时过载或当机                 |
| 504 Gateway Timeout            | 网关超时                                         |
| 505 HTTP Version Not Supported | 服务器不支持请求中指明的HTTP协议版本             |
