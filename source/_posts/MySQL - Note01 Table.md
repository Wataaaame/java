---
title: MySQL - Note01 Table
date: 2022-04-17 19:51:34
tags: [Basics, Table]
categories: MySQL
---

# DB, DBMS, SQL

- DB(DataBase): 数据库（在硬盘上以文件形式存在）
- DBMS(DataBase Management System): 数据库管理系统（MySQL, Oracle, SQL Server, DB2, Sybase...)
- SQL(Sequel): 结构化查询语言，是一门标准通用的语言，适合所有的数据库产品

> DBMS -(执行)-> SQL -(操作)-> DB

# Table

table是数据库的基本组成单元，所有的数据都以表格的形式组织，可读性非常强。

- 一个表包括行和列

  行：被称为数据/记录（data）

  列：被称为字段（column）

  | 学号（int） | 姓名（archar） | 年龄（int） |
  | :---------: | :------------: | :---------: |
  |     101     |      张三      |     20      |
  |     102     |      李四      |     21      |

  > 每个字段应该包括：字段名、数据类型、相关约束

- 导入的learning表结构

  ```mysql
  mysql> show tables;
  /*
  	+--------------------+
      | Tables_in_learning |
      +--------------------+
      | dept               |	部门表
      | emp                |	员工表
      | salgrade           |	工资等级表
      +--------------------+
      3 rows in set (0.00 sec)
  */
  ```

  ```mysql
  mysql> desc dept;
  /*
      +--------+-------------+------+-----+---------+-------+
      | Field  | Type        | Null | Key | Default | Extra |
      +--------+-------------+------+-----+---------+-------+
      | DEPTNO | int(2)      | NO   | PRI | NULL    |       |	部门编号
      | DNAME  | varchar(14) | YES  |     | NULL    |       |	部门名称
      | LOC    | varchar(13) | YES  |     | NULL    |       |	部门位置
      +--------+-------------+------+-----+---------+-------+
      3 rows in set (0.01 sec)
  */
  ```

  ```mysql
  mysql> desc emp;
  /*
      +----------+-------------+------+-----+---------+-------+
      | Field    | Type        | Null | Key | Default | Extra |
      +----------+-------------+------+-----+---------+-------+
      | EMPNO    | int(4)      | NO   | PRI | NULL    |       |	员工编号
      | ENAME    | varchar(10) | YES  |     | NULL    |       |	员工姓名
      | JOB      | varchar(9)  | YES  |     | NULL    |       |	员工岗位
      | MGR      | int(4)      | YES  |     | NULL    |       |	上级领导编号
      | HIREDATE | date        | YES  |     | NULL    |       |	入职日期
      | SAL      | double(7,2) | YES  |     | NULL    |       |	薪资
      | COMM     | double(7,2) | YES  |     | NULL    |       |	补助/津贴
      | DEPTNO   | int(2)      | YES  |     | NULL    |       |	部门编号
      +----------+-------------+------+-----+---------+-------+
      8 rows in set (0.00 sec)
  */
  ```

  ```mysql
  mysql> desc salgrade;
  /*
      +-------+---------+------+-----+---------+-------+
      | Field | Type    | Null | Key | Default | Extra |
      +-------+---------+------+-----+---------+-------+
      | GRADE | int(11) | YES  |     | NULL    |       |	等级
      | LOSAL | int(11) | YES  |     | NULL    |       |	最低薪资
      | HISAL | int(11) | YES  |     | NULL    |       |	最高薪资
      +-------+---------+------+-----+---------+-------+
      3 rows in set (0.00 sec)
  */
  ```

## 创建表

```mysql
# 建表语法格式
# 表名一般用t_或tbl_开头用以区分
create table [table_name](
	[字段1] [数据类型] [约束],
    [字段2] [数据类型] [约束],
    [字段3] [数据类型] [约束],
    ...
);

/*
	eg.创建学生表：
	学号（bigint）、姓名（varchar）、性别（char）、班级编号（int）、生日（char）
*/
create table t_student(
    no bigint(11),
    name varchar(255),
    sex char(1),
    classno int(4),
    birth char(10)
);
```

### MySQL中常见的数据类型：

| Date Struct             | Java                        | Description                                            |
| ----------------------- | --------------------------- | ------------------------------------------------------ |
| int（整数型）           | int                         |                                                        |
| bigint（长整型）        | long                        |                                                        |
| float（浮点型）         | float, double               |                                                        |
| char（定长字符串）      | String                      | 性别、生日                                             |
| varchar（可变长字符串） | StringBuffer, StringBuilder | 姓名、简介                                             |
| date（日期类型）        | java.sql.Date               |                                                        |
| BLOB（二进制大对象）    | Object                      | Binary Large Object（存储图片、视频等流媒体信息）      |
| CLOB（字符大对象）      | Object                      | Character Large Object（存储较大的文本，如4G的字符串） |
| ...                     | ...                         | ...                                                    |

### 约束（Constraint）

在创建表的时候，可以给表的字段添加相应的约束。添加约束的目的是为了保证表中数据的合法性、有效性、完整性。

常见的约束

| 约束名                  | 约束作用                                   |
| ----------------------- | ------------------------------------------ |
| 非空约束（not null）    | 约束的字段不能为NULL                       |
| 唯一约束（unique）      | 约束的字段不能重复                         |
| 主键约束（primary key） | 简称PK，约束的字段既不能为NULL，也不能重复 |
| 外键约束（foreign key） | 简称FK...                                  |
| default                 | default [value]：添加默认数据              |

> 检查约束（check）：仅Oracle数据库有，MySQL暂不支持

#### 非空约束（not null）

```mysql
create table t_user(
	id int,
    username varchar(255) not null,
    password varchar(255)
);

insert into t_user(id, password) value(101, '123');
/*
	ERROR 1364 (HY000): Field 'username' doesn't have a default value
*/
```

#### 唯一性约束（unique）

唯一性约束修饰的字段具有唯一性，不能重复，但可以为NULL

```mysql
# eg.给某一列添加 unique（列级约束）
drop table if exists t_user;

create table t_user(
	id int unique,
    username varchar(255)
);

insert into t_user values(101, 'vv'), (101, 'yy');

# ERROR 1062 (23000): Duplicate entry '101' for key 'id'
```

```mysql
# eg.给两个列或者多个列添加 unique (表级约束)
/*
	使用：unique([name1], [name2])
    联合检测 [name1][name2]
*/
drop table if exists t_user;

create table t_user(
	id int,
    user_id int,
    user_name char(2),
    unique(user_id, user_name)
);

insert into t_user
values(101, 001, 'vv'),
	(101, 002, 'yy'),
	(102, 001, 'zz');

/*
    +------+---------+-----------+
    | id   | user_id | user_name |
    +------+---------+-----------+
    |  101 |       1 | vv        |
    |  101 |       2 | yy        |
    |  102 |       1 | zz        |
    +------+---------+-----------+
    3 rows in set (0.00 sec)
*/
```

#### 主键约束

主键中的字段不能为空，也不能重复

如何给一张表添加主键约束？

```mysql
drop table if exists t_user;

create table t_user(
	id int primary key,
    username varchar(2),
    email varchar(255)
);

insert into t_user
values(101, 'vv', 'vv@123.com'),
	(102, 'yy', 'yy@456.com'),
	(103, 'zz', 'zz@789.com');

insert into t_user(id, username, email) values(101, 'xx', 'xx@101.com');
# ERROR 1062 (23000): Duplicate entry '101' for key 'PRIMARY'

insert into t_user(username, email) values('xx', 'xx@101.com');
# ERROR 1364 (HY000): Field 'id' doesn't have a default value
```

> 表级约束：`primary key([字段1], [字段2])`

##### 相关术语

主键约束：`primary key`

主键字段：添加了主键约束的字段

主键值：id字段中的每个值

##### 主键作用

主键值是这行记录在这样表中的唯一标识，**一张表的主键约束只能有一个**

> 表的设计三范式中，第一范式就要求任何一张表都应有主键

##### 主键分类

- 根据主键字段的字段数量来划分：

  单一主键：推荐、常用

  复合主键：使用表级约束多个字段联合起来添加一个主键约束（不建议，违背三范式）

- 根据主键性质来划分：

  自然主键：和业务没有任何关系的自然数

  业务主键：主键和系统的业务挂钩（不建议）

  > 最好不要用业务挂钩的字段作为主键，业务改变后，主键无法更改时则冲突
  >
  > 例如：银行卡卡号、身份证号

##### *MySQL 提供主键值自增

在约束后添加 `auto_increment`

```mysql
drop table if exists t_user;

create table t_user(
	id int primary key auto_increment,
    user_name varchar(2)
);

insert into t_user(user_name)
values('vv'), ('yy'), ('zz'), ('xx');
	
select * from t_user;
```

#### 外键约束

外键约束的字段必须来自于指定表中的某一字段值

> 外键可以为 NULL
>
> 不一定引用主键，起码得有唯一性（unique）

##### 相关术语

外键约束：`foreign key([name]) references [table]([name])`

外键字段：添加有外键约束的字段

外键值：外键字段中的每一个值

##### 父子表

设计数据库表，用来维护学生和班级信息

方案1：一张表存储所有数据（不推荐，冗余）

```mysql
---------------------------------------------
sno(pk)		sname		classno		classname
---------------------------------------------
1			vv			1804		软工4班
2			yy			1806		软工6班
3			zz			1703		软工3班
```

方案2：两张表（班级表和学生表）

```mysql
t_class 班级表（父表）
----------------------
classno(pk)		cname
----------------------
1804			软工4班
1806			软工6班
1703			软工3班

t_student 学生表（子表）
--------------------------------
sno		sname		classno（fk）
--------------------------------
1		vv			1804
2		yy			1806
3		zz			1703
```

- t_student 中的 calssno 引用 t_class 中的 classno 字段，此时 t_student 叫做子表，t_class 叫做父表
- 顺序要求：
  - 创建时先父再子
  - 删除时先子再父

示例代码：

```mysql
drop table if exists t_student;
drop table if exists t_class;

create table t_class(
	classno int,
    classname varchar(255),
    primary key(classno)
);

create table t_student(
	sno int,
    sname varchar(255),
    classno int,
    primary key(sno),
    foreign key(classno) references t_class(classno)
);

insert into t_class
values(1804, 'PG4'),
	(1806, 'PG6'),
	(1703, 'PG3');
	
insert into t_student
values(1, 'vv', 1804),
	(2, 'yy', 1806),
	(3, 'zz', 1703);
	
select * from t_class;
select * from t_student;

/*
    +---------+-----------+
    | classno | classname |
    +---------+-----------+
    |    1703 | PG3       |
    |    1804 | PG4       |
    |    1806 | PG6       |
    +---------+-----------+
    3 rows in set (0.00 sec)
    
    +-----+-------+---------+
    | sno | sname | classno |
    +-----+-------+---------+
    |   1 | vv    |    1804 |
    |   2 | yy    |    1806 |
    |   3 | zz    |    1703 |
    +-----+-------+---------+
    3 rows in set (0.00 sec)
*/

# 子表尝试插入不存在的 fk
insert into t_student values(4, 'zz', 1901);
/*
    ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`learning`.`t_student`, CONSTRAINT `t_student_ibfk_1` FOREIGN KEY (`classno`) REFERENCES `t_class` (`classno`))
*/
```

## insert语句插入数据

```mysql
# 语法格式
insert into [table_name](字段1,字段2,字段3,...) values(值1,值2,值3,...)

# eg1.插入数据
insert into t_student(no, name, sex, classno, birth)
values(20184225131, 'vv', '1', 1804, '2000-12-26');

# eg2.插入数据
# 省略字段，则values全填写
insert into t_student
values(20184225132, 'yy', '0', 1804, '2000-4-7');

# eg3.插入数据
# 一次插入多行数据
insert into t_student
values(20184225133, 'xx', '1', 1804, '2011-12-5'),
	(20184225134, 'zz', '0', 1804, '2001-4-16');

/*
    +-------------+------+------+---------+------------+
    | no          | name | sex  | classno | birth      |
    +-------------+------+------+---------+------------+
    | 20184225131 | vv   | 1    |    1804 | 2000-12-26 |
    | 20184225132 | yy   | 0    |    1804 | 2000-4-7   |
    | 20184225133 | xx   | 1    |    1804 | 2011-12-5  |
    | 20184225134 | zz   | 0    |    1804 | 2001-4-16  |
    +-------------+------+------+---------+------------+
    4 rows in set (0.00 sec)
*/

/*
    字段的数量和值的数量相同
    数据类型要对应相同
    顺序无关
    空字段自动插入NULL
    insert只能增，执行成功后表格必然多一行记录
*/
```

## 删除表

通用语法： `drop table [table_name]`

MySQL语法： `drop table if exists [table_name];`

> `if exists`：如果存在

## 复制表

将查询结果当作表创建出来

```mysql
# 语法格式
create table [table_name] 
as [DQL语句];

# eg
create table emp1
as select * from emp;
```

## 插入表

将查询结果当作表插入到别的表

```mysql
# 语法格式
insert into [table_name] [DQL语句];

# eg
insert into emp1 select * from dept;
```

## 修改表数据

语法格式

```mysql
update [table_name]
set [字段1]=[值1], [字段2]=[值2], ...
where [条件];
```

- 没有条件整张表数据全部更新

```mysql
# 将部门10的LOC修改为SHANGHAI，将部门名称修改为RENSHIBU
update dept1 set dname='RENSHIBU', loc='SHANGHAI' where deptno='10';
/*
    +--------+------------+----------+
    | DEPTNO | DNAME      | LOC      |
    +--------+------------+----------+
    |     10 | RENSHIBU   | SHANGHAI |
    |     20 | RESEARCH   | DALLAS   |
    |     30 | SALES      | CHICAGO  |
    |     40 | OPERATIONS | BOSTON   |
    +--------+------------+----------+
*/
```

## 删除表数据

语法格式：`delete from [table_name] where [条件];`

```mysql
# eg.删除20部门数据
delete from dept1 where deptno='20';
```

- 没有条件全部删除

```mysql
# eg.删除所有数据
delete from dept1;
```

### *如何删除大表中的数据？

可以使用 `truncate` 截断表数据，仅留下表头字段名，危险系数高，不可以回滚

> delete 仅擦除数据，不释放空间，删除效率低下

语法格式：`truncate table [table_name];`

