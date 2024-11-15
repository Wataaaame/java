---
title: JDBC - Note
date: 2022-07-18 11:16:59
categories: JDBC
tags: Note
---

# JDBC

Java DataBase Connectivity（Java 语言连接数据库）

本质是 SUN 公司制定的一套接口

`java.sql.*`

![JDBC底层原理](/images/image-jdbc/note-01-JDBC底层原理.PNG)

## Before Start

从官网下载对应的驱动 jar 包，然后配置到环境变量 classpath 中

将 jar 包存在自定的 tool 文件，其中 `;.` 代表当前目录所有文件

```
.;C:\vv\code\Java\Tool\mysql-connector-java-5.1.23.jar
```

- 以上配置是针对文本编辑器开发，使用 IDEA 不需要配置以上环境变量（直接导入 jar 包即可）

## Six Step

1. 注册驱动（告诉 Java 程序，即将连接的是哪一种数据库）
2. 获取连接（打开 JVM 进程与数据库**进程**之间的通道）
3. 获取数据库操作对象（专门执行 SQL 语句的对象）
4. 执行 SQL 语句（DQL DML 等）
5. 处理查询结果集（只有第四步执行的是 DQL 时才有查询结果）
6. 释放资源（使用完需关闭进程）

通过 Java 执行 insert 语句：

```java
package io.wataaaame.github.jdbc;

import java.sql.*;

public class JDBCTest01 {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;

        try {
            // 1. 注册驱动
            // 多态，父类引用指向子类型对象
            Driver driver = new com.mysql.jdbc.Driver();
            // Oracle 创建对象
            // new oracle.jdbc.driver.OracleDriver();
            DriverManager.registerDriver(driver);

            // 2. 获取连接
            /*
                url：统一资源定位符（网络中某个资源的绝对路径）
                例如：https://wataaaame.github.io
                https://185.199.109.153:80/index.html

                url 包含：
                    通信协议：https（通信前提前定好的数据传输格式）
                    服务器IP：185.199.109.153
                    服务器软件：PORT：80
                    资源名：index.html
             */
            // 127.0.0.1 和 localhost 都是指本机 IP
            String url = "jdbc:mysql://127.0.0.1:3306/learning";
            // Oracle url：
            // jdbc:oracle:thin:@localhost:1521:orcl
            String user = "root";
            String psw = "0.0";
            conn = DriverManager.getConnection(url, user, psw);
//            System.out.println(conn);

            // 3. 获取数据库操作对象
            // Statement 用于执行 sql 语句
            stmt = conn.createStatement();

            // 4. 执行 sql
            // JDBC 中的 SQL 语句不需要写分号
            String sql = "insert into emp1(empno, ename) values(101, 'vv')";
            // executeUpdate() 专门执行 DML 语句（insert, delete, update）
            // 返回值 int 是“影响数据库中的记录条数”
            int count = stmt.executeUpdate(sql);
            System.out.println(count == 1 ? "success" : "false");

            // 5. 处理查询结果集
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 6. 释放资源
            // 由内而外依次关闭，且分别对其 try...catch...
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
    }
}
```

注册驱动的另一种方式（常用）：

```java
package io.wataaaame.github.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class JDBCTest03 {
    public static void main(String[] args) {
        Connection conn = null;

        try {
            // 1. 注册驱动
            // 旧版写法
//            DriverManager.registerDriver(new com.mysql.jdbc.Driver());
            // 新版写法
            // Class.forName() 执行能够使 com.mysql.jdbc.Driver 中的无参构造方法自动加载
            // 以下方法不需要接受返回值，只需要利用其类加载动作
            Class.forName("com.mysql.jdbc.Driver");

            // 2. 获取连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");
            System.out.println(conn);
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

将连接数据库的所有信息配置到配置文件中：

```java
package io.wataaaame.github.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ResourceBundle;

public class JDBCTest04 {
    public static void main(String[] args) {
        // 使用资源绑定器绑定属性配置文件
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        // 获取配置文件中的 String
        String driver = bundle.getString("driver");
        String url = bundle.getString("url");
        String user = bundle.getString("user");
        String password = bundle.getString("password");

        Connection conn = null;
        Statement stmt = null;

        try {
            // 1. 注册驱动
            Class.forName(driver);

            // 2. 连接数据库
            conn = DriverManager.getConnection(url, user, password);

            // 3. 获取数据库操作对象
            stmt = conn.createStatement();

            // 4. 执行 SQL 语句
            String sql = "insert into emp1(empno, ename) values(101, 'vv')";
            int count = stmt.executeUpdate(sql);
            System.out.println(count == 1 ? "completed" : "fail");

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
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
    }
}
```

处理查询结果集：

```java
package io.wataaaame.github.jdbc;

import java.sql.*;
import java.util.ResourceBundle;

public class JDBCTest05 {
    public static void main(String[] args) {
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        String driver = bundle.getString("driver");
        String url = bundle.getString("url");
        String user = bundle.getString("user");
        String password = bundle.getString("password");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            // 1. 注册驱动
            Class.forName(driver);

            // 2. 获取连接
            conn = DriverManager.getConnection(url, user, password);

            // 3. 获取数据库操作对象
            stmt = conn.createStatement();

            // 4. 执行 SQL 语句
            String sql = "select empno, ename, sal from emp";
            // 通过 executeQuery()，专门执行 DQL 语句，返回 ResultSet
            rs = stmt.executeQuery(sql);

            // 5. 处理查询结果集
            // rs.next() 光标后移一位，若为真，则光标指向行有数据
            while (rs.next()) {
                // 取数据
                // getString()：不管原类型，都以 String 类型取出
                // 若使用 getInt() 等特定类型，可直接进行数据运算（需底层类型相同）
                // JDBC 中，所有的下标都从 1 开始，代表第一列
//                System.out.println(rs.getString(1) + ", " +
//                        rs.getString(2) + ", " +
//                        rs.getString(3));
                // 建议用列名获取，更加健壮（若查询使用别名，则需要同步使用）
                System.out.println(rs.getString("empno") + ", " +
                        rs.getString("ename") + ", " +
                        rs.getString("sal"));
            }

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 6. 释放资源
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
    }
}
```

![遍历结果集原理](/images/image-jdbc/note-02-遍历结果集原理.PNG)

## Use IDEA

`Module 右键 -> Open Module Settings -> Project Setting -> Libraries -> + -> Java -> [path]/mysql-connector-java-5.1.23.jar -> choose Module`

## Power Designer

使用 PD 工具来进行数据库表的设计

## 实现功能

模拟用户登录功能的实现

业务描述：

程序运行的时候，提供一个输入的入口，可以让用户输入用户名和密码

用户输入用户名和密码之后，提交信息，Java 程序收集到用户信息

Java 程序连接数据库验证用户名和密码是否合法

合法：显示登录成功

不合法：显示登录失败

代码如下：

```java
package io.wataaaame.github.jdbc;

import java.sql.*;
import java.util.HashMap;
import java.util.Map;
import java.util.ResourceBundle;
import java.util.Scanner;

public class JDBCTest06 {
    public static void main(String[] args) {
        // 初始化一个界面
        Map<String, String> userLoginInfo = initUI();

        // 验证用户名和密码
        boolean isLogin = login(userLoginInfo);
        System.out.println(isLogin == true ? "success" : "fail");
    }

    /**
     * 用户登录
     * @param userLoginInfo 用户登录信息
     * @return true 表示登录成功，false 表示登录失败
     */
    private static boolean login(Map<String, String> userLoginInfo) {
        // JDBC 代码
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        String driver = bundle.getString("driver");
        String url = bundle.getString("url");
        String user = bundle.getString("user");
        String password = bundle.getString("password");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        String loginName = userLoginInfo.get("loginName");
        String loginPwd = userLoginInfo.get("loginPwd");

        boolean isLogin = false;

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(url, user, password);

            stmt = conn.createStatement();
            String sql = "select * from t_user where loginName = '" + loginName + "' and loginPwd = '" + loginPwd + "'";
            rs = stmt.executeQuery(sql);
            if (rs.next()) {
                // 登录成功
                isLogin = true;
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

        return isLogin;
    }

    /**
     * 初始化用户界面
     * @return 用户输入的用户名和密码等等录信息
     */
    private static Map<String, String> initUI() {
        Scanner s = new Scanner(System.in);

        System.out.print("username: ");
        String loginName = s.nextLine();

        System.out.print("password: ");
        String loginPwd = s.nextLine();

        Map<String, String> userLoginInfo = new HashMap<>();
        userLoginInfo.put("loginName", loginName);
        userLoginInfo.put("loginPwd", loginPwd);

        return userLoginInfo;
    }
}
/*
    username: vv
    password: 123
    success

    username: vv
    password: 22
    fail
*/
```

### SQL 注入

当前程序存在的问题：

```java
/*
    username: vv
    password: vv' or '1'='1
    success
*/
```

这种现象被称为 “SQL 注入”（黑客经常使用）

- 用户输入的信息中含有 sql 语句的关键字，并且这些关键字参与 sql 语句的编译过程，导致 sql 语句的原意被扭曲

```java
String sql = "select * from t_user where loginName = '" + loginName + "' and loginPwd = '" + loginPwd + "'";
// 以上代码正好完成了 sql 语句的拼接：
// where loginName = 'vv' and loginPwd = 'vv' or '1' = '1'
// 正好将用户提供的非法信息编译进去（1 = 1 恒成立）
rs = stmt.executeQuery(sql);
```

### 解决 SQL 注入问题

用户提供的信息不参与 SQL 语句的编译过程

`java.sql.PreparedStatement`

- 该接口继承 `java.sql.Statement`
- 该对象属于预编译的数据库操作对象
- 原理是：预先对 SQL 语句的框架进行编译，然后再给 SQL 语句传值

代码如下：

```java
package io.wataaaame.github.jdbc;

import java.sql.*;
import java.util.HashMap;
import java.util.Map;
import java.util.ResourceBundle;
import java.util.Scanner;

/**
 * 解决 SQL 注入问题
 */
public class JDBCTest07 {
    public static void main(String[] args) {
        Map<String, String> userLoginInfo = initUI();

        boolean isLogin = login(userLoginInfo);
        System.out.println(isLogin == true ? "success" : "fail");
    }

    private static boolean login(Map<String, String> userLoginInfo) {
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        String driver = bundle.getString("driver");
        String url = bundle.getString("url");
        String user = bundle.getString("user");
        String password = bundle.getString("password");

        Connection conn = null;
        // 这里使用 PreparedStatement 预编译的数据库操作对象
        PreparedStatement ps = null;
        ResultSet rs = null;

        String loginName = userLoginInfo.get("loginName");
        String loginPwd = userLoginInfo.get("loginPwd");

        boolean isLogin = false;

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(url, user, password);

            // 3. 获取预编译的数据库操作对象
            // 获取预编译的数据库操作对象
            // sql 语句（框架）提前，且值变为 ？ 占位符（不可加单引号）
            String sql = "select * from t_user where loginName = ? and loginPwd = ?";
            ps = conn.prepareStatement(sql);
            // 给占位符 ？ 传值（JDBC 下标从 1 开始，第一个 ？ 为 1）
            ps.setString(1, loginName);
            ps.setString(2, loginPwd);

            // 4. 执行 SQL
            // 执行不必传 sql 语句，直接调用对象的 executeQuery 方法
            rs = ps.executeQuery();
            if (rs.next()) {
                isLogin = true;
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

        return isLogin;
    }

    private static Map<String, String> initUI() {
        Scanner s = new Scanner(System.in);

        System.out.print("username: ");
        String loginName = s.nextLine();

        System.out.print("password: ");
        String loginPwd = s.nextLine();

        Map<String, String> userLoginInfo = new HashMap<>();
        userLoginInfo.put("loginName", loginName);
        userLoginInfo.put("loginPwd", loginPwd);

        return userLoginInfo;
    }
}
/*
    username: vv
    password: vv' or '1' = '1
    fail
*/
```

### Statement 与 PreparedStatement 对比

|              | Statement        | PreparedStatement                |
| ------------ | ---------------- | -------------------------------- |
| SQL 注入问题 | 存在             | 不存在                           |
| 编译运行     | 编译一次运行一次 | 编译一次运行多次（预先编译）     |
| 类型安全检查 | 无               | 编译阶段占位符传入类型做安全检查 |
| 使用         | 较少             | 较多                             |

- 业务要求使用 SQL 语句拼接时使用 Statement（如升序降序中的 desc 无法使用单引号）
- 问号占位符不能带双引号

> SQL 相同语句不会重复编译

实例代码：

```java
package io.wataaaame.github.jdbc;

import java.sql.*;
import java.util.ResourceBundle;
import java.util.Scanner;

public class JDBCTest08 {
    public static void main(String[] args) {
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        String driver = bundle.getString("driver");
        String url = bundle.getString("url");
        String user = bundle.getString("user");
        String password = bundle.getString("password");

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        Scanner s = new Scanner(System.in);

        System.out.println("order by desc/asc");
        System.out.print("Plz enter: ");
        String order = s.nextLine();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(url, user, password);

            stmt = conn.createStatement();
            String sql = "select * from t_user order by id " + order;
            rs = stmt.executeQuery(sql);
            while (rs.next()) {
                System.out.println(rs.getString("id") + "\t" +
                        rs.getString("loginName"));
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
    }
}
/*
    order by desc/asc
    Plz enter: desc
    2	ee
    1	vv
*/
```

### PreparedStatement 实现增删改

代码如下：

```java
package io.wataaaame.github.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class JDBCTest09 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost/learning", "root", "0.0");

            // 插入
            /*String sql = "insert into t_user(loginName, loginPwd, realName) values(?, ?, ?)";
            ps = conn.prepareStatement(sql);
            ps.setString(1, "xx");
            ps.setString(2, "111");
            ps.setString(3, "Mr.X");
            int count = ps.executeUpdate();*/

            // 修改
            /*String sql = "update t_user set loginName = ?, loginPwd = ?, realName = ? where id = 4";
            ps = conn.prepareStatement(sql);
            ps.setString(1, "yy");
            ps.setString(2, "111");
            ps.setString(3, "Mr.Y");
            int count = ps.executeUpdate();*/

            // 删除
            String sql = "delete from t_user where id > ?";
            ps = conn.prepareStatement(sql);
            ps.setInt(1, 5);
            int count = ps.executeUpdate();

            System.out.println("Query OK, " + count + " row affected");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
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
    }
}
```

## 事务

JDBC 中的事物自动提交，只要执行任意一条 DML 语句，则自动提交一次

测试代码如下：

```java
package io.wataaaame.github.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class JDBCTest10 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");

            String sql = "update t_user set loginName = ? where id = ?";
            ps = conn.prepareStatement(sql);
            // 第一次给占位符传值
            ps.setString(1, "aa");
            ps.setInt(2, 4);
            // 执行第一条 update 语句
            int count = ps.executeUpdate();
            System.out.println(count);

            // 重新给占位符传值
            ps = conn.prepareStatement(sql);
            ps.setString(1, "aa");
            ps.setInt(2, 4);
            // 执行第二条 update 语句
            count = ps.executeUpdate();
            System.out.println(count);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
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
    }
}
```

使用以下三行代码解决：

```java
conn.setAutoCommit(false);
conn.commit();
conn.rollback();
```

示例代码如下：

```java
package io.wataaaame.github.jdbc;

import java.sql.*;

/**
 * 模拟银行转账事务
 * sql脚本：
 *      drop table if exists t_act;
 *      create table t_act (
 *          actno int,
 *          balance double(7, 2)    // 7代表有效数字，2代表小数个数
 *      );
 *      insert into t_act values(101, 20000);
 *      insert into t_act values(102, 0);
 *      commit;
 *      select * from t_act;
 */

public class JDBCTest11 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;

        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/learning", "root", "0.0");
            // 1. 将自动提交机制修改为手动提交
            conn.setAutoCommit(false);

            String sql = "update t_act set balance = ? where actno = ?";
            ps = conn.prepareStatement(sql);

            // 101 向 102 转账 10000，101 扣款 10000
            ps.setDouble(1, 10000);
            ps.setInt(2, 101);
            int count = ps.executeUpdate();

            // 手动执行一个空指针异常，后面代码不会执行
            // 此时101 balance 10000，102 balance 0 出错
            String s = null;
            s.toString();

            // 102 收入 10000
            ps.setDouble(1, 10000);
            ps.setInt(2, 102);
            count += ps.executeUpdate();

            // 2. 手动提交
            conn.commit();
            System.out.println(count == 2 ? "success" : "fail");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            // 3. 回滚事务
            if (conn != null) {
                try {
                    conn.rollback();
                } catch (SQLException ex) {
                    ex.printStackTrace();
                }
            }
            e.printStackTrace();
        } finally {
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
    }
}
```

## 工具类的封装

```java
package io.wataaaame.github.jdbc.utils;

import java.sql.*;

/**
 * JDBC 工具类，简化 JDBC 编程
 */

public class JDBCUtils {
    /**
     * 工具类中的构造方法都是私有的
     * 因为工具类中的方法都是静态的，不需要 new 对象，直接采用类名调用
     */
    private JDBCUtils() {
    }

    // 静态代码块中执行一次驱动注册
    static {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 连接数据库的方法
     * @return 数据库连接对象
     * @throws SQLException 由于方法的调用是在 try...catch 中，所以直接抛出异常
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection("jdbc:mysql://localhost/learning", "root", "0,0");
    }

    /**
     * 释放资源方法
     * @param conn 连接对象
     * @param stmt 数据库操作对象
     * @param rs 查询结果集
     */
    public static void close(Connection conn, Statement stmt, ResultSet rs) {
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
}
```

## 行级锁

查询语句后加上 `for update` 可以保证当前事务还未结束时，行不能被其他事务修改（悲观锁，不支持并发）

> 乐观锁是多线程并发，事务都可以进行修改，自动添加修改后的版本号