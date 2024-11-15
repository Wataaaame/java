---
title: MySQL - Note04 Database
date: 2022-07-14 16:36:15
categories: MySQL
tags: [DBA, Paradigm]
---

# DBA 命令

## 导出

1. 导出整个库

   ```dos
   mysqldump [database]>[path.sql] -u[name] -p[psw]
   ```

2. 导出库中表

   ```dos
   mysqldump [database] [table]>[path.sql] -u[name] -p[psw]
   ```

## 导入

```mysql
create database [name];
use [name];
source [path.sql]
```

# *数据库设计三范式

设计表的依据，按照这个三范式设计的表不会出现数据冗余

## 第一范式

任何一个表都应该有主键，并且每个字段原子性不可再分

## 第二范式

建立在第一范式的基础之上，所有非主键字段完全依赖主键，不能产生部分依赖

- 多对多，三张表，关系表两个外键

  t_student 学生表：

  ```
  sno(pk)		sname
  -----------------
  1			vv
  2			ee
  ```

  t_teacher 讲师表：

  ```
  tno(pk)		tname
  -----------------
  1			t1
  2			t2
  3			t3
  ```

  t_student_teacher_relation 学生讲师关系表：

  ```
  id(pk)		sno(fk)		tno(fk)
  -------------------------------
  1			1			1
  2			1			3
  3			2			2
  4			2			1
  ```

## 第三范式

建立在第二范式基础上，所有非主键字段直接依赖主键，不能产生传递依赖

- 一对多，两张表，多的表加外键（蝴蝶）

  班级 t_class

  ```
  cno(pk)		cname
  -----------------
  1			a
  2			b
  ```

  学生 t_student

  ```
  sno(pk)		sname		classno(fk)
  -----------------------------------
  101			vv			1
  102			ee			2
  ```

提示：在实际开发中，以满足客户需求为主，可能会用冗余换执行速度

## 一对一

1. 主键共享

   t_user_login 用户登录表

   ```
   id(pk)		username	password
   --------------------------------
   1			vv			123
   2			ee			456
   ```

   t_user_detail 用户详情信息表

   ```
   id(pk+fk)	realname	tel			...
   ---------------------------------------
   1			vay			321			...
   2			yiyi		543			...
   ```

2. 外键唯一

   t_user_login 用户登录表

   ```
   id(pk)		username		password
   ------------------------------------
   1			vv				123
   2			ee				456
   ```

   t_user_detail 用户详情信息表

   ```
   id(pk)		realname		tel			userid(fk+unique)
   -----------------------------------------------------
   1			vay				321			1
   2			yiyi			543			2
   ```