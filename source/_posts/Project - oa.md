---
title: Project - oa
date: 2022-08-03 16:24:42
categories: Project
tags: [Servlet, JSP, EL, JSTL]
---

# 纯 Servlet 实现单表查询（B/S 架构）

使用纯 Servlet 技术实现数据库单表查询

Github: https://github.com/Wataaaame/demo/tree/main/01-oa

## v1.0

1. 准备一张数据库表（sql 脚本）

   ```mysql
   drop table if exists jdept;
   create table jdept (
   	deptno int primary key,
   	deptname varchar(255),
   	loc varchar(255)
   );
   
   insert into jdept values(10, 'XiaoShou', 'Beijing');
   insert into jdept values(20, 'YanFa', 'Shanghai');
   insert into jdept values(30, 'JiShu', 'Guangzhou');
   insert into jdept values(40, 'MeiTi', 'Shenzhen');
   commit;
   
   select * from jdept;
   ```

   - 使用英文方便 dos 窗口展示

   > vv：`create table [table_name] ();` 要用小括号

2. 准备一套 HTML 页面（项目原型）

   - 欢迎页面：index.html
   - 列表页面：list.html（以列表页面为核心展开操作）

   - 新增页面：add.html
   - 修改页面：edit.html
   - 详情页面：detail.html

3. 分析系统功能

   操作连接了数据库，就代表一个独立的功能

   - 查看部门列表
   - 新增部门
   - 删除部门
   - 查看部门详情
   - 跳转到修改页面
   - 修改部门信息

4. 在 IDEA 中搭建开发环境

   1. 创建一个 webapp（添加 servlet.jar 到 classpath）

   2. 向 webapp 中添加 MySQL 驱动

   3. JDBC 工具类

      `io.github.wataaaame.oa.utils.DBUtil`

5. 实现

   - 查看部门列表

     1. 修改前端页面超链接

     2. 编写 web.xml 文件

     3. 编写 DeptListServlet 类继承 HttpServlet 类，然后重写 doGet 方法

        `io.github.wataaaame.oa.web.action.DeptListServlet`

     4. 在 DeptListServlet 类的 doGet 方法中连接数据库，查询所有部门，动态地展示部门列表页面

        - 分析 list.html 页面中静态部分、动态部分
        - list.html 页面中所有双引号要换成单引号，因为 out.print("") 有一对双引号，会引起冲突

   - 查看详情

     1. 修改超链接

        项目名可以使用 `request.getContextPath` 动态获取

        需要传递 `deptno` 供数据库查询

     2. 更新 web.xml 文件

     3. 编写 DeptDetailServlet 类，重写 doGet 方法，动态展示详情页

   - 删除部门

     1. 在前端页面上写 JS 代码，提示用户是否删除
     2. 将前端程序写到后端 Java 代码中
     3. 配置 web.xml
     4. 写 DeptDeleteServlet 类，重写 doGet 方法
     5. 删除成功和失败的处理

   - 新增部门

     最后保存成功之后，转发到 `/dept/list` 时，会出现 405

     1. 保存使用的是 post 请求，底层要执行 doPost 方法
     2. 转发时一次请求，之前是 post，之后还需 post
     3. `/dept/list` 中只有一个 doGet 方法

     解决方案：

     1. `/dept/list` 中添加 doPost 方法，然后在 doPost 方法中调用 doGet 方法
     2. *重定向*

   - 跳转到修改页面

   - 修改部门

## v1.1

### 重定向

服务器内跳转改用重定向

- 删除之后
- 修改之后
- 保存之后

```java
if (count == 1) {
    // 执行成功跳转回 list
    //            req.getRequestDispatcher("/dept/list").forward(req, resp);
    // 重定向
    resp.sendRedirect(contextPath + "/dept/list");
} else {
    // 执行失败跳转到 error
    //            req.getRequestDispatcher("/error.html").forward(req, resp);
    // 重定向
    resp.sendRedirect(contextPath + "/error.html");
}
```

## v1.2 

### Servlet 注解

注解解决了配置文件的问题

```java
@WebServlet({"/dept/list", "/dept/add", "/dept/edit", "/dept/modify", "/dept/detail", "/dept/delete"})
// 模板类
public class DeptServlet extends HttpServlet {}
```

### 模板方法设计模式

Servlet app 过多会导致类爆炸

以前的设计是一个请求一个 Servlet 类

可以一个请求对应一个方法，一个业务对应一个 Servlet 类

> 处理部门相关业务对应一个 DeptServlet，处理用户相关业务对应一个 UserServlet

```java
// 模板方法设计模式
// 重写 service 方法而不是 doGet/doPost，以应对所有请求
@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 获取 Servlet Path
    String servletPathPath = req.getServletPath();
    if ("/dept/list".equals(servletPathPath)) {
        doList(req, resp);
    } else if ("/dept/add".equals(servletPathPath)) {
        doAdd(req, resp);
    } else if ("/dept/edit".equals(servletPathPath)) {
        doEdit(req, resp);
    } else if ("/dept/modify".equals(servletPathPath)) {
        doModify(req, resp);
    } else if ("/dept/detail".equals(servletPathPath)) {
        doDetail(req, resp);
    } else if ("/dept/delete".equals(servletPathPath)) {
        doDel(req, resp);
    }
}
```

每个请求再通过方法实现即可

## 设计缺陷

在 Servlet 中编写前端代码

- 难度大、繁琐

- 耦合度非常高

- 代码非常不美观

- 维护成本太高

  改动前端代码，就需要重新编译 Java 代码，生成 新的 class 文件，打一个新的 war 包，重新发布

# 使用 Servlet + JSP 改造

使用 Servlet 处理业务，收集数据

使用 JSP 展示数据

## v2.1

### JSP

1. 将 HTML 资源文件转换成 JSP 文件，添加中文输出格式 page 指令后，复制到 web 根目录

2. 完成页面正常流转

   在 JSP 中动态地获取路径

   ```jsp
   <a href="<%=request.getContextPath()%>/list.jsp"></a>
   ```


### 实现登录页面

1. 数据库中添加一个用户表（juser）

   - 存储用户登录信息，最基本包括用户名和登录密码
   - 密码一般在数据库表当中存储的是密文（这里先采用明文方式）
   - 向表中插入数据

2. 实现登录页面

   - 登录表单（post），有用户名和密码输入框

   - 用户点击登录，提交表单（用户名和密码）

3. 后台对应 Servlet 处理登录请求

   - 登录成功：部门列表页面
   - 登录失败：跳转失败页面

### Session

登录成功后，可以将用户信息存储到 Session 当中

如果 Session 中有用户信息代表登录成功，否则跳转到登录页面

### Cookie

实现十天免登录

1. 修改前端页面，加一个十天内免登录的复选框

   用户选择了复选框表示支持十天免登录

2. 修改 Servlet 中的 login 方法

   如果用户登录成功，并且选中了十天免登录，则在 login 方法中创建 Cookie，用来存储用户名和密码，并且设置默认 path，设置有效期，最后将 Cookie 响应给浏览器（浏览器将自动保存这两个 Cookie 到硬盘中，有效期为10天）

3. 用户再次访问该网站时，有两个走向

   1. 符合自动登录条件，直接跳转到部门列表页面
   2. 不符合自动登录条件，跳转到登录页面

   配置 web.xml 文件，新增 AutoLogin 为欢迎页面

# 使用 EL + JSTL 改造

## v3.1

- 使用 EL + JSTL 改造 JSP 页面

- 使用 base 标签：

  ```html
  <base href="${pageContext.request.scheme}://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
  ```

## 设计缺陷

若还有其他 Servlet，同样需要判断登录状态才能够使用，则 DeptServlet 中的 Cookie 判断需要重复写，过于繁琐

# 使用 Filter 过滤器改造

## v4.1

使用 Filter 过滤器实现登录验证、字符集配置

- 传递来的是 HttpServletRequest 与 HttpServletResponse，在 Filter 中使用需做强制类型转换

# 使用监听器统计登录在线人数

## v5.1

用户登录

1. session.setAttribute()

用户退出

1. session.removeAttribute()
2. Session 超时销毁

设计方法：

1. 写一个 User 类实现 HttpSessionBindingListener 接口

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

2. 修改代码，登录时将 User 对象存储到 session 中
