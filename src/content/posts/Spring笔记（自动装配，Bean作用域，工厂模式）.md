---
title: Spring笔记（自动装配，Bean作用域，工厂模式）
published: 2025-05-20
updated: 2025-05-20
description: '自动装配，引用外部配置文件，Bean作用域，工厂模式'
image: ''
tags: [Spring]
category: 'Frame'
draft: false 
---

# Spring笔记

## 自动装配

基于set实现

Spring还可以完成自动化的注入，又被称为自动装配，可以根据名字进行自动装配，也可以根据类型进行自动装配

接下来以OrderService和OrderDao进行举例

```java
public class OrderDao implements UserDao {
    @Override
    public void insert() {
        System.out.println("OrderDao.insert");
    }
}
```

```java
public class OrderService implements UserService {
    UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```



### 根据名字

传统的方式进行注入

```html
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="orderDao" class="com.thrinisty.dao.OrderDao"/>
    <bean id="orderService" class="com.thrinisty.service.OrderService">
        <property name="userDao" ref="orderDao"/>
    </bean>
</beans>
```

```java
@Test
public void Test() {
    ApplicationContext context = new ClassPathXmlApplicationContext("autowire.xml");
    OrderDao orderDao = context.getBean("orderDao", OrderDao.class);
    System.out.println(orderDao);
}
```

自动装配，这里对于OrderDao的id有严格要求，和set方法对应

```html
<bean id="orderService" class="com.thrinisty.service.OrderService" autowire="byName"/>
<bean id="orderDao" class="com.thrinisty.dao.OrderDao"/>
```



### 根据类型

根据类型进行装配，类型的实例不可以有多个

```html
<bean id="orderDao" class="com.thrinisty.dao.OrderDao"/>
<bean id="orderService" class="com.thrinisty.service.OrderService" autowire="byType"/>
```



## 外部属性配置文件

相关外部配置properties

```properties
driverClass=com.mysql.cj.jdbc.Driver
username=root
password=123456
```

第一步：引入context命名空间

第二步：通过${}使用配置文件中的数据，完成注入

```html
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="jdbc.properties"/>
    <bean id="ds" class="com.thrinisty.jdbc.Source01">
        <property name="driver" value="${driverClass}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
    </bean>
</beans>
```

```
Source01{driver='com.mysql.cj.jdbc.Driver', username='71460', password='123456'}
```

这里看到我们的username值为71460，这是我的系统变量配置，和预期的有些不符（root）

:::tip

在写数据库配置文件的时候在配置前加上jdbc.以与系统的配置进行区分

:::

```properties
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.username=root
jdbc.password=123456
```

```html
<property name="driver" value="${jdbc.driverClass}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
```

这样就没有什么问题了



## Bean作用域

### 单例与多例

在默认的情况下Bean对象是单例的singleton，在加载spring的xml配置文件就会创建出这样一个对象，而后续在运用getBean获取到的对象是同一个

```java
@Test
public void test1() {
    ApplicationContext context = new ClassPathXmlApplicationContext("scope.xml");
    SpringBean springBean1 = context.getBean("springBean", SpringBean.class);
    System.out.println(springBean1);
    SpringBean springBean2 = context.getBean("springBean", SpringBean.class);
    System.out.println(springBean2);
    SpringBean springBean3 = context.getBean("springBean", SpringBean.class);
    System.out.println(springBean3);
}
```

```
com.thrinisty.bean.SpringBean@51a9ad5e
com.thrinisty.bean.SpringBean@51a9ad5e
com.thrinisty.bean.SpringBean@51a9ad5e
```



可以通过设置Bean的作用域（scope）实现多例 prototype

```html
<bean id="springBean" class="com.thrinisty.bean.SpringBean" scope="prototype"/>
```

```
com.thrinisty.bean.SpringBean@6950ed69
com.thrinisty.bean.SpringBean@6dd7b5a3
com.thrinisty.bean.SpringBean@6f3187b0
```

这样的话加载配置文件不会初始化实例对象，而getBean时获取新的实例

其余的我们还可以设置为session（在一个会话中只有一个实例）request（在一个请求中只有一个实例）需要引入SpringMVC的Maven配置



### 自定义范围

除此以外还可以自定义范围（很少使用，了解即可）

```java
public class TestScope {
    @Test
    public void test1() {
        ApplicationContext context = new ClassPathXmlApplicationContext("scope.xml");
        SpringBean springBean1 = context.getBean("springBean", SpringBean.class);
        System.out.println(springBean1);

        Runnable runnable = () -> {
            SpringBean springBean2 = context.getBean("springBean", SpringBean.class);
            System.out.println(springBean2);
        };
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

```
com.thrinisty.bean.SpringBean@7d94beb9
com.thrinisty.bean.SpringBean@7d94beb9
```

判断在多个线程中，通过Spring创建的对象为单例



现在，我们需要在每一个线程中创建对应的实例

第一步：自定义Scope，实现Scope接口

Spring内置了一个线程范围的类：SimpleThreadScope，这里不再实现

第二步：将自定义的Scope注册到Spring容器中

```html
<bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
    <property name="scopes">
        <map>
            <entry key="myThread">
                <bean class="org.springframework.context.support.SimpleThreadScope"/>
            </entry>
        </map>
    </property>
</bean>
```

第三步：使用Scope

```html
<bean id="springBean" class="com.thrinisty.bean.SpringBean" scope="myThread"/>
```

编写测试

```java
@Test
public void test1() {
    ApplicationContext context = new ClassPathXmlApplicationContext("scope.xml");
    SpringBean springBean1 = context.getBean("springBean", SpringBean.class);
    System.out.println(springBean1);
    SpringBean springBean3 = context.getBean("springBean", SpringBean.class);
    System.out.println(springBean3);
    Runnable runnable = () -> {
        SpringBean springBean2 = context.getBean("springBean", SpringBean.class);
        System.out.println(springBean2);
        SpringBean springBean4 = context.getBean("springBean", SpringBean.class);
        System.out.println(springBean4);
    };
    Thread thread = new Thread(runnable);
    thread.start();
}
```

```
com.thrinisty.bean.SpringBean@39a2bb97
com.thrinisty.bean.SpringBean@39a2bb97
com.thrinisty.bean.SpringBean@1f702edb
com.thrinisty.bean.SpringBean@1f702edb
```

证明在线程中安全



## GoF工厂模式

### 23种设计模式

**创建型：用于解决创建对象问题**

单例模式，工厂方法模式，抽象工厂模式，建造者模式，原型模式

**结构型：一些类和对象组合在一起的经典结构**

代理模式，修饰模式，适配器模式，组合模式，享元模式，外观模式，桥接模式

**行为型：解决类或者对象之间的交互问题**

策略模式，模式方法模式，责任链模式，观察者模式，迭代子模型，命令模式，备忘录模式，状态模式，访问者模式，中介者模式，解释器模式



### 工厂三种形态

简单工厂模式，工厂方法模式，抽象工厂模式

#### 简单工厂模式

是工厂方法模式的一种特殊实现，又称为静态工厂方法模式

有三个角色：抽象产品，具体产品，工厂类

具体产品

```java
public class Fighter extends Weapon{
    @Override
    public void attack() {
        System.out.println("飞机攻击");
    }
}
```

```java
public class Tank extends Weapon{
    @Override
    public void attack() {
        System.out.println("坦克攻击");
    }
}
```

抽象产品

```java
public abstract class Weapon {
    public abstract void attack();
}
```

工厂类

```java
public class WeaponFactory {
    public static Weapon get(String weaponType) {
        if ("Tank".equals(weaponType)) {
            return new Tank();
        } else if ("Fighter".equals(weaponType)) {
            return new Fighter();
        } else {
            System.out.println("创建失败");
            return null;
        }
    }
}
```

测试程序

```java
public class Test {
    public static void main(String[] args) {
        Weapon tank = WeaponFactory.get("Tank");
        if (tank != null) {
            tank.attack();
        }
    }
}
```

**优点：**

客户只负责消费，工厂类负责生产，生产者和消费者分离，客户端不需要关注实现细节，只负责传入索要即可使用

**缺点：**

增加工厂产品，需要修改工厂类代码，违反OCP原则，另外工厂的责任大，发生问题的时候系统瘫痪，健壮性差



#### 工厂方法模式

一个产品对应一个工厂

解决了简单工厂模式中出现的OCP问题

有四个角色：抽象产品，具体产品（多个），抽象工厂类，具体工厂类（多个）

**抽象工厂**

```java
public abstract class WeaponFactory {
    public abstract Weapon get();
}
```

**具体工厂**

```java
public class TankFactory extends WeaponFactory{
    @Override
    public Weapon get() {
        return new Tank();
    }
}
```

**测试程序**

```java
public class Test {
    public static void main(String[] args) {
        WeaponFactory factory = new TankFactory();
        Weapon tank = factory.get();
        tank.attack();
    }
}
```

添加新的产品的时候，只需要增加Weapon的实现类，以及对应武器的工厂实现类即可在不影响原有代码的基础上，通过对应的具体工厂创建武器的实例

缺点：类的复杂性增加，类的数量大



这两天除了Spring的学习以外，还而外的抽出时间刷了下LeetCode的Hot100，之前没有系统性地刷过题，于是计划按照通过率，从简单到难按顺序一步一步来，这段时间至少每日一题，保持一下编程的手感

另外，这段时间忙着机器学习的大作业（5.30），以及编译原理实验（5.24），还要复习编译原理理论考试（5.28），网络编程理论考试，不是很能抽得出时间完成Spring的学习，我想等到端午节后再继续Spring的学习
