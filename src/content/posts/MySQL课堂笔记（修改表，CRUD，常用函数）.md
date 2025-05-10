---
title: MySQL笔记
published: 2025-04-11
updated: 2025-04-11
description: '修改表，CRUD，常用函数'
image: ''
tags: [MySQL]
category: ''
draft: false 
---

# MySQL笔记

## 修改表

我们修改表可以通过图形化界面进行修改，这一般比较方便，但是有一些限制，例如需要批量修改，条件限制的情况，我们也需要学会通过指令来对于表进行操作



### 添加列

```sql
ALTER TABLE table_name ADD(column datatype [DEFAULT expr]
, column datatype [DEFAULT expr]...);
```

### 修改列

```sql
ALTER TABLE table_name MODIFY(column datatype [DEFAULT expr]
, column datatype [DEFAULT expr]...);
```

### 删除列

```sql
ALTER TABLE table_name DROP(column);
```

### 重命名表

```sql
RENAME TABLE `oldTable` TO `newTable`;
```

### 查看表结构

```sql
desc newtable;
```

```cmd
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| id         | int         | YES  |     | NULL    |       |
| name       | varchar(20) | YES  |     | NULL    |       |
| sex        | char(1)     | YES  |     | NULL    |       |
| birthday   | date        | YES  |     | NULL    |       |
| entry_date | datetime    | YES  |     | NULL    |       |
| job        | varchar(20) | YES  |     | NULL    |       |
| Salary     | double      | YES  |     | NULL    |       |
| resume     | text        | YES  |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
8 rows in set (0.09 sec)
```



### 修改表字符集

```sql
ALTER TABLE table_name character set utf8;
```



### 例题

1.在创建的员工表上增加一个image列，varchar类型

```sql
ALTER TABLE employ ADD `image` VARCHAR ( 30 );
```

2.修改job列，使其长度为60

```sql
ALTER TABLE employ MODIFY `job` VARCHAR ( 60 );
```

3.删除sex列

```sql
ALTER TABLE employ DROP `sex`;
```

4.将表名更改为employee

```sql
rename table employ to employee;
```

5.修改表的字符集为utf8

```sql
ALTER TABLE employee CHARACTER SET utf8;
```

6.将列名name修改为user_name

```sql
ALTER TABLE employee CHANGE `name` user_name VARCHAR(32); 
```



我们在修改列属性的时候可以加一些规则，例如不为空，默认为'null'

```sql
ALTER TABLE employee MODIFY `user_name` VARCHAR(32) NOT NULL DEFAULT 'null'; 
```



## CRUD

create read update delete 增删改查

我们在之前也曾用到过这一些语句，现在我们系统的来进行学习CRUD



### Insert（增）

#### 基本使用

```sql
INSERT INTO table_name [(column, column, ...)] VALUES (value, value, ...)
```

例如

创建一张商品表(id int , goods_name varchar(10), price double)

```sql
CREATE TABLE goods(
`id` int,
`goods_name` varchar(10),
`price` double);
```

其中添加两条数据

```sql
INSERT INTO goods VALUES (1, '牙刷', 10.0);
```

```sql
INSERT INTO goods (id, goods_name) VALUES (2, '牙膏');
```

```cmd
mysql> select * from goods;

+----+------------+-------+
| id | goods_name | price |
+----+------------+-------+
|  1 | 牙刷       |    10 |
|  2 | 牙膏       | NULL  |
+----+------------+-------+
2 rows in set (0.08 sec)
```

#### 注意细节

1.插入的数据应该与字段的数据类型相同

2.数据的长度因该在列的规定范围内

3.value中列出的数据位置必须于被加入的列的排列位置对应

4.字符和日期类型数据需要包含在单引号中

5.列允许插入空值（前期是字段不是NOT NULL）

6.插入的记录可以有多条，在后续加（），（）即可

7.如果是表中的所有字段添加数据的时候，可以不写字段名称

8.默认值的使用，当不给某个字段值的时候，如果有默认值就会添加，否则填入null，如果NOT NULL则报错



### Update（改）

#### 基本使用

```sql
UPDATE table_name SET col_name1 = expr1 [col_name2 = expr2] [WHERE where_definition]
```

以下是一个使用案例

1.将所有的员工薪水修改为5000元

```sql
UPDATE employee SET Salary = 5000;
```

2.将力宏的薪水修改为3000元

```sql
UPDATE employee SET Salary = 3000 WHERE `user_name`='力宏';
```

3.将lory的薪水上调1000元

```sql
UPDATE employee SET Salary = Salary + 1000 WHERE `user_name`='lory';
```



#### 使用细节

1.UPDATE语法可以指定用新值更新原有表行中的各列

2.SET子句指定要修改那些列和赋予的哪些值

3.WHERE子句指定应该更新哪些行，如果没有，则更新所有的行

4.如果需要修改多个字段，可以通过set 字段1= value，set 字段2= value...完成，例如如下代码，就顺便进行了id++

```sql
UPDATE employee SET Salary = Salary + 1000, id = id + 1 WHERE `user_name`='lory';
```



### Delete（删）

#### 基本使用

```sql
DELETE FROM table_name [WHERE where_definition];
```

案例

1.删除用户名为力宏的记录

```sql
DELETE FROM employee WHERE `user_name`='力宏';
```

2.删除表中的所有记录

```sql
DELETE FROM employee;
```

#### 使用细节

1.如果不加WHERE子句，就会删除表中所有的记录

2.DELETE语句不可以删除某一列的值（可以使用update更新字段为null）

3.使用DELETE语句尽可以删除表的记录，不可以删除表本身，需要使用DROP删除表



### Select（查）

算是数据库的一个重点

#### 基本使用

```sql
SELECT [DISTINCT] * | [column1, colum2, ...] FROM table_name [WHERE where_definition];
```

其中DISTANCT可选，表示是否去除重复数据

1.查询所有的员工

```sql
select * from employee;
```

```cmd
+----+-----------+------+--------+
| id | user_name | job  | Salary |
+----+-----------+------+--------+
|  1 | 李明      | NULL |   2000 |
|  1 | 李明      | NULL |   2000 |
|  2 | 王丽      | NULL |   2000 |
|  3 | 丽华      | NULL |  20003 |
|  4 | 李儒      | NULL |   2000 |
|  5 | 小明      | NULL |   2000 |
|  6 | lory      | NULL |  20003 |
+----+-----------+------+--------+
```

2.查询非重复的员工（每个字段都相同）

```sql
select distinct * from employee;
```

```cmd
+----+-----------+------+--------+
| id | user_name | job  | Salary |
+----+-----------+------+--------+
|  1 | 李明      | NULL |   2000 |
|  2 | 王丽      | NULL |   2000 |
|  3 | 丽华      | NULL |  20003 |
|  4 | 李儒      | NULL |   2000 |
|  5 | 小明      | NULL |   2000 |
|  6 | lory      | NULL |  20003 |
+----+-----------+------+--------+
6 rows in set (0.07 sec)
```



3.SELECT语句可以进行一些运算

```sql
SELECT * | (column1 | expression, column2 | expression, ...)FROME table_name;
```

例如在查询科目成绩时可以指定查询的一列为其他的几列相加

```sql
SELECT (english + math + chinese) as `grades` from table_name;
```



4.SELECT可以使用as语句取别名

```sql
SELECT column_name as other_name from table_name;
```

```sql
SELECT `user_name` as `name`, `Salary` as `money` FROM `employee`
```

```cmd
+------+-------+
| name | money |
+------+-------+
| 李明 |  2000 |
| 李明 |  2000 |
| 王丽 |  2000 |
| 丽华 | 20003 |
| 李儒 |  2000 |
| 小明 |  2000 |
| lory | 20003 |
+------+-------+
7 rows in set (0.07 sec)
```



5.和改删一样，SELECT一样可以运用WHERE过滤

常见的运算符

| 运算符                 | 说明                                    |
| ---------------------- | --------------------------------------- |
| >  <  <=  >=  =  <>    | 大于 小于 大于等于 小于等于 等于 不等于 |
| BETWEEN...AND...       | 显示在某一区间内的值                    |
| IN(set)                | 显示在in列表中的值，如in(100，200)      |
| [NOT] LIKE '张pattern' | 模糊查询                                |
| IS NULL                | 判断是否为空                            |
| and                    | 并                                      |
| or                     | 或                                      |
| not                    | 非                                      |



6.使用order by设置排序

```sql
SELECT  * | [column1, colum2, ...] FROM table_name 
ORDER BY column1 asc|desc;
```

排序的列可以是表中的列名，也可以select语句中指定的列名

Asc升序 Desc降序

ORDER BY 子句应该位于SELECT语句的末尾



7.group by + having

使用group by子句对列进行分组

```sql
SELECT column1, colum2, ... FROM table_name 
group by column;
```

使用having子句对分组后的结果过滤

```sql
SELECT column1, colum2, ... FROM table_name 
group by column1 having ...;
```

使用案例

1.显示每个部门的平均工资和最高工资

```sql
SELECT AVG(sal), MAX(sal), deptno FROM emp GROUP BY deptno;
```

2.显示每个部门的每种岗位的平均工资和最低工资

可以先显示每个部门的平均工资和最低工资

```sql
SELECT AVG(sal), MIN(sal), deptno FROM emp GROUP BY deptno;
```

还需要再添加一个分组的标准

```sql
SELECT AVG(sal), MIN(sal), deptno, job FROM emp GROUP BY deptno, job;
```

3.显示平均工资低于2000的部门号和他的平均工资

先显示各个部门的平均工资和部门号

```sql
SELECT AVG(sal), deptno FROM emp 
GROUP BY deptno;
```

然后运用having进行过滤实现平均工资小于2000

```sql
SELECT AVG(sal) as avg_sal, deptno FROM emp 
GROUP BY deptno
HAVING avg_sal < 2000;
```



以下是SELECT使用例题：

1.对数学成绩排序后输出[升序]

```sql
SELECT * FROM student ORDER BY `math` asc;
```

2.对总分按照降序排序

```sql
SELECT `name` , (`math`+`english`+`chinese`) as grade FROM student ORDER BY grade desc;
```

3.对姓李的学生按照成绩排序输出[升序]

```sql
SELECT * FROM student WHERE `name` LIKE '李%' ORDER BY (`math`+`english`+`chinese`)  asc;
```



#### 例题

1.查询math大于60并且id大于90的学生成绩

```sql
SELECT * FROM student WHERE `math` > 60 and id > 90;
```

2.查询英语成绩大于语文成绩的同学

```sql
SELECT * FROM student WHERE `math` > `chinese`;
```

3.查询总分大于200分并且数学成绩小于语文成绩的韩姓同学

```sql
SELECT * FROM student WHERE (`math`+`chinese`+`english`)>200 and `math`<`chinese` and user_name LIKE '韩%';
```

4.查询英语成绩在80-90分的同学(闭区间)

```SQL
SELECT * FROM student WHERE `english` BETWEEN 80 AND 90;
```

5.查询数学分数为89，90，91的同学

```sql
SELECT * FROM student WHERE `english` IN(89,90,91);
```



## 重要的函数

### Count

统计函数

#### 基本使用

```sql
SELECT COUNT(*) | COUNT(column) FROM table_name [WHERE where_definition];
```

count(*)和count(column)区别

count(*)返回满足条件记录的行数

count(column)统计满足条件的某列有多少的，但排除对应列为null的情况



#### 使用案例

1.统计班级里一共有几个学生

```sql
select count(*) from student where `grade`
```

```cmd
+----------+
| count(*) |
+----------+
|        7 |
+----------+
```

2.统计总分大于300的学生个数

```sql
select count(*) from student where `grade` > 300;
```

```cmd
+----------+
| count(*) |
+----------+
|        4 |
+----------+
```



### SUM

合计函数

Sum函数返回满足where条件的行的和---一般使用在数值列



#### 基本使用

```sql
SELECT SUM(column) FROM table_name [WHERE where_definition];
```

仅对数值起作用



#### 使用案例

1.统计一个班数学总成绩

```sql
SELECT SUM(math) FROM student;
```

2.统计一个班语文、英语、数学各科的总成绩

```sql
SELECT SUM(math),SUM(english),SUM(chinese) FROM student;
```

3.统计一个班语文、英语、数学各科和的总成绩

```sql
SELECT SUM(math + english + chinese) FROM student;
```

4.统计一个班级语文的平均分

```SQL
SELECT SUM(chinese)/COUNT(*) FROM student;
```

或者我们也可以使用AVG统计函数求平均值

```sql
SELECT AVG(chinese) FROM student;
```



### AVG

#### 基本使用

```sql
SELECT AVG(column) FROM table_name [WHERE where_definition];
```

使用案例如上，不再过多赘述



### MAX/MIN

数值范围在统计中特别有用

#### 基本使用

```sql
SELECT MAX/MIN(column) FROM table_name [WHERE where_definition];
```

#### 使用案例

1.求班级最高分

```sql
SELECT MAX(math + english + chinese)，MIN(math + english + chinese) FROM student;
```

2.求出班级数学最高分

```sql
SELECT MAX(math)，MIN(math) FROM student;
```



### 字符串函数

| 字符串函数                            | 说明                                            |
| ------------------------------------- | ----------------------------------------------- |
| CHARSET(str)                          | 返回字串字符集                                  |
| CONCAT(str2 [,...])                   | 连接字串                                        |
| INSTR(string, substring)              | 返回substring在string中出现的位置，没有则返回0  |
| UCASE(str2)                           | 转为大写                                        |
| LCASE(str2)                           | 转为小写                                        |
| LEFT(str2, length)                    | 从str2中的左边起取length个字符                  |
| LENGTH(string)                        | string长度[按照字节]                            |
| REPLACE(str, search_str, replace_str) | 在str中用字符串替换                             |
| STRCMP(str1,str2)                     | 逐个字符比较两个字符串大小                      |
| SUBSTRING(str, position [length])     | 从str的position开始，取length个字符，不写则取完 |
| LTRIM(str2) RTRIM(str2)               | 去除前端空格去除后端空格                        |
| trim                                  | 去除左右两边空格                                |

#### 基本使用

CHARSET(str)

```sql
SELECT CHARSET(name) FROM emp;
//返回字符集（utf8）
```

CONCAT(str2 [,...])

```sql
SELECT CONCAT(name, 'job is', job) as name_job FROM emp;
//字符串拼接
```

INSTR(string, substring)

```sql
SELECT INSTR(string, 'target') FROM DUAL;--其中DUAL是亚元表
//返回出现位置
```

UCASE(str2) LCASE(str2)

```sql
SELECT UCASE(name) FROM emp;//转为大写
SELECT LCASE(name) FROM emp;//转为小写
```

LEFT(str2, length) RIGHT(str2, length)

```sql
SELECT LEFT(name, 2) FROM emp;//从左取2个字符
SELECT RIGHT(name, 2) FROM emp;//从右取2个字符
```

LENGTH(string)

```sql
SELECT LENGTH(name) FROM emp;//返回名字长度
```

REPLACE(str, search_str, replace_str)

```sql
SELECT REPLACE(name, 'a', 'b') FROM emp;
//将名字中的a替换为b
```

STRCMP(str1,str2)

```sql
SELECT STRCMP(str1,str2) FROM DUAL;
//比较字符串大小 小于返回-1 大于返回1 等于返回0
```

SUBSTRING(str, position [length])

```sql
SELECT SUBSTRING(name, 1, 2) FROM emp;
//从第一个位置开始取两个字符
```

LTRIM(str2)

```sql
SELECT LTRIM(' 名字') FROM DUAL;//'名字'
```



#### 例题

以首字母小写的方式显示所有员工emp表的名字

取出第一个字符转为小写然后和后面的字符拼接

```sql
SELECT CONCAT(LCASE(LEFT(name, 1)),
SUBSTRING(name,2,LENGTH(name))) FROM emp;
```



### 数学函数

有很多，我挑几个常用的



#### ABS(num)

绝对值

```sql
SELECT ABS(-1) FROM DUAL;//1
```



#### CEILING(number)

向上取整，得到比number大的最小整数

```sql
SELECT CEILING(1.34) FROM DUAL;//2
```



#### FLOOR(number)

向下取整，得到比number小的最大整数

```sql
SELECT FLOOR(1.34) FROM DUAL;//1
```



#### RAND([seed])

随机数范围为[0,1,0]

不加种子返回的数每次都不同

种子指定后返回的数不变

```sql
SELECT RAND(6) FROM DUAL;
```



### 日期相关函数

对于日期而言也有非常多的相关函数，有点小多，在这里不过多介绍，即查即用



### 加密函数于系统函数

#### USER()

显示登录到mysql有哪些用户以及登陆的IP

```sql
select user() from dual;
```

```cmd
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
```



#### DATABASE()

查询使用数据库名

```sql
select DATABASE() from dual;
```

```cmd
+------------+
| DATABASE() |
+------------+
| dbtest     |
+------------+
```



#### MD5(str)

为字符串计算出一个MD5 32的字符串，常用为用户密码加密(32位)

```sql
select MD5('密码') from dual;
```

```cmd
+----------------------------------+
| MD5('密码')                      |
+----------------------------------+
| a8105204604a0b11e916f3879aae3b0b |
+----------------------------------+
```

```sql
UPDATE user SET `password`=MD5(654321);
```



#### PASSWORD(str)

加密函数，是MySQL使用的加密方式

```sql
select * from mysql.user
//其中的$A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED就是加密后的密码
```



### 流程控制函数

#### IF(expr1,expr2,expr3)

如果表达式1为真，返回expr2，否则返回expr3

```SQL
SELECT IF(TRUE, 'BEIJING', 'SHANGHAI') FROM DUAL;//BEIJING
```



#### IFNULL(expr1,expr2)

如果表达式1为NULL，返回expr1，否则返回expr2

```SQL
SELECT IFNULL('BEIJING', 'SHANGHAI') FROM DUAL;//BEIJING
SELECT IFNULL('NULL', 'SHANGHAI') FROM DUAL;//SHANGHAI
```



#### 多分支函数

```sql
SELECT CASE 
WHEN expr1 THEN expr2 
WHEN expr3 THEN expr4 
ELSE expr5
```

```sql
SELECT CASE 
WHEN FALSE THEN expr2 
WHEN TRUE THEN expr4 
ELSE expr5 END
//返回expr2
```



#### 例题

查询emp表，如果comm是null，就显示0.0

```sql
SELECT name, IF(comm IS NULL, 0.0, comm) FROM emp;
```

```sql
SELECT name, IFNULL(comm, 0.0) FROM emp;
```



如果emp表的job是CLERK则显示职员，如果是MANAGER则显示经理，如果是SALESMAN就显示销售人员，其他正常显示

```sql
SELECT name, (
SELECT CASE 
WHEN job='CLERK' THEN '职员' 
WHEN job='MANAGER' THEN '经理' 
WHEN job='SALESMAN' THEN '销售人员' 
ELSE job END) AS 'job'
FROM emp;
```

以上就是MySQL的基本语句，查询的基础部分，和一些常见的函数，我们明天会进入增强查询（多表查询）
