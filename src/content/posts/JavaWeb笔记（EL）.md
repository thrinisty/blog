---
title: JavaWeb笔记(EL)
published: 2025-05-11
updated: 2025-05-11
description: 'EL表达式'
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



### 域对象数据输出

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
    Person persion = new Person("Jack", phones, list, map);
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

