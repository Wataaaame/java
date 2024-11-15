---
title: MySQL - Note02 Data Query Language
date: 2022-04-25 09:51:00
tags: DQL
categories: MySQL
---

# 查询语句（DQL）

## 简单查询

语法格式：`select [字段1], [字段2], [字段3], ... from [table_name];`

> 1. 任何一条sql语句以分号";"结尾
>
> 2. sql语句不区分大小写
>
> 3. 字段可以参与数学运算
>
> 4. 查询全字段使用"*"（实际开发效率低下）
>
> 5. 查询结果列重命名：`[字段] as [name]`
>
>    > 中文名建议用单引号，所有数据库系统通用
>    >
>    > as关键字可以省略

```mysql
# 查询emp表所有数据
select * from emp;
/*
	+-------+--------+-----------+------+------------+---------+---------+--------+
    | EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
    +-------+--------+-----------+------+------------+---------+---------+--------+
    |  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     20 |
    |  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     30 |
    |  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
    |  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     20 |
    |  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
    |  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     30 |
    |  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450.00 |    NULL |     10 |
    |  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     20 |
    |  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000.00 |    NULL |     10 |
    |  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     30 |
    |  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     20 |
    |  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
    |  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     20 |
    |  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300.00 |    NULL |     10 |
    +-------+--------+-----------+------+------------+---------+---------+--------+
    14 rows in set (0.00 sec)
*/
```

## 条件查询

- 语法格式：`select [字段], ... from [table_name] where [条件];`

- 执行顺序：from -> where > select

### 关系运算符

```mysql
# 查询工资等于5000的员工姓名？
select ename from emp where sal=5000;
/*
    +-------+
    | ename |
    +-------+
    | KING  |
    +-------+
    1 row in set (0.00 sec)
*/
```

```mysql
# 查询SMITH的工资
select sal from emp where ename='SMITH';
/*
    +--------+
    | sal    |
    +--------+
    | 800.00 |
    +--------+
    1 row in set (0.00 sec)
*/
```

```mysql
# 找出工资高于3000的员工
select ename from emp where sal>3000;
/*
    +-------+
    | ename |
    +-------+
    | KING  |
    +-------+
    1 row in set (0.00 sec)
*/
```

```mysql
# 找出工资不等于3000的员工
select ename from emp where sal<>3000;
# select ename from emp where sal!=3000;
/*
    +--------+
    | ename  |
    +--------+
    | SMITH  |
    | ALLEN  |
    | WARD   |
    | JONES  |
    | MARTIN |
    | BLAKE  |
    | CLARK  |
    | KING   |
    | TURNER |
    | ADAMS  |
    | JAMES  |
    | MILLER |
    +--------+
    12 rows in set (0.00 sec)
*/
```

### between ... and ...

```mysql
# 找出工资在1100到3000的员工
select ename from emp where sal between 1100 and 3000;
# select ename from emp where sal>=1100 and sal<=3000;
/*
    +--------+
    | ename  |
    +--------+
    | ALLEN  |
    | WARD   |
    | JONES  |
    | MARTIN |
    | BLAKE  |
    | CLARK  |
    | SCOTT  |
    | TURNER |
    | ADAMS  |
    | FORD   |
    | MILLER |
    +--------+
    11 rows in set (0.00 sec)
*/

# 左小右大，左右闭区间
```

```mysql
# 找出姓名首字母在A到C之间的员工
select ename from emp where ename between 'A' and 'D';
/*
    +-------+
    | ename |
    +-------+
    | ALLEN |
    | BLAKE |
    | CLARK |
    | ADAMS |
    +-------+
    4 rows in set (0.00 sec)
*/

# 用在字符左开右闭
```

### is null

- 数据库中NULL不是一个值，代表什么也没有，为空
- 不能用等号衡量
- 必须使用`is null`或者`is not null`

```mysql
# 找出哪些人没有津贴
select ename, sal, comm from emp where comm is null or comm=0;
/*
    +--------+---------+------+
    | ename  | sal     | comm |
    +--------+---------+------+
    | SMITH  |  800.00 | NULL |
    | JONES  | 2975.00 | NULL |
    | BLAKE  | 2850.00 | NULL |
    | CLARK  | 2450.00 | NULL |
    | SCOTT  | 3000.00 | NULL |
    | KING   | 5000.00 | NULL |
    | TURNER | 1500.00 | 0.00 |
    | ADAMS  | 1100.00 | NULL |
    | JAMES  |  950.00 | NULL |
    | FORD   | 3000.00 | NULL |
    | MILLER | 1300.00 | NULL |
    +--------+---------+------+
    11 rows in set (0.00 sec)
*/
```

### and or

```mysql
# 找出工作岗位是MANAGER和SALESMAN的员工
select ename, job from emp where job='MANAGER' or job='SALESMAN';
/*
    +--------+----------+
    | ename  | job      |
    +--------+----------+
    | ALLEN  | SALESMAN |
    | WARD   | SALESMAN |
    | JONES  | MANAGER  |
    | MARTIN | SALESMAN |
    | BLAKE  | MANAGER  |
    | CLARK  | MANAGER  |
    | TURNER | SALESMAN |
    +--------+----------+
    7 rows in set (0.00 sec)
*/
```

```mysql
# 找出薪资大于3000并且部门编号是20或30的员工
select ename, sal, deptno from emp where sal>1000 and (deptno=20 or deptno=30);
/*
    +--------+---------+--------+
    | ename  | sal     | deptno |
    +--------+---------+--------+
    | ALLEN  | 1600.00 |     30 |
    | WARD   | 1250.00 |     30 |
    | JONES  | 2975.00 |     20 |
    | MARTIN | 1250.00 |     30 |
    | BLAKE  | 2850.00 |     30 |
    | SCOTT  | 3000.00 |     20 |
    | TURNER | 1500.00 |     30 |
    | ADAMS  | 1100.00 |     20 |
    | FORD   | 3000.00 |     20 |
    +--------+---------+--------+
    9 rows in set (0.00 sec)
*/

# and优先级大于or
```

### in

- in等同于or

```mysql
# 找出工作岗位是MANAGER和SALESMAN的员工？
select ename, job from emp where job in('SALESMAN', 'MANAGER');
select ename, job from emp where job='SALESMAN' or job='MANAGER';
/*
    +--------+----------+
    | ename  | job      |
    +--------+----------+
    | ALLEN  | SALESMAN |
    | WARD   | SALESMAN |
    | JONES  | MANAGER  |
    | MARTIN | SALESMAN |
    | BLAKE  | MANAGER  |
    | CLARK  | MANAGER  |
    | TURNER | SALESMAN |
    +--------+----------+
    7 rows in set (0.00 sec)
*/
```

- not in：不在in值当中

```mysql
# 找出工资不是800和5000的员工
select ename, sal from emp where sal not in(800, 5000);
/*
    +--------+---------+
    | ename  | sal     |
    +--------+---------+
    | ALLEN  | 1600.00 |
    | WARD   | 1250.00 |
    | JONES  | 2975.00 |
    | MARTIN | 1250.00 |
    | BLAKE  | 2850.00 |
    | CLARK  | 2450.00 |
    | SCOTT  | 3000.00 |
    | TURNER | 1500.00 |
    | ADAMS  | 1100.00 |
    | JAMES  |  950.00 |
    | FORD   | 3000.00 |
    | MILLER | 1300.00 |
    +--------+---------+
    12 rows in set (0.00 sec)
*/
```

- in 表中若有 null，需手动排除

  ```mysql
  # 取得普通员工（员工代码不在 mgr 字段上）
  select ename
  from emp
  where empno not in (
  	select mgr
      from emp
      where mgr is not null
  );
  /*
      +--------+
      | ename  |
      +--------+
      | SMITH  |
      | ALLEN  |
      | WARD   |
      | MARTIN |
      | TURNER |
      | ADAMS  |
      | JAMES  |
      | MILLER |
      +--------+
  */
  ```

### like模糊查询

- % 代表任意多个字符
- _ 代表任意一个字符

```mysql
# 找出名字当中有“O”的员工
select ename from emp where ename like '%O%';
/*
    +-------+
    | ename |
    +-------+
    | JONES |
    | SCOTT |
    | FORD  |
    +-------+
    3 rows in set (0.00 sec)
*/
```

```mysql
# 找出第二个字母是“A”的员工
select ename from emp where ename like '_A%';
/*
    +--------+
    | ename  |
    +--------+
    | WARD   |
    | MARTIN |
    | JAMES  |
    +--------+
    3 rows in set (0.00 sec)
*/
```

```mysql
# 找出名字中带有下划线的员工
select ename from emp where ename like '%\_%';

# 使用转义符
```

```mysql
# 找出名字中最后一个字母是“T”的员工
select ename from emp where ename like '%T';
/*
    +-------+
    | ename |
    +-------+
    | SCOTT |
    +-------+
    1 row in set (0.00 sec)
*/

# oracle账户中有一个默认账户为SCOTT，密码是Tiger，用于学习数据库的演示账户
```

## 排序（升序，降序）

```mysql
# 按照工资升序，找出员工名和薪资？
select ename, sal from emp order by sal;
/*
    +--------+---------+
    | ename  | sal     |
    +--------+---------+
    | SMITH  |  800.00 |
    | JAMES  |  950.00 |
    | ADAMS  | 1100.00 |
    | WARD   | 1250.00 |
    | MARTIN | 1250.00 |
    | MILLER | 1300.00 |
    | TURNER | 1500.00 |
    | ALLEN  | 1600.00 |
    | CLARK  | 2450.00 |
    | BLAKE  | 2850.00 |
    | JONES  | 2975.00 |
    | FORD   | 3000.00 |
    | SCOTT  | 3000.00 |
    | KING   | 5000.00 |
    +--------+---------+
    14 rows in set (0.00 sec)
*/

# 升序asc（可省略）
select ename, sal from emp order by sal asc;
# 降序desc
select ename, sal from emp order by sal desc;
```

```mysql
# 按照工资的降序排列，当工资相同时按照名字升序排列
select ename, sal from emp order by sal desc, ename asc;
/*
    +--------+---------+
    | ename  | sal     |
    +--------+---------+
    | KING   | 5000.00 |
    | FORD   | 3000.00 |
    | SCOTT  | 3000.00 |
    | JONES  | 2975.00 |
    | BLAKE  | 2850.00 |
    | CLARK  | 2450.00 |
    | ALLEN  | 1600.00 |
    | TURNER | 1500.00 |
    | MILLER | 1300.00 |
    | MARTIN | 1250.00 |
    | WARD   | 1250.00 |
    | ADAMS  | 1100.00 |
    | JAMES  |  950.00 |
    | SMITH  |  800.00 |
    +--------+---------+
    14 rows in set (0.00 sec)
*/

# 多个排序字段使用“，”隔开
# 越靠前的字段主导能力越强
# 当前面的字段无法排序时，遵从后面的排序规则
# 排序可以按照select的字段顺序数排序（不够健壮）
select * from emp order by 6;
```

```mysql
# 找出工作岗位是SALESMAN的员工，并且按照工资的降序排列
select ename, job, sal from emp where job='SALESMAN' order by sal desc;
/*
    +--------+----------+---------+
    | ename  | job      | sal     |
    +--------+----------+---------+
    | ALLEN  | SALESMAN | 1600.00 |
    | TURNER | SALESMAN | 1500.00 |
    | WARD   | SALESMAN | 1250.00 |
    | MARTIN | SALESMAN | 1250.00 |
    +--------+----------+---------+
    4 rows in set (0.00 sec)
*/

# 执行顺序：from -> where -> select -> order by
```

## 分组函数（多行处理行数）

特点：输入多行，输出一行

> 单行处理函数：输入一行，输出一行
>
> **任何数据与NULL运算结果都为NULL**（使用`ifnull([column], 0)`空处理函数将NULL转换为0）
>
> ```mysql
> select ename, ifnull(comm, 0) from emp;
> /*
> +--------+-----------------+
> | ename  | ifnull(comm, 0) |
> +--------+-----------------+
> | SMITH  |            0.00 |
> | ALLEN  |          300.00 |
> | WARD   |          500.00 |
> | JONES  |            0.00 |
> | MARTIN |         1400.00 |
> | BLAKE  |            0.00 |
> | CLARK  |            0.00 |
> | SCOTT  |            0.00 |
> | KING   |            0.00 |
> | TURNER |            0.00 |
> | ADAMS  |            0.00 |
> | JAMES  |            0.00 |
> | FORD   |            0.00 |
> | MILLER |            0.00 |
> +--------+-----------------+
> 14 rows in set (0.01 sec)
> */
> ```
>
> ```mysql
> # 计算每个员工的年薪
> select ename, (sal+ifnull(comm,0))*12 as year_sal from emp;
> /*
> +--------+----------+
> | ename  | year_sal |
> +--------+----------+
> | SMITH  |  9600.00 |
> | ALLEN  | 22800.00 |
> | WARD   | 21000.00 |
> | JONES  | 35700.00 |
> | MARTIN | 31800.00 |
> | BLAKE  | 34200.00 |
> | CLARK  | 29400.00 |
> | SCOTT  | 36000.00 |
> | KING   | 60000.00 |
> | TURNER | 18000.00 |
> | ADAMS  | 13200.00 |
> | JAMES  | 11400.00 |
> | FORD   | 36000.00 |
> | MILLER | 15600.00 |
> +--------+----------+
> 14 rows in set (0.00 sec)
> */
> ```

分组函数只有5个

- count 计数
- sum 求和
- avg 平均值
- max 最大值
- min 最小值

> 所有的分组函数都是对**“某一组”数据**进行操作的
>
> **分组函数自动忽略NULL**
>
> 分组函数不可以直接出现在where语句当中（分组函数执行在group by、having后，而这两个在where后）
>
> count(*)和count([column])：前者统计总记录条数，后者统计字段中不为NULL的元素总数
>
> 分组处理函数可组合起来使用

```mysql
# 找出工资总和
select sum(sal) from emp;
/*
    +----------+
    | sum(sal) |
    +----------+
    | 29025.00 |
    +----------+
    1 row in set (0.01 sec)
*/
```

```mysql
# 找出工资高于平均工资的员工（子查询）
select ename, sal from emp where sal>(select avg(sal) from emp);
/*
    +-------+---------+
    | ename | sal     |
    +-------+---------+
    | JONES | 2975.00 |
    | BLAKE | 2850.00 |
    | CLARK | 2450.00 |
    | SCOTT | 3000.00 |
    | KING  | 5000.00 |
    | FORD  | 3000.00 |
    +-------+---------+
    6 rows in set (0.00 sec)
*/
```

## group by 和 having

- group by：按照某个字段或者某些字段进行分组
- having：对分组之后的数据进行再次过滤

> 执行顺序：from -> where -> group by -> having -> select分组函数 -> order by
>
> 分组函数一般会与group by联合使用
>
> sql语句没有group by，整张语句将会自成一组
>
> **当一条语句中有group by，select后面只能跟分组函数和分组的字段**
>
> 在where中筛选条件比使用having效率更高

```mysql
# 找出每个工作岗位的最高薪资
select job, max(sal) from emp group by job;
/*
    +-----------+----------+
    | job       | max(sal) |
    +-----------+----------+
    | ANALYST   |  3000.00 |
    | CLERK     |  1300.00 |
    | MANAGER   |  2975.00 |
    | PRESIDENT |  5000.00 |
    | SALESMAN  |  1600.00 |
    +-----------+----------+
    5 rows in set (0.01 sec)
*/
```

```mysql
# 找出每个工作岗位的平均工资
select job, avg(sal) from emp group by job;
/*
    +-----------+-------------+
    | job       | avg(sal)    |
    +-----------+-------------+
    | ANALYST   | 3000.000000 |
    | CLERK     | 1037.500000 |
    | MANAGER   | 2758.333333 |
    | PRESIDENT | 5000.000000 |
    | SALESMAN  | 1400.000000 |
    +-----------+-------------+
    5 rows in set (0.00 sec)
*/
```

```mysql
# 找出每个部门不同工作岗位的最高薪资
select deptno, job, max(sal) from emp group by deptno, job;
/*
    +--------+-----------+----------+
    | deptno | job       | max(sal) |
    +--------+-----------+----------+
    |     10 | CLERK     |  1300.00 |
    |     10 | MANAGER   |  2450.00 |
    |     10 | PRESIDENT |  5000.00 |
    |     20 | ANALYST   |  3000.00 |
    |     20 | CLERK     |  1100.00 |
    |     20 | MANAGER   |  2975.00 |
    |     30 | CLERK     |   950.00 |
    |     30 | MANAGER   |  2850.00 |
    |     30 | SALESMAN  |  1600.00 |
    +--------+-----------+----------+
    9 rows in set (0.00 sec)
*/
```

```mysql
# 找出每个部门的最高薪资，要求显示薪资大于2900的数据

# 效率过低，可以先筛除数据，再进行分组
# select deptno, max(sal) from emp group by deptno having max(sal)>2900;

# 效率高，优先考虑使用where过滤数据
select deptno, max(sal) from emp where sal>2900 group by deptno;
/*
    +--------+----------+
    | deptno | max(sal) |
    +--------+----------+
    |     10 |  5000.00 |
    |     20 |  3000.00 |
    +--------+----------+
    2 rows in set (0.00 sec)
*/
```

```mysql
# 找出每个部门的平均薪资，要求显示薪资大于2000的数据（where搞不定）
select deptno, avg(sal) from emp group by deptno having avg(sal)>2000;
/*
    +--------+-------------+
    | deptno | avg(sal)    |
    +--------+-------------+
    |     10 | 2916.666667 |
    |     20 | 2175.000000 |
    +--------+-------------+
    2 rows in set (0.00 sec)
*/
```

## 完整的DQL语句

```mysql
select		5
	...
from		1
	...
where		2
	...
group by	3
	...
having		4
	...
order by	6
	...
```

## 查询结果集的去重

select后加上distinct关键字即可

> distinct只能出现在所有字段前

```mysql
select distinct job from emp;
/*
    +-----------+
    | job       |
    +-----------+
    | CLERK     |
    | SALESMAN  |
    | MANAGER   |
    | ANALYST   |
    | PRESIDENT |
    +-----------+
    5 rows in set (0.00 sec)
*/
```

```mysql
# 统计岗位数量
select count(distinct job) from emp;
/*
    +---------------------+
    | count(distinct job) |
    +---------------------+
    |                   5 |
    +---------------------+
    1 row in set (0.00 sec)
*/
```

## 笛卡尔积现象

当两张表进行连接查询的时候，没有任何条件进行限制，最终的查询结果是两张表记录条数的乘积

```mysql
# 找出每一个员工的部门名称，要求显示员工名和部门名
select ename,dname from emp, dept;
/*
    +--------+------------+
    | ename  | dname      |
    +--------+------------+
    | SMITH  | ACCOUNTING |
    | SMITH  | RESEARCH   |
    | SMITH  | SALES      |
    | SMITH  | OPERATIONS |
    | ALLEN  | ACCOUNTING |
    | ALLEN  | RESEARCH   |
    | ALLEN  | SALES      |
    | ALLEN  | OPERATIONS |
    ...
    56 rows in set (0.01 sec)
*/
```

加条件进行过滤以避免笛卡尔积现象

> 避免了笛卡尔积现象，不会减少记录的匹配次数，只不过显示的是有效记录

```mysql
# 找出每一个员工的部门名称，要求显示员工名和部门名
# SQL92语法，以后不用
select e.ename, d.dname
from emp e, dept d
where e.deptno = d.deptno;
/*
    +--------+------------+
    | ename  | dname      |
    +--------+------------+
    | CLARK  | ACCOUNTING |
    | KING   | ACCOUNTING |
    | MILLER | ACCOUNTING |
    | SMITH  | RESEARCH   |
    | JONES  | RESEARCH   |
    | SCOTT  | RESEARCH   |
    | ADAMS  | RESEARCH   |
    | FORD   | RESEARCH   |
    | ALLEN  | SALES      |
    | WARD   | SALES      |
    | MARTIN | SALES      |
    | BLAKE  | SALES      |
    | TURNER | SALES      |
    | JAMES  | SALES      |
    +--------+------------+
    14 rows in set (0.00 sec)
*/
```



## 表的别名

关于表的别名

`select e.name,d.dname from emp e,dept d;`

- 执行效率高
- 可读性好

# 连接查询

实际的业务中，大部分情况都是多张表联合查询取出最终结果，否则存在数据冗余

根据表的连接方式来分类，包括

- 内连接
  - 等值连接：等量关系
  - 非等值连接：例如between...and...
  - 自连接
- 外连接
  - 左外连接（左连接）
  - 右外连接（右连接）
- 全连接（很少用）

## 内连接

A和B表进行连接，凡是A表和B表能够匹配上的记录查询出来，就是内连接

> AB两张表没有主副之分，两张表平等
>
> `(inner) join [table] on [table]`语法inner可省略

### 等值连接

最大的特点是条件是等量关系

语法：`... A join B on 连接条件 where ...`

```mysql
# 查询每个员工的部门名称，要求显示员工名和部门名

# SQL92（古早）
select e.ename, d.dname
from emp e, dept d
where e.deptno = d.deptno;

# SQL99（常用）
# SQL99语法结构更清晰一些：表的连接条件和后来的where条件分离
select e.ename, d.dname
from emp e
inner join dept d
on e.deptno = d.deptno;

/*
    +--------+------------+
    | ename  | dname      |
    +--------+------------+
    | CLARK  | ACCOUNTING |
    | KING   | ACCOUNTING |
    | MILLER | ACCOUNTING |
    | SMITH  | RESEARCH   |
    | JONES  | RESEARCH   |
    | SCOTT  | RESEARCH   |
    | ADAMS  | RESEARCH   |
    | FORD   | RESEARCH   |
    | ALLEN  | SALES      |
    | WARD   | SALES      |
    | MARTIN | SALES      |
    | BLAKE  | SALES      |
    | TURNER | SALES      |
    | JAMES  | SALES      |
    +--------+------------+
    14 rows in set (0.01 sec)
*/
```

### 非等值连接

```mysql
# 找出每个员工的工资等级，要求显示员工名、工资、工资等级
select e.ename, e.sal, s.grade
from emp e
inner join salgrade s
on e.sal between s.losal and s.hisal;
/*
    +--------+---------+-------+
    | ename  | sal     | grade |
    +--------+---------+-------+
    | SMITH  |  800.00 |     1 |
    | ALLEN  | 1600.00 |     3 |
    | WARD   | 1250.00 |     2 |
    | JONES  | 2975.00 |     4 |
    | MARTIN | 1250.00 |     2 |
    | BLAKE  | 2850.00 |     4 |
    | CLARK  | 2450.00 |     4 |
    | SCOTT  | 3000.00 |     4 |
    | KING   | 5000.00 |     5 |
    | TURNER | 1500.00 |     3 |
    | ADAMS  | 1100.00 |     1 |
    | JAMES  |  950.00 |     1 |
    | FORD   | 3000.00 |     4 |
    | MILLER | 1300.00 |     2 |
    +--------+---------+-------+
    14 rows in set (0.02 sec)
*/
```

### 自连接

最大的特点是：一张表看作两张表（自己连接自己）

```mysql
# 找出每个员工的上级领导，要求显示员工名和对应的领导名
# 内连接跳过了King（最高领导）
select a.ename as emp_name, b.ename as mgr_name
from emp a
inner join emp b
on a.mgr = b.empno;
/*
    +----------+----------+
    | emp_name | mgr_name |
    +----------+----------+
    | SMITH    | FORD     |
    | ALLEN    | BLAKE    |
    | WARD     | BLAKE    |
    | JONES    | KING     |
    | MARTIN   | BLAKE    |
    | BLAKE    | KING     |
    | CLARK    | KING     |
    | SCOTT    | JONES    |
    | TURNER   | BLAKE    |
    | ADAMS    | SCOTT    |
    | JAMES    | BLAKE    |
    | FORD     | JONES    |
    | MILLER   | CLARK    |
    +----------+----------+
    13 rows in set (0.00 sec)
*/
```

## 外连接

A和B表进行连接，AB两张表中有一张是主表，一张表是副表，主要查询主表的数据，捎带着查询副表

> 当副表没有数据和主表匹配上，**副表自动模拟出NULL与之匹配**（区别于内连接的直接丢弃）
>
> 左右外连接可以相互转换
>
> `[left|right] (outer) join [table]`中outer可以省略
>
> 实际开发外连接使用居多

### 左外连接（左连接）

左边的是主表

```mysql
# 找出每个员工的上级领导，显示员工名和领导
# 外连接包含King（最高领导），显示为NULL
select a.ename as emp_name, b.ename as mgr_name
from emp a
left join emp b
on a.mgr = b.empno;
/*
    +----------+----------+
    | emp_name | mgr_name |
    +----------+----------+
    | SMITH    | FORD     |
    | ALLEN    | BLAKE    |
    | WARD     | BLAKE    |
    | JONES    | KING     |
    | MARTIN   | BLAKE    |
    | BLAKE    | KING     |
    | CLARK    | KING     |
    | SCOTT    | JONES    |
    | KING     | NULL     |
    | TURNER   | BLAKE    |
    | ADAMS    | SCOTT    |
    | JAMES    | BLAKE    |
    | FORD     | JONES    |
    | MILLER   | CLARK    |
    +----------+----------+
    14 rows in set (0.03 sec)
*/
```



### 右外连接（右连接）

右边的是主表

```mysql
# 找出每个员工的上级领导，显示员工名和领导
# 外连接包含King（最高领导），显示为NULL
select a.ename, b.ename
from emp b
right join emp a
on a.mgr = b.empno;
/*
    +--------+-------+
    | ename  | ename |
    +--------+-------+
    | SMITH  | FORD  |
    | ALLEN  | BLAKE |
    | WARD   | BLAKE |
    | JONES  | KING  |
    | MARTIN | BLAKE |
    | BLAKE  | KING  |
    | CLARK  | KING  |
    | SCOTT  | JONES |
    | KING   | NULL  |
    | TURNER | BLAKE |
    | ADAMS  | SCOTT |
    | JAMES  | BLAKE |
    | FORD   | JONES |
    | MILLER | CLARK |
    +--------+-------+
    14 rows in set (0.00 sec)
*/
```

### Puzzle

```mysql
# 找出哪个部门没有员工
select d.*
from emp e
right join dept d
on d.deptno = e.deptno
where e.deptno is null;
/*
    +--------+------------+--------+
    | DEPTNO | DNAME      | LOC    |
    +--------+------------+--------+
    |     40 | OPERATIONS | BOSTON |
    +--------+------------+--------+
    1 row in set (0.01 sec)
*/
```

```mysql
# 三张表查询
# 找出每一个员工的部门名称以及工资等级
select e.ename, d.dname, s.grade
from emp e
join dept d
on e.deptno=d.deptno
join salgrade s
on e.sal between s.losal and s.hisal;
/*
    +--------+------------+-------+
    | ename  | dname      | grade |
    +--------+------------+-------+
    | SMITH  | RESEARCH   |     1 |
    | ALLEN  | SALES      |     3 |
    | WARD   | SALES      |     2 |
    | JONES  | RESEARCH   |     4 |
    | MARTIN | SALES      |     2 |
    | BLAKE  | SALES      |     4 |
    | CLARK  | ACCOUNTING |     4 |
    | SCOTT  | RESEARCH   |     4 |
    | KING   | ACCOUNTING |     5 |
    | TURNER | SALES      |     3 |
    | ADAMS  | RESEARCH   |     1 |
    | JAMES  | SALES      |     1 |
    | FORD   | RESEARCH   |     4 |
    | MILLER | ACCOUNTING |     2 |
    +--------+------------+-------+
    14 rows in set (0.00 sec)
*/
```

```mysql
# 找出每个员工的部门名称、工资等级以及上级领导
select e.ename, d.dname, s.grade, e1.ename as mgr
from emp e
join dept d
on e.deptno = d.deptno
join salgrade s
on e.sal between s.losal and s.hisal
left join emp e1
on e.mgr = e1.empno;
/*
    +--------+------------+-------+-------+
    | ename  | dname      | grade | mgr   |
    +--------+------------+-------+-------+
    | SMITH  | RESEARCH   |     1 | FORD  |
    | ALLEN  | SALES      |     3 | BLAKE |
    | WARD   | SALES      |     2 | BLAKE |
    | JONES  | RESEARCH   |     4 | KING  |
    | MARTIN | SALES      |     2 | BLAKE |
    | BLAKE  | SALES      |     4 | KING  |
    | CLARK  | ACCOUNTING |     4 | KING  |
    | SCOTT  | RESEARCH   |     4 | JONES |
    | KING   | ACCOUNTING |     5 | NULL  |
    | TURNER | SALES      |     3 | BLAKE |
    | ADAMS  | RESEARCH   |     1 | SCOTT |
    | JAMES  | SALES      |     1 | BLAKE |
    | FORD   | RESEARCH   |     4 | JONES |
    | MILLER | ACCOUNTING |     2 | CLARK |
    +--------+------------+-------+-------+
    14 rows in set (0.00 sec)
*/
```

# 子查询

select语句中嵌套select语句，被嵌套的select语句是子查询

## 子查询出现位置

select

​	...(select)...

from

​	...(select)...

where

​	...(select)...

### where中嵌套子查询

```mysql
# 找出高于平均薪资的员工信息
select ename, sal
from emp
where sal > (select avg(sal) from emp);
/*
    +-------+---------+
    | ename | sal     |
    +-------+---------+
    | JONES | 2975.00 |
    | BLAKE | 2850.00 |
    | CLARK | 2450.00 |
    | SCOTT | 3000.00 |
    | KING  | 5000.00 |
    | FORD  | 3000.00 |
    +-------+---------+
    6 rows in set (0.00 sec)
*/
```

### from中嵌套子查询

```mysql
# 不一定非要在from中嵌套子查询
# 找出每个部门平均薪水的等级
select t.*, s.grade
from (select deptno, avg(sal) as avg_sal from emp group by deptno) t
join salgrade s
on t.avg_sal between s.losal and s.hisal;
/*
    +--------+-------------+-------+
    | deptno | avg_sal     | grade |
    +--------+-------------+-------+
    |     30 | 1566.666667 |     3 |
    |     10 | 2916.666667 |     4 |
    |     20 | 2175.000000 |     4 |
    +--------+-------------+-------+
    3 rows in set (0.00 sec)
*/
```

```mysql
# 找出每个部门平均的薪水等级
select e.deptno, avg(grade) as avg_grade
from emp e
join salgrade s
on e.sal between s.losal and s.hisal
group by e.deptno;
/*
    +--------+-----------+
    | deptno | avg_grade |
    +--------+-----------+
    |     10 |    3.6667 |
    |     20 |    2.8000 |
    |     30 |    2.5000 |
    +--------+-----------+
    3 rows in set (0.00 sec)
*/
```

### 在select中嵌套子查询

```mysql
# 找出每个员工所在的部门名，要求显示员工名和部门名
/* 
	# 之前的方法
    select e.ename, d.dname
    from emp e
    join dept d
    on e.deptno = d.deptno;
*/

# 本方法
select e.ename, (
    select d.dname
    from dept d
    where e.deptno = d.deptno
) as dname
from emp e;

/*
    +--------+------------+
    | ename  | dname      |
    +--------+------------+
    | SMITH  | RESEARCH   |
    | ALLEN  | SALES      |
    | WARD   | SALES      |
    | JONES  | RESEARCH   |
    | MARTIN | SALES      |
    | BLAKE  | SALES      |
    | CLARK  | ACCOUNTING |
    | SCOTT  | RESEARCH   |
    | KING   | ACCOUNTING |
    | TURNER | SALES      |
    | ADAMS  | RESEARCH   |
    | JAMES  | SALES      |
    | FORD   | RESEARCH   |
    | MILLER | ACCOUNTING |
    +--------+------------+
    14 rows in set (0.00 sec)
*/
```

# union

可以将查询结果集相加（可以用作两张不相关的表）

```mysql
# 找出工作岗位是SALESMAN和MANAGER的员工？

# 经典写法（or或in）
# select ename,job from emp where job in('SALESMAN', 'MANAGER');
/*
    +--------+----------+
    | ename  | job      |
    +--------+----------+
    | ALLEN  | SALESMAN |
    | WARD   | SALESMAN |
    | JONES  | MANAGER  |
    | MARTIN | SALESMAN |
    | BLAKE  | MANAGER  |
    | CLARK  | MANAGER  |
    | TURNER | SALESMAN |
    +--------+----------+
    7 rows in set (0.01 sec)
*/

# union写法（结果一样但顺序不一样）
select ename, job from emp where job='SALESMAN'
union
select ename, job from emp where job='MANAGER';
/*
    +--------+----------+
    | ename  | job      |
    +--------+----------+
    | ALLEN  | SALESMAN |
    | WARD   | SALESMAN |
    | MARTIN | SALESMAN |
    | TURNER | SALESMAN |
    | JONES  | MANAGER  |
    | BLAKE  | MANAGER  |
    | CLARK  | MANAGER  |
    +--------+----------+
    7 rows in set (0.01 sec)
*/
```

# *limit

- 用作分页查询，取结果集中的部分数据

- 语法机制：`limit [startIndex], [length]`

  - startIndex表示起始位置

  - length表示取几个

> MySQL特有（Oracle有相同的机制：rownum）	
>
> 若只填一个数字，则默认`startIndex`为0
>
> limit 是sql语句最后执行的一个环节
>
> ```mysql
> # sql语句执行顺序
> select		5	
> 	...
> from		1
> 	...
> where		2
> 	...
> group by	3
> 	...
> having		4
> 	...
> order by	6
> 	...
> limit		7
> 	...;
> ```

```mysql
# 取出工资前5名的员工（思路：降序取前5个）
select ename,sal from emp order by sal desc limit 0, 5;
/*
    +-------+---------+
    | ename | sal     |
    +-------+---------+
    | KING  | 5000.00 |
    | SCOTT | 3000.00 |
    | FORD  | 3000.00 |
    | JONES | 2975.00 |
    | BLAKE | 2850.00 |
    +-------+---------+
    5 rows in set (0.00 sec)
*/
```

```mysql
# 找出工资排名在第4到第9名的员工？
select ename, sal
from emp
order by sal desc
limit 3, 6;
/*
    +--------+---------+
    | ename  | sal     |
    +--------+---------+
    | JONES  | 2975.00 |
    | BLAKE  | 2850.00 |
    | CLARK  | 2450.00 |
    | ALLEN  | 1600.00 |
    | TURNER | 1500.00 |
    | MILLER | 1300.00 |
    +--------+---------+
    6 rows in set (0.00 sec)
*/
```

## 通用的标准分页sql

每页显示`page_size`条记录

第`page_no`页：`limit (pages_no-1)*page_size, page_size`

```java
// Java代码示例
int page_no = 2;	// 页码是2
int page_size = 10;	// 每页显示10条

limit (page_no-1)*page_size, page_size;
```

