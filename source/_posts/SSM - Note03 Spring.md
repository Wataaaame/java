---
title: SSM - Note03 Spring
date: 2022-08-25 15:15:48
categories: SSM
tags: Spring
---

# Spring

是一个容器，整合其他框架的框架，核心是 IOC 和 AOP，由 20 多个模块构成，在很多领域都提供优秀的解决方案

## 特点

1. 轻量级

   由 20 多个模块构成，对代码无污染

2. 面向切面编程

   使用接口就是面向灵活，项目可扩展性可维护性极高

3. AOP 面向切面编程

   将公共、通用、重复的代码单独开发，在需要的时候反织回去，底层原理是动态代理

4. 整合其他框架

   使其他框架更易用

# *IOC 控制反转

IoC（Inversion of Control）是一个概念、一种思想，由 Spring 容器进行对象的创建和依赖注入，程序员在使用时直接取出使用

- 正转：由程序员进行对象的创建和依赖注入

  ```java
  Student stu = new Student();	// 程序员创建对象
  sut.setName("vv");				// 程序员进行赋值
  stu.setAge(22);
  ```

- 反转：由 Spring 容器创建对象和依赖注入

  ```xml
  <!-- Sprint 创建对象 -->
  <bean id="stu" class="github.wataaaame.bean.Stuent">
      <!-- Sprint 进行赋值 -->
  	<property name="name" value="vv"></property>
      <property name"age" value="22"></property>
  </bean>
  ```

## XML 创建对象

添加依赖文件：

pom.xml

```xml
<!-- Spring -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>
```

Student 实体类：

Student.java

```java
package github.wataaaame.bean;

public class Student {
    private String name;
    private int age;

    public Student() {
        System.out.println("no value executed");
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

Resources 新建一个 `XML Configuration File -> Spring Config` 文件

创建好 Student 实体类后在此文件中创建对象：

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 创建学生对象
        等同于 Student stu = new Student();
        id：创建对象的名称
        class：创建对象的类型，底层通过反射构建
     -->
    <bean id="stu" class="github.wataaaame.bean.Student"></bean>
</beans>
```

使用 Spring 反转创建对象：

Test.java

```java
@Test
public void testStudentSpring() {
    // 若想从 Spring 容器中取出对象，则要先创建容器对象并启动
    // 启动容器的同时创建对象
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 取出对象
    Student student = (Student) ac.getBean("stu");
    System.out.println(student);
}
```

### 对象赋值

1. 使用 Setter 注入

   必须提供 Setter 方法

   1. 简单类型注入：使用 value 属性
   2. 引用类型注入：使用 ref 属性（引用类位置无影响）

   applicationContext.xml

   ```xml
   <bean id="stu" class="github.wataaaame.bean.Student">
       <!-- 使用 Setter 注入值 -->
       <property name="name" value="vv"></property>
       <property name="age" value="22"></property>
   </bean>
   ```

2. 使用构造方法注入

   必须提供带参构造方法

   1. 使用构造方法的参数名称注入

      ```xml
      <bean id="school" class="github.wataaaame.bean.School">
      	<constructor-arg name="name" value="WUT"></constructor-arg>
          <constructor-arg name="address" value="Jiangxia Street"></constructor-arg>
      </bean>
      ```

   2. 使用构造方法参数下标注入

      ```xml
      <bean id="school" class="github.wataaaame.bean.School">
      	<constructor-arg index="0" value="WUT"></constructor-arg>
          <constructor-arg index="1" value="Jiangxia Street"></constructor-arg>
      </bean>
      ```

   3. 使用默认的构造方法参数顺序注入

      ```xml
      <bean id="school" class="github.wataaaame.bean.School">
      	<constructor-arg value="WUT"></constructor-arg>
          <constructor-arg value="Jiangxia Street"></constructor-arg>
      </bean>
      ```

### 引用类型自动注入

在 XML 文件中注入引用类型时，可以在标签中声明 autowire 属性来根据条件匹配引用类型对象，从而代替引用类型标签

```xml
<!-- 创建学校对象 -->
<bean id="school" class="github.wataaaame.bean.School">
	<property name="name" value="WUT"></property>
    <property name="address" value="Jiangxia"></property>
</bean>

<!-- 创建学生对象 -->
<!-- autowire 引用类型自动注入
	byType：通过类型注入，类似于 @Autowire
	byName：通过名称注入，类似于 @Autowired + @Qualifier
-->
<bean id="student" class="github.wataaaame.bean.Student" autowire="byType">
	<property name="name" value="vv"></property>
    <property name="age" value="22"></property>
    <!-- <property name="school" ref="school"></property> -->
</bean>
```

### Example

使用**三层架构**进行用户的插入操作

界面层、业务逻辑层、数据访问层（模拟）

> 除实体类的创建，Spring 接管了整个三层架构

#### 非 Spring 接管

- 实体类

  `github.wataaaame.bean` User

  ```java
  package github.wataaaame.bean;
  
  public class User {
      private int uid;
      private String uname;
      private int uage;
  
      public User() {
      }
  
      public User(int uid, String uname, int uage) {
          this.uid = uid;
          this.uname = uname;
          this.uage = uage;
      }
  
      public int getUid() {
          return uid;
      }
  
      public void setUid(int uid) {
          this.uid = uid;
      }
  
      public String getUname() {
          return uname;
      }
  
      public void setUname(String uname) {
          this.uname = uname;
      }
  
      public int getUage() {
          return uage;
      }
  
      public void setUage(int uage) {
          this.uage = uage;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "uid=" + uid +
                  ", uname='" + uname + '\'' +
                  ", uage=" + uage +
                  '}';
      }
  }
  ```

- 数据访问层

  `github.wataaaame.dao`

  UserMapper.java（接口）

  ```java
  package github.wataaaame.dao;
  
  import github.wataaaame.bean.User;
  
  public interface UserMapper {
      int insert(User user);
  }
  ```

  UserMapperImpl.java（实现类）（模拟）

  ```java
  package github.wataaaame.dao;
  
  import github.wataaaame.bean.User;
  
  /**
   * 数据访问层的实现类
   */
  public class UserMapperImpl implements UserMapper {
      @Override
      public int insert(User user) {
          System.out.println(user.getUname() + " has inserted");
          return 1;
      }
  }
  ```

- 业务逻辑层

  `github.wataaaame.service`

  UserService.java（接口）

  ```java
  package github.wataaaame.service;
  
  import github.wataaaame.bean.User;
  
  public interface UserService {
      int insert(User user);
  }
  ```

  UserServiceImpl.java（实现类）

  ```java
  package github.wataaaame.service.impl;
  
  import github.wataaaame.bean.User;
  import github.wataaaame.dao.UserMapper;
  import github.wataaaame.dao.UserMapperImpl;
  import github.wataaaame.service.UserService;
  
  /**
   * 业务逻辑层的实现类
   */
  public class UserServiceImpl implements UserService {
      // 切记：所有业务逻辑层都必定有数据访问层的对象，通过该对象交流
      private UserMapper userMapper = new UserMapperImpl();
  
      @Override
      public int insert(User user) {
          return userMapper.insert(user);
      }
  }
  ```

- 界面层

  `github.wataaaame.controller`

  UserController.java

  ```java
  package github.wataaaame.controller;
  
  import github.wataaaame.bean.User;
  import github.wataaaame.service.UserService;
  import github.wataaaame.service.impl.UserServiceImpl;
  
  /**
   * 界面层
   */
  public class UserController {
      // 创建业务逻辑层对象供页面层访问
      UserService userService = new UserServiceImpl();
  
      // 界面层的功能实现，对外提供访问的功能
      public int insert(User user) {
          return userService.insert(user);
      }
  }
  ```

#### Spring 接管

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 创建各种对象 -->
    <!-- 创建数据访问层对象 -->
    <bean id="userMapper" class="github.wataaaame.dao.UserMapperImpl"></bean>
    <!-- 创建业务逻辑层对象 -->
    <bean id="userService" class="github.wataaaame.service.impl.UserServiceImpl">
        <property name="userMapper" ref="userMapper"></property>
    </bean>
    <!-- 创建界面层对象 -->
    <bean id="userController" class="github.wataaaame.controller.UserController">
        <property name="userService" ref="userService"></property>
    </bean>
</beans>
```

取消各个实现类中手动创建的对象

Test.java

```java
@Test
public void testInsert() {
    // 创建容器并启动
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");

    // 取出对象
    UserController userController = (UserController) applicationContext.getBean("userController");

    // 测试功能
    System.out.println(userController.insert(new User(101, "vv", 22)));
}
```

## 基于注解的 IOC

也成为 DI（Dependency Injection），依赖注入，它是 IOC 的具体实现技术

**必须在 Spring 核心配置文件中添加包扫描**

1. **单个包扫描**

   ```xml
   <!-- 添加包扫描 -->
   <context:component-scan base-package="github.wataaaame.bean1"></context:component-scan>
   ```

2. 可以使用逗号分隔多个包

3. *扫描根包*

### 创建对象注解

- @Component：可以创建任意对象

  创建对象的默认名称是类名的驼峰命名法，也可以指定对象的名称 `@Component("stu")`

  ```java
  @Component
  public class Student {
      private String name;
      private int age;
  
      public Student() {
          System.out.println("Student's no param method execute");
      }
  
      @Override
      public String toString() {
          return "Student{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  '}';
      }
  }
  ```

- @Controller：专门用来创建控制器的对象（Servlet），这种对象可以接受用户请求、返回处理结果给客户端

- @Service：专门用来创建业务逻辑层对象，负责向下访问数据访问层，处理完毕后的结果返回给界面层

- @Repository：专门用来创建数据库访问层对象，负责数据库中的增删改查所有操作

### 依赖注入注解

#### 值类型注入

- @Value：用来给简单类型注入值

  ```java
  @Value("vv")
  private String name;
  @Value("22")
  private int age;
  ```

#### 引用类型注入

> 注入条件：
>
> 1. 同源类型（类型相同）
>
>    同源类型会自动在 Bean 工厂中找到相应对象
>
> 2. 父子类型
>
>    子类创建默认会调用父类无参构造方法
>
>    **直接按名称进行注入值**
>
> 3. 接口实现

- @Autowired：使用类型注入值，从整个 Bean 工厂中搜索同源类型的对象进行注入

  ```java
  // 同源类型
  @Autowired
  private School school;
  ```

  ```java
  // 父子类型
  @Autowired
  private School school;
  ```

  - 按照对象名称进行二次筛选，选中与被注入对象相同名称的对象进行注入（名称可在创建对象时指定）

- @Autowired + @Qualifier：使用名称注入值，从整个 Bean 工厂中搜索相同名称的对象进行注入

  ```java
  // 同源类型
  @Autowired
  @Qualifier("school")
  private School school;
  ```

  ```java
  // 父子类型
  @Autowired
  @Qualifier("subSchool")
  private School school;
  ```

  - **直接使用名称挑选指定对象**

## 配置文件拆分

1. 按层拆

   applicationContext_controller.xml

   ```xml
   <bean id="uController" class="github.wataaaame.controller.UserController"></bean>
   <bean id="bController" class="github.wataaaame.controller.BookController"></bean>
   ```

   applicationContext_service.xml

   ```xml
   <bean id="uService" class="github.wataaaame.controller.UserService"></bean>
   <bean id="bService" class="github.wataaaame.controller.BookService"></bean>
   ```

   applicationContext_mapper.xml

   ```xml
   <bean id="uMapper" class="github.wataaaame.controller.UserMapper"></bean>
   <bean id="bMapper" class="github.wataaaame.controller.BookMapper"></bean>
   ```

2. 按功能拆

   applicationContext_user.xml

   ```xml
   <bean id="uController" class="github.wataaaame.controller.UserController"></bean>
   <bean id="uService" class="github.wataaaame.service.UserService"></bean>
   <bean id="uMapper" class="github.wataaaame.mapper.UserMapper"></bean>
   ```

   applicationContext_book.xml

   ```xml
   <bean id="bController" class="github.wataaaame.controller.BookController"></bean>
   <bean id="bService" class="github.wataaaame.service.BookService"></bean>
   <bean id="bMapper" class="github.wataaaame.mapper.BookMapper"></bean>
   ```

## 配置文件合并

1. 单条导入

   ```xml
   <!-- 单个文件导入 -->
   <import resource="applicationContext_mapper.xml"></import>
   <import resource="applicationContext_service.xml"></import>
   <import resource="applicationContext_controller.xml"></import>
   ```

2. 批量导入

   支持星号

   ```xml
   <!-- 批量导入 -->
   <import resource="applicationContext_*.xml"></import>
   ```

# *AOP 面向切面编程

AOP（Aspect Orient Programming），面向切面编程

公共的、通用的、重复的功能成为切面，面向切面编程就是将切面提取出来，单独开发，在需要调用的方法中通过动态代理的方式进行置入

## Example

手写 AOP 框架

业务：图书购买业务

切面：事务

- 第一个版本：业务和切面紧耦合在一起，没有拆分
- 第二个版本：使用**子类代理**拆分业务和切面
- 第三个版本：使用静态代理拆分业务和切面，业务和业务接口已拆分，此时切面紧耦合在业务中
- 第四个版本：使用静态代理拆分业务和业务接口，切面和切面接口
- 第五个版本：使用动态代理完成第四个版本的优化

## Spring 支持的 AOP

Spring 支持 AOP 的编程，常用的有以下几种

1. Before 通知

   `org.springframework.aop.MethodBeforeAdvice`

   在目标方法被调用前调用

2. After 通知

   `org.springframework.aop.AfterReturningAdvice`

   在目标方法被调用后调用

3. Throws 通知

   `org.springframework.aop.ThrowsAdvice`

   目标方法抛出异常时调用

4. Around 通知

   `org.aopalliance.intercept.MethodInterceptor`

   拦截对目标对象方法调用

## 常用术语

1. 切面：重复的、公共的、通用的功能，例如：日志、事务、权限
2. 连接点：就是目标方法，因为在目标方法中要实现目标方法的功能和切面功能
3. 切入点（Pointcut）：指定切入位置，多个连接点构成切入点，可以是一个目标方法；可以是一个类中所有方法；可以是某个包下所有类中的方法
4. 目标对象：操作谁，谁就是目标对象
5. 通知（Advice）：指定切入时机，在目标方法执行前、执行后、出错时、环绕目标方法切入切面功能

## AspectJ 框架

AspectJ 是一个优秀面向切面的框架，扩展了 Java 语言，提供了强大的切面实现

### 常用通知类型

1. 前置通知：@Before
2. 后置通知：@AfterReturning
3. 环绕通知：@Around
4. 最终通知：@After
5. *定义切入点：@Pointcut*

通知执行顺序为：`环绕前置 —> 前置 -> 业务 -> 环绕后置 -> 最终 -> 后置 -> 目标返回值`

### 切入点表达式

规范公式：`execution(访问权限 方法返回值 方法声明(参数) 异常类型)`

简化公式：`execution(方法返回值 方法声明(参数))`

用到的符号：

- `*`：通配符（任意个任意字符）

- `..`：若出现在方法参数中，则代表任意参数

  若出现在路径中，则代表本路径及其所有子路径

示例：

1. `execution(public * *(..))`

   任意公共方法

2. `execution(* set*(..))`

   任何一个以 set 开始的方法

3. `execution(* com.xyz.service.impl.*.*(..))`

   定义在包内任意类的任意方法

4. `execution(* com.xyz.service.impl..*.*(..))`

   定义在包内所有子路径的任意类的任意方法

5. `execution(* *..service.*.*(..))`

   service 之前可以有任意包

6. `execution(* *.service.*.*(..))`

   service 之前只有一个包

### 前置通知

在目标方法执行前切入切面功能，切面方法中不可以获得目标方法的返回值，只能得到目标方法的签名（除方法体）

前置通知的切面方法规范：

1. 访问权限是 public

2. 方法的返回值是 void

3. 方法名称自定义

4. 方法没有参数，有也只能是 JoinPoint 类型

5. 必须使用 @Before 注解声明切入时机为前切和切入点

6. 参数：

   value：指定切入点表达式

实现步骤：

1. 添加依赖

   ```xml
   <dependency>
   	<groupId>org.springframework</groupId>
       <artifactId>spring-aspects</artifactId>
       <version>5.2.5.RELEASE</version>
   </dependency>
   ```

2. 创建业务接口

   ```java
   package github.wataaaame.s1;
   
   public interface SomeService {
       String doSome(String name, int age);
   }
   ```

3. 创建业务实现

   ```java
   package github.wataaaame.s1;
   
   public class SomeServiceImpl implements SomeService {
       @Override
       public String doSome(String name, int age) {
           return "abcd";
       }
   }
   ```

4. 创建切面类

   ```java
   package github.wataaaame.s1;
   
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   
   /**
    * 此类为切面类，包含各种切面方法
    */
   @Aspect
   public class MyAspect {
       /**
        * 所有切面功能都是由切面方法实现的
        * 可以将各种切面都在此类中进行开发
        *
        * public String doSome(String name, int age)
        */
       @Before(value="execution(public String github.wataaaame.s1.SomeServiceImpl.doSome(String, int))")
       public void myBefore() {
           System.out.println("切面方法中的前置通知功能实现...");
       }
   }
   ```

5. 在 applicationContext.xml 中进行切面绑定

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
   
       <!-- 创建业务对象 -->
       <bean id="someService" class="github.wataaaame.s1.SomeServiceImpl"></bean>
       <!-- 创建切面对象 -->
       <bean id="myAspect" class="github.wataaaame.s1.MyAspect"></bean>
       <!-- 绑定 -->
       <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
   </beans>
   ```

可以使用 注解的方式代替 XML 创建对象

前置方法中可以添加参数 `JoinPoint jp`

- jp.getSingnature()：获取签名

- jp.getArgs()：获取参数

  可通过 `Arrays.toString(jp.getArgs())` 打印参数列表

> 切换 JDK、CGLib 动态代理：
>
> ```xml
> <!-- 默认是 JDK 动态代理，取时必须使用接口类型 -->
> <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
> <!-- 设置为 CGLib 子类代理，可以使用接口或实现类（不推荐） -->
> <aop:aspectj-autoproxy proxy-target-class="true"></aop:aspectj-autoproxy>
> ```

### 后置通知

后置通知的方法规范：

1. 访问权限是 public

2. 方法没有返回值

3. 方法名称自定义

4. 方法有参数（接收目标方法的返回值，目标方法无返回值可以无参，写上则两种情况皆可处理）

5. 使用 `@AfterReturning` 注解

6. 参数：

   value：指定切入点表达式

   returning：指定目标方法的返回值名称（必须与方法的参数名称一致）

如果目标方法返回值是 8 种基本类型或 String 则后置通知方法无法改变输出结果，其余类型会改变（参考指针原理）

### 环绕通知

通过拦截目标方法的方式，在目标方法前后增强功能的通知

它是功能最强大的通知，一般事务使用此通知，可轻易地改变目标方法的返回值

环绕方法参数为目标方法，返回值为目标方法返回值

环绕通知方法规范：

1. 访问权限是 public

2. 有返回值，此返回值为目标方法的返回值

3. 方法名称自定义

4. 方法有参数，此参数为目标方法

   参数类型：`ProceedingJoinPoint`

5. 回避异常

6. 使用 @Around 注解声明

7. 参数：

   value：指定切入点表达式

调用目标方法：

- 使用 `.proceed(pjp.getArgs())`
- getArgs() 获取目标方法参数

### 最终通知

无论目标方法是否正常执行，最终通知的代码都会被执行

最终通知方法规范：

1. 访问权限是 public

2. 方法没有返回值

3. 方法名称自定义

4. 方法没有参数，如果有只能是 JoinPoint

5. 使用 `@After` 注解表明是最终通知

6. 参数：

   value：指定切入点表达式

### 定义切入点

如果多个切面切入到同一个切入点，可以使用别名简化开发

使用 `@Pointcut` 注解创建一个空方法，此方法的名称就是别名

# Spring & MyBaits

1. 创建数据库表：

   ```mysql
   use ssm;
   
   drop table if exists user, student;
   
   create table user(
   	uid int primary key,
       uname varchar(255),
       upwd varchar(255)
   );
   
   create table account(
   	aid int primary key,
       aname varchar(255),
       actt varchar(255)
   );
   
   select uid, uname, upwd from user;
   select aid, aname, actt from account;
   ```

2. 新建项目，使用 quickstart 模板

3. 修改目录

4. 修改 pom.xml 文件，添加相关依赖（模板），添加数据库可视化

5. 添加 MyBatis 相应模板

   点击 `File -> Settings... -> Editor -> File and Code Templates`，点击 `+` 号添加模板文件

   SqlMapConfig 模板

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <!-- 读取属性文件中数据库的配置 -->
       <properties resource="db.properties"></properties>
       <!-- 设置日志输出语句，显示相应操作的 sql -->
       <settings>
           <setting name="logImpl" value="STDOUT_LOGGING"/>
       </settings>
       <typeAliases>
           <package name="github.wataaaame.bean"/>
       </typeAliases>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"></transactionManager>
               <dataSource type="POOLED">
                   <property name="driver" value="github.wataaaame.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/ssm?useSSL=false&amp;serverTimezone=UTC&amp;
                   allowPublicKeyRetrieval=true"/>
                   <property name="username" value="root"/>
                   <property name="password" value="0.0"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
           <package name="mapper 文件所在的包名"/>
       </mappers>
   </configuration>
   ```

   Mapper 模板

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="接口完全限定名">
       
   </mapper>
   ```

6. 添加 MyBatis 核心配置文件（SqlMapConfig.xml）

   其中大部分配置被 spring 框架接管

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <!-- 读取属性文件中数据库的配置 -->
   <!--    <properties resource="db.properties"></properties>-->
       <!-- 设置日志输出语句，显示相应操作的 sql -->
       <settings>
           <setting name="logImpl" value="STDOUT_LOGGING"/>
       </settings>
   <!--    <typeAliases>-->
   <!--        <package name="github.wataaaame.bean"/>-->
   <!--    </typeAliases>-->
   <!--    <environments default="development">-->
   <!--        <environment id="development">-->
   <!--            <transactionManager type="JDBC"></transactionManager>-->
   <!--            <dataSource type="POOLED">-->
   <!--                <property name="driver" value="github.wataaaame.jdbc.Driver"/>-->
   <!--                <property name="url" value="jdbc:mysql://localhost:3306/ssm?useSSL=false&amp;serverTimezone=UTC&amp;-->
   <!--                allowPublicKeyRetrieval=true"/>-->
   <!--                <property name="username" value="root"/>-->
   <!--                <property name="password" value="0.0"/>-->
   <!--            </dataSource>-->
   <!--        </environment>-->
   <!--    </environments>-->
   <!--    <mappers>-->
   <!--        <package name="mapper 文件所在的包名"/>-->
   <!--    </mappers>-->
   </configuration>
   ```

7. 添加 jdbc.properties 文件

   ```properties
   jdbc.driverClassName=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql://localhost:3306/ssm?useUnicode=true&characterEncoding=utf8
   jdbc.username=root
   jdbc.password=0.0
   ```

8. 添加 applicationContext_mapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!-- 读取属性文件 jdbc.properties -->
       <context:property-placeholder location="jdbc.properties"></context:property-placeholder>
       <!-- 创建数据源 -->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
           <property name="driverClassName" value="${jdbc.driverClassName}"></property>
           <property name="url" value="${jdbc.url}"></property>
           <property name="username" value="${jdbc.username}"></property>
           <property name="password" value="${jdbc.password}"></property>
       </bean>
       <!-- 配置 SqlSessionFactoryBean 类 -->
       <bean class="org.mybatis.spring.SqlSessionFactoryBean">
           <!-- 配置数据源 -->
           <property name="dataSource" ref="dataSource"></property>
           <!-- 配置 MyBatis 的核心配置文件 -->
           <property name="configLocation" value="SqlMapConfig.xml"></property>
           <!-- 注册实体类别名 -->
           <property name="typeAliasesPackage" value="github.wataaaame.bean"></property>
       </bean>
       <!-- 注册 mapper.xml 文件 -->
       <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
           <property name="basePackage" value="github.wataaaame.mapper"></property>
       </bean>
   </beans>
   ```

9. 添加 applicationContext_service.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!-- SM 是基于注解式开发，需要添加包扫描 -->
       <context:component-scan base-package="github.wataaaame.service"></context:component-scan>
       <!-- 事务处理 -->
   </beans>
   ```

9. 添加 User 实体类，Account 实体类

10. 添加 mapper 包，添加 UserMapper 接口和 UserMapper.xml 并开发

11. 添加 service 包，添加 UserService 接口和 UserServiceImpl 实现类

12. 添加测试类进行功能测试

## 添加事务回滚

1. 在 applicationContext_service.xml 文件中添加配置信息：

   ```xml
   <!-- 事务处理 -->
   <!-- 1. 添加事务管理器 -->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <!-- 事务需关联数据库处理，所以需要配置数据源 -->
       <property name="dataSource" ref="dataSource"></property>
   </bean>
   <!-- 2. 添加事务的注解驱动 -->
   <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
   ```

2. 在对应的 ServiceImpl 中添加**增删改**的事务传播注解：

   ```java
   // 配置事务传播特性
   @Transactional(propagation = Propagation.REQUIRED)
   ```

   - 若想取消特定异常回滚，则添加以下字段：

     ```java
     // 指定某异常名不回滚
     
     // 使用异常名（多个使用大括号）
     @Transactional(propagation = Propagation.REQUIRED, noRollbackForClassName="ArithmeticException")
     
     // 使用异常类型
     @Transactional(propagation = Propagation.REQUIRED, noRollbackFor = ArithmeticException.class)
     ```

   - 若想指定自定义异常回滚

     ```java
     // 指定某异常名必须回滚
     
     // 使用异常名（多个使用大括号）
     @Transactional(propagation = Propagation.REQUIRED, RollbackForClassName="ArithmeticException")
     
     // 使用异常类型
     @Transactional(propagation = Propagation.REQUIRED, RollbackFor = ArithmeticException.class)
     ```

   - timeout = -1，代表永不超时

   - readOnly = false，若为查询操作，必须设置为 true

   - isolation = Isolation.DEFAULT，使用对应数据库默认的隔离级别

### 事务处理方式

1. 注解式事务

   使用 `@Transactional` 注解完成事务控制，可添加到类或方法，添加到类对所有方法生效，存在弊端

   以上示例即为注解式事务

2. **声明式事务**

   配置文件中添加一次，整个项目遵循此配置

### 事务隔离级别

Spring 中事务的隔离级别在 [数据库的四大隔离级别](https://wataaaame.github.io/2022/05/11/MySQL%20-%20Note03%20Storage%20Engine,%20Transaction,%20Index%20and%20View/#事务的隔离性) 中多出一条：使用数据库默认的隔离级别 `isolation = Isolation.DEFAULT`

### 事务管理器

用来生成相应技术的连接与执行语句的对象

- JDBC -> Connection
- MyBatis -> SqlSession
- Hibernate -> Session

如果使用 MyBatis 框架，必须使用 DataSourceTransactionManager 类完成处理

```xml
<!-- 1. 添加事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!-- 事务需关联数据库处理，所以需要配置数据源 -->
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

### 事务传播特性

多个事务之间的合并、互斥等关系都可以通过设置事务的传播特性来解决

常用：

1. PROPAGATION_REQUIRED

   必被包含事务（**增删改**必用）

2. PROPAGATION_REQUIRES_NEW

   自己新开事务，不管之前是否有事务

3. PROPAGATION_SUPPORTS

   如果加入的方法有事务，则支持事务

4. PROPAGATION_NEVER

   不能运行在事务中，否则抛异常

5. PROPAGATION_NOT_SUPPORTED

   不支持事务，运行在非事务的环境

不常用：

1. PROPAGATION_MANDATORY

   必须包在事务中，没有事务则抛异常

2. PROPAGATION_NESTED

   嵌套事务

项目中的所有事务，必须添加到业务逻辑层上

### 八种事务场景

| UserServiceImpl | AccountServiceImpl(Exception) |    Result     |
| :-------------: | :---------------------------: | :-----------: |
|      none       |             none              | user, account |
|      none       |           REQUIRED            |   **user**    |
|    REQUIRED     |             none              |               |
|    REQUIRED     |         NOT_SUPPORTED         |    account    |
|    REQUIRED     |           SUPPORTS            |               |
|    REQUIRED     |         REQUIRES_NEW          |               |
|    REQUIRED     |           REQUIRED            |               |
|    REQUIRED     |             NEVER             |               |

### 声明式事务

要求项目中的方法命名有规范

1. 增加操作：add、save、insert、set
2. 更新操作：update、change、modify
3. 删除操作：delete、drop、remove、clear
4. 查询操作：select、find、search、get

配置事务切面时可以使用通配符 * 来匹配所有方法

applicationContext_transaction.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 此配置文件与 applicationContext_service.xml 功能一样，只是事务配置不同 -->

    <!-- 导入 applicationContext_mapper.xml -->
    <import resource="applicationContext_mapper.xml"></import>
    <!-- 添加包扫描 -->
    <context:component-scan base-package="github.wataaaame.service.impl"></context:component-scan>
    <!-- 添加事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 配置事务切面 -->
    <tx:advice id="myAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*select*" read-only="true"/>
            <tx:method name="*insert*" propagation="REQUIRED" />
            <tx:method name="*" propagation="SUPPORTS" />
        </tx:attributes>
    </tx:advice>
    <!-- 绑定切面和切入点 -->
    <aop:config>
        <aop:pointcut id="myPointcut" expression="execution(* github.wataaaame.service.impl.*.*(..))"/>
        <aop:advisor advice-ref="myAdvice" pointcut-ref="myPointcut"></aop:advisor>
    </aop:config>
</beans>
```

可以在 aop:advisor 标签中添加 order 字段设置 声明式/注解式 优先级