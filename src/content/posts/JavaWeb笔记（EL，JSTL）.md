---
title: JavaWeb笔记(EL，JSTL)
published: 2025-05-11
updated: 2025-05-11
description: 'EL表达式，标签库'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 

---

# JSP

这两天事情确实有一点多，但好在也是顺利完成了，昨晚上还抽空更新了一下主页的样式，把就博客给迁移到了 Fuwari 新的主题上，看着确实稍微顺眼一点，今天没什么多的事情，就学一下EL表达式，JSTL标签库部分的知识



## EL表达式

EL表达式全称是：Expression Language，是表达式语言

作用：代替JSP页面中的表达式脚本，在JSP页面中进行数据的输出，格式比JSP输出脚本要简洁得多

EL基本上是为了JSP而开发的（所以使用地位上也基本和JSP等同），在现代企业开发中如果不是为了维护老的项目，其实已经不再继续使用EL表达式了，但是多学一点总是没有坏处的



### 入门使用

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>EL表达式</title>
</head>
<body>
    <%
        request.setAttribute("key", "value");
    %>
    <%=request.getAttribute("key")%>
    ${key}
</body>
</html>
```

我们之前学过了通过表达式脚本标签输出内容到页面上，我们现在使用EL表达式来实现相同的功能

```elm
${key1}
```

可以看到相比较于原先的一大串，EL表达式的形式上要简洁的多



而且我们现在来尝试输出一个不存在的key对应的值

```jsp
<%=request.getAttribute("keyNull")%>
${keyNull}
```

```
null	//null字符串	
		//无结果
```

在用户的角度而言，无结果肯定是优于null的，EL表达式做了一些优化

当然我们使用三目运算符，编写表达式脚本也可以实现类似的效果

```jsp
<%=request.getAttribute("key") == null? "" : request.getAttribute("key")%>
```



**域对象数据输出**

EL表达式主要输出的是域对象的数据

```jsp
<%
    pageContext.setAttribute("key", "value1");
    request.setAttribute("key", "value2");
    session.setAttribute("key", "value3");
    application.setAttribute("key", "value4");
%>
${key}
```

```
value1
```



:::tip
这里在key相同的情况下，优先输出域范围小的时候的值，这里和c语言中的屏蔽机制有些类似，但是在实际使用的时候不建议这么做，回减少代码的可读性
:::



### Bean对象输出

#### 对象构造

我们先编写一个Java Person类

:::important
get方法必须要添加，因为后续EL表达式调用的不是类本身的属性值，而是通过getXxx方法获取到的属性对象
:::

```java
package com.bean;

import java.util.Arrays;
import java.util.List;
import java.util.Map;

public class Person {
    private String name;
    private String[] phones;
    private List<String> cities;
    private Map<String, Object> map;

    public Person() {}

    public Person(String name, String[] phones, List<String> cities, Map<String, Object> map) {
        this.name = name;
        this.phones = phones;
        this.cities = cities;
        this.map = map;
    }
//省略set、get方法，get必须要添加
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", phones=" + Arrays.toString(phones) +
                ", cities=" + cities +
                ", map=" + map +
                '}';
    }
}
```



在JSP页面中运用代码脚本设置初值以及构造Person类的实例对象

```jsp
<%
    String[] phones = {"phone1", "phone2", "phone3"};
    List<String> list = new ArrayList<>();
    list.add("Beijing");
    list.add("Shanghai");
    Map<String, Object> map = new HashMap<>();
    map.put("key1", "value1");
    map.put("key2", "value2");
    map.put("key3", "value3");
    map.put("key4", "value4");
    Person person = new Person("Jack", phones, list, map);
    pageContext.setAttribute("person", person);
%>
```



#### 输出方式

找到对应的get方法

##### 类的输出

```html
输出Person类数据${person}<br/>
```

```
输出Person类数据Person{name='Jack', phones=[phone1, phone2, phone3], cities=[Beijing, Shanghai], map={key1=value1, key2=value2, key3=value3,
```



##### 普通属性

```
输出name属性${person.name}<br/>
```

```
输出name属性Jack
```



##### 数组属性

```
输出phones数组属性${person.phones[0]}<br/>
```

```
输出phones数组属性phone1
```



##### List集合

单个数据

```
输出city：list集合${person.cities[0]}<br/>
```

```
输出city：list集合Beijing
```

整个list

```
输出city：list集合${person.cities}<br/>
```

```
输出city：list集合[Beijing, Shanghai]
```



##### map集合

单个数据

```
输出map集合${person.map.key1}<br/>
```

```
输出map集合value1
```

整个map

```
输出map集合${person.map}<br/>
```

```
输出map集合{key1=value1, key2=value2, key3=value3, key4=value4}
```



### EL表达式运算

#### 关系运算

```jsp
<body>
    ${12 == 12}或${12 eq 12}<br/>
    ${12 != 12}或${12 ne 12}<br/>
    ${1 < 2}或${1 lt 2}<br/>
    ${1 > 2}或${1 gt 2}<br/>
    ${1 <= 2}或${1 le 2}<br/>
    ${1 >= 2}或${1 ge 2}<br/>
</body>
```

页面显示

```
true或true
false或false
true或true
false或false
true或true
false或false
```



#### 逻辑运算

```elm
<body>
    ${true && true}或${true and true}<br/>
    ${true || false}或${true or false}<br/>
    ${!true}或${not true}<br/>
</body>
```

页面显示

```
true或true
true或true
false或false
```



#### 算术运算

```elm
<body>
    ${12 + 18}<br/>
    ${12 - 10}<br/>
    ${12 * 2}<br/>
    ${12 / 2} 或 ${12 div 2}<br/>
    ${13 % 2} 或 ${13 mod 2}<br/>
</body>
```

页面显示

```
30
2
24
6.0 或 6.0
1 或 1
```



#### empty运算

可以判断一个数据是否为空，为空则输出true，其余为false

值为null  值为空串  值为Object类型数组且长度为0  list、map集合元素个数为0 

定义一些（为空的）对象

```jsp
<%
    Integer a = null;
    String b ="";
    Object[] c = new Object[0];
    List<Object> d = new ArrayList<>();
    Map<String, Object> e = new HashMap<>();
    request.setAttribute("a", a);
    request.setAttribute("b", b);
    request.setAttribute("c", c);
    request.setAttribute("d", d);
    request.setAttribute("e", e);
%>
```

```elm
${empty a}<br/>
${empty b}<br/>
${empty c}<br/>
${empty d}<br/>
${empty e}<br/>
```

这些值显示都是true



#### 点运算与中括号

点运算：可以输出Bean对象中的某个元素值

中括号：可以输出有序集合中某个元素的值，还可以输出map集合中key里含有特殊字符的key值

例如有一个key为a.a.a的数据放在名为map的Map集合中，我们可以使用如下的方式取出数据

```
${map['a.a.a']}
```



### EL表达式隐含对象

EL表达式中定义的，可以直接使用

#### 隐含对象表格

| 变量             | 类型                 | 作用                         |
| ---------------- | -------------------- | ---------------------------- |
| pageContext      | PageConextImpl       | 获取jsp中的九大内置对象      |
| pageScope        | Map<String,Object>   | 获取PageContext域中的数据    |
| requestScope     | Map<String,Object>   | 获取Request域中的数据        |
| sessionScope     | Map<String,Object>   | 获取Session域中的数据        |
| applicationScope | Map<String,Object>   | 获取ServletContext域中的数据 |
| param            | Map<String,Object>   | 获取请求参数的值             |
| paramValues      | Map<String,Object[]> | 获取请求参数的值(多个)       |
| header           | Map<String,String>   | 获取请求头的信息             |
| headerValues     | Map<String,String[]> | 获取请求头的信息(多个)       |
| cookie           | Map<String,Cookie>   | 获取当前请求的Cookie信息     |
| initParam        | Map<String,String>   | web.xml配置中的上下文参数    |



#### 域对象

对于四个域对象而言

相比于之前获取域数据直接填入key查找对应的value，我们在使用隐含变量获取key可以避免数据的屏蔽，选择性的输出想要的域对应的值

```
${变量.key}
```



#### pageContext

获取JSP内置对象

常见作用

常用于输出常用信息：协议，获取服务器ip，获取服务器端口，工程路径，请求方法，获取客户端ip，获取会话id编号（上边是JSP表达式，下边是EL表达式）

协议

```
<%request.getScheme()%>
${pageContext.request.scheme}
```

获取服务器ip

```
<%request.getServerName()%>
${pageContext.request.servername}
```

获取服务器端口

```
<%request.getServerPort()%>
${pageContext.request.serverPort}
```

工程路径

```
<%request.getContextPath()%>
${pageContext.request.contextPath}
```

请求方法

```
<%request.getMethod()%>
${pageContext.request.method}
```

获取客户端ip

```
<%request.getRemoteHost()%>
${pageContext.request.remoteHost}
```

获取会话id编号

```
<%session.getId())%>
${pageContext.session.id}
```



## JSTL标签库

JSTL标签库是 JSP标准的标签库，EL表达式是为了替换JSP中的表达式脚本，而标签库则是为了替换代码脚本，使得JSP页面更加的简洁



### JSTL的五个标签

对应五个不同功能

| 功能范围   | URI                                    | 前缀 |
| ---------- | -------------------------------------- | ---- |
| 核心标签库 | http://java.sun.com/jsp/jstl/core      | c    |
| 格式化     | http://java.sun.com/jsp/jstl/fmt       | fmt  |
| 函数       | http://java.sun.com/jsp/jstl/functions | fn   |
| 数据库     | http://java.sun.com/jsp/jstl/sql       | sql  |
| XML        | http://java.sun.com/jsp/jstl/xml       | x    |

一般不使用数据库标签（破坏了三层结构），XML标签（通过Session传数据）



### 使用步骤

导入jar包，我这里给出了Maven构建的对应坐标文件

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

在JSP文件下导入标签库（IDEA可以自动导入）

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```



### core核心库

#### set

可以往域里面那保存数据

```jsp
<c:set scope="request" var="key" value="123"/>
${requestScope.key}
```



#### if

做选择判断，但是不可以做if-else分支，需要使用需要再写一个if标签

```jsp
<c:set scope="request" var="key" value="123"/>
<c:if test="${false or true}">
    ${requestScope.key}
</c:if>
```



#### choose

用于多路判断，和switch类似

```jsp
<c:choose>
    <c:when test="${key > 180}">
        ${key}
    </c:when>
    <c:when test="${key < 180 && key > 100}">
        ${key1}
    </c:when>
    <c:otherwise>
        ${key2}
    </c:otherwise>
</c:choose>
```

:::warning
choose里不可以使用html注释.

otherwise和when标签的直接父标签一定要是choose

:::



#### forEach

作用是遍历输出

遍历1~10，var属性表示循环的变量

```jsp
<c:forEach begin="1" end="10" var="i">
    ${i}
</c:forEach>
```

遍历Object数组

```jsp
<body>
    <%
        Object[] arr = {"Java", "Cpp", 10};
        request.setAttribute("key", arr);
    %>
    <c:forEach items="${requestScope.key}" var="i">
       ${i}
    </c:forEach>
</body>
```

遍历List集合（Bean对象一般可以放在表格中显示，要整齐一点）

```jsp
<c:forEach items="${requestScope.person}" var="person">
    ${person}
</c:forEach>
```

遍历Map集合

```jsp
<c:forEach items="${requestScope.key}" var="entry">
    ${entry}
    ${entry.key}
    ${entry.value}
</c:forEach>
```

这个部分其实还有很多的内容，但是考虑到代码脚本也可以轻松完成，而且JSTL伴随着JSP慢慢减少使用，我不想在这个部分浪费太多时间
