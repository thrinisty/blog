---
title: MySQL笔记
published: 2025-04-15
updated: 2025-04-15
description: '习题练习'
image: ''
tags: [MySQL]
category: ''
draft: false 
---

# MySQL习题

我们系统的学完了MySQL相关知识，我们现在来用例题巩固一下我们的知识

## 练习一

1.显示所有部门的名称

```sql
select deptno,dname from dept;
```

2.显示所有雇员名以及全年收入（12*薪水 + 奖金）取名为年收入

这里道题注意null+数字为空，需要使用判断语句处理

```sql
select ename, (sal*12 + if(comm is null,0,comm))
as'年收入' from emp;
```

3.显示工资超过2850雇员的姓名以及工资

```sql
select ename,sal from emp where sal > 2850;
```

4.显示工资不再1500到2850之间雇员的姓名以及工资

```sql
select ename,sal from emp where sal > 2850 and sal < 1500;
```

5.显示编号为7796的雇员名称以及所在部门编号

```sql
select ename,deptno from emp where empno=7796;
```

6.显示部门在20，30中工资超过1500的雇员名以及工资

```sql
select ename,sal from emp where sal > 1500 
and deptno in(20,30);
```

7.显示无管理者的雇员名称以及岗位

```sql
select ename,job from emp where mgr is null;
```

8.显示在1991-2-1日到1991-5-1日之间雇佣的员工名，岗位以及雇佣日期，并以雇佣日期降序排序

```sql
select ename,job,hiredate from emp 
where hiredate between 1991-02-01 
and 1991-05-01 
order by hiredate desc;
```



## 练习二

1.选出部门30中的所有员工

```sql
select * from emp where deptno = 30;
```

2.列出所有CLERK的姓名，编号和部门编号

```sql
select ename, empno, deptno from emp where job = 'CLERK';
```

3.找出佣金高于平均薪水60%的员工

```sql
select ename from emp 
where sal>(0.6*(select avg(sal) from emp));
```

4.找出部门30中所有MANNAGER和部门20中所有CLERK的详细资料

```sql
select * from emp 
where(deptno = 20 and job = 'CLERK') 
or (deptno = 30 and job = 'MANNAGER');
```

或者

```sql
select * from emp 
where deptno = 20 and job = 'CLERK' 
union select * from emp 
where deptno = 30 and job = 'MANNAGER';
```

5.查询20部门中既不是MANAGER又不是CLERK而薪水小于等于2000的所有员工

```sql
select * from emp where deptno = 30 
and sal <= 2000 and (job <> 'MANAGER' and job <> 'CLERK');
```

6.找出不收奖金或奖金低于100的员工

```sql
select * from emp where comm is null or ifnull(comm,0)<100;
```

7.找出每月倒数第三天受雇的员工

```sql
select * from emp where last_day(hiredate) - 2 = hiredate;
```

8.找出至今工作12年以上的员工

```sql
select * from emp where date_add(hiredate, interval 12 year) < now();
```

9.以首字母小写的方式显示所有员工的姓名

```sql
select concat(
lcase(substring(ename,1,1))
,substring(ename,2)) from emp;
```

10.显示名字长度为5的员工

```sql
select * from emp where length(ename)=5;
```



## 练习三

1.显示不带有R的员工姓名

```sql
select * from emp where ename not like '%R%';
```

2.显示所有员工姓名的前三个字符

```sql
select left(ename,3) from emp;
```

3.显示所有的员工姓名，a用A替换

```sql
select replace(ename,'A','a') from emp;
```



## 练习四

1.列出一个至少有一个员工的所有部门

```sql
select distinct dname from dept,emp 
where dept.deptno = emp.deptno;
```

2.列出薪水比smith多的所有员工

```sql
select * from emp where sal > (select sal from emp where ename = 'SMITH');
```

3.列出受雇日期晚于其直接上级的所有员工

```sql
select emy.* from emp as emy,emp as boss 
where emy.hiredate > boss.hiredate 
and emy.mgr = boss.empno;
```

4.列出部门名称和这些部门的员工信息，同时列出没有员工的部门

```sql
select dname,emp.* from dept left join emp 
on dept.deptno = emp.deptno;
```

5.列出所有CLERK的姓名以及部门的名称

```sql
select dname,emp.ename from dept left join emp 
on dept.deptno = emp.deptno where job = 'CLERK';
```

6.列出最低薪水大于1500的各种工作

```sql
select min(sal)as min_sal,job from emp 
group by job 
having min_sal > 1500;
```

7.列出部门在SALES工作的员工名称

```sql
select ename from emp,dept 
where emp.deptno = dept.deptno and dname = 'SALES';
```



## 练习五

1.列出与SMITH从事相同工作的所有员工

```sql
select * from emp 
where job = (select job from emp where ename = 'SMITH') 
and ename <> 'SMITH';
```

2.列出薪水高于在部门30工作的所有员工的薪水的员工姓名和薪水

```sql
select ename,sal from emp where sal>all(select sal from emp where deptno = 30);
```

3.列出在每个部门工作的员工数量，平均工资

```sql
select dname,count(emp.deptno),avg(sal) from dept 
left join emp on dept.deptno = emp.deptno 
group by dname;
```

4.列出所有部门的详细信息以及部门人数

```sql
select dept.*,temp.con from dept left join 
(select count(*) as con,deptno 
 from dept group by deptno)temp 
 on dept.deptno = temp.deptno;
```



5.列出MANAGER的最低工资

```sql
select min(sal) from emp where job = 'MANAGER';
```



## 综合练习

设学校环境如下：一个系有若干个专业，每个专业一年只招一个班，每个班有若干个学生，先要建立于系、班级、学生数据库

关系模式如下：

班级class（班号classid，课程名subject，系名deptname，入学年份time，人数num）

学生student（学号studentid，姓名name，年龄age，班号classid）

系department（系号departmentid，系名deptname）



### 建表

定义每个表的主外码

deptname唯一约束

学生姓名不可以为空

班级class

```sql
create table class(
`classid` int primary key,
`subject` varchar(32),
`deptname` varchar(32),
`time` int,
`num` int
);
```

学生student

```sql
create table student(
`studentid` int,
`name` varchar(32) not null default '',
`age` int,
`classid` int,
primary key(`studentid`),
foreign key(`classid`) references class(classid)
);
```

系department

```sql
create table department(
`departmentid` int primary key,
`deptname` varchar(32) unique not null
);
```

### 插入数据

```sql
-- 插入系部数据
INSERT INTO department VALUES
(1, '计算机科学系'),
(2, '电子工程系'),
(3, '机械工程系'),
(4, '外国语学院'),
(5, '经济管理学院');

-- 插入班级数据
INSERT INTO class VALUES
(101, '数据结构', '计算机科学系', 64, 45),
(102, '数据库原理', '计算机科学系', 48, 40),
(201, '电路分析', '电子工程系', 56, 38),
(202, '数字电子技术', '电子工程系', 48, 42),
(301, '机械制图', '机械工程系', 64, 36),
(401, '英语语言学', '外国语学院', 32, 28),
(501, '微观经济学', '经济管理学院', 48, 50);

-- 插入学生数据
INSERT INTO student VALUES
(1001, '张三', 20, 101),
(1002, '李四', 21, 101),
(1003, '王五', 19, 102),
(1004, '赵六', 20, 102),
(1005, '钱七', 22, 201),
(1006, '孙八', 20, 201),
(1007, '周九', 21, 202),
(1008, '吴十', 19, 301),
(1009, '郑十一', 20, 401),
(1010, '王十二', 21, 501);
```



### 查询

完成如下查询

1.找出所有姓李的学生

```sql
select * from student where name like '李%';
```

2.列出开设超过一个课程的系的名称

```sql
select deptname,count(*)as class_count 
from class group by deptname 
having class_count > 1;
```

```sql
+--------------+-------------+
| deptname     | class_count |
+--------------+-------------+
| 计算机科学系  |           2 |
| 电子工程系    |           2 |
+--------------+-------------+
```

3.列出人数大于等于50，系的编号和名字

```sql
select department.*,temp.sum_num from department,(select deptname,sum(num) as sum_num from class group by deptname having sum_num >= 50)temp where temp.deptname = department.deptname;
```

```sql
+--------------+--------------+---------+
| departmentid | deptname     | sum_num |
+--------------+--------------+---------+
|            2 | 电子工程系   | 80       |
|            5 | 经济管理学院 | 50       |
|            1 | 计算机科学系 | 85       |
+--------------+--------------+---------+
```



### 移除学生

我们现在需要移除一名张三学生，考虑的地方有以下两点，使张三所在班级的人数减一，再将张三从student表中移除，而这个过程我们要由事务来完成

```sql
start transaction;
update class set num = num-1 where classid = (select classid from student where name = '张三');
delete from student where name = '张三';
commit;
```

