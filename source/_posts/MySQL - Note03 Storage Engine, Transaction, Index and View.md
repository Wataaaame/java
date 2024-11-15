---
title: MySQL - Note03 Storage Engine, Transaction, Index and View
date: 2022-05-11 08:59:56
categories: MySQL
tags: [Storage Engine, Transaction, Index, View]
---

# *存储引擎*

MySQL特有（Oracle对应的机制就叫”存储方式“），描述的是底层表的存储方式

通过 `show create table [table_name]` 查看表的存储引擎

```mysql
# 查看表的存储引擎
show create table emp;
/*
	...
	ENGINE=InnoDB DEFAULT CHARSET=utf8
*/
```

## 完整的建表语句

```mysql
CREATE TABLE `[table_name]` (
	`id` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf-8;
# 在MySQL当中，凡是标识符都可以用飘号“`”括起来（不通用）
# 建表时可以指定存储引擎，也可以指定字符集
# 默认引擎是 InnoDB，默认字符集是 utf-8
```

## 查看 MySQL 当前版本支持的存储引擎

```mysql
# 查看当前版本
select version();
/*
    +-----------+
    | version() |
    +-----------+
    | 5.5.28    |
    +-----------+
    1 row in set (0.01 sec)
*/
```

```mysql
# 查看存储引擎
show engines \G;
/*
    *************************** 1. row ***************************
          Engine: FEDERATED
         Support: NO
         Comment: Federated MySQL storage engine
    Transactions: NULL
              XA: NULL
      Savepoints: NULL
    *************************** 2. row ***************************
          Engine: MRG_MYISAM
         Support: YES
         Comment: Collection of identical MyISAM tables
    Transactions: NO
              XA: NO
      Savepoints: NO
    *************************** 3. row ***************************
          Engine: MyISAM
         Support: YES
         Comment: MyISAM storage engine
    Transactions: NO
              XA: NO
      Savepoints: NO
    *************************** 4. row ***************************
          Engine: BLACKHOLE
         Support: YES
         Comment: /dev/null storage engine (anything you write to it disappears)
    Transactions: NO
              XA: NO
      Savepoints: NO
    *************************** 5. row ***************************
          Engine: CSV
         Support: YES
         Comment: CSV storage engine
    Transactions: NO
              XA: NO
      Savepoints: NO
    *************************** 6. row ***************************
          Engine: MEMORY
         Support: YES
         Comment: Hash based, stored in memory, useful for temporary tables
    Transactions: NO
              XA: NO
      Savepoints: NO
    *************************** 7. row ***************************
          Engine: ARCHIVE
         Support: YES
         Comment: Archive storage engine
    Transactions: NO
              XA: NO
      Savepoints: NO
    *************************** 8. row ***************************
          Engine: InnoDB
         Support: DEFAULT
         Comment: Supports transactions, row-level locking, and foreign keys
    Transactions: YES
              XA: YES
      Savepoints: YES
    *************************** 9. row ***************************
          Engine: PERFORMANCE_SCHEMA
         Support: YES
         Comment: Performance Schema
    Transactions: NO
              XA: NO
      Savepoints: NO
    9 rows in set (0.01 sec)
*/
```

## 常见的存储引擎

### MyISAM 引擎

```
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO	// 不支持事务
          XA: NO
  Savepoints: NO
```
- 最常用的存储引擎，但不是默认的
- 具有以下特征：
  - 使用三个文件表示每个表
    1. 格式文件 - 存储表结构的定义（[table].frm）
    2. 数据文件 - 存储表行的内容（[table].MYD）
    3. 索引文件 - 存储表上的索引（[table].MYI）
  - 灵活的 AUTO_INCREMENT 字段处理
  - 可被转换为压缩、只读表来节省空间

### InnoDB 引擎

          Engine: InnoDB
         Support: DEFAULT
         Comment: Supports transactions, row-level locking, and foreign keys	// 支持事务、行级锁、外键
    Transactions: YES
              XA: YES
      Savepoints: YES

- MySQL 中缺省引擎，安全得到保障
- 具有以下特征：
  - 每个 InnoDB 表在数据库目录中以 .frm 格式文件表示
  - InnoDB 表空间 tablespace 被用于存储表内容
  - 提供一组用来记录事务性活动的日志文件
  - 用 COMMIT（提交）、SAVEPOINT、ROLLBACK（回滚）支持事务处理
  - 提供全 ACID 兼容
  - 在 MySQL 数据库崩溃后提供自动恢复
  - 多版本（MVCC）和行级锁定
  - 支持外键及引用的完整性，包括级联删除和更新

### MEMORY 引擎

          Engine: MEMORY
         Support: YES
         Comment: Hash based, stored in memory, useful for temporary tables
    Transactions: NO
              XA: NO
      Savepoints: NO

- 缺点：不支持事务、数据容易丢失（所有数据和索引存储在内存）
- 优点：查询速度最快
- 具有以下特征：
  - 在数据库目录内，每个表均以 .frm 格式的文件表示
  - **表数据及索引**被存在**内存**中
  - 表级锁机制
  - 不能包含 TEXT 或 BLOB 字段

> 以前叫做 HEAP 引擎

# 事务

一个事务是一个完整的业务逻辑单元，不可再分

> 多条 DML 语句捆绑一起，同时成功/同时失败

例如：银行账户转账，从 A 账户向 B 账户转账10000，需要执行两条 update 语句

```mysql
update t_act set balance = balance - 10000 where actno = 'act-01';
update t_act set balance = balance + 10000 where actno = 'act-02';
```

以上两条 DML 语句必须同时成功或同时失败

- 和事务相关的语句只有 DML 语句（insert delete updat）
- 事务的存在是为了保证数据的完整性、安全性

原理：

```
开启事务机制（开始）

执行 insert 语句：执行成功后，将执行记录到数据库的操作历史中，并不会向文件中保存一条数据，不会真正地修改硬盘上的数据
执行 ... 语句
...

提交事务或回滚事务（结束）
```

- 提交事务：将历史操作同步到硬盘文件中，然后清空历史记录
- 回滚事务：直接清空历史记录

## 特性

事务包括四大特性：ACID

A 原子性：事务是最小的工作单元，不可再分

C 一致性：事务必须保证多条 DML 语句同时成功/失败

I 隔离性：事务 A 与事务 B 之间具有隔离性

D 持久性：最终数据必须持久化到硬盘文件才算成功结束

### 事务的隔离性

事务隔离性存在隔离级别，理论上隔离级别包括4个：

1. 读未提交（read uncommitted）

   对方事务还没有提交，当前事务可以读取到对方未提交的数据

   - 问题：存在脏读（Dirty Read）现象：表示读到了脏的数据

2. 读已提交（read committed）

   对方事务提交之后的数据我方可读取到

   - 解决：脏读现象

   - 问题：不可重复读

3. 可重复读（repeatable read）

   可以重复读取我方当前事务的数据

   - 解决：不可重复读问题
   - 问题：读取到的数据是幻象

4. 序列化读/串行化读

   事务同步提交，必须排队

   - 解决：所有问题
   - 问题：效率低下

Oracle 数据库默认的隔离级别是：第二级 读已提交

MySQL 数据默认的隔离级别是：第三级 可重复读

## 演示事务

MySQL 事务默认自动提交（只要执行一条 DML 语句则提交一次）

commit/rollback 演示：

```mysql
# 关闭自动提交/开启事务
start transaction;

# DML 语句
# ...
# 这里可以创建保存点
savepoint a1;

# 提交
commit;

# 或回滚
rollback;
# 回滚到保存点
rollback a1;
```

### 设置隔离级别

设置事物的隔离级别：

```mysql
set global transaction isolation level read uncommitted;
```

查看事物的全局隔离级别：

```mysql
select @@tx_global isolation;
/*
    +-----------------+
    | @@tx_isolation  |
    +-----------------+
    | REPEATABLE-READ |
    +-----------------+
*/
```

# 索引

相当于一本书的目录，通过目录可以快速找到对应资源

数据库查询表的两种检索方式：

1. 全表扫描
2. 索引检索（缩小扫描范围，效率高）

不能随意添加索引，索引也是数据库中的对象，也需要不断维护（维护成本），数据修改索引就需要重新排列

## 操作

添加索引是给某一个字段添加

添加索引：`create index 索引名称 on 表名(字段名)`

```mysql
create index emp_sal_index on emp(sal);
```

删除索引：`drop index 索引名称 on 表名;`

## 使用场景

1. 数据量庞大（根据需求和环境）
2. 该字段 DML 操作很少（字段修改，索引需要维护）
3. 该字段经常出现在 where 字句中

主键和具有 unique 约束的字段会自动添加索引，所以尽量根据主键检索

模糊查询第一个通配符使用 % 时索引失效

## 实现原理

通过 B + Tree 数据结构缩小扫描范围，底层索引进行了排序、分区，索引会携带表中的“物理地址”，最终通过索引检索到数据之后，获取到关联的物理地址，通过物理地址定位表中瞬息，效率较高

## 索引分类

单一索引：单个字段添加索引

复合索引：多个字段联合添加一个索引

主键索引：主键自动添加索引

唯一索引：unique 约束字段自动添加索引

...

# 视图

同一张表的数据，通过不同的角度去看待

可以隐藏表的实现细节，保密级别较高的系统，数据库只对外提供相关视图，Java 程序员只对视图对象进行 CRUD

## 视图操作

创建视图：

`create view 视图名称 as select 字段1, 字段2 from emp;`

- 只有 DQL 语句才能以视图对象的方式创建出来

删除视图：

`drop view 视图名称;`

对视图的增删改查会影响到原数据