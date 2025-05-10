---
title: JDBC笔记
published: 2025-04-18
updated: 2025-04-18
description: '批处理，连接池，DBUtils，BasicDao'
image: ''
tags: [JDBC]
category: ''
draft: false 
---

# JDBC笔记

## 批处理

### 基本介绍

1.当需要成批插入或者更新记录的时候，可以使用Java批处理，这一机制允许多条语句一次性提交给数据库批量处理，通常情况下比单独提交更有效率

2.JDBC的批量处理语句包括了以下的方法

| 方法名称     | 说明                              |
| ------------ | --------------------------------- |
| addBatch     | 添加需要批量处理的SQL语句或者参数 |
| executeBatch | 执行批量处理语句                  |
| clearBatch   | 清空批处理包的语句                |

3.JDBC连接MySQL时，如果要使用批处理，需要在url中添加参数

```sql
?rewriteBatchedStatements=true
```

4.批处理往往与PreparedStatement一起搭配使用，可以及减少编译次数又减少运行次数，效率大大提升



### 传统的添加方式

```java
public class Test {
    @SuppressWarnings ({"all"})
    public static void main(String[] args) throws Exception{
        Connection connection = JDBCUtils.getConnection();
        String sql = "insert into balance values (?,?,?)";
        PreparedStatement statement = connection.prepareStatement(sql);

        for (int i = 1; i < 1000; i++) {
            statement.setInt(1, i);
            statement.setString(2, "TomCat");
            statement.setDouble(3, i * 2);
            statement.executeUpdate();
        }
        JDBCUtils.close(null, statement, connection);
    }
}
```

耗时还是挺久的大概6、7秒钟完成，感兴趣可以输出运行时间测试一下



### 批处理运行

```sql
public class Test {
    @SuppressWarnings ({"all"})
    public static void main(String[] args) throws Exception{
        Connection connection = JDBCUtils.getConnection();
        String sql = "insert into balance values (?,?,?)";
        PreparedStatement statement = connection.prepareStatement(sql);

        for (int i = 1; i < 1000; i++) {
            statement.setInt(1, i);
            statement.setString(2, "TomCat");
            statement.setDouble(3, i * 2);
            statement.addBatch();
            if((i + 1) % 1000 == 0) {
                statement.executeBatch();
                statement.clearBatch();
            }
        }
        JDBCUtils.close(null, statement, connection);
    }
}
```

注意在JDBCUtils工具类中的url添加相关参数

```sql
String url = agreement + "//" + address + ":" + port + "/" + dataBase + "?rewriteBatchedStatements=true";
```

运用批处理在处理大量的SQL语句的时候，速度相较于单条执行要快很多，大概的用时只需要1、2秒钟



## 连接池

### 连接池的引入

在传统的连接方式中往往程序和数据库进行网络的连接是多次的，这张方式的连接数上限往往是有限制的，在太多的连接的时候和数据库连接取得Connection的时候会抛出Too many connection的异常，就算及时关闭连接，连接的时长也会很长

问题分析

1.传统的JDBC数据库连接使用DriverManager来获取，每一次向数据库建立连接的时候都要将Connection加载到内存中，再验证IP地址，用户名，密码（0.05-1s）。需要数据库链接的时候，就会向数据库要求一个，频繁的进行数据库连接操作将占用很多的系统资源，容易造成服务器崩溃

2.每一次数据库连接，使用完后都得断开，如果程序出现异常未能关闭，将导致数据库内存泄漏，最终导致重启数据库

3.传统获取连接的方式，不能控制创建的连接数量，如果连接过多，也可能导致内存泄漏，数据库崩溃

4.解决传统开发中的数据库连接问题，可以采用数据库连接池技术（connection pool）



### 基本介绍

1.预先在缓冲池中放入一定数量的连接，当需要建立数据据库连接的时候，只需要从缓冲池中取出连接，使用完毕的时候再将连接放回缓冲池中即可

2.数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用现有的数据库连接，而不是重新建立一个

3.当应用程序向连接池请求的连接数超过最大连接数的时候，这些请求将被添加入等待队列



### 数据库连接池种类

1.JDBC的数据库连接池使用javax.sql.DataSource表示，DataSource是一个接口，通常由第三方来实现

2.**C3P0**数据库连接池，速度相对较慢，但是稳定性不错（hibernate，spring）

3.DBCP数据库连接池，速度相比于C3P0较快，但是不稳定

4.Proxool数据库连接处，有监控连接池状态的功能，稳定性较C3P0差一点

5.BoneCP数据库连接池，速度快

6.**Druid**（德鲁伊）是由阿里提供的数据库连接池，集以上（C3P0、DBCP、Proxool）数据库优点于一体的数据库连接池

C3P0和德鲁伊连接池是目前经常会使用的连接池，我们接下来使用的也是这两个



### C3P0连接池

注意在连接池中的close不是指的是将连接断开，而是将连接放回到连接池中，供下一个getConnection使用

```java
public static void test01() throws Exception{
    ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
    String agreement = "jdbc:mysql:";
    String address = "localhost";
    String port = "3306";
    String dataBase = "jdbc";
    String url = agreement + "//" + address + ":" + port + "/" + dataBase;
    String user = "root";
    String password = "654321";
    String driver = "com.mysql.cj.jdbc.Driver";

    //设置连接池基础信息
    comboPooledDataSource.setDriverClass(driver);
    comboPooledDataSource.setJdbcUrl(url);
    comboPooledDataSource.setUser(user);
    comboPooledDataSource.setPassword(password);

    //初始化连接数
    comboPooledDataSource.setInitialPoolSize(10);
    comboPooledDataSource.setMaxPoolSize(50);

    //从连接池中获取连接
    Connection connection = comboPooledDataSource.getConnection();
    connection.close();
}
```

如果通过这种方式创建连接相比于JDBC的从DriverManager获取连接要快很多

除此之外你还可以使用配置文件设置连接池的基础信息（在创建连接池对象的时候指定配置文件参数），感兴趣的可以下去了解



### Druid连接池

#### 连接池基本配置

我们在使用Druid连接池的时候需要使用到配置文件

```properties
# 数据库连接配置
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/tableselect?useSSL=false&serverTimezone=UTC&characterEncoding=utf8
username=root
password=654321

# 连接池核心参数
initialSize=10
maxActive=50
minIdle=5
maxWait=60000
#maxWait指的是等待队列中最长的等待时间
```



#### 连接池使用

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        testDruid();
    }

    public static void testDruid() throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\druid.properties"));
        //创建连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        //过去Connection连接
        Connection connection = dataSource.getConnection();
        connection.close();
    }
}
```

Druid速度非常快，目前而言能用Druid尽量别用C3P0，除非要进行兼容



#### 工具类封装

便于使用者使用，我们将德鲁伊连接池做成一个工具类

```java
package com.utils;
import com.alibaba.druid.pool.DruidDataSourceFactory;
import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JDBCUtilsDruid {
    private static DataSource ds;

    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileInputStream("src\\druid.properties"));
            ds = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection() {
        try {
            return ds.getConnection();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    public static void close(ResultSet result, Statement statement, Connection connection) {
        try {
            if (result != null) {
                result.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

使用测试方法调用，这样做会方便很多

```java
public static void testUtils() {
    Connection connection = JDBCUtilsDruid.getConnection();
    JDBCUtilsDruid.close(null, null, connection);
    //这里用的是工具类的close方法
}
```

再多说一嘴，其实这种情况下使用connection.close()方法关闭连接也没有问题，因为通过动态绑定机制，connection实际上调用的是德鲁伊的close方法。这个方法和通过DriverManager创建的连接调用的close方法有本质的区别，不会将链接关闭，而是将连接放回连接池中



## DBUtils

### 问题引出

当我们关闭connection后，resultSet结果集无法使用（二者相关联），而有的时候我们需要复用resultSet中的数据，关闭connection后任需要结果集

resultSet不利于数据的管理，使用起来也不太方便，这个时候我们需要再Java程序中创建一个类（JavaBean）与数据库的记录做一个映射，再创建一个结果集记录封装在ArrayList<JavaBean>下，每一个集合元素对应一条记录

这样的话我们使用类来获取数据就会方便一些，而且生命期更加持久，可以在不用的时候再将ArrayList集合对象释放（以上的思路可以去自己实现，以下是一个Apache提供的一个工具类，相比于自己写的更加完善方便）



### commons-dbutils

#### 基本介绍

commons-dbutils是Apache组织提供的一个开源JDBC工具类库，它是对JDBC的封装，使用dbutils可以极大地简化JDBC编码的工作量



#### DBUtils类

1.QueryRunner类：该类封装了SQL的执行，是线程安全的，可以实现增删改查，批处理

2.ResultSetHandler接口：该接口用于处理ResultSet，将数据按要求转化为另一种形式

![123](../images/123.png)



#### 构造返回记录类型

要使这个自动映射正常工作，`Balance`类需要：

- 是一个标准的JavaBean（有无参构造函数）
- 有与数据库列名对应的属性
- 有这些属性的setter方法

构造一个Balance接收返回结果的类

```java
public class Balance {
    private int id;
    private String name;
    private String money;
    public Balance(){}

    @Override
    public String toString() {
        return id + " " + name + " " + money;
    }

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setMoney(String money) {
        this.money = money;
    }
}
```

#### 查询结果

利用QueryRunner对象查询数据库返回结果

返回单条单列的记录（返回一个Object对象）

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        Connection connection = JDBCUtilsDruid.getConnection();
        QueryRunner queryRunner = new QueryRunner();
        //通过使用queryRunner就可以查询数据库，返回结果
        String sql = "select `name` from balance where id = ?;";
        String name = (String) queryRunner.query(connection, sql, new ScalarHandler(), 100);
        System.out.println(name);
        connection.close();
    }
}
```

返回单条记录（返回一个对应的Balance对象）

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        Connection connection = JDBCUtilsDruid.getConnection();
        QueryRunner queryRunner = new QueryRunner();
        //通过使用queryRunner就可以查询数据库，返回结果
        String sql = "select * from balance where id = ?;";
        Balance balance = queryRunner.query(connection, sql, new BeanHandler<>(Balance.class), 100);
        System.out.println(balance);
        connection.close();
    }
}
```

返回多条结果（返回一个存放了Balance对象的ArrayList）

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        Connection connection = JDBCUtilsDruid.getConnection();
        QueryRunner queryRunner = new QueryRunner();
        //通过使用queryRunner就可以查询数据库，返回结果
        String sql = "select * from balance where id = ? or id = ?;";
        List<Balance> list = queryRunner.query(connection, sql, new BeanListHandler<>(Balance.class), 100, 200);
        //这里的100和200是可变参数，代表填入的替换？的内容
        Iterator<Balance> iterator = list.iterator();
        while(iterator.hasNext()) {
            Balance balance = iterator.next();
            System.out.println(balance);
        }
        connection.close();
    }
}
```

如果步入源码中我们可以发现在query方法执行获取ArrayList结果集的过程中用到的Statement和ResultSet就已经被关闭

而返回的结果类型取决于第三个参数的类型，其中result返回结果底层运用到了泛型来确定result的返回类型（更具不同的参数类型有不同的处理逻辑）



#### 增删改

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        Connection connection = JDBCUtilsDruid.getConnection();
        QueryRunner queryRunner = new QueryRunner();
        //通过使用queryRunner就可以查询数据库，返回结果
        String sql = "insert into balance values (300, 'Tom', 4000), (400, 'King', 5000);";
        int update = queryRunner.update(connection, sql);
        System.out.println(update);
        connection.close();
    }
}
```

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        Connection connection = JDBCUtilsDruid.getConnection();
        QueryRunner queryRunner = new QueryRunner();
        //通过使用queryRunner就可以查询数据库，返回结果
        String sql = "delete from balance;";
        int update = queryRunner.update(connection, sql);
        System.out.println(update);
        connection.close();
    }
}
```

```java
public class Druid_ {
    public static void main(String[] args) throws Exception {
        Connection connection = JDBCUtilsDruid.getConnection();
        QueryRunner queryRunner = new QueryRunner();
        //通过使用queryRunner就可以查询数据库，返回结果
        String sql = "update balance set name = 'Cat'";
        int update = queryRunner.update(connection, sql);
        //返回的结果是受影响的行数
        System.out.println(update);//2
        connection.close();
    }
}
```



## BasicDao

### 问题引出

1.SQL语句是固定的，不可以通过参数传入，使用起来不灵活

2.对于select操作，返回类型不确定，需要使用到泛型

3.对于很多表而言，业务负责不可能单靠一个Java类完成



### Dao

data access object 数据访问对象

我们对于每一个表都会有相应的操作，我们可以将这一些操作的共有部分提到BasicDao中，这样做可以简化代码，维护可读性

1.这样的通用类我们称为BasicDao，是专门和数据库交互的，即完成数据库的增删改查操作

2.再BasicDao的基础上，实现一张表对应一个Dao，可以更好的完成功能

接下来我们就通过代码实际实现Dao



### Dao实现

设计com.dao

#### com.dao.utils  

工具类

```java
public class JDBCUtilsDruid {
    private static DataSource ds;

    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileInputStream("src\\druid.properties"));
            ds = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection() {
        try {
            return ds.getConnection();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    public static void close(ResultSet result, Statement statement, Connection connection) {
        try {
            if (result != null) {
                result.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```



#### com.dao.domain 

javabean

```java
public class Balance {
    private int id;
    private String name;
    private String money;
    public Balance(){}

    @Override
    public String toString() {
        return id + " " + name + " " + money;
    }

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setMoney(String money) {
        this.money = money;
    }
}
```



#### com.dao.dao  

存放BasicDao

```java
public class BasicDao<T> {//使用泛型指定具体类型
    private QueryRunner qr = new QueryRunner();

    //开发通用的dml方法，针对任意的表
    public int update(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsDruid.getConnection();
            return qr.update(connection, sql, parameters);
            //返回受影响的行数，执行语句
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsDruid.close(null, null, connection);
        }
    }

    //返回对个查询对象
    public List<T> queryMulti(String sql, Class<T> cls, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsDruid.getConnection();
            return qr.query(connection, sql, new BeanListHandler<T>(cls), parameters);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsDruid.close(null, null, connection);
        }
    }

    //返回查询单行结果
    public T querySingle(String sql, Class<T> cls, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsDruid.getConnection();
            return qr.query(connection, sql, new BeanHandler<T>(cls), parameters);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsDruid.close(null, null, connection);
        }
    }

    //查询单行单列的方法
    public Object queryScalar(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsDruid.getConnection();
            return qr.query(connection, sql, new ScalarHandler(), parameters);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsDruid.close(null, null, connection);
        }
    }
}
```

接下来我们根据BasicDao来开发针对于balance表的Dao，制定了前面我们设计的BasicDao的泛型T为Balance类

```java
public class BalanceDao extends BasicDao<Balance>{
    //拥有BasicDao方法
    //除此之外还可以自定义其他的方法
}
```

可以看出在没有特殊要求的情况下，我们只需要在继承的时候指定泛型类型即可创建出新的对应表Dao，非常的方便



#### com.dao.test  

测试类

```java
public class TestDao {
    public static void main(String[] args) {
        BalanceDao balanceDao = new BalanceDao();

        String sql = "select * from balance";
        List<Balance> balances = balanceDao.queryMulti(sql, Balance.class);
        for(Balance balance : balances) {
            System.out.println(balance);
        }

        sql = "select * from balance where id = ?";
        Balance balance = balanceDao.querySingle(sql, Balance.class, 100);
        System.out.println(balance);

        sql = "select name from balance where id = 100";
        String name =(String) balanceDao.queryScalar(sql);
        System.out.println(name);

        sql = "insert into balance values (300, 'Tom', 4000), (400, 'King', 5000);";
        int update = balanceDao.update(sql);
        System.out.println(update);
    }
}
```

看的出来还是非常方便的啊

#### 补充

其实你要在方便一点（不想要传入Balance.class）的话还可以在BasicDao中用反射获取T类对象，将类对象传入



# JDBC结语

两天的时间，学的还是挺快的，过了一遍代码，学会了使用方式，对于JDBC，连接池，DB工具，自建BasicDao有了初步的认识，对于底层的代码看了一眼，不算完全掌握

反思了以下自己泛型的部分掌握还不足，决定回宿舍复习一下泛型相关的内容

明天打算开始Java8特性的学习，计划三天完成，估计我的有一个面试在三天之后，面试结束再决定Java8后学哪一个开发框架，在此之前先准备下面试，背下八股之类的
