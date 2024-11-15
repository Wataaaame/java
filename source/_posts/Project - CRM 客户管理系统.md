---
title: Project - CRM 客户关系管理系统
date: 2022-09-06 09:07:42
categories: Project
tags: CRM
---

# 技术架构

- 视图层（View）：展示数据，与用户交互

  `html, css, js, jQuery, bootstrap (ext, easyUI), jsp`

- 控制层（Controller）：控制业务处理流程

  - 接收请求，接收参数，封装参数

  - 根据不同请求调用业务层处理业务

  - 根据处理结果，返回响应信息

  `(servlet) springMVC (webwork, struts)`

- 业务层（Service）：处理业务逻辑

  处理业务的步骤以及操作的原子性

  `JavaSE (activiti, JBPM (工作流：处理复杂业务))`

- 持久层（Dao/Mapper）：操作数据库

  (jdbc) MyBatis (Hibernate, ibatis)

- 整合层：维护类资源，维护数据库资源

  `spring(IOC, AOP) (corba, ejb)`

# 软件公司

## 组织架构

研发部（前后端、美工、DBA）；测试部；产品部；实施部；运维部；市场部

## 生命周期

1. `招标` *标书*

   - 投标：制作标书

2. `可行性分析` *可行性分析报告*

   - do or not do

3. `需求分析` *需求文档*

   - 产品经理、程序员：需求调研

   - 项目原型：容易确定需求；开发项目时作为 JSP 网页

4. 分析与设计

   - `架构设计`（架构师） *架构设计文档*

     - 物理架构设计

       应用服务器：`tomcat(Apache), weblogic(bea->Oracle), websphere(IBM), jBoss(Redhet), resion(MS)`

       数据库服务器：`MySQL, Oracle, DB2, SQLServre, 达梦`

     - 逻辑架构设计：代码分层

     - 技术选型：Java

   - `项目设计`（项目经理） *项目设计文档*
     
     - **物理模型设计**：数据库表设计（PowerDesigner） *.pdm*
     - 逻辑模型设计：类、属性、关系等（Rational Rose） *.pdl*
     - 界面设计（产品经理、美工）：企业级应用 *项目原型*
     - 算法设计 *算法设计文档*

5. `搭建开发环境` *技术架构文档*

   创建项目，添加 Jar 包，添加配置文件，添加静态页面，添加公共类以及其他资源，能够正常启动运行

6. `编码实现` *文档注释*

7. `测试` *测试用例*

8. `试运行` *使用手册*

9. `上线` *实施手册*

10. `运维` *运维手册*

# CRM

客户关系管理简称 CRM（Customer Relationship Management）。它是以**客户数据的管理**为核心，保持客户的联系信息处于最新状态，跟踪他们与企业的每次交互并管理他们的帐，以建立、发展和维护客户关系为主要目的

- 本质是吸引客户，留住客户，实现客户利益最大化
- 目标是帮助发展客户关系，推动业务增长并提高客户忠诚度

- 企业级应用、传统应用，给销售或贸易型公司使用（扩展市场，销售产品/服务，各个环节维护客户关系）

此 CRM 提供给大型进出口贸易公司使用，做大宗商品的进出口贸易，受国家管制，预计开发时间为 8 个月

## 核心业务

- 系统管理功能：保证业务管理功能正常安全地运行而设计的功能
  1. 用户登录，安全退出，登录验证等（超级管理员、开发人员等使用）

- 业务管理功能：处理业务数据

  1. 市场活动：市场部，设计市场活动/营销活动

  2. 线索（潜在用户）：销售部（初级销售），增加线索

  3. 客户/联系人：销售部（高级销售），有效地区分和跟踪客户（公司）和联系人（个人）

  4. 交易（商机）：销售部（高级销售），更好地区分和统计交易的各个阶段

  5. 售后回访：客服部，妥善安排售后回访

     > 主动提醒功能，需要长连接协议，实现较复杂

  6. 统计图表：管理层，统计各种销售数据，给领导决策做数据支撑

## 表结构

tbl_user：用户表

tbl_dic_type：数据字典类型表，存储下拉列表类型

tbl_dic_value：数据字典值，存储下拉列表内容

tbl_activity：市场活动表

tbl_activity_remark：市场活动备注表

tbl_clue：线索表

tbl_clue_remark：线索备注表

tbl_clue_activity_relation 线索和市场活动关联关系表

tbl_customer：客户表

tbl_customer_remark：客户备注表

tbl_contacts：联系人表

tbl_contacts_remark：联系人备注表

tbl_contacts_activity_relation 联系人和市场活动关联关系表

tbl_tran：交易表

tbl_tran_remark：交易备注表

tbl_tran_history：交易历史表

### 主键字段

在数据库表中唯一确定一条记录，推荐使用一个字段做主键；没有业务含义的字段做主键

主键字段类型和长度由主键值的生成方式决定

主键的生成方式：

1. 自增：借助数据库自身主键生成机制

   数值型，长度由数据量来决定

   - 运行效率较低，开发效率高

2. **assighed**：算法手动生成主键值，需唯一非空

   hi / low：数值型，长度由数据量决定

   UUDI：字符串，长度是 32 位

3. *共享主键*：由另一张表的类型和长度决定

   - 耦合度太高

4. 联合主键：由多个字段的类型和长度决定

### 外键字段

用来确定表和表之间的关系

1. 一对多：一张表中的一条记录对应另一张表中的多条记录；另一张表中的一条记录只能对应一张表中的一条记录

   使用外键：一为父表，多为子表，子表使用外键

   - 添加数据时，先添加父表
   - 删除数据时，先删除子表

   > 内连接：查询所有符合条件的数据，并且要求结果在两张表中都有相对应的数据
   >
   > 外连接（左）：查询左侧表中所有符合条件的数据，即使在右侧表中没有相对应的数据
   >
   > *如果外键不能为空，优先使用内连接反之看实际业务需求

2. 一对一：一张表中的一条记录对应另一张表中的一条记录；另一张表中的一条记录只能对应一张表中的一条记录

   1. *共享主键*

   2. 唯一外键：类似一对多结构，但外键唯一，不会重复

      添加顺序同一对多

3. 多对多：一张表中的一条记录对应另一张表中的多条记录；另一张表中的一条记录只能对应一张表中的多条记录

   添加关联关系表（类似于父表）

   添加顺序同一对多

### 日期和时间字段

1. 按照字符串处理

   char(10) yyyy-MM-dd

   char(19) yyyy-MM-dd HH:mm:ss

2. [SpringMVC 日期处理](https://wataaaame.github.io/2022/09/01/SSM%20-%20Note04%20SpringMVC/#日期处理)

## 搭建开发环境

1. 导入数据库

2. 创建项目：Project / CRM

   设置 JDK

3. 创建工程 crm

   补全目录结构

   设置编码格式为 UTF-8：`File -> Settings -> Editor -> File Encodings -> Project Encoding / Properties Files - Transparent`

4. 添加 jar 包：添加依赖

5. 添加配置文件

6. 添加静态页面资源

7. 把项目部署到服务器

## 功能分析与设计

### 首页

1. 需求分析

   用户访问项目首页，首先进入登录页面

2. 流程设计

   ![首页时序图](/images/image-project/crm/01-首页时序图.png)

3. 重点回顾

   是否创建 Controller 类，取决于访问的资源目录（一个资源目录占一个 Controller 类）

4. 编码实现

   UserController.java

   ```java
   /**
        * 跳转到登录页面
        * @return 返回页面地址
        */
   @RequestMapping("/toLogin")
   public String toLogin() {
       return "settings/qx/user/login";
   }
   ```

### 用户登录

1. 需求分析

   用户在登录页面，输入用户名和密码，点击登录按钮或者回车，完成用户登录的功能

   - 用户名和密码不能为空
   - 用户名或者密码错误，用户己过期，用户状态被锁定，ip受限都不能登录成功。
   - 登录成功之后，所有业务页面显示当前用户的名称
   - 实现10天记住密码
   - 登录成功之后，跳转到业务主页面
   - 登录失败，页面不跳转，提示信息

2. 流程设计

   ![用户登录时序图](/images/image-project/crm/02-用户登录时序图.png)

   异步请求与同步请求的取舍：全局刷新使用同步请求，局部刷新使用异步请求，二者皆含使用局部刷新

3. 重点回顾

   是否新建 Mapper/Service 类取决于访问的数据库表

   - 使用逆向工程，根据表生成 mapper 层三部分代码：实体类、mapper 接口、映射文件：

     1. 新建空 Maven Module `crm-mybatis-generator`
     2. 添加 MyBatis 逆向工程插件依赖
     3. 配置文件提供：数据库连接信息；代码保存目录；表信息
     4. 可在 Maven 菜单 `crm-mybatis-generator -> Plugins -> mybatis-generator ->mybatis-generator:generate` 点击使用

     使用逆向工程则无需为实体类起别名

   - 使用 jQuery 获取指定元素的指定属性的值

     - 选择器 `.attr("属性名")`：用来获取值非 true/false 的属性值
     - 选择器 `.prop("属性名")`：用来获取是 true/false 的属性值，如 checked，selected，readonly，disabled 等

   - 作用域传递数据：

     1. pageContext：在同一个页面的不同标签之间传递数据
     2. request：在同一个请求中传递数据
     3. session：同一个浏览器窗口的不同请求之间传递数据
     4. application：所有用户共享的数据，并且长久频繁使用的数据


   - 使用 EL 表达式获取 cookie：`${cookie.loginAct.value}`

4. 编码实现

   UserController.java

   ```java
   /**
    * 登录页面的登录判断
    * @param loginAct 账号
    * @param loginPwd 密码
    * @param isRemPwd 记住密码
    * @param request 获取访问的 IP 地址
    * @param response 添加 Cookie 到浏览器
    * @param session 保存会话信息
    * @return 返回登录状态给前端
    */
   @RequestMapping("/login")
   @ResponseBody
   public Object login(String loginAct, String loginPwd, String isRemPwd, HttpServletRequest request, HttpServletResponse response, HttpSession session) {
       Map<String, Object> map = new HashMap<>();
   
       map.put("loginAct", loginAct);
       map.put("loginPwd", loginPwd);
   
       User user = userService.selectUserByActAndPwd(map);
   
       ReturnObject returnObject = new ReturnObject();
       if (user == null) {
           returnObject.setCode(Constant.FAIL);
           returnObject.setMsg("Error name or password");
       } else {
           if (DateUtils.formatDateTime(new Date()).compareTo(user.getExpireTime()) > 0) {
               // 判断账户过期
               returnObject.setCode(Constant.OVERDUE);
               returnObject.setMsg("Account is overdue");
           } else if ("0".equals(user.getLockState())) {
               returnObject.setCode(Constant.LOCKED);
               returnObject.setMsg("Account locked");
           } else if (user.getAllowIps()==null || !user.getAllowIps().contains(request.getRemoteAddr())) {
               returnObject.setCode(Constant.LIMITED);
               returnObject.setMsg("IP is limited");
           } else {
               returnObject.setCode(Constant.SUCCESS);
               returnObject.setMsg("Success");
               // 保存用户信息到 Session 作用域中
               session.setAttribute(Constant.SESSION_USER, user);
   
               // 用户名可以 10 天内自动填充
               Cookie c1 = new Cookie("loginAct", user.getLoginAct());
               c1.setMaxAge(10 * 24 * 60 * 60);
               response.addCookie(c1);
               // 记住密码
               if ("true".equals(isRemPwd)) {
                   Cookie c2 = new Cookie("loginPwd", user.getLoginPwd());
                   c2.setMaxAge(10 * 24 * 60 * 60);
   
                   response.addCookie(c2);
               } else {
                   Cookie c2 = new Cookie("loginPwd", user.getLoginPwd());
                   c2.setMaxAge(0);
   
                   response.addCookie(c2);
               }
           }
       }
   
       return returnObject;
   }
   ```

   > v：
   >
   > 表单中哪怕提交使用 button 标签，想要发送 AJAX 请求，也要将 type 属性改为 button
   >
   > IP6 下的本机地址为：0:0:0:0:0:0:0:1

### 安全退出

1. 需求分析

   用户在任意的业务页面，点击“退出”按钮，弹出确认退出的模态窗口；用户在确认退出的模态窗口，点击“确定”按钮，完成安全退出的功能

   - 安全退出，清空 cookie，销毁session

   - 退出完成之后，跳转到登录页面

2. 流程设计

   ![安全退出时序图](/images/image-project/crm/03-安全退出时序图.png)

3. 编码实现

   UserController.java
   
   ```java
   /**
    * 安全退出系统，清空存储的密码 cookie 与 session 会话
    * @param response 写入 cookie
    * @param session  销毁 session
    * @return 重定向到登录页面
    */
   @RequestMapping("/logout")
   public String logout(HttpServletResponse response, HttpSession session) {
       // 销毁 cookie
       Cookie cookie = new Cookie("loginPwd", null);
       cookie.setMaxAge(0);
       response.addCookie(cookie);
   
       // 销毁 session
       session.invalidate();
   
       // 跳转回登录页面
       // 借助 SpringMVC 重定向，默认添加了 /crm
       return "redirect:/settings/qx/user/toLogin";
   }
   ```
   
   > v：
   >
   > 退出跳转需要发送重定向请求，否则地址栏显示为 /logout，且 cookie 依旧存在
   >
   > 发送重定向需要填写完整路径

### 登录验证

1. 需求分析

   用户访问任何业务资源，都需要进行登录验证

   - 只有登录成功的用户才能访问业务资源

   - 没有登录成功的用户访问业务资源，跳转到登录页面

2. 重点回顾

   - 过滤器（简单）

     ```java
     implements Filter {
     	init() {}
         doFilter() {}
         destroy() {}
     }
     ```

     配置过滤器 web.xml

   - **拦截器**（复杂）

     ```java
     implements HandlerInterceptor {
         pre() {}
         post() {}
         after() {}
     }
     ```

     配置拦截器 springmvc.xml

3. 编码实现

   拦截器：

   ```java
   /**
    * 用户登录状态判断，若 session 无用户信息，则重定向到登录页面
    */
   public class LoginInterceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           if (request.getSession().getAttribute(Constant.SESSION_USER) == null) {
               // 重定向时，url 必须加上项目名
               response.sendRedirect(request.getContextPath() + "/settings/qx/user/toLogin");
               return false;
           }
           return true;
       }
   }
   ```

   配置拦截器：

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

### 创建市场活动

1. 需求分析

   用户在市场活动主页面，点击“创建”按钮，弹出创建市场活动的模态窗口；用户在创建市场活动的模态窗口填写表单，点击“保存”按钮，完成创建市场活动的功能。

   - 所有者是动态的（在现实市场活动主页面时，就从数据库中查询出所有用户并且显示在创建的模态窗口中）

   - 所有者和名称不能为空

   - 如果开始日期和结束日期都不为空，则结束日期不能比开始日期小

   - 成本只能为非负整数

   - 创建成功之后，关闭模态窗口，刷新市场活动列，显示第一页数据，保持每页显示条数不变

   - 创建失败，提示信息创建失败，模态窗口不关闭，市场活动列表也不刷新

2. 流程设计

   ![创建市场活动时序图](/images/image-project/crm/04-创建市场活动时序图.png)

3. 重点回顾

   - 页面切割技术：

     1. *frameset 和 frame 标签*

        frameset：切割页面

        frame：显示页面

        - 每一个 frame 标签就是一个独立的浏览器窗口

        ```html
        <frameset cols="20%, 60%, 20%">
            <frame src="url1" name="f1" />
            <frame src="url2" name="f1" />
            <frame src="url3" name="f1" />
        </frameset>
        
        <!-- target 指定打开的窗口 -->
        <a href="url" target="f3">go</a>
        ```

     2. div 和 iframe 标签

        div：切割页面

        iframe：显示页面

        ```html
        <div style="height: 10%; width=20%">
            <iframe href="url" />
        </div>
        ```
   
   
      - 模态窗口：
   
        从前使用 `window.open("url", "_blank")` 方式打开注册页面等，比较繁琐。而模拟的窗口，本质上是 div，通过设置 z-index 大小来实现层叠顺序
   
        - z-index 初始参数 <0，需要显示时，z-index 值设置成 >0 即可
   
        - 通过引入 bootstrap js 框架，在 class 属性中声明 `modal fade` 来添加模态窗口
   
        控制模态窗口的现实与隐藏：
   
        1. 通过指定元素标签
   
           设置 `data-toggle="modal"` 来开启模态窗口，通过 `data-target="id"` 来指定开启的模态窗口
   
        2. 通过 js 函数
   
           显示：`选择器（选中 div）.modal("show")`
   
           隐藏：`选择器（选中 div）.modal("hide")`
   
        3. 通过标签的属性
   
           点击添加了 `data-dismiss=""` 属性的元素会关闭所在的模态窗口
   
   
   
      - 正则表达式
   
        定义字符串的匹配模式，可以用来判断指定的具体字符串是否符合匹配模式
   
        语法规则：
   
        1. //：声明一个正则表达式
   
           `var regExp=/.../;`
   
        2. ^：匹配字符串的开头
   
           $：匹配字符串的结尾
   
        3. []：匹配指定字符集中的一位字符
   
           `/^[abc]$/`
   
           `/^[a-z0-9]$/`
   
        4. {}：匹配次数
   
           `/^[abc]{5}$/`
   
           {m}：匹配 m 次
   
           {m, n}：匹配 m 到 n 次
   
           {m, }：匹配 m 次以上
   
        5. 特殊符号
   
           \d：匹配一位数字，相当于 `[0-9]`
   
           \D：匹配一位非数字
   
           \w：匹配所有字符，包括字母、数字、下划线
   
           \W：匹配非字符
   
           *：匹配 0 次或多次，相当于 `{0, }`
   
           +：匹配 1 次或多次，相当于 `{1, }`
   
           ?：匹配 0 次或 1 次，相当于 `{0, 1}`
   
        [常用正则表达式](https://wataaaame.github.io/2022/09/04/Project%20-%20Configuration%20and%20Appendix/#Regular-Expression)
   
4. 编码实现

   ActivityController.java

   ```java
   /**
    * 新建活动
    * @param activity 实体类自动填充
    * @param session  session 获取创建者对象
    * @return 返回 json 数组
    */
   @RequestMapping("/insertActivity")
   @ResponseBody
   public Object insertActivity(Activity activity, HttpSession session) {
       User user = (User)session.getAttribute(Constant.SESSION_USER);
       // 封装参数
       activity.setId(UUIDUtils.getUUID());
       activity.setCreateTime(DateUtils.formatDateTime(new Date()));
       activity.setCreateBy(user.getId());
   
       ReturnObject returnObject = new ReturnObject();
       try {
           // 调用 service 层方法，保存创建的市场活动
           int ret = activityService.insertActivity(activity);
           if (ret > 0) {
               returnObject.setCode(Constant.SUCCESS);
               returnObject.setMsg("Success");
           } else {
               returnObject.setCode(Constant.FAIL);
               returnObject.setMsg("The system is busy. Please try again later");
           }
       } catch (Exception e) {
           e.printStackTrace();
   
           returnObject.setCode(Constant.FAIL);
           returnObject.setMsg("The system is busy. Please try again later");
       }
       return returnObject;
   }
   ```

   > v：
   >
   > 发送 AJAX 请求的 data 会自动匹配给 Service 中形参的实体类
   >
   > jsp 引入 js 乱码刷新浏览器缓存！！！

### 分页查询活动

1. 需求分析

   当市场活动主页面加载完成之后，显示所有数据的第一页

   用户在市场活动主页面填写查询条件，点击“查询”按钮，显示所有符合条件的数据的第一页，保持每页显示条数不变

   实现翻页功能：

   - 在市场活动主页面，显示市场活动列表和记录的总条数

   - 默认每页显示条数：10

2. 流程设计

   ![分页查询市场活动时序图](/images/image-project/crm/05-分页查询市场活动时序图.png)

3. 重点回顾

   - Mapper.xml 中，使用 `&gt;` 代表 >（greater than）；使用 `&lt;` 代表 <（less than）

   - 无法通过 activityList.length 计算出总条目，该数组只包含本页数据

   - EL 表达式用来操作 js 代码

     JSTL 标签用来操作 jsp 页面

   - 在指定的标签中显示 jsp 页面片段：

     `选择器.html();`：覆盖显示 html 在标签内部

     `选择器.text();`：覆盖显示文本在标签内部

     `选择器.append();`：追加显示在指定标签内部后

     `选择器.before();`：追加显示在指定标签外部前

     `选择器.after();`：追加显示在指定标签外部后
     
   - 分页查询插件：bs_pagination

   - 块元素自动分行，行元素不自动换行

   - js 系统函数

     eval()：将字符串转换为 js 执行

     parseInt()：获取小数的整数部分

4. 编码实现

   ActivityController.java

   ```java
   /**
    * 获取所有第一页数据以及总条目
    * @param owner
    * @param name
    * @param startDate
    * @param endDate
    * @param pageNo
    * @param pageSize
    * @return
    */
   @RequestMapping("/selectActivityByConditionForPage")
   @ResponseBody
   public Object selectActivityByConditionForPage(String owner, String name,
                                                  String startDate, String endDate,
                                                  int pageNo, int pageSize) {
       // 封装参数
       Map<String, Object> map = new HashMap<>();
       map.put("owner", owner);
       map.put("name", name);
       map.put("startDate", startDate);
       map.put("endDate", endDate);
       map.put("pageNo", (pageNo-1)*pageSize);
       map.put("pageSize", pageSize);
   
       // 调用 Service 层方法，查询数据
       List<Activity> activityList = activityService.selectActivityByConditionForPage(map);
       int totalRows = activityService.selectCountOfActivityByCondition(map);
   
       // 根据查询结果，生成响应信息
       Map<String, Object> retMap = new HashMap<>();
       retMap.put("activityList", activityList);
       retMap.put("totalRows", totalRows);
   
       return retMap;
   }
   ```

### 删除市场活动

1. 需求分析

   用户在市场活动主页面，选择要删除的市场活动，点击“删除”按钮，弹出确认窗口

   用户点击"确定"按钮，完成删除市场活动的功能

   - 每次至少删除一条市场活动
   - 可以批量删除市场活动
   - 删除成功之后，刷新市场活动列表，显示第一页数据，保持每页显示条数不变
   - 删除失败，提示信息，列表不刷新

2. 流程设计

   ![删除市场活动时序图](/images/image-project/crm/06-删除市场活动时序图.png)

3. 重点回顾

   - 在页面中给元素添加事件

     1. 使用元素事件属性：`onxxxx="f()"`

     2. 使用 jQuery 对象：`选择器.xxxx(function() {})`

        以上两种只能给固有元素添加事件

        固有元素：当调用事件函数给元素添加事件时，如果元素已经生成，则这些元素称为固有元素

        动态元素：当调用事件函数给元素添加事件时，若元素还未生成，称为动态元素

        > js 异步唤醒每个元素，于是给动态元素绑定事件时，元素可能还未渲染到页面，需要使用同步机制进行处理，见下

     3. 使用 jQuery 的 on 函数：`父选择器.on("事件类型", 子选择器)`

        - 父元素：必须是固有元素，可以是直接父元素，也可以是间接父元素

        - 事件类型：跟事件属性和事件函数一一对应

        - 子选择器：目标元素，跟父选择器构成一个父子选择器

        不但能给固有元素添加事件，还能给动态生成的元素添加事件

   - str.substr(index, num)

     str.substring(index, end)

   - AJAX 向后台发送请求时，可以通过 data 提交参数，提交格式有两种

     1. `data: {k: v}`

        一个参数名只能对应一个参数值

     2. `data: k1=v1&k2=v2`

        一个参数名可对应多个参数值

     3. `data:FormData`

        提交二进制数据（文件）

4. 编码实现

   ```java
   /**
    * 通过 id 删除一条或多条数据
    * @param id
    * @return
    */
   @RequestMapping("/deleteActivityByIds")
   @ResponseBody
   public Object deleteActivityByIds(String[] id) {
       ReturnObject retObj = new ReturnObject();
       try {
           int res = activityService.deleteActivityByIds(id);
           if (res > 0) {
               retObj.setCode(Constant.SUCCESS);
               retObj.setMsg("Success");
           } else {
               retObj.setCode(Constant.FAIL);
               retObj.setMsg("Sorry, System is busy...");
           }
       } catch (Exception e) {
           e.printStackTrace();
           retObj.setCode(Constant.FAIL);
           retObj.setMsg("Sorry, System is busy...");
       }
   
       return retObj;
   }
   ```

   > v：$("#listTbd :checkbox:checked").size())
   >
   > 筛选器不可以含空格

### 修改市场活动

1. 需求分析

   用户在市场活动主页面，选择要修改的市场活动，点击“修改”按钮，弹出修改市场活动的模态窗口

   用户在修改市场活动的模态窗口填写表单，点击“更新”按钮，完成修改市场活动的功能。

   - 每次能且只能修改一条市场活动
   - 所有者是动态的
   - 表单验证（同创建）
   - 修改成功之后，关闭模态窗口，刷新市场活动列表，保持页号和每页显示条数都不变
   - 修改失败，提示信息，模态窗口不关闭，列表也不刷新

2. 流程设计

   ![修改市场活动时序图](/images/image-project/crm/07-修改市场活动时序图.png)

3. 重点回顾

   - 封装参数

     1. 如果做查询条件，或者参数之间不属于一个实体类对象，封装成 map
     2. 如果写数据，并且参数本来就属于一个实体类对象，封装成实体类对象

   - 使用 jQuery 获取/设置指定元素的 value 属性值

     获取：`选择器.val()`

     设置：`选择器.val(属性值)`

4. 编码实现

   ActivityController.java

   ```java
   /**
    * 根据主键修改信息
    * @param activity
    * @param session
    * @return
    */
   @RequestMapping("/updateByPrimaryKeySelective")
   @ResponseBody
   public Object updateByPrimaryKeySelective(Activity activity, HttpSession session) {
       User user = (User) session.getAttribute(Constant.SESSION_USER);
       // 封装修改信息
       activity.setEditTime(DateUtils.formatDateTime(new Date()));
       activity.setEditBy(user.getId());
   
       // 调用 Service 方法，保存修改信息
       ReturnObject retObj = new ReturnObject(Constant.FAIL, "edit fail");
       try {
           int res = activityService.updateByPrimaryKeySelective(activity);
           if (res == 1) {
               retObj.setCode(Constant.SUCCESS);
               retObj.setMsg("Success");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
       return retObj;
   }
   ```

### 批量导出活动

1. 需求分析

   用户在市场活动主页面，点击“批量导出”按钮，把所有市场活动生成一个 excel 文件，弹出文件下载的对话框

   用户选择要保存的目录，完成导出市场活动的功能。

   - 导出成功之后，页面不刷新

2. 流程设计

   ![批量导出市场活动时序图](/images/image-project/crm/08-批量导出市场活动时序图.png)

3. 技术准备

   1. 使用 Java 生成 Excel 文件（*图形化 API 非研究方向，使用插件即可*）

      iText（收费）

      **apache-poi**

      文件：`HSSFWorkbook`

      页：`HSSFSheet`

      行：`HSSFRow`

      列：`HSSFCell`

      样式：`HSSFCellStyle`

      1. 添加依赖

         ```xml
         <!--poi依赖-->
         <dependency>
           <groupId>org.apache.poi</groupId>
           <artifactId>poi</artifactId>
           <version>3.15</version>
         </dependency>
         ```

      2. 生成 Excell

         ```java
         package com.bjpowernode.crm.poi;
         
         import org.apache.poi.hssf.usermodel.*;
         import org.apache.poi.ss.usermodel.HorizontalAlignment;
         
         import java.io.FileOutputStream;
         import java.io.OutputStream;
         
         /**
          * 使用apache-poi生成excel文件
          */
         public class CreateExcelTest {
             public static void main(String[] args) throws Exception{
                 //创建HSSFWorkbook对象，对应一个excel文件
                 HSSFWorkbook wb=new HSSFWorkbook();
                 //使用wb创建HSSFSheet对象，对应wb文件中的一页
                 HSSFSheet sheet=wb.createSheet("学生列表");
                 //使用sheet创建HSSFRow对象，对应sheet中的一行
                 HSSFRow row=sheet.createRow(0);//行号：从0开始,依次增加
                 //使用row创建HSSFCell对象，对应row中的列
                 HSSFCell cell=row.createCell(0);//列的编号：从0开始，依次增加
                 cell.setCellValue("学号");
                 cell=row.createCell(1);
                 cell.setCellValue("姓名");
                 cell=row.createCell(2);
                 cell.setCellValue("年龄");
         
                 //生成HSSFCellStyle对象
                 HSSFCellStyle style=wb.createCellStyle();
                 style.setAlignment(HorizontalAlignment.CENTER);
         
                 //使用sheet创建10个HSSFRow对象，对应sheet中的10行
                 for(int i=1;i<=10;i++){
                     row=sheet.createRow(i);
         
                     cell=row.createCell(0);//列的编号：从0开始，依次增加
                     cell.setCellValue(100+i);
                     cell=row.createCell(1);
                     cell.setCellValue("NAME"+i);
                     cell=row.createCell(2);
                     cell.setCellStyle(style);
                     cell.setCellValue(20+i);
                 }
         
                 //调用工具函数生成excel文件
                 OutputStream os=new FileOutputStream("D:\\course\\18-CRM\\阶段资料\\serverDir\\studentList.xls");//目录必须手动创建，文件如果不存在，poi会自动创建
                 wb.write(os);
         
                 //关闭资源
                 os.close();
                 wb.close();
         
                 System.out.println("===========create ok==========");
             }
         }
         
         ```

   2. 文件下载

      所有文件下载请求只能发同步请求

      ```java
      @RequestMapping("/workbench/activity/fileDownload.do")
      public void fileDownload(HttpServletResponse response) throws Exception{
          //1.设置响应类型
          response.setContentType("application/octet-stream;charset=UTF-8");
          //2.获取输出流
          OutputStream out=response.getOutputStream();
      
          //浏览器接收到响应信息之后，默认情况下，直接在显示窗口中打开响应信息；即使打不开，也会调用应用程序打开；只有实在打不开，才会激活文件下载窗口。
          //可以设置响应头信息，使浏览器接收到响应信息之后，直接激活文件下载窗口，即使能打开也不打开
          response.addHeader("Content-Disposition","attachment;filename=mystudentList.xls");
      
          //读取excel文件(InputStream)，把输出到浏览器(OutoutStream)
          InputStream is=new FileInputStream("D:\\course\\18-CRM\\阶段资料\\serverDir\\studentList.xls");
          byte[] buff=new byte[256];
          int len=0;
          while((len=is.read(buff))!=-1){
              out.write(buff,0,len);
          }
      
          //关闭资源
          is.close();
          out.flush();
      }
      ```

4. 编码实现

   ```java
   /**
        * 导出所有活动 Excel
        * @param response
        * @throws IOException
        */
   @RequestMapping("/exportAllActivity")
   public void exportAllActivity(HttpServletResponse response) throws IOException {
       // 获取所有 Activity
       List<Activity> activityList = activityService.selectAllActivity();
       HSSFWorkbook workbook = HSSFUtils.exportActivity(activityList);
   
       // 实现文件下载
       // 设置响应头
       response.setContentType("application/octet-stream;charset=UTF-8");
       response.setHeader("Content-Disposition", "attachment;filename=activityList.xls");
       OutputStream out = response.getOutputStream();
       
       // 直接将文件输出到输出流
       workbook.write(out);
   
       out.flush();
   
       // 释放资源
       workbook.close();
   }
   ```
   
   HSSFUtils.java
   
   ```java
   /**
    * 通过查询到的活动列表创建 Excel 文档
    * @param activityList
    * @return
    */
   public static HSSFWorkbook exportActivity(List<Activity> activityList) {
       // 创建 Excel 文件，并将获取的数据写入
       HSSFWorkbook workbook = new HSSFWorkbook();
       HSSFSheet sheet = workbook.createSheet("市场活动信息");
       // 创建表头
       HSSFRow row = sheet.createRow(0);
       HSSFCell cell = row.createCell(0);
       cell.setCellValue("ID");
       cell = row.createCell(1);
       cell.setCellValue("所有者");
       cell = row.createCell(2);
       cell.setCellValue("活动名");
       cell = row.createCell(3);
       cell.setCellValue("开始日期");
       cell = row.createCell(4);
       cell.setCellValue("截止日期");
       cell = row.createCell(5);
       cell.setCellValue("成本");
       cell = row.createCell(6);
       cell.setCellValue("描述");
       cell = row.createCell(7);
       cell.setCellValue("创建时间");
       cell = row.createCell(8);
       cell.setCellValue("创建者");
       cell = row.createCell(9);
       cell.setCellValue("编辑时间");
       cell = row.createCell(10);
       cell.setCellValue("编辑者");
   
       // 判断 activityList 是非为空
       if (activityList!=null && activityList.size()>0) {
           // 创建表内容
           Activity activity = null;
           for (int i=0; i<activityList.size(); i++) {
               activity = activityList.get(i);
               row = sheet.createRow(i + 1);
               cell = row.createCell(0);
               cell.setCellValue(activity.getId());
               cell = row.createCell(1);
               cell.setCellValue(activity.getOwner());
               cell = row.createCell(2);
               cell.setCellValue(activity.getName());
               cell = row.createCell(3);
               cell.setCellValue(activity.getStartDate());
               cell = row.createCell(4);
               cell.setCellValue(activity.getEndDate());
               cell = row.createCell(5);
               cell.setCellValue(activity.getCost());
               cell = row.createCell(6);
               cell.setCellValue(activity.getDescription());
               cell = row.createCell(7);
               cell.setCellValue(activity.getCreateTime());
               cell = row.createCell(8);
               cell.setCellValue(activity.getCreateBy());
               cell = row.createCell(9);
               cell.setCellValue(activity.getEditTime());
               cell = row.createCell(10);
               cell.setCellValue(activity.getEditBy());
           }
       }
   
       return workbook;
   }
   ```

### 选择导出活动

1. 需求分析

   用户在市场活动主页面，选择要导出的市场活动，点击“选择导出”按钮，把所有选择的数据生成一个 excel 文件，弹出文件下载的对话框

   用户选择要保存的目录，完成选择导出市场活动的功能

   - 每次至少选择导出一条记录

   - 导出成功之后，页面不刷新

2. 编码实现

   ```java
   /**
    * 导出选中的活动 Excel
    * @param id
    * @param response
    * @throws IOException
    */
   @RequestMapping("/exploreByIds")
   public void exploreByIds(String[] id, HttpServletResponse response) throws IOException {
       // 通过 ids 查询 activity
       List<Activity> activityList = activityService.selectByIds(id);
       HSSFWorkbook workbook = ExportFormatUtils.exportActivity(activityList);
   
       // 实现文件下载
       // 设置响应头
       response.setContentType("application/octet-stream;charset=UTF-8");
       response.setHeader("Content-Disposition", "attachment;filename=selectedActivityList.xls");
       OutputStream out = response.getOutputStream();
   
       workbook.write(out);
   
       out.flush();
   
       workbook.close();
   }
   ```

   > v：
   >
   > 不能发送 ajax 请求

### 批量导入活动

1. 需求分析

   用户在市场活动主页面，点击“导入”按钮，弹出导入市场活动的模态窗口

   用户在导入市场活动的模态窗口选择要上传的文件，点击“导入”按钮，完成导入市场活动的功能。

   - 只支持。xls

   - 文件大小不超过5MB

   - 导入成功之后，提示成功导入记录条数，关闭模态窗口，刷新市场活动列表，显示第一页数据，保持每页显示条数不变

   - 导入失败，提示信息，模态窗口不关闭，列表也不刷新

2. 流程设计

   ![导入市场活动时序图](/images/image-project/crm/09-导入市场活动时序图.png)

3. 技术准备

   1. 文件上传

      1. 提交表单

         使用 `<input type="file" name="myFile">` 标签

         只能使用 `POST` 请求

         表单的编码格式：`multipart/form-data`

         ```html
         <form action="url" method="post" enctype="multipart/form-data">
             <input type="file" name="myFile"><br>
             <input type="text" name="userName"><br>
             <input type="submit" value="提交">
         </form>
         ```

      2. 提交 AJAX

         data 项中使用 AJAX 提供的接口 `FormData`，可以将文件模拟成键值对向后台提交数据

         FormData 最大的优势是不但能提交文本数据，还能提交二进制数据

         - 使用 `append` 向对象追加数据，无数量限制

         - AJAX 配置

           `processData: false`：向后台提交参数前，是否同意转换成字符串，默认 true

           `contentType: false`：向后台提交参数前，是否统一按 urlencoding 编码，默认 true

         示例代码：

         ```js
         var formData = new FormData();
         // 参数名需与 Controller 层形参名一致
         formData.append("file", file);
         
         // 发送请求
         $.ajax({
             data: formData
             processData: false,
             contentType: false
         })
         ```

   2. 服务器接收

      file 文件使用 `MultipartFile` 形参接收

      添加 springMVC 文件上传配置

      ```java
      /**
           * 配置springmvc的文件上传解析器
           *
           */
      @RequestMapping("/workbench/activity/fileUpload.do")
      public @ResponseBody Object fileUpload(String userName, MultipartFile myFile) throws Exception{
          //把文本数据打印到控制台
          System.out.println("userName="+userName);
          //把文件在服务指定的目录中生成一个同样的文件
          String originalFilename=myFile.getOriginalFilename();
          File file=new File("D:\\course\\18-CRM\\阶段资料\\serverDir\\",originalFilename);//路径必须手动创建好，文件如果不存在，会自动创建
          myFile.transferTo(file);
      
          //返回响应信息
          ReturnObject returnObject=new ReturnObject();
          returnObject.setCode(Contants.RETURN_OBJECT_CODE_SUCCESS);
          returnObject.setMessage("上传成功");
          return returnObject;
      }
      ```

   3. 使用 Java 解析 Excel 文件

      ```java
      /**
       * 使用apache-poi解析excel文件
       */
      public class ParseExcelTest {
          public static void main(String[] args) throws Exception{
              //根据excel文件生成HSSFWorkbook对象，封装了excel文件的所有信息
              InputStream is=new FileInputStream("D:\\course\\18-CRM\\阶段资料\\serverDir\\aaa.xls");
              HSSFWorkbook wb=new HSSFWorkbook(is);
              //根据wb获取HSSFSheet对象，封装了一页的所有信息
              HSSFSheet sheet=wb.getSheetAt(0);//页的下标，下标从0开始，依次增加
              //根据sheet获取HSSFRow对象，封装了一行的所有信息
              HSSFRow row=null;
              HSSFCell cell=null;
              for(int i=0;i<=sheet.getLastRowNum();i++) {//sheet.getLastRowNum()：最后一行的下标
                  row=sheet.getRow(i);//行的下标，下标从0开始，依次增加
      
                  for(int j=0;j<row.getLastCellNum();j++) {//row.getLastCellNum():最后一列的下标+1
                      //根据row获取HSSFCell对象，封装了一列的所有信息
                      cell=row.getCell(j);//列的下标，下标从0开始，依次增加
      
                      //获取列中的数据
                      System.out.print(HSSFUtils.getCellValueForStr(cell)+" ");;
                  }
      
                  //每一行中所有列都打完，打印一个换行
                  System.out.println();
              }
          }
      }
      ```

      封装工具类：

      ```java
      /**
           * 从指定的HSSFCell对象中获取列的值
           * @return
           */
      public static String getCellValueForStr(HSSFCell cell){
          String ret="";
          if(cell.getCellType()==HSSFCell.CELL_TYPE_STRING){
              ret=cell.getStringCellValue();
          }else if(cell.getCellType()==HSSFCell.CELL_TYPE_NUMERIC){
              ret=cell.getNumericCellValue()+"";
          }else if(cell.getCellType()==HSSFCell.CELL_TYPE_BOOLEAN){
              ret=cell.getBooleanCellValue()+"";
          }else if(cell.getCellType()==HSSFCell.CELL_TYPE_FORMULA){
              ret=cell.getCellFormula();
          }else{
              ret="";
          }
      
          return ret;
      }
      ```

4. 编码实现

   ```java
   /**
        * 批量导入市场活动
        * @param activityFile
        * @param session
        * @return
        */
   @RequestMapping("/importActivity")
   @ResponseBody
   public Object importActivity(MultipartFile activityFile, HttpSession session){
       User user = (User) session.getAttribute(Constant.SESSION_USER);
       ReturnObject retObj = new ReturnObject(Constant.FAIL, "系统忙");
       
       try {
           HSSFWorkbook workbook = new HSSFWorkbook(activityFile.getInputStream());
           HSSFSheet sheet = workbook.getSheetAt(0);
           HSSFRow row;
           HSSFCell cell;
           Activity activity;
           List<Activity> activityList = new ArrayList<>();
   
           for (int i=1; i<=sheet.getLastRowNum(); i++) {
               row = sheet.getRow(i);
               activity = new Activity();
               String cellVal;
   
               activity.setId(UUIDUtils.getUUID());
               // 谁导入的数据，所有者就是谁
               activity.setOwner(user.getId());
               activity.setCreateTime(DateUtils.formatDateTime(new Date()));
               activity.setCreateBy(user.getId());
   
               for (int j=0; j<row.getLastCellNum(); j++) {
                   cell = row.getCell(j);
                   cellVal = HSSFUtils.getCellValueForStr(cell);
                   if (j == 0) {
                       activity.setName(cellVal);
                   } else if (j == 1) {
                       activity.setStartDate(cellVal);
                   } else if (j == 2) {
                       activity.setEndDate(cellVal);
                   } else if (j == 3) {
                       activity.setCost(cellVal);
                   } else if (j == 4) {
                       activity.setDescription(cellVal);
                   }
               }
               // 每遍历出一行，保存到 List 集合中
               activityList.add(activity);
           }
           // 调用 Service 层方法，保存市场活动
           int res = activityService.saveByList(activityList);
           retObj.setCode(Constant.SUCCESS);
           retObj.setRetObject(res);
       } catch (IOException e) {
           e.printStackTrace();
       }
       return retObj;
   }
   ```
   
   > v：
   >
   > - 读取 xls 文件时
   >
   >   sheet.getLastRowNum()：最后一行的下标
   >
   >   row.getLastCellNum()：最后一列的下标+1

### 市场活动明细

1. 需求分析

   用户在市场活动主页面，点击市场活动名称超级链接，跳转到明细页面，完成查看市场活动明细的功能。

   - 在市场活动明细页面，展示：
     1. 市场活动的基本信息
     2. 该市场活动下所有的备注信息

2. 流程设计

   ![查看市场活动明细时序图](/images/image-project/crm/10-查看市场活动明细时序图.png)

3. 重点回顾

   - 使用标签保存数据，以供不时之需

     1. 表单组件标签，优先使用 value

        不方便时，使用自定义属性

     2. 如果不是表单组件，优先使用自定义属性

     数据的获取：

     1. value 属性：`dom对象.val()`
     2. 自定义属性：`dom对象.attr("属性名")`

4. 编码实现

   ```java
   /**
    * 活动详情
    * @param activityId
    * @param request
    * @return
    */
   @RequestMapping("/activityDetail")
   public String activityDetail(String activityId, HttpServletRequest request) {
       // 获取市场活动详情
       Activity activity = activityService.queryActivityDetailById(activityId);
       // 获取市场活动评论
       List<ActivityRemark> activityRemarkList = activityRemarkService.queryActivityRemarkByActivityId(activityId);
   
       // 装载到 request 作用域
       request.setAttribute("activity", activity);
       request.setAttribute("activityRemarkList", activityRemarkList);
   
       return "workbench/activity/detail";
   }
   ```

### 添加活动备注

1. 需求分析

   用户在市场活动明细页面，输入备注内容，点击“保存”按钮，完成添加市场活动备注的功能

   - 备注内容不能为空

   - 添加成功之后，清空输入框，刷新备注列表

   - 添加失败，提示信息，输入框不清空，列表也不刷新

2. 流程设计

   ![添加市场活动备注时序图](/images/image-project/crm/11-添加市场活动备注时序图.png)

4. 编码实现

   ```java
   /**
    * 保存 remark 信息
    * @param activityRemark
    * @param session
    * @return
    */
   @RequestMapping("/saveActivityRemarkSelective")
   @ResponseBody
   public Object saveSelective(ActivityRemark activityRemark, HttpSession session) {
       User user = (User) session.getAttribute(Constant.SESSION_USER);
       // 封装参数
       activityRemark.setId(UUIDUtils.getUUID());
       activityRemark.setCreateTime(DateUtils.formatDateTime(new Date()));
       activityRemark.setCreateBy(user.getId());
       activityRemark.setEditFlag(Constant.REMARK_EDIT_FLAG_NO);
   
       ReturnObject returnObject = new ReturnObject(Constant.FAIL, "系统忙");
       try {
           // 访问 Service 层，保存创建市场活动备注
           int count = activityRemarkService.saveSelective(activityRemark);
   
           if (count > 0) {
               returnObject.setCode(Constant.SUCCESS);
               returnObject.setMsg("Success");
               returnObject.setRetObject(activityRemark);
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
   
       return returnObject;
   }
   ```

   > v：
   >
   > AJAX 访问 Controller 层，控制台报错找不到 jsp，查看是否添加了 ResponseBody 注解
   >
   > 新增加的 remark 属于动态元素，没有鼠标悬停等特效，需将绑定的函数改为 on 的形式

### 删除活动备注

1. 需求分析

   用户在市场活动明细页面，点击“删除”市场活动备注的图标，完成删除市场活动备注的功能

   - 删除成功之后，刷新备注列表

   - 删除失败，提示信息，备注列表不刷新

2. 流程设计

   ![删除市场活动备注时序图](/images/image-project/crm/12-删除市场活动备注时序图.png)

3. 重点回顾

   - 使用标签定位：

     1. 优先考虑 id 属性（id 不可重复），其次考虑 name 属性，最后考虑自定义属性

   - 选中指定标签中的指定属性（表单可以使用 `:` 过滤器替代）

     ```js
     a[name='deleteA']
     ```

4. 编码实现

   ```java
   /**
    * 删除指定的留言
    * @param id
    * @return
    */
   @RequestMapping("/deleteActivityRemarkById")
   @ResponseBody
   public Object deleteActivityRemarkById(String id) {
       ReturnObject returnObject = new ReturnObject(Constant.FAIL, "系统忙");
       try {
           // 调用 Service 方法删除
           int ret = activityRemarkService.deleteById(id);
           if (ret > 0) {
               returnObject.setCode(Constant.SUCCESS);
               returnObject.setMsg("Success");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
       return returnObject;
   }
   ```

### 修改活动备注

1. 需求分析

   用户在市场活动明细页面，点击“修改”市场活动备注的图标，弹出修改市场活动备注的模态窗口

   用户在修改市场活动备注的模态窗口，填写表单，点击“更新”按钮，完成修改市场活动备注的功能

   - 备注内容不能为空

   - 修改成功之后，关闭模态窗口，刷新备注列表

   - 修改失败，提示信息，模态窗口不关闭，列表也不刷新

2. 流程设计

   ![修改市场活动备注时序图](/images/image-project/crm/13-修改市场活动备注时序图.png)

3. 编码实现

   ```java
   /**
    * 修改市场活动备注
    * @param remark
    * @param session
    * @return
    */
   @RequestMapping("/editActivityRemarkById")
   @ResponseBody
   public Object editActivityRemarkById(ActivityRemark remark, HttpSession session) {
       User user = (User) session.getAttribute(Constant.SESSION_USER);
       remark.setEditTime(DateUtils.formatDateTime(new Date()));
       remark.setEditBy(user.getId());
       remark.setEditFlag(Constant.REMARK_EDIT_FLAG_YES);
       ReturnObject returnObject = new ReturnObject(Constant.FAIL, "服务忙");
   
       try {
           int count = activityRemarkService.editSelective(remark);
           if (count > 0) {
               returnObject.setCode(Constant.SUCCESS);
               returnObject.setMsg("Success");
               returnObject.setRetObject(remark);
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
   
       return returnObject;
   }
   ```

### 创建线索

1. 需求分析

   用户在线索主页面，点击“创建”按钮，弹出创建线索的模态窗口

   用户在创建线索的模态窗口，填写表单，点击”保存”按钮，完成创建线索的功能

   - 所有者、称呼、线索状态、线索来源是动态的

   - 表单验证

   - 创建成功之后，关闭模态窗口，刷新线索列表，显示第一页数据，保持每页显示条数不变

   - 创建失败，提示信息，模态窗口不关闭，列表也不刷新

2. 流程设计

   ![创建线索时序图](/images/image-project/crm/14-创建线索时序图.png)

3. 技术准备

   - 数据字典

     tbl_dic_type 为数据字典表，属于系统功能，对应每一个下拉列表，字典值为用户维护

     tbl_dic_value 为数据字典值，对应每一个下拉列表中的值，通过 type_code 字段与 tbl_dic_type 表一一对应

4. 编码实现

   ```java
   /**
    * 创建线索
    * @param clue
    * @param session
    * @return
    */
   @RequestMapping("/create")
   @ResponseBody
   public Object create(Clue clue, HttpSession session) {
       User user = (User) session.getAttribute(Constant.SESSION_USER);
       clue.setId(UUIDUtils.getUUID());
       clue.setCreateBy(user.getId());
       clue.setCreateTime(DateUtils.formatDateTime(new Date()));
   
       ReturnObject returnObject = new ReturnObject(Constant.FAIL, "服务忙");
       try {
           int count = clueService.createSelective(clue);
           if (count == 1) {
               returnObject.setCode(Constant.SUCCESS);
               returnObject.setMsg("Success");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
       return returnObject;
   }
   ```
   
   > v：
   >
   > 数据库字符串排序数字会出现 1、10、2 的情况，此时需做以下处理：
   >
   > 1. 先进行位数排序，再进行字符排序
   >
   >    `order by length(order_no), order_no asc`
   >
   > 2. 转化为数字类型进行排序
   >
   >    `order by cast(order_no decimal) asc`

### 查看线索明细

1. 需求分析

   用户在线索主页面，点击线索名称（fullname 和 appellition）超级链接，跳转到线索明细页面，完成查看线索明细的功能

   - 在线索明细页面，展示：
     1. 线索的基本信息
     2. 线索的备注信息
     3. 跟该线索相关联的市场活动信息

2. 流程设计

   ![查看线索明细时序图](/images/image-project/crm/15-查看线索明细时序图.png)

3. 编码实现

   ```java
   /**
    * 线索详情
    */
   @RequestMapping("/detail")
   public String detail(String clueId, HttpServletRequest request) {
       // 调用 Service 查询所需信息
       Clue clue = clueService.queryDetailById(clueId);
       List<Activity> activityList = activityService.queryByClueId(clueId);
   
       // 封装到 request 页面
       request.setAttribute("clue", clue);
       request.setAttribute("activityList", activityList);
   
       return "workbench/clue/detail";
   }
   ```

### 线索关联活动

1. 需求分析

   用户在线索明细页面，点击“关联市场活动”按钮，弹出线索关联市场活动的模态窗口

   用户在线索关联市场活动的模态窗口，输入搜索条件，每次键盘弹起，根据名称模糊查询市场活动，把所有符合条件的市场活动显示到列表中；用户选择要关联的市场活动，点击“关联”按钮，完成线索关联市场活动的功能

   - 每次至少关联一个市场活动

   - 同一个市场活动只能跟同一个线索关联一次

   - 关联成功之后，关闭模态窗口，刷新已经关联过的市场活动列表

   - 关联失败，提示信息，模态窗口不关闭，已经关联过的市场活动列表也不刷新

2. 流程设计

   ![线索关联市场活动时序图](/images/image-project/crm/16-线索关联市场活动时序图.png)

3. 编码实现

   ```java
   @RequestMapping("/saveRelation")
   @ResponseBody
   public Object saveRelation(String[] activityIds, String clueId) {
       ClueActivityRelation relation;
       List<ClueActivityRelation> relationList = new ArrayList<>();
   
       for (String activityId: activityIds) {
           relation = new ClueActivityRelation();
           relation.setId(UUIDUtils.getUUID());
           relation.setActivityId(activityId);
           relation.setClueId(clueId);
           relationList.add(relation);
       }
       int countCAR = carService.createByList(relationList);
   
       ReturnObject retObj = new ReturnObject(Constant.FAIL, "服务忙");
       try {
           if (countCAR > 0) {
               retObj.setCode(Constant.SUCCESS);
               retObj.setMsg("Success");
               retObj.setRetObject(activityService.queryDetailByIds(activityIds));
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
       return retObj;
   }
   ```

### 线索删除关联

1. 需求分析

   用户在线索明细页面，点击某一个"解除关联“按钮”，弹出确认解除的窗口

   用户点击“确定”按钮，完成解除线索关联市场活动的功能

   - 解除成功之后，刷新已经关联的市场活动列表

   - 解除失败，提示信息，列表也不刷新

2. 流程设计

   ![解除线索关联市场活动时序图](/images/image-project/crm/17-解除线索关联市场活动时序图.png)

3. 编码实现

   ```java
   /**
    * 删除关联的线索
    * @param relation
    * @return
    */
   @RequestMapping("/deleteRelation")
   @ResponseBody
   public Object deleteRelation(ClueActivityRelation relation) {
       ReturnObject retObj = new ReturnObject(Constant.FAIL, "Server Busy");
       // 调用 Service 层删除线索活动关系
       int count = carService.deleteByActivityIdAndClueId(relation);
       try {
           if (count > 0) {
               retObj.setCode(Constant.SUCCESS);
               retObj.setMsg("Success");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
       return retObj;
   }
   ```

### 线索转换

1. 需求分析

   用户在线索明细页面，点击“转换”按钮，跳转到线索转换页面

   用户在线索转换页面，如果需要创建创建交易，则填写交易表单数据，点击“转换”按钮，完成线索转换的功能

   - 在线索转换页面，展示：fullName，appellation，company，owner

   - 市场活动源是可搜索的

   - 数据转换：

     - 把线索中有关公司的信息转换到客户表中

     - 把线索中有关个人的信息转换到联系人表中

     - 把线索的备注信息转换到客户备注表中一份

     - 把线索的备注信息转换到联系人备注表中一份

     - 把线索和市场活动的关联关系转换到联系人和市场活动的关联关系表中

     - 如果需要创建交易，还要往交易表中添加一条记录

     - 如果需要创建交易，还要把线索的备注信息转换到交易备注表中一份

     - 删除线索的备注

     - 删除线索和市场活动的关联关系

     - 删除线索

     - 在一同个事务中完成

   - 转换成功之后，跳转到线索主页面

   - 转换失败，提示信息，页面不跳转

2. 流程设计

   ![线索转换时序图](/images/image-project/crm/18-线索转换时序图.png)

3. 编码实现

   Service

   ```java
   @Override
   public void saveConvert(Map<String, Object> map) {
       /* 把线索中有关公司的信息转换到客户表中 */
       String clueId = (String) map.get("clueId");
       // Controller 获取的用户信息传递到 Service
       User user = (User) map.get(Constant.SESSION_USER);
       // 根据 clueId 查询 clue 信息
       Clue clue = clueMapper.selectByPrimaryKey(clueId);
   
       Customer customer = new Customer();
       customer.setId(UUIDUtils.getUUID());
       customer.setOwner(user.getId());    // 谁创建的谁是所有者
       customer.setName(clue.getCompany());
       customer.setWebsite(clue.getWebsite());
       customer.setPhone(clue.getPhone());
       customer.setCreateBy(user.getId());
       customer.setCreateTime(DateUtils.formatDateTime(new Date()));
       customer.setContactSummary(clue.getContactSummary());
       customer.setNextContactTime(clue.getNextContactTime());
       customer.setDescription(clue.getDescription());
       customer.setAddress(clue.getAddress());
       // 保存到客户
       customerMapper.insertSelective(customer);
   
       /* 把线索中有关个人的信息转换到联系人表中 */
       Contacts contacts = new Contacts();
       contacts.setId(UUIDUtils.getUUID());
       contacts.setOwner(user.getId());
       contacts.setSource(clue.getSource());
       contacts.setCustomerId(UUIDUtils.getUUID());
       contacts.setFullname(clue.getFullname());
       contacts.setAppellation(clue.getAppellation());
       contacts.setEmail(clue.getEmail());
       contacts.setMphone(clue.getMphone());
       contacts.setJob(clue.getJob());
       contacts.setCreateBy(user.getId());
       contacts.setCreateTime(DateUtils.formatDateTime(new Date()));
       contacts.setDescription(clue.getDescription());
       contacts.setContactSummary(clue.getContactSummary());
       contacts.setNextContactTime(clue.getNextContactTime());
       contacts.setAddress(clue.getAddress());
       contactsMapper.insertSelective(contacts);
   
       /* 把线索的备注信息转换到客户、联系人备注表中一份 */
       // 根据 clueId 查询 clueRemark 信息
       List<ClueRemark> clueRemarkList = clueRemarkMapper.selectByClueId(clueId);
       if (clueRemarkList!=null && clueRemarkList.size()>0) {
           CustomerRemark customerRemark;
           ContactsRemark contactsRemark;
           for (ClueRemark cr : clueRemarkList) {
               // 获取参数
               String id = cr.getId();
               String noteContent = cr.getNoteContent();
               String createBy = cr.getCreateBy();
               String createTime = cr.getCreateTime();
               String editBy = cr.getEditBy();
               String editTime = cr.getEditTime();
               String editFlag = cr.getEditFlag();
   
               // CustomerRemark
               customerRemark = new CustomerRemark();
               customerRemark.setId(id);
               customerRemark.setNoteContent(noteContent);
               customerRemark.setCreateBy(createBy);
               customerRemark.setCreateTime(createTime);
               customerRemark.setEditBy(editBy);
               customerRemark.setEditTime(editTime);
               customerRemark.setEditFlag(editFlag);
               customerRemark.setCustomerId(customer.getId());
               customerRemarkMapper.insert(customerRemark);
   
               // ContactsRemark
               contactsRemark = new ContactsRemark();
               contactsRemark.setId(id);
               contactsRemark.setNoteContent(noteContent);
               contactsRemark.setCreateBy(createBy);
               contactsRemark.setCreateTime(createTime);
               contactsRemark.setEditBy(editBy);
               contactsRemark.setEditTime(editTime);
               contactsRemark.setEditFlag(editFlag);
               contactsRemark.setContactsId(contacts.getId());
               contactsRemarkMapper.insert(contactsRemark);
           }
       }
   
       /* 把线索和市场活动的关联关系转换到联系人和市场活动的关联关系表中 */
       // 根据 clueId 查询 clueActivityRelationList
       List<ClueActivityRelation> clueARList = clueARMapper.selectByClueId(clueId);
       if (clueARList!=null && clueARList.size()>0) {
           ContactsActivityRelation conAR;
           for (ClueActivityRelation clueAR : clueARList) {
               conAR = new ContactsActivityRelation();
               conAR.setId(clueAR.getId());
               conAR.setActivityId(clueAR.getActivityId());
               conAR.setContactsId(contacts.getId());
               conARMapper.insert(conAR);
           }
       }
   
       /* 如果需要创建交易，还要往交易表中添加一条记录 */
       if ((boolean) map.get("isCreateTran")) {
           Transaction tran = (Transaction) map.get("transaction");
           tran.setId(UUIDUtils.getUUID());
           tran.setOwner(user.getId());
           tran.setCustomerId(customer.getId());
           tran.setContactsId(contacts.getId());
           tran.setCreateBy(user.getId());
           tran.setCreateTime(DateUtils.formatDateTime(new Date()));
           transactionMapper.insertSelective(tran);
   
           /* 如果需要创建交易，还要把线索的备注信息转换到交易备注表中一份 */
           // TransactionRemark
           if (clueRemarkList!=null && clueRemarkList.size()>0) {
               TransactionRemark tranRemark;
               for (ClueRemark cr : clueRemarkList) {
                   tranRemark = new TransactionRemark();
                   tranRemark.setId(cr.getId());
                   tranRemark.setNoteContent(cr.getNoteContent());
                   tranRemark.setCreateBy(cr.getCreateBy());
                   tranRemark.setCreateTime(cr.getCreateTime());
                   tranRemark.setEditBy(cr.getEditBy());
                   tranRemark.setEditTime(cr.getEditTime());
                   tranRemark.setEditFlag(cr.getEditFlag());
                   tranRemark.setTranId(tran.getId());
                   transactionRemarkMapper.insert(tranRemark);
               }
           }
       }
   
       /* 删除线索备注 */
       clueRemarkMapper.deleteByClueId(clueId);
       /* 删除线索和市场活动的关联关系 */
       clueARMapper.deleteByClueId(clueId);
       /* 删除线索 */
       clueMapper.deleteByPrimaryKey(clueId);
   }
   ```

   Controller

   ```java
   /**
    * 线索转换 - 获取线索信息、数据字典
    * @param clueId
    * @param request
    * @return
    */
   @RequestMapping("/convert")
   public String convert(String clueId, HttpServletRequest request) {
       // 查询需要动态显示的线索信息
       Clue clue = clueService.queryDetailById(clueId);
       // 查询交易阶段字典值
       List<DictionaryValue> stageList = dictionaryValueService.queryByTypeCode("stage");
   
       // 封装到 request 域中
       request.setAttribute("clue", clue);
       request.setAttribute("stageList", stageList);
   
       // 请求转发
       return "workbench/clue/convert";
   }
   
   /**
    * 线索转换 - 查询活动列表（仅已关联活动）
    * @param activityName
    * @param clueId
    * @return
    */
   @RequestMapping("/convert/activity")
   @ResponseBody
   public Object convertActivity(String activityName, String clueId) {
       Map<String, Object> map = new HashMap<>();
       map.put("activityName", activityName);
       map.put("clueId", clueId);
   
       return activityService.queryByNameAndClueIdOnlyBounded(map);
   }
   
   /**
    * 线索转换 - 业务执行
    * @param clueId
    * @param transaction
    * @param isCreateTran
    * @param session
    * @return
    */
   @RequestMapping("/convert/execute")
   @ResponseBody
   public Object convertExecute(String clueId, Transaction transaction, boolean isCreateTran, HttpSession session) {
       // 封装所需信息到 Map
       Map<String, Object> map = new HashMap<>();
       map.put("clueId", clueId);
       map.put("transaction", transaction);
       map.put("isCreateTran", isCreateTran);
       map.put(Constant.SESSION_USER, session.getAttribute(Constant.SESSION_USER));
   
       ReturnObject retObj = new ReturnObject(Constant.FAIL, "Server Busy");
       try {
           clueService.saveConvert(map);
   
           // 还没报异常则执行成功
           retObj.setCode(Constant.SUCCESS);
           retObj.setMsg("Success");
       } catch (Exception e) {
           e.printStackTrace();
       }
   
       return retObj;
   }
   ```

### 查询、创建交易

1. 需求分析

   用户在交易主页面，点击“创建”按钮，跳转到创建交易的页面

   用户在创建交易的页面填写表单，点击”保存”按钮，完成创建交易的功能

   - 所有者、阶段、类型、来源 都是动态的

   - 市场活动源是可搜索的

   - 联系人也是可搜索的

   - 可能性是可配置的

   - 客户名称支持自动补全

   - 表单验证

   - 保存成功之后，跳转到交易主页面

   - 保存失败，提示信息，页面不跳转

2. 流程设计

   ![查询、创建交易时序图](/images/image-project/crm/19-查询、创建交易时序图.png)

3. 技术准备

   - 可能性配置

     创建交易时选择“阶段”，“可能性”自动填充该阶段对应成交的百分比

     用户提供配置文件，配置每个阶段对应的可能性，显示到输入框

     1. 提供配置文件，保存在后台服务器

        possibility.properties

        ```properties
        资质审查=10
        需求分析=20
        价值建议=30
        确定决策者=50
        提案/报价=70
        谈判/复审=90
        成交=100
        丢失的线索=0
        因竞争丢失关闭=0
        ```

     2. 用户每次选择阶段，向后台发送请求

     3. 后台提供 Controller，接收请求，根据选择的阶段，解析配置文件，获取对应的可能性

     4. 把可能性返回前台，显示在输入框

   - jQuery find()

     找到某一标签下的子标签

     ```js
     var stageValue = $(this).find("option:selected").text();
     ```

   - Service 层传递 Map

     若要传递 Map 到 Service 层，则需要在 Map 形参定义前添加 `@RequestParam` 注解

     ```java
     public Object save(@RequestParam Map<String, Object> map){}
     ```

4. 编码实现

   Service

   ```java
   public int saveSelective(Transaction transaction, User user) {
       // 封装 transaction
       transaction.setId(UUIDUtils.getUUID());
       transaction.setCreateBy(user.getId());
       transaction.setCreateTime(DateUtils.formatDateTime(new Date()));
   
       // 调用 customerMapper 判断该公司名是否存在
       String customerId = customerMapper.selectIdByCustomerId(transaction.getCustomerId());
   
       if (customerId != null) {
           transaction.setCustomerId(customerId);
       } else {
           // 若查无此公司，则新建一个公司
           Customer customer = new Customer();
           customer.setId(UUIDUtils.getUUID());
           customer.setOwner(user.getId());
           customer.setName(transaction.getCustomerId());
           customer.setCreateBy(user.getId());
           customer.setCreateTime(DateUtils.formatDateTime(new Date()));
           customerMapper.insertSelective(customer);
   
           transaction.setCustomerId(customer.getId());
       }
   
       // 调用完成后，往 TransactionHistory 中添加一条数据
       TransactionHistory tranHistory = new TransactionHistory();
       tranHistory.setId(UUIDUtils.getUUID());
       tranHistory.setStage(transaction.getStage());
       tranHistory.setMoney(transaction.getMoney());
       tranHistory.setExpectedDate(transaction.getExpectedDate());
       tranHistory.setCreateTime(DateUtils.formatDateTime(new Date()));
       tranHistory.setCreateBy(user.getId());
       tranHistory.setTranId(transaction.getId());
       transactionHistoryMapper.insert(tranHistory);
   
       return transactionMapper.insertSelective(transaction);
   }
   ```
   
   Controller
   
   ```java
   /**
        * 保存 - 跳转到保存页面
        * @param request
        * @return
        */
   @RequestMapping("/save")
   public String save(HttpServletRequest request) {
       request.setAttribute("userList", userService.selectAll());
       request.setAttribute("stageList", dictionaryValueService.queryByTypeCode("stage"));
       request.setAttribute("typeList", dictionaryValueService.queryByTypeCode("transactionType"));
       request.setAttribute("sourceList", dictionaryValueService.queryByTypeCode("source"));
   
       return "workbench/transaction/save";
   }
   
   /**
        * 保存 - 获取阶段可能性
        * @param stageValue
        * @return
        */
   @RequestMapping("/save/getPossibilityByStage")
   @ResponseBody
   public Object getPossibilityByStage(String stageValue) {
       ResourceBundle bundle = ResourceBundle.getBundle("possibility");
       return bundle.getString(stageValue);
   }
   
   /**
        * 保存 - 自动补全公司名
        * @param name
        * @return
        */
   @RequestMapping("/save/queryAllCustomerName")
   @ResponseBody
   public Object queryAllCustomerName(String name) {
       return customerService.queryCustomerName(name);
   }
   
   /**
        * 保存 transaction
        * @param transaction
        * @param session
        * @return
        */
   @RequestMapping("/save/do")
   @ResponseBody
   public Object doSave(Transaction transaction, HttpSession session) {
       User user = (User) session.getAttribute(Constant.SESSION_USER);
   
       ReturnObject retObj = new ReturnObject(Constant.FAIL, "Server Busy");
       try {
           if (transactionService.saveSelective(transaction, user) > 0) {
               retObj.setCode(Constant.SUCCESS);
               retObj.setMsg("Success");
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
       return retObj;
   }
   ```

### 查看交易明细

1. 需求分析

   用户在交易主页面，点击交易名称超级链接，跳转到交易明细页面，完成查看交易明细的功能

   - 显示交易的基本信息

   - 显示交易的备注信息

   - 显示交易的历史信息

   - 显示交易的阶段图标信息

2. 流程设计

   ![查看交易明细时序图](/images/image-project/crm/20-查看交易明细时序图.png)

3. 技术准备

   - Java 中的实体类不止操作表，可自定义添加属性，附带传输其他数据

     > 数据库表在 Java 中一定有实体类相对应，数据库表字段在 Java 中一定有属性相对应

   - 显示交易阶段图标

     按照顺序查询交易所有的阶段：stageList

     遍历 stageList，显示每个阶段对应的图标，图标上显示的阶段名从遍历中取

4. 编码实现

   Controller

   ```java
   /**
    * 详情页面
    * @param id
    * @param request
    * @return
    */
   @RequestMapping("/detail")
   public String detail(String id, HttpServletRequest request) {
       Transaction tran = transactionService.queryDetailById(id);
       List<TransactionHistory> tranHistoryList = transactionHistoryService.queryDetailByTranId(id);
       // 从配置文件查询可能性
       ResourceBundle bundle = ResourceBundle.getBundle("possibility");
       // 调用数据字典值查询 stage
       List<DictionaryValue> stageList = dicValService.queryByTypeCode("stage");
   
       request.setAttribute("tran", tran);
       request.setAttribute("tranHistoryList", tranHistoryList);
       request.setAttribute("possibility", bundle.getString(tran.getStage()));
       request.setAttribute("stageList", stageList);
   
       return "workbench/transaction/detail";
   }
   ```

### 交易统计图表

1. 需求分析

   用户点击”交易统计图表”菜单，显示交易统计图表页面，以销售漏斗图的形式显示交易表中各个阶段的记录数量，完成查看交易统计图表的功能

2. 流程设计

   ![交易统计图表时序图](/images/image-project/crm/21-交易统计图表时序图.png)

3. 技术准备

   - 销售漏斗图

     展示商品销售数据、销售业绩
     
     > 报表插件：jfreechart，iReport，锐浪，echarts
     
     [echarts](https://echarts.apache.org/zh/tutorial.html) 的使用：
     
     1. 引入开发包 `echarts.min.js`（需 jQuery 包）
     2. 创建容器
     3. 当容器加载完成后，对容器调用工具函数
     
     示例代码
     
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <meta charset="utf-8">
         <title>第一个 ECharts 实例</title>
         <!-- 引入 echarts.js -->
         <script src="https://cdn.staticfile.org/echarts/4.3.0/echarts.min.js"></script>
     </head>
     <body>
         <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
         <div id="main" style="width: 600px;height:400px;"></div>
         <script type="text/javascript">
             // 基于准备好的dom，初始化echarts实例
             var myChart = echarts.init(document.getElementById('main'));
      
             // 指定图表的配置项和数据
             var option = {
                 title: {
                     text: '第一个 ECharts 实例'
                 },
                 tooltip: {},
                 legend: {
                     data:['销量']
                 },
                 xAxis: {
                     data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
                 },
                 yAxis: {},
                 series: [{
                     name: '销量',
                     type: 'bar',
                     data: [5, 20, 36, 10, 10, 20]
                 }]
             };
      
             // 使用刚指定的配置项和数据显示图表。
             myChart.setOption(option);
         </script>
     </body>
     </html>
     ```

4. 编码实现

   index.jsp

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%
       String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + request.getContextPath() + "/";
   %>
   <!DOCTYPE html>
   <html>
   <head>
       <base href="<%=basePath%>">
       <meta charset="UTF-8">
       <script type="text/javascript" src="jquery/jquery-1.11.1-min.js"></script>
       <script type="text/javascript" src="jquery/echars/echarts.min.js"></script>
       <script type="text/javascript">
           $(function () {
               $.ajax({
                   url: 'workbench/chart/transaction/queryCountOfTranGroupByStage?t=' + new Date().valueOf(),
                   type: 'get',
                   dataType: 'json',
                   success: function (res) {
                       // 基于准备好的dom，初始化 ECharts 实例
                       var myChart = echarts.init($("#main")[0]);
   
                       // 指定图表的配置项和数据
                       var option = {
                           title: {
                               text: '交易统计图表',
                               subtext: '交易表中各个阶段的数量'
                           },
                           tooltip: {
                               trigger: 'item',
                               formatter: "{a} <br/>{b} : {c}"
                           },
                           toolbox: {
                               feature: {
                                   dataView: {readOnly: false},
                                   restore: {},
                                   saveAsImage: {}
                               }
                           },
                           series: [
                               {
                                   name: '数据量',
                                   type: 'funnel',
                                   left: '10%',
                                   width: '80%',
                                   label: {
                                       formatter: '{b}'
                                   },
                                   labelLine: {
                                       show: true
                                   },
                                   itemStyle: {
                                       opacity: 0.7
                                   },
                                   emphasis: {
                                       label: {
                                           position: 'inside',
                                           formatter: '{b}: {c}'
                                       }
                                   },
                                   data: res
                               }
                           ]
                       };
   
                       // 使用刚指定的配置项和数据显示图表。
                       myChart.setOption(option);
                   }
               })
           });
       </script>
   </head>
   <body>
   <!-- 为 ECharts 准备一个具备大小（宽高）的 Dom -->
   <div id="main" style="width: 600px;height:400px;"></div>
   </body>
   ```
