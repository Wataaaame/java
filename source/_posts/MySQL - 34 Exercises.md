---
title: MySQL - 34 Exercises
date: 2022-07-13 18:30:25
categories: MySQL
tags: Exercises
---

# 34道练习题

1. 取得每个部门最高薪水的人员名称

   ```mysql
   # 取得每个部门最高薪水
   select deptno, max(sal) as maxsal
   from emp
   group by deptno;
   /*
       +--------+---------+
       | deptno | maxsal  |
       +--------+---------+
       |     10 | 5000.00 |
       |     20 | 3000.00 |
       |     30 | 2850.00 |
       +--------+---------+
   */
   
   # 将以上结果作为临时表 t，t 表与 emp e 表进行连接
   select e.ename, t.*
   from (select deptno, max(sal) as maxsal
        from emp
        group by deptno) t
   join emp e
   on t.deptno = e.deptno AND t.maxsal = e.sal;
   /*
       +-------+--------+---------+
       | ename | deptno | maxsal  |
       +-------+--------+---------+
       | BLAKE |     30 | 2850.00 |
       | SCOTT |     20 | 3000.00 |
       | KING  |     10 | 5000.00 |
       | FORD  |     20 | 3000.00 |
       +-------+--------+---------+
   */
   ```

2. 哪些人的薪水在部门的平均薪水之上

   ```mysql
   # 部门平均薪水
   select deptno, avg(sal) avgsal
   from emp
   group by deptno;
   /*
       +--------+-------------+
       | deptno | avgsal      |
       +--------+-------------+
       |     10 | 2916.666667 |
       |     20 | 2175.000000 |
       |     30 | 1566.666667 |
       +--------+-------------+
   */
   
   # 高于平均薪水名单
   select e.ename, e.sal, t.*
   from emp e
   join (select deptno, avg(sal) avgsal
   	from emp
   	group by deptno) t
   on e.deptno = t.deptno AND e.sal > avgsal;
   /*
       +-------+---------+--------+-------------+
       | ename | sal     | deptno | avgsal      |
       +-------+---------+--------+-------------+
       | ALLEN | 1600.00 |     30 | 1566.666667 |
       | JONES | 2975.00 |     20 | 2175.000000 |
       | BLAKE | 2850.00 |     30 | 1566.666667 |
       | SCOTT | 3000.00 |     20 | 2175.000000 |
       | KING  | 5000.00 |     10 | 2916.666667 |
       | FORD  | 3000.00 |     20 | 2175.000000 |
       +-------+---------+--------+-------------+
   */
   ```

3. 取得部门中（所有人的）平均薪水等级

   ```mysql
   # 获取薪水等级表
   select e.ename, e.sal, s.grade
   from emp e
   join salgrade s
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
   */
   
   # 求平均薪水等级（按部门分组）
   select e.deptno, avg(s.grade)
   from emp e
   join salgrade s
   on e.sal between s.losal and s.hisal
   group by e.deptno;
   /*
       +--------+--------------+
       | deptno | avg(s.grade) |
       +--------+--------------+
       |     10 |       3.6667 |
       |     20 |       2.8000 |
       |     30 |       2.5000 |
       +--------+--------------+
   */
   ```

   - 重复连接的表不考虑嵌套

4. **不准用组函数（Max），取得最高薪水**（给出两种方案）

   ```mysql
   # 1. 降序，limit 1
   select sal from emp order by sal desc limit 1;
   
   # 2. 自连接
   select sal
   from emp
   where sal not in (
   	select distinct a.sal
       from emp a
       join emp b
       on a.sal < b.sal
   );
   
   /*
       +---------+
       | sal     |
       +---------+
       | 5000.00 |
       +---------+
   */
   ```

   - a.sal < b.sal，则列出除最大值以外的所有工资表 t（笛卡尔积需去重）

     再与 emp 连接，不在 t 中的只剩最大值

5. 取得平均薪水最高的部门的部门编号（至少给出两种方案）

   ```mysql
   # 每个部门平均薪水
   select deptno, avg(sal) avgsal
   from emp
   group by deptno;
   /*
       +--------+-------------+
       | deptno | avgsal      |
       +--------+-------------+
       |     10 | 2916.666667 |
       |     20 | 2175.000000 |
       |     30 | 1566.666667 |
       +--------+-------------+
   */
   
   # 1 最高部门
   select deptno
   from emp
   group by deptno
   order by avg(sal) desc limit 1;
   
   # 2 max
   select deptno
   from emp
   group by deptno
   having avg(sal) = (
   	select max(avgsal)
   	from (
   		select deptno, avg(sal) avgsal
       	from emp
       	group by deptno
   	) t
   );
   
   ;
   
   # 3 自连接
   select distinct deptno
   from (
       select deptno, avg(sal) avgsal
       from emp
       group by deptno
   ) t
   where avgsal not in (
   	select a.avgsal
       from (
       	select avg(sal) avgsal
       	from emp
       	group by deptno
       ) a
       join (
       	select avg(sal) avgsal
       	from emp
       	group by deptno
       ) b
       on a.avgsal < b.avgsal
   );
   
   /*
       +--------+
       | deptno |
       +--------+
       |     10 |
       +--------+
   */
   ```

6. 取得平均薪水最高的部门的部门名称

   ```mysql
   # 同第 5 题
   # 借用 5-1
   select d.dname
   from emp e
   join dept d
   on e.deptno = d.deptno
   group by e.deptno
   order by avg(e.sal) desc limit 1;
   
   /*
       +------------+
       | dname      |
       +------------+
       | ACCOUNTING |
       +------------+
   */
   ```

7. 求平均薪水的等级最低的部门的部门名称

   ```mysql
   # 部门平均薪水
   select deptno, avg(sal) avg_sal
   from emp
   group by deptno;
   /*
       +--------+-------------+
       | deptno | avg_sal     |
       +--------+-------------+
       |     10 | 2916.666667 |
       |     20 | 2175.000000 |
       |     30 | 1566.666667 |
       +--------+-------------+
   */
   
   # 部门平均薪水的等级
   select s.grade, t.*
   from salgrade s
   join (
   	select deptno, avg(sal) avg_sal
       from emp
       group by deptno
   ) t
   on t.avg_sal between s.losal and s.hisal;
   /*
       +-------+--------+-------------+
       | grade | deptno | avg_sal     |
       +-------+--------+-------------+
       |     3 |     30 | 1566.666667 |
       |     4 |     10 | 2916.666667 |
       |     4 |     20 | 2175.000000 |
       +-------+--------+-------------+
   */
   
   # 部门平均薪水最低的等级
   select s.grade
   from salgrade s
   join (
   	select avg(sal) avg_sal
       from emp
       group by deptno
   ) t
   on t.avg_sal between s.losal and s.hisal
   order by t.avg_sal limit 1;
   /*
       +-------+
       | grade |
       +-------+
       |     3 |
       +-------+
   */
   
   # 平均薪水的等级最低的部门名称
   select s.grade, t.dname
   from salgrade s
   join (
   	select d.dname, avg(e.sal) avg_sal
       from emp e
       join dept d
       on e.deptno = d.deptno
       group by d.dname
   ) t
   on t.avg_sal between s.losal and s.hisal
   where s.grade = (
       select s.grade
       from salgrade s
       join (
           select avg(sal) avg_sal
           from emp
           group by deptno
       ) t
       on t.avg_sal between s.losal and s.hisal
       order by t.avg_sal limit 1
   );
   /*
       +-------+-------+
       | grade | dname |
       +-------+-------+
       |     3 | SALES |
       +-------+-------+
   */
   ```

   - 平均薪水最低一定是等级最低
   - 平均薪水不是最低，也可能是等级最低

8. 取得比普通员工（员工代码不在 mgr 字段上）的最高薪资还要高的领导人姓名

   ```mysql
   # 普通员工的最高薪资
   select max(sal)
   from emp
   where empno not in (
   	select mgr
       from emp
       where mgr is not null
   );
   /*
       +----------+
       | max(sal) |
       +----------+
       |  1600.00 |
       +----------+
   */
   
   # answer
   select ename
   from emp
   where sal > (
   	select max(sal)
       from emp
       where empno not in (
           select mgr
           from emp
           where mgr is not null
       )
   );
   ```

   - 比普通员工最高工资高的一定是领导

9. 取得薪水最高的前五名员工

   ```mysql
   select ename
   from emp
   order by sal desc limit 5;
   /*
       +-------+
       | ename |
       +-------+
       | KING  |
       | SCOTT |
       | FORD  |
       | JONES |
       | BLAKE |
       +-------+
   */
   ```

10. 取得薪水最高的第六到第十名员工

    ```mysql
    select ename
    from emp
    order by sal desc limit 5, 5;
    /*
        +--------+
        | ename  |
        +--------+
        | CLARK  |
        | ALLEN  |
        | TURNER |
        | MILLER |
        | MARTIN |
        +--------+
    */
    ```

11. 取得最后入职的5名员工

    ```mysql
    select ename, hiredate
    from emp
    order by hiredate desc limit 5;
    /*
        +--------+------------+
        | ename  | hiredate   |
        +--------+------------+
        | ADAMS  | 1987-05-23 |
        | SCOTT  | 1987-04-19 |
        | MILLER | 1982-01-23 |
        | FORD   | 1981-12-03 |
        | JAMES  | 1981-12-03 |
        +--------+------------+
    */
    ```

12. 取得每个薪水等级有多少员工

    ```mysql
    select s.grade, count(e.empno) num
    from salgrade s
    join emp e
    on e.sal between s.losal and s.hisal
    group by s.grade;
    /*
        +-------+-----+
        | grade | num |
        +-------+-----+
        |     1 |   3 |
        |     2 |   3 |
        |     3 |   2 |
        |     4 |   5 |
        |     5 |   1 |
        +-------+-----+
    */
    ```

13. 面试题

    有3个表 S（学生表），C（课程表），SC（学生选课表）

    S（SNO，SNAME）代表（学号，姓名）

    C（CNO，CNAME，CTEACHER）代表（课号，课名，教师）

    SC（SNO，CNO，SCGRADE）代表（学号，课号，成绩）

    问题：

    1. 找出没选过“黎明”老师的所有学生姓名
    
       ```mysql
       select s.sname
       from s
       join sc
       on s.sno = sc.sno
       join c
       on sc.cno = c.cno
       where c.cteacher not like '黎明';
       ```
    
    2. 列出2门以上（含两门）不及格学生姓名及平均成绩
    
       ```mysql
       select s.sname, avg(ifnull(sc.scgrade, 0)) avg_grade
       from s
       left join sc
       on s.sno = sc.sno
       where sc.scgrade < 60
       group by sc.cno
       having sc.cno >= 2;
       ```
    
    3. 既学过1号课程又学过2号课程所有学生的姓名
    
       ```mysql
       select s.sname
       from s
       join sc
       on s.sno = sc.sno
       where sc.cno = 1 or sc.cno = 2;
       ```
    
14. **列出所有员工及领导的姓名**

    ```mysql
    # 外连接
    select a.ename, b.ename mgr
    from emp a
    left join emp b
    on a.mgr = b.empno;
    /*
        +--------+-------+
        | ename  | mgr   |
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
    */
    ```

    - 逐条检索 emp a 中的 a.ename，若条目中的 mgr 存在于 emp b 中的 b.ename，则说明为该条目的领导，并打印领导名称
    - 外连接区别于内连接，不会忽略 null 

15. 列出受雇日期早于其直接上级的所有员工的编号，姓名，部门名称

    ```mysql
    # 受雇日期早于其直接上级的所有员工
    select t.empno, t.ename, d.dname
    from (
        select a.empno, a.ename, a.deptno
        from emp a
        left join emp b
        on a.mgr = b.empno
        where a.hiredate < b.hiredate
    ) t
    join dept d
    on t.deptno = d.deptno;
    
    # 支持连续 join
    select a.empno, a.ename, d.dname
    from emp a
    join emp b
    on a.mgr = b.empno and a.hiredate < b.hiredate
    join dept d
    on a.deptno = d.deptno;
    /*
        +-------+-------+------------+
        | empno | ename | dname      |
        +-------+-------+------------+
        |  7782 | CLARK | ACCOUNTING |
        |  7369 | SMITH | RESEARCH   |
        |  7566 | JONES | RESEARCH   |
        |  7499 | ALLEN | SALES      |
        |  7521 | WARD  | SALES      |
        |  7698 | BLAKE | SALES      |
        +-------+-------+------------+
    */
    ```

16. 列出部门名称和这些部门的员工信息，同时列出那些没有员工的部门

    ```mysql
    select d.dname, e.*
    from emp e
    right join dept d
    on e.deptno = d.deptno;
    
    /*
        +------------+-------+--------+-----------+------+---------+--------+
        | dname      | empno | ename  | job       | mgr  | sal     | deptno |
        +------------+-------+--------+-----------+------+---------+--------+
        | ACCOUNTING |  7782 | CLARK  | MANAGER   | 7839 | 2450.00 |     10 |
        | ACCOUNTING |  7839 | KING   | PRESIDENT | NULL | 5000.00 |     10 |
        | ACCOUNTING |  7934 | MILLER | CLERK     | 7782 | 1300.00 |     10 |
        | RESEARCH   |  7369 | SMITH  | CLERK     | 7902 |  800.00 |     20 |
        | RESEARCH   |  7566 | JONES  | MANAGER   | 7839 | 2975.00 |     20 |
        | RESEARCH   |  7788 | SCOTT  | ANALYST   | 7566 | 3000.00 |     20 |
        | RESEARCH   |  7876 | ADAMS  | CLERK     | 7788 | 1100.00 |     20 |
        | RESEARCH   |  7902 | FORD   | ANALYST   | 7566 | 3000.00 |     20 |
        | SALES      |  7499 | ALLEN  | SALESMAN  | 7698 | 1600.00 |     30 |
        | SALES      |  7521 | WARD   | SALESMAN  | 7698 | 1250.00 |     30 |
        | SALES      |  7654 | MARTIN | SALESMAN  | 7698 | 1250.00 |     30 |
        | SALES      |  7698 | BLAKE  | MANAGER   | 7839 | 2850.00 |     30 |
        | SALES      |  7844 | TURNER | SALESMAN  | 7698 | 1500.00 |     30 |
        | SALES      |  7900 | JAMES  | CLERK     | 7698 |  950.00 |     30 |
        | OPERATIONS |  NULL | NULL   | NULL      | NULL |    NULL |   NULL |
        +------------+-------+--------+-----------+------+---------+--------+
    */
    ```

17. 列出至少有5个员工的所有部门

    ```mysql
    select d.deptno, count(e.empno) num
    from emp e
    join dept d
    on e.deptno = d.deptno
    group by deptno
    having num >= 5;
    /*
        +--------+-----+
        | deptno | num |
        +--------+-----+
        |     20 |   5 |
        |     30 |   6 |
        +--------+-----+
    */
    ```

18. 列出薪资比 “SMITH” 高的所有员工信息

    ```mysql
    select *
    from emp
    where sal > (
    	select sal
        from emp
        where ename = 'smith'
    );
    /*
        +-------+--------+-----------+------+------------+---------+---------+--------+
        | EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
        +-------+--------+-----------+------+------------+---------+---------+--------+
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
    */
    ```

19. 列出所有 “CLERK”（办事员）的姓名及其部门名称、部门人数

    ```mysql
    # 部门人数
    select deptno, count(*)
    from emp
    group by deptno;
    
    # all CLERK
    select e.ename, d.dname, t.dept_num
    from emp e
    join dept d
    on e.deptno = d.deptno
    join (
    	select deptno, count(*) dept_num
        from emp
        group by deptno
    ) t
    on e.deptno = t.deptno
    where e.job = 'clerk';
    /*
        +--------+------------+----------+
        | ename  | dname      | dept_num |
        +--------+------------+----------+
        | SMITH  | RESEARCH   |        5 |
        | ADAMS  | RESEARCH   |        5 |
        | JAMES  | SALES      |        6 |
        | MILLER | ACCOUNTING |        3 |
        +--------+------------+----------+
    */
    ```

20. 列出最低薪资大于1500的各种工作及从事此工作的全部雇员人数

    ```mysql
    # 最低薪资大于1500的工作
    select job, count(*)
    from emp
    group by job
    having min(sal) > 1500;
    /*
        +-----------+----------+
        | job       | count(*) |
        +-----------+----------+
        | ANALYST   |        2 |
        | MANAGER   |        3 |
        | PRESIDENT |        1 |
        +-----------+----------+
    */
    ```

    - 分组函数需要用 having 筛选
    - 分号的组可以直接 count 组中人数
    - 要求岗位的最低薪金，故不能使用 where 先过滤

21. 列出在部门 “SALES”（销售部）工作的员工姓名，假定不知道销售部的部门编号

    ```mysql
    # 两边连结筛选
    select e.ename
    from emp e
    join dept d
    on e.deptno = d.deptno
    where d.dname = 'SALES';
    
    # 直接查询部门编号
    select ename
    from emp
    where deptno = (
    	select deptno
        from dept
        where dname = 'SALES'
    );
    
    /*
        +--------+
        | ename  |
        +--------+
        | ALLEN  |
        | WARD   |
        | MARTIN |
        | BLAKE  |
        | TURNER |
        | JAMES  |
        +--------+
    */
    ```

22. 列出薪金高于公司平均薪金的所有员工、所在部门、上级领导、雇员工资等级

    ```mysql
    select a.ename, a.deptno, b.ename mgr, s.grade
    from emp a
    left join emp b
    on a.mgr = b.empno
    join salgrade s
    on a.sal between s.losal and s.hisal
    where a.sal > (
    	select avg(sal)
        from emp
    );
    /*
        +-------+--------+-------+-------+
        | ename | deptno | mgr   | grade |
        +-------+--------+-------+-------+
        | JONES |     20 | KING  |     4 |
        | BLAKE |     30 | KING  |     4 |
        | CLARK |     10 | KING  |     4 |
        | SCOTT |     20 | JONES |     4 |
        | KING  |     10 | NULL  |     5 |
        | FORD  |     20 | JONES |     4 |
        +-------+--------+-------+-------+
    */
    ```

23. 列出与 “SCOTT” 从事相同工作的所有员工及部门名称

    ```mysql
    select e.ename, d.dname
    from emp e
    join dept d
    on e.deptno = d.deptno
    where e.job = (
    	select job
        from emp
        where ename = 'SCOTT'
    ) and e.ename <> 'SCOTT';
    /*
        +-------+----------+
        | ename | dname    |
        +-------+----------+
        | FORD  | RESEARCH |
        +-------+----------+
    */
    ```

24. 列出薪金等于部门30中员工的薪金的其他员工的姓名和薪金

    ```mysql
    select ename, sal
    from emp
    where sal in (
    	select distinct sal
        from emp
        where deptno = 30 and deptno <> 30
    );
    /*
    	Empty set
    */
    ```

    - 其他员工，则排除30号部门人员

25. 列出薪金高于在部门 30 工作的所有员工的薪金的员工姓名和薪金、部门名称

    ```mysql
    select e.ename, e.sal, d.dname
    from emp e
    join dept d
    on e.deptno = d.deptno
    where e.sal > (
    	select max(sal)
        from emp
        where deptno = 30
        group by deptno
    );
    /*
        +-------+---------+------------+
        | ename | sal     | dname      |
        +-------+---------+------------+
        | KING  | 5000.00 | ACCOUNTING |
        | JONES | 2975.00 | RESEARCH   |
        | SCOTT | 3000.00 | RESEARCH   |
        | FORD  | 3000.00 | RESEARCH   |
        +-------+---------+------------+
    */
    ```

26. 列出在每个部门工作的员工数量、平均工资和平均服务年限

    ```mysql
    select d.dname, count(e.ename) num, ifnull(avg(e.sal), 0) avg_sal, TimeStampDiff(YEAR, e.hiredate, now()) avg_hiredate
    from emp e
    right join dept d
    on e.deptno = d.deptno
    group by e.deptno;
    ```

    - 计算间隔时间函数：`TimeStampDiff(间隔类型, 前一个日期, 后一个日期)`

      间隔类型有：

      | 间隔类型    | 表示 |
      | ----------- | ---- |
      | FRAC_SECOND | 毫秒 |
      | SECOND      | 秒   |
      | MINUTE      | 分钟 |
      | HOUR        | 小时 |
      | DAY         | 天   |
      | WEEK        | 周   |
      | MONTH       | 月   |
      | QUARTER     | 季   |
      | YEAR        | 年   |

      现在的时间可以使用 `now()`

27. 列出所有员工的姓名、部门名称和工资

    ```mysql
    select e.ename, d.dname, e.sal
    from emp e
    join dept d
    on e.deptno = d.deptno;
    /*
        +--------+------------+---------+
        | ename  | dname      | sal     |
        +--------+------------+---------+
        | CLARK  | ACCOUNTING | 2450.00 |
        | KING   | ACCOUNTING | 5000.00 |
        | MILLER | ACCOUNTING | 1300.00 |
        | SMITH  | RESEARCH   |  800.00 |
        | JONES  | RESEARCH   | 2975.00 |
        | SCOTT  | RESEARCH   | 3000.00 |
        | ADAMS  | RESEARCH   | 1100.00 |
        | FORD   | RESEARCH   | 3000.00 |
        | ALLEN  | SALES      | 1600.00 |
        | WARD   | SALES      | 1250.00 |
        | MARTIN | SALES      | 1250.00 |
        | BLAKE  | SALES      | 2850.00 |
        | TURNER | SALES      | 1500.00 |
        | JAMES  | SALES      |  950.00 |
        +--------+------------+---------+
    */
    ```

28. 列出所有部门的详细信息和人数

    ```mysql
    select d.*, count(e.empno) num
    from emp e
    right join dept d
    on e.deptno = d.deptno
    group by e.deptno;
    /*
        +--------+------------+----------+-----+
        | DEPTNO | DNAME      | LOC      | num |
        +--------+------------+----------+-----+
        |     40 | OPERATIONS | BOSTON   |   0 |
        |     10 | ACCOUNTING | NEW YORK |   3 |
        |     20 | RESEARCH   | DALLAS   |   5 |
        |     30 | SALES      | CHICAGO  |   6 |
        +--------+------------+----------+-----+
    */
    ```

29. 列出各种工作的最低工资及从事此工作的雇员名称

    ```mysql
    select ename, job, sal
    from emp
    where sal in (
    	select distinct min(sal)
        from emp
        group by job
    );
    ```

    - 还可以通过岗位名称做连接查询

30. 列出各个部门的 MANAGER（主管）的最低薪资

    ```mysql
    select deptno, ename, min(sal) min_sal
    from emp
    where job = 'MANAGER'
    group by deptno;
    /*
        +--------+-------+---------+
        | deptno | ename | min_sal |
        +--------+-------+---------+
        |     10 | CLARK | 2450.00 |
        |     20 | JONES | 2975.00 |
        |     30 | BLAKE | 2850.00 |
        +--------+-------+---------+
    */
    ```

31. 列出所有员工的年工资，按年薪从低到高排序

    ```mysql
    select ename, (sal + ifnull(comm, 0)) * 12 year_sal
    from emp
    order by year_sal;
    /*
        +--------+----------+
        | ename  | year_sal |
        +--------+----------+
        | SMITH  |  9600.00 |
        | JAMES  | 11400.00 |
        | ADAMS  | 13200.00 |
        | MILLER | 15600.00 |
        | TURNER | 18000.00 |
        | WARD   | 21000.00 |
        | ALLEN  | 22800.00 |
        | CLARK  | 29400.00 |
        | MARTIN | 31800.00 |
        | BLAKE  | 34200.00 |
        | JONES  | 35700.00 |
        | FORD   | 36000.00 |
        | SCOTT  | 36000.00 |
        | KING   | 60000.00 |
        +--------+----------+
    */
    ```

32. 求出员工领导的薪水超过 3000 的员工名称与领导名称

    ```mysql
    select a.ename, b.ename
    from emp a
    join emp b
    on a.mgr = b.empno
    where b.sal > 3000;
    /*
        +-------+-------+
        | ename | ename |
        +-------+-------+
        | JONES | KING  |
        | BLAKE | KING  |
        | CLARK | KING  |
        +-------+-------+
    */
    ```

33. 求出部门名称中带 'S' 字符的部门员工的工资合计、部门人数

    ```mysql
    select d.dname, count(e.empno) num, ifnull(sum(e.sal), 0) sum_sal
    from emp e
    right join dept d
    on e.deptno = d.deptno
    where d.dname like '%s%'
    group by e.deptno;
    /*
        +------------+-----+----------+
        | dname      | num | sum_sal  |
        +------------+-----+----------+
        | OPERATIONS |   0 |     0.00 |
        | RESEARCH   |   5 | 10875.00 |
        | SALES      |   6 |  9400.00 |
        +------------+-----+----------+
    */
    ```

34. 给任职日期超过 30 年的员工加薪 10%

    ```mysql
    update emp1
    set sal = sal * 1.1
    where TimeStampDiff(YEAR, hiredate, now()) > 30;
    ```

    
