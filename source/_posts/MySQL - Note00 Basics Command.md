---
title: MySQL - Note00 Basics Command
date: 2022-04-17 11:31:31
tags: Command
categories: MySQL
---

# 通用SQL语句分类

- DQL（Data **Query** Language 数据**查询**语言）：`select`语句都是DQL查询语句
- DML（Data **Manipulation** Language 数据**操作**语言）：`insert` `delete` `update`，对表中的数据进行增删改
- DDL（Data Definition Language 数据定义语言）：`create` `drop` `alter`，对表结构的增删改
- TCL（**Transaction** Control Language **事务**控制语言）：`commit`提交事务、`rollback`回滚事务
- DCL（Data Control Language 数据控制语言）：`grant`授权、`revoke`撤销权限等

# 常用命令

- 控制面板登录命令：`mysql -u[user] -p[password]`

  > 若考虑安全性可以使用：`mysql -u[user] -p`，密码在提示框内以星号显示。

- 结束一条语句：`/c`
- 退出MySQL：`exit`
- 使用数据库：`use [database_name];`
- 初始化DB脚本文件：`source [path.sql]`

## 增

- 创建数据库：`create database [database_name];`

## 删

- 删除数据库：`drop database [database_name];`

## 改

## 查

- 查看所有数据库：`show databases;`
- 查询当前使用的数据库：`select database();`
- 查询数据库版本：`select version();`
- 查看表：`show tables;`
- 查看表结构：`desc [table_name];`
- 查看创建表的语句：`show create table emp;`
- 查看其它数据库中的表：`show tables from [database_name];`
- 查看执行计划：语句前加上 `explain`

# 修改root密码

## 记得原密码

- 登录MySQL

  `set password for [user]@localhost = password('new_password');`

- 登录MySQL，用update直接编辑user表

  ```mysql
  use mysql;
  update user set password=password('[password]') where user='root' and host='localhost';
  flush privileges;
  ```

- 用mysqladmin

  `mysqladmin -u[user] -p[old_password] password [new_password]`

## 忘记原密码

以Windows为例

1. 关闭正在运行的MySQL服务

2. 打开DOS窗口，转到`mysql\bin`目录

3. 输入`mysqld --skip-grant-tables`后回车

   > 启动MySQL时跳过权限表认证

4. 再打开一个DOS窗口，转到`mysql\bin`目录

5. 输入`mysql`回车，如果成功，将出现MySQL提示符>

6. 输入`use mysql;`连接权限数据库

7. 输入`update user set password=password('[password]') where user='root';`更改密码

8. 输入`flush privileges;`刷新权限（必须步骤）

9. 输入`quit`退出

10. 注销系统再进入，使用root和新密码登录
