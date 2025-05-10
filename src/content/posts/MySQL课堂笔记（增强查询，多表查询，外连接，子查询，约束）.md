---
title: MySQL笔记
published: 2025-04-13
updated: 2025-04-13
description: '增强查询，多表查询，外连接，子查询，约束'
image: ''
tags: [MySQL]
category: ''
draft: false 
---

# MySQL

## MySQL表查询

在实际开发的过程中，我们需要用到的查询不只有前面的简单查询，我们还需要学习一些更为符合实际的查询方式，如日期查询，多表查询等

### 相关表格

我们先来建立几个表格，填入响应数据

#### 员工表emp

```sql
CREATE TABLE emp (
	`empno` INT,
	`ename` VARCHAR ( 30 ),
	`job` VARCHAR ( 20 ),
	`mgr` INT,
	`hiredate` DATE,
	`sal` DOUBLE,
	`comm` DOUBLE,
`deptno` INT 
);
```

```sql
INSERT INTO emp
VALUES
	( 7396, 'SMITH', 'CLERK', 7902, '1990-12-17', 800.00, NULL, 20 ),
	( 7499, 'ALLEN', 'SALESMAN', 7902, '1991-02-20', 1600.00, 300.00, 30 ),
	( 7596, 'WARD', 'SALESMAN', 7896, '1993-02-07', 1250.00, 500.00, 30 ),
	( 7696, 'JOOES', 'MANAGER', 7839, '1980-02-01', 2975.00, NULL, 20 ),
	( 7796, 'MARTIN', 'SALESMAN', 7698, '1999-04-17', 1250.00, 1400.00, 30 ),
	( 7896, 'BLAKE', 'MANAGER', 7839, '1990-12-17', 2850.00, NULL, 30 );
```



#### 工作部门表dept

```sql
CREATE TABLE dept ( 
`deptno` INT, 
`dname` VARCHAR ( 30 ), 
`loc` VARCHAR ( 30 ) 
);
```

```SQL
INSERT INTO dept
VALUES
	( 10, 'ACCOUNTING', 'NEW YORK' ),
	( 20, 'RESEARCH', 'DALLAS' ),
	( 30, 'SALES', 'CHICAGO' ),
	( 40, 'OPERATIONS', 'BOSTON' );
```



#### 薪水表格salgrade

```sql
CREATE TABLE salgrade ( 
    `grade` INT, 
    `losal` DOUBLE, 
    `hisal` DOUBLE 
);
```

```sql
INSERT INTO salgrade
VALUES
	( 1, 700.00, 1200.00 ),
	( 2, 1200.00, 1400.00 ),
	( 3, 1400.00, 3000.00 ),
	( 4, 3000.00, 9000.00 );
```



### 查询加强



#### where筛选日期

查找1992.1.1后入职的员工

需要至于格式01-01

```sql
select ename,hiredate from emp where hiredate > '1992-01-01';
```

```cmd
+--------+------------+
| ename  | hiredate   |
+--------+------------+
| WARD   | 1993-02-07 |
| MARTIN | 1999-04-17 |
+--------+------------+
```



#### like模糊查询

​	%：表示0到多个任意字符

​	_ ：表示单个任意字符

如何显示首字符为s的员工共姓名和工资

```sql
select ename,sal from emp where ename like 's%';
```

```cmd
+-------+-----+
| ename | sal |
+-------+-----+
| SMITH | 800 |
+-------+-----+
```



如何显示第三个字符为大写o的所有员工姓名和工资

```sql
select ename,sal from emp where ename like '__o%';
```

```cmd
+-------+------+
| ename | sal  |
+-------+------+
| JOOES | 2975 |
+-------+------+
```



#### order by排序

从工资低到高显示员工

```sql
select * from emp order by sal asc;
```

```cmd
+-------+--------+----------+------+------------+------+------+--------+
| empno | ename  | job      | mgr  | hiredate   | sal  | comm | deptno |
+-------+--------+----------+------+------------+------+------+--------+
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 | NULL |     20 |
+-------+--------+----------+------+------------+------+------+--------+
```

按照部门编号升序，员工工资降序排序（中间用，隔开）

```sql
select * from emp order by deptno asc , sal desc;
```

```cmd
+-------+--------+----------+------+------------+------+------+--------+
| empno | ename  | job      | mgr  | hiredate   | sal  | comm | deptno |
+-------+--------+----------+------+------------+------+------+--------+
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 | NULL |     20 |
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |
+-------+--------+----------+------+------------+------+------+--------+
```



#### 分页查询

在实际开发中我们数据量往往很大，我们会用到分页来进行查询，这里介绍一个关键字limit

基本语法

```sql
select ... limit start, rows
```

表示从start + 1 行开始取，取出rows行，例如上述的例子中，我们需要取出前5行结果使用如下的语句即可，从第1行开始取5行

```sql
select * from emp order by deptno asc , sal desc limit 0, 5;
```



我们是实际中使用我们就可以运用limit限制每一次查询的范围

要将一个大表范围若干个小表可以这么做

```sql
select * from emp order by deptno asc , sal desc limit 0, 4;
select * from emp order by deptno asc , sal desc limit 4, 4;
select * from emp order by deptno asc , sal desc limit 8, 4;
...
```



#### group by分组

我们有如下的几个题目

1.显示每种岗位的雇员总数，平均工资

```sql
select count(*), avg(sal), job from emp group by job;
```



2.显示雇员的总数，以及获得补助的雇员数

这里输入comm，comm字段为空则不参与统计

```sql
select count(*), count(comm) from emp;
```

如果我们扩展一下

没有获得补助的员工

```sql
select count(*), count(*)-count(comm) from emp;
```

```sql
select count(*), count(if(comm is null, 1, null)) from emp;
```



3.显示管理者的总人数

先count再去重

```sql
select count(distinct mgr) from emp;
```

4.显示雇员工资的最大差值

```sql
select max(sal)-min(sal) from emp;
```



#### 多子句查询

顺序：如果一个select语句同时包含了group by，having，limit，order by，那么它们的顺序应该为：group by，having，order by，limit

例如统计各个部门的平均工资，并且是大于1000的，按照平均工资从高到低排序，取出前两行记录

```sql
select avg(sal),deptno from emp 
group by deptno 
having avg(sal)>1000 
order by avg(sal) desc 
limit 0, 2;
```

其实更建议使用as重命名，可以增快速度

```sql
select avg(sal) as asql, deptno from emp 
group by deptno 
having asql>1000 
order by asql desc 
limit 0, 2;
```

```cmd
+----------+--------+
| avg(sal) | deptno |
+----------+--------+
|   1887.5 |     20 |
|   1737.5 |     30 |
+----------+--------+
```



### 多表查询

多表查询是指基于两个和两个以上的表的查询，在实际的应用中，查询单个表可能不能满足需求

例如说显示雇员名，雇员工资，所在部门的名字，就需要员工表和工作部门表

#### 多表笛卡尔集

```sql
select * from emp, dept;
```

可见笛卡尔集需要将两种表做笛卡尔积（在不加条件的结果非常夸张）

```cmd
+-------+--------+----------+------+------------+------+------+--------+--------+------------+----------+
| empno | ename  | job      | mgr  | hiredate   | sal  | comm | deptno | deptno | dname      | loc      |
+-------+--------+----------+------+------------+------+------+--------+--------+------------+----------+
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |     40 | OPERATIONS | BOSTON   |
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |     30 | SALES      | CHICAGO  |
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |     10 | ACCOUNTING | NEW YORK |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |     40 | OPERATIONS | BOSTON   |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |     30 | SALES      | CHICAGO  |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |     20 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |     40 | OPERATIONS | BOSTON   |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |     30 | SALES      | CHICAGO  |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |     20 | RESEARCH   | DALLAS   |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 |  100 |     20 |     40 | OPERATIONS | BOSTON   |
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 |  100 |     20 |     30 | SALES      | CHICAGO  |
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 |  100 |     20 |     20 | RESEARCH   | DALLAS   |
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 |  100 |     20 |     10 | ACCOUNTING | NEW YORK |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |     40 | OPERATIONS | BOSTON   |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |     30 | SALES      | CHICAGO  |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |     20 | RESEARCH   | DALLAS   |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |     40 | OPERATIONS | BOSTON   |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |     30 | SALES      | CHICAGO  |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |     20 | RESEARCH   | DALLAS   |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |     10 | ACCOUNTING | NEW YORK |
+-------+--------+----------+------+------------+------+------+--------+--------+------------+----------+
```

当不加限制的情况下，两张表的查询规则：

把第一张表的每一条记录和第二张表的每一条记录组合，返回结果是两张表的所有列，一共返回的记录数是第一张表的行数和第二张表的行数的乘积，而组成的表称为笛卡尔集



我们需要使用where对于笛卡尔集进行过滤，需要进行分析

当两张表的deptno编号相等的时候才是我们需要的结果

```sql
select * from emp, dept where emp.deptno=dept.deptno;
```

```sql
+-------+--------+----------+------+------------+------+------+--------+--------+----------+---------+
| empno | ename  | job      | mgr  | hiredate   | sal  | comm | deptno | deptno | dname    | loc     |
+-------+--------+----------+------+------------+------+------+--------+--------+----------+---------+
|  7396 | SMITH  | CLERK    | 7902 | 1990-12-17 |  800 | NULL |     20 |     20 | RESEARCH | DALLAS  |
|  7499 | ALLEN  | SALESMAN | 7902 | 1991-02-20 | 1600 |  300 |     30 |     30 | SALES    | CHICAGO |
|  7596 | WARD   | SALESMAN | 7902 | 1993-02-07 | 1250 |  500 |     30 |     30 | SALES    | CHICAGO |
|  7696 | JOOES  | MANAGER  | 7839 | 1980-02-01 | 2975 |  100 |     20 |     20 | RESEARCH | DALLAS  |
|  7796 | MARTIN | SALESMAN | 7698 | 1999-04-17 | 1250 | 1400 |     30 |     30 | SALES    | CHICAGO |
|  7896 | BLAKE  | MANAGER  | 7839 | 1990-12-17 | 2850 | NULL |     30 |     30 | SALES    | CHICAGO |
+-------+--------+----------+------+------------+------+------+--------+--------+----------+---------+
```

#### 笛卡尔例题

显示雇员名，雇员工资，所在部门的名字

```sql
select ename,sal,dname from emp, dept where emp.deptno=dept.deptno;
```

```sql
+--------+------+----------+
| ename  | sal  | dname    |
+--------+------+----------+
| SMITH  |  800 | RESEARCH |
| ALLEN  | 1600 | SALES    |
| WARD   | 1250 | SALES    |
| JOOES  | 2975 | RESEARCH |
| MARTIN | 1250 | SALES    |
| BLAKE  | 2850 | SALES    |
+--------+------+----------+
```

而在选取重复列的时候，需要指定是哪一个表的列

```sql
select ename,sal,dname,emp.deptno from emp, dept where emp.deptno=dept.deptno;
```

```sql
+--------+------+----------+--------+
| ename  | sal  | dname    | deptno |
+--------+------+----------+--------+
| SMITH  |  800 | RESEARCH |     20 |
| ALLEN  | 1600 | SALES    |     30 |
| WARD   | 1250 | SALES    |     30 |
| JOOES  | 2975 | RESEARCH |     20 |
| MARTIN | 1250 | SALES    |     30 |
| BLAKE  | 2850 | SALES    |     30 |
+--------+------+----------+--------+
```



显示部门编号为20的部门名称，员工名，工资

```sql
select ename,sal,dname,emp.deptno 
from emp,dept 
where emp.deptno = dept.deptno 
and emp.deptno = 20;
```

```sql
+-------+------+----------+--------+
| ename | sal  | dname    | deptno |
+-------+------+----------+--------+
| SMITH |  800 | RESEARCH |     20 |
| JOOES | 2975 | RESEARCH |     20 |
+-------+------+----------+--------+
```



显示各个员工的姓名，工资，以及工资级别

我们先选好需要的表格是emp表和salgrade表，我们这一道题的要点在于过滤条件该如何筛选

```sql
select ename,sal,grade from emp, salgrade;
```

```cmd
+--------+------+-------+
| ename  | sal  | grade |
+--------+------+-------+
| SMITH  |  800 |     4 |
| SMITH  |  800 |     3 |
| SMITH  |  800 |     2 |
| SMITH  |  800 |     1 |
| ALLEN  | 1600 |     4 |
| ALLEN  | 1600 |     3 |
| ALLEN  | 1600 |     2 |
| ALLEN  | 1600 |     1 |
| WARD   | 1250 |     4 |
| WARD   | 1250 |     3 |
| WARD   | 1250 |     2 |
| WARD   | 1250 |     1 |
| JOOES  | 2975 |     4 |
| JOOES  | 2975 |     3 |
| JOOES  | 2975 |     2 |
| JOOES  | 2975 |     1 |
| MARTIN | 1250 |     4 |
| MARTIN | 1250 |     3 |
| MARTIN | 1250 |     2 |
| MARTIN | 1250 |     1 |
| BLAKE  | 2850 |     4 |
| BLAKE  | 2850 |     3 |
| BLAKE  | 2850 |     2 |
| BLAKE  | 2850 |     1 |
+--------+------+-------+
```



运用between and语句即可完成，判断sal位于最高工资和最低工资之间即可

```sql
select ename,sal,grade from emp, salgrade 
where sal between losal and hisal;
```

```cmd
+--------+------+-------+
| ename  | sal  | grade |
+--------+------+-------+
| SMITH  |  800 |     1 |
| ALLEN  | 1600 |     3 |
| WARD   | 1250 |     2 |
| JOOES  | 2975 |     3 |
| MARTIN | 1250 |     2 |
| BLAKE  | 2850 |     3 |
+--------+------+-------+
```



#### 自连接

是指的是同一张表的连接查询

显示公司员工和它上级的名字

这个时候我们就需要使用到表的自连接，将员工和上级放在同一行中筛选完成（还需要有一个别名）

```sql
select * from emp empee, emp boss;
```

这样就可以将同一张表连接起来，我们再去写过滤条件

```sql
select empee.ename,boss.ename  
from emp empee, emp boss 
where empee.mgr = boss.empno;
```

```cmd
+-------+-------+
| ename | ename |
+-------+-------+
| WARD  | BLAKE |
+-------+-------+
```



### 子查询

子查询是指嵌入在其他sql语句中的select语句，也成为嵌套查询

单行子查询：只返回一行数据的子查询语句

多行子查询：返回多行数据的子查询语句（in）



单行子查询例题

如何显示于SMITH同一部门的所有员工

我们首先需要得到SMITH的部门号，再通过部门号筛选

```sql
select deptno from emp where ename = 'SMITH';//20
```

```sql
select * from emp where deptno = 20;
```

我们可以把第一个查询嵌入

```sql
select * from emp 
where deptno = (
	select deptno 
    from emp 
    where ename = 'SMITH'
);
```

即可完成



多行子查询例题

查询和部门20的工作相同的雇员的名字、岗位、部门号，但是不包含20自己的

第一步：查询20号部门有哪一些工作岗位（还需要去重）

```sql
select distinct job from emp where deptno = 20;
```

```cmd
+---------+
| job     |
+---------+
| CLERK   |
| MANAGER |
+---------+
```

第二步：运用job的工作字段进行查询筛选

```sql
select ename,job,sal,deptno from emp 
where job in('CLERK','MANAGER');
```

第三步：不包含20号部门

```sql
select ename,job,sal,deptno from emp 
where job in('CLERK','MANAGER') and deptno<>20;
```

综合嵌套一下

```sql
select ename,job,sal,deptno from emp 
where job in(
	select distinct job 
	from emp 
	where deptno = 20) 
and deptno<>20;
```



#### 子查询临时表

查询ecshop中各个类别中，价格最高的商品

先得到各个类别中价格最高的商品

```sql
select cat_id, max(shop_price) from ecsshop
group by cat_id;
```

我们可以将这个表格和ecsshop表进行连接，在设置过滤条件即可完成要求

```sql
select ecshop.cat_id, ecshop.goods_name 
from ecshop,(
select cat_id, max(shop_price) as max_price
from ecshop
group by cat_id)temp
where ecshop.cat_id=temp.cat_id 
and ecshop.shop_price=temp.max_price;
```



#### all和any操作符

all

所有的字段都满足

显示比部门30的所有员工的工资高的员工姓名、工资、部门号

```sql
select ename,sal,deptno from emp 
where sal>
all(
    select sal 
    from emp 
    where deptno = 30);
```

也可以运用max查询最大的薪水，再通过判断条件筛选

```sql
select ename,sal,deptno from emp 
where sal>
(select max(sal) 
 from emp 
 where deptno = 30);
```



any

有一个字段满足条件即可

```sql
select ename,sal,deptno from emp 
where sal>
any(
    select sal 
    from emp 
    where deptno = 30);
```

也可以改为

```sql
select ename,sal,deptno from emp 
where sal>
(select min(sal) 
 from emp 
 where deptno = 30);
```



#### 多列子查询

多列子查询指的是查询返回多个列数据的子查询语句

例如查询于SMITH部门，岗位完全相同的所有雇员

第一步：查询SMITH所在的部门以及岗位

```sql
select deptno,job from emp where ename='SMITH';
```

```cmd
+--------+-------+
| deptno | job   |
+--------+-------+
|     20 | CLERK |
+--------+-------+
```

第二步：将上述的查询结果当作查询使用

```sql
select ename, deptno, job from emp 
where deptno=20 
and job='CLERK'
and ename<>'SMITH';
```

综合嵌套(运用（）来进行相应的字段匹配)

```sql
select ename, deptno, job from emp 
where (deptno, job) = (
	select deptno,job from emp 
    where ename='SMITH'
)
and ename<>'SMITH';
```



#### 相关例题

例题一：

查找每个部门工资高于本部门平均工资人的资料

第一步：查询部门的平均工资

```sql
select deptno, avg(sal) from emp group by deptno;
```

第二步：将查询的结果表和员工表进行拼接，再进行筛选

(这里的emp.*代表选出emp表的所有字段)

```sql
select emp.* from emp,(
	select deptno, avg(sal)as avg_sal
	from emp group by deptno
)temp where emp.deptno=temp.deptno 
and emp.sal>temp.avg_sal;
```



例题二：

查询每个部门工资最高的人的详细资料

第一步：查询部门的最高工资

```sql
select deptno, max(sal) from emp group by deptno;
```

第二步：将查询的结果表和员工表进行拼接，再进行筛选

```sql
select emp.* from emp,(
	select deptno, max(sal)as avg_sal
	from emp group by deptno
)temp where emp.deptno=temp.deptno 
and emp.sal=temp.avg_sal;
```



例题三：

查询每个部门的信息（包括：部门名，编号，地址）和人员数量

思路：我们的信息来自于dept表，而人员数量需要构建临时表格

```sql
select count(*),deptno from emp group by deptno;
```

```cmd
+----------+--------+
| count(*) | deptno |
+----------+--------+
|        2 |     20 |
|        4 |     30 |
+----------+--------+
```

和部门信息表格拼接

```sql
select dept.*, countp 
from dept,(
	select deptno,count(*)as countp 
	from emp 
    group by deptno)temp 
where dept.deptno=temp.deptno;
```

```sql
+--------+----------+---------+--------+
| deptno | dname    | loc     | countp |
+--------+----------+---------+--------+
|     20 | RESEARCH | DALLAS  |      2 |
|     30 | SALES    | CHICAGO |      4 |
+--------+----------+---------+--------+
```



### 表复制

#### 自我复制数据

有的时候，为了对于某个sql语句进行效率测试，我们需要海量的数据，可以使用此法为表创建海量的数据

例如创建这一张表

```sql
create table my_table(	
	id int,
    `name` varchar(32),
	sal double,
	job varchar(32),
	deptno int
	);
```

我们先将emp表中的内容复制到创建的新表中

```sql
insert into my_table(id,`name`,sal,job,deptno)
select empno,ename,sal,job,deptno from emp;
```

自我复制：每一次都会将表中的内容复制添加到自己的表中

```sql
insert into my_table
select * from my_table;
```



#### 去除表中的重复数据

可以通过like创建一个新表，格式和旧表一样

```sql
create table table2 like table1
```



思路：

1.创建一张临时表mytemp,该表数据结构和my_table一样

2.把my_temp的记录通过distinct关键字处理复制

3.清除掉原先表my_table的记录

4.将my_temp的记录复制到my_table

5.drop删除临时表my_temp

```sql
create table my_temp like my_table;

insert into my_temp
select distinct * from my_table;

delete from my_table;

insert into my_table
select * from my_temp;

drop table my_temp;
```

或者你直接删除原先的my_table表，再将my_temp表格更改名称为my_table

```sql
rename table my_temp to my_table;
```



### 合并查询

#### union

在实际使用的时候，有的时候我们需要合并多个select语句查询到的结果，我们会用到union操作

```sql
select... union select...;
```

例如我们查询职业名称为clerk和部门号为20的并集

```sql
select * from emp where job='clerk' 
union select * from emp where deptno=20;
```

#### union all

如果你添加了all，则不会去除重复内容

```sql
select * from emp where job='clerk' 
union all select * from emp where deptno=20;
```



### 外连接

#### 问题引出

还记得之前我们的多表查询嘛，我们在合并表的时候会求多表的笛卡尔集

我们有一个要求如下：列出部门名称和这些部门的员工名称和工作，同时要求显示出那些没有员工的部门名

我们如果用笛卡尔积的话查询结果如下

```sql
select dname,ename,job 
from emp,dept 
where emp.deptno=dept.deptno;
```

```cmd
+----------+--------+----------+
| dname    | ename  | job      |
+----------+--------+----------+
| RESEARCH | SMITH  | CLERK    |
| RESEARCH | JOOES  | MANAGER  |
| SALES    | ALLEN  | SALESMAN |
| SALES    | WARD   | SALESMAN |
| SALES    | MARTIN | SALESMAN |
| SALES    | BLAKE  | MANAGER  |
+----------+--------+----------+
```



但是事实上我们的部门中还有其他的部门没有显示出来

```cmd
+--------+------------+----------+
| deptno | dname      | loc      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
```

这是因为员工表的部门号没有10、40无法匹配到部门表，我们要实现显示出那些没有员工的部门，我们需要外连接，我们创建如下的表作演示

```sql
CREATE TABLE stu (
`id` INT,
`name` VARCHAR ( 32 ));

INSERT INTO stu
VALUES
	( 1, 'Jack' ),
	( 2, 'Tom' ),
	( 3, 'Kity' ),
	( 4, 'nono' );
```

```sql
CREATE TABLE exam (
`id` INT,
`grade` INT);

INSERT INTO exam
VALUES
	( 1, 56 ),
	( 2, 76 ),
	( 11, 8 );
```

用where条件筛选后

```sql
select * from stu,exam where stu.id=exam.id;
```

```cmd
+----+------+----+-------+
| id | name | id | grade |
+----+------+----+-------+
|  1 | Jack |  1 |    56 |
|  2 | Tom  |  2 |    76 |
+----+------+----+-------+
```

可见是没有11号id和4号id的行记录的，因为id不是左右两表都有



#### 左外连接

左侧的表完全显示（如果没有成绩也要显示姓名与id）

```sql
select * from stu left join exam on stu.id=exam.id;
```

```sql
+----+------+------+-------+
| id | name | id   | grade |
+----+------+------+-------+
|  1 | Jack |    1 |    56 |
|  2 | Tom  |    2 |    76 |
|  3 | Kity | NULL | NULL  |
|  4 | nono | NULL | NULL  |
+----+------+------+-------+
```



#### 右外连接

又侧的表完全显示（如果没有姓名也要显示成绩与id）

```sql
select * from stu right join exam on stu.id=exam.id;
```

```sql
+------+------+----+-------+
| id   | name | id | grade |
+------+------+----+-------+
|    1 | Jack |  1 |    56 |
|    2 | Tom  |  2 |    76 |
| NULL | NULL | 11 |     8 |
+------+------+----+-------+
```



#### 例题

回到我们的例题：列出部门名称和这些部门的员工名称和工作，同时要求显示出那些没有员工的部门名

左外连接实现：

```sql
select dept.deptno,dname,ename,job 
from dept left join emp 
on dept.deptno=emp.deptno; 
```

```sql
+--------+------------+--------+----------+
| deptno | dname      | ename  | job      |
+--------+------------+--------+----------+
|     10 | ACCOUNTING | NULL   | NULL     |
|     20 | RESEARCH   | JOOES  | MANAGER  |
|     20 | RESEARCH   | SMITH  | CLERK    |
|     30 | SALES      | BLAKE  | MANAGER  |
|     30 | SALES      | MARTIN | SALESMAN |
|     30 | SALES      | WARD   | SALESMAN |
|     30 | SALES      | ALLEN  | SALESMAN |
|     40 | OPERATIONS | NULL   | NULL     |
+--------+------------+--------+----------+
```

右外连接实现：

```sql
select dept.deptno,dname,ename,job 
from emp right join dept 
on dept.deptno=emp.deptno; 
```

```sql
+--------+------------+--------+----------+
| deptno | dname      | ename  | job      |
+--------+------------+--------+----------+
|     10 | ACCOUNTING | NULL   | NULL     |
|     20 | RESEARCH   | JOOES  | MANAGER  |
|     20 | RESEARCH   | SMITH  | CLERK    |
|     30 | SALES      | BLAKE  | MANAGER  |
|     30 | SALES      | MARTIN | SALESMAN |
|     30 | SALES      | WARD   | SALESMAN |
|     30 | SALES      | ALLEN  | SALESMAN |
|     40 | OPERATIONS | NULL   | NULL     |
+--------+------------+--------+----------+
```



## MySQL中的约束

何为约束？

约束是用于确保数据库数据满足特定的商业规则，而再mysql中约束包括not null、unique、primary key、foreign key、check五种

### primary key

primary key：用于唯一的标示表行的数据，当定义主键约束之后，该列不可以重复，以下是一个案例

创建一个主键为id表，向其中插入数据

```sql
CREATE TABLE primary_test (
	`id` INT PRIMARY KEY,
	`name` VARCHAR ( 32 ),
	`email` VARCHAR ( 32 ) 
)
```

```sql
insert into primary_test values
(1,'jack','jack@qq.com'),
(2,'marry','marry@gmail.com'),
(3,'lory','lory@163.com');
```

我们现在加一条语句，id为1（之前加过id为1的记录）

```sql
insert into primary_test values(1,'jerry','jerry@qq.com');
```

报错

```cmd
> 1062 - Duplicate entry '1' for key 'primary_test.PRIMARY'
```



主键的细节

1.一张表最多只能有一个主键，但可以是复合主键

```sql
CREATE TABLE primary_test2 (
	`id` INT ,
	`name` VARCHAR ( 32 ),
	`email` VARCHAR ( 32 ) ,
	 primary key(`id`,`name`)
) ;
```

2.主键的指定方式有两种，第一种是直接在字段名 类型后加上primary key，第二种是在表的定义最后加上primary key(列名)

```sql
CREATE TABLE primary_test4 (
	`id` INT ,
	`name` VARCHAR ( 32 ),
	`email` VARCHAR ( 32 ) ,
	 primary key(`id`)
) ;
```

3.使用desc可以查看主键情况

```sql
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | NO   | PRI | NULL    |       |
| name  | varchar(32) | YES  |     | NULL    |       |
| email | varchar(32) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
```

4.主键不可以重复且不能为空



### not null

非空约束，当列上定义了not null，那么插入数据的时候必须要提供数据



### unique

唯一约束

1.当定义了唯一约束的时候，该列的值不可以重复

2.一张表可以有多个unique字段

3.当unique没有添加非空约束，则unique字段可以有多个null



### foreign key

外键

用于定义主表和从表之间的关系：外键约束要定义在从表上，主表则必须要拥有主键约束或者是unique约束，当定义外键约束后，要求外键列数据必须要在主表的主键列存在或者是null

举个栗子：

例如我们有一个学生表其中有id和class_id，还有一个班级表，有属性id，如果我们要求，每个学生所在的班级号class_id是存在于班级表的，就可以把class_id做成外键约束（班级表为主表，而学生表为从表）

当我们插入一个学生它的班级编号不存在于班级表，则插入失败

当我们学生表有一个学生他的班级编号存在班级表，则删除对应的班级表id会删除失败



班级表（主表）

```sql
CREATE TABLE my_class (
	`id` INT primary key,
	`name` VARCHAR ( 32 ),
	`add` VARCHAR ( 32 ) 
) ;
```

学生表（从表）

```sql
CREATE TABLE my_stu (
	`id` INT primary key,
	`name` VARCHAR (32) NOT NULL,
	`class_id` int,
	foreign key(class_id) references my_class(id)
) ;
```



外键细节

1.外键指向的表字段，要求为primary key或者是unique

2.表的类型是innodb，这样的表才支持外键

3.外键字段的类型要求和主键字段类型一致

4.外键字段的值必须要求在主键字段中出现过，或者为null

5.一旦建立主外键的关系，数据就不可以随意删除了



### check

用于强制行数据必须满足条件，注意在mysql 5.7，这里插入不符合check的数据会成功（只是会提示）8.0是会生效的

假设sal列上定义了check约束，并要求sql列值在1000-2000之间，性别为两个，那么如果插入的记录如果不满足相关条件就会提示报错

```sql
CREATE TABLE test (
	`id` INT primary key,
	`name` VARCHAR (32) NOT NULL,
	`sex` VARCHAR(10) CHECK (`sex` IN('man','woman')),
	`sal` double CHECK (`sal`>1000 and `sal`<2000)
) ;
```



### 表设计

现有一个商店的数据库shop_database，记录客户以及其购物情况，有以下的三个表组成

goods商品表：

商品号goods_id  商品名称goods_name  价格price  商品类别category 供货商provider

customer顾客表：

客户号customer_id  姓名name  住址address  邮箱email  性别sex  身份证id

purchase购买表：

订单号order_id  客户号customer_id  商品号goods_id  购买数量nums



要求如下：

1.合理定义主键、外键

2.客户姓名不可以为空值

3.电子邮箱不可以为重复

4.客户的性别约束‘男’|‘女’

5.价格在1.0——9999.99之间



goods

```sql
create table goods(
goods_id int primary key,
goods_name varchar(30) not null default '',
price decimal(10,2)not null 
    check(price between 1.0 and 9999.99),
category int not null default '',
provider varchar(30)
);
```

customer

```sql
create table customer(
customer_id int primary key,
name varchar(30) not null,
address varchar(30),
email varchar(30),
sex varchar(10) check(sex IN('男','女')),
id decimal(20,0) unique
);
```

purchase

```sql
create table purchase(
order_id int primary key,
customer_id int not null,
goods_id int not null,
nums int,
FOREIGN key(customer_id) REFERENCES customer(customer_id),
FOREIGN key(goods_id) REFERENCES goods(goods_id)
);
```

