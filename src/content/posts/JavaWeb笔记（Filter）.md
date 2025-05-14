---
title: JavaWeb笔记(Filter，json，ajax，i18n)
published: 2025-05-14
updated: 2025-05-14
description: 'Filter，json，ajax，i18n'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 

---

# JavaWeb笔记

## Filter

:::note

- 过滤器：是JavaWeb的三大组件之一（Servlet，Listener，Filter）
- 过滤器是JavaEE的规范，也就是接口
- Filter过滤器作用是：拦截请求，过滤响应

:::

### 基本使用

#### JSP页面实现

要求web工程下有一个admin目录，这个admin目录下的所有资源都要求登录后才可以使用

根据之前的情况分析，我们需要将登陆的信息存储在Cookie中，如果其中的键user值为null，则没有登陆，跳转至登录页面，而又user信息，则跳转至目标资源

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Index</title>
</head>
<body>
<%
    Object user = session.getAttribute("user");
    if (user == null) {
        request.getRequestDispatcher("/login.jsp").forward(request,response);
        return;
    }
%>
</body>
</html>
```

但是这样一种方式是有局限性的，没有办法跳到图片，html页面，而且当直接用对应的路径访问也可以访问到权限资源，我们这个时候就可以使用Filter过滤器来完成



#### Filter实现

:::note

使用步骤：

1.我们创建一个继承于import javax.servlet.Filter的过滤器类

2.实现拦截方法doFilter

3.配置拦截路径

:::

```java
package com.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebFilter("/admin/*")//配置拦截路径
public class AdminFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpSession session = request.getSession();
        Object user = session.getAttribute("user");
        if (user == null) {
            servletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest, servletResponse);
            return;
        } else {
            //让程序继续向下访问目标资源
            filterChain.doFilter(servletRequest, servletResponse);
        }
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }
}
```

这样做就会拦截Session中user为空的客户端访问

我们成功登录的逻辑通过Servlet完成，当成功将session域的user设置对应的用户名，当Session的user非空即可访问被拦截限制的资源

登录JSP界面

```html
<form action="http://localhost:8080/Fliter/login" method="get">
    用户名称<input type="text" name="username" value="${cookie.username.value}"><br/>
    密码<input type="password" name="password" value="${cookie.password.value}"><br/>
    <input type="submit" value="Submit">
</form>
```

登录Servlet

```java
@WebServlet("/login")
public class Login extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
        resp.setContentType("text/html;charset=UTF-8");
        HttpSession session = req.getSession();
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        if(username.equals("admin") && password.equals("123456")){
            System.out.println("login success");
            session.setAttribute("user", username);
            resp.getWriter().write("<h1>Login Success</h1>");
        } else {
            System.out.println("login failed");
            req.getRequestDispatcher("/login.jsp").forward(req, resp);
        }
    }
}
```



### 生命周期

Filter的生命周期包含几个方法：1.构造器方法 2.nit初始化方法  3.doFilter方法  4.destory销毁方法

在Web工程启动的时候，1、2两部就已经被执行（Filter被创建）

当在访问被拦截资源的时候的时候，调用3方法（每次）

当停止Web工程的时候执行



### FilterConfig类

是Filter过滤器的配置文件类

Tomcat每次创建Filter的时候，同时也会创建一个FilterConfig类，包含了Filter配置文件的配置信息

:::note

作用：获取filter过滤器的配置内容

1.获取Filter的名称 filter-name内容

2.获取Filter中配置init-param初始化参数

3.获取ServletContext对象

:::

运用注解设置过滤器的名称初始化参数的设置

```java
@WebFilter(
        filterName = "myFilter",
        urlPatterns = {"/admin/*"},
        initParams = {
                @WebInitParam(name = "param1", value = "value1"),
                @WebInitParam(name = "param2", value = "value2")
        }
)
```



获取过滤器名称以及初始化参数

```java
public void init(FilterConfig filterConfig) throws ServletException {
    System.out.println(filterConfig.getFilterName());//myFilter
    System.out.println(filterConfig.getInitParameter("param1"));//value1
    System.out.println(filterConfig.getInitParameter("param2"));//value2
    Filter.super.init(filterConfig);
}
```



### Filter拦截路径

:::note

有三种匹配方式，Filter只关心请求的地址是否匹配，不关心资源存在

:::

#### 精确匹配

请求地址必须为 http://localhost:8080/path/admin/1.jsp

```
"/admin/1.jsp"
```

#### 目录匹配

对应目录下的所有资源

```
"/admin/*"
```

#### 后缀名匹配

请求地址必须以.html结尾

```
"*.html"
```



### FilterChain过滤器链

FilterChain：多个过滤器如何一起工作

![152](../images/152.png)



## JSON

:::note

JSON是一种轻量级的数据交换格式（和XML比较），易于人的阅读，同时易于机器的读写，采用的是完全独立于语言的文本格式，被多种语言支持，成为了理想的数据交换语言（服务器与客户端）

:::



### 基本使用

JSON是由键值对组成，并由大括号包围，每个键由“”引起，键值之间使用冒号分隔，多组键值对之间使用逗号进行分割

```html
<script type="text/javascript">
    var jsonObj = {
        "key1": 12,
        "key2": "value2",
        "key3": true,
        "key4": [11,33,"arr",false],
        "key5": {
            "key1": "value1",
            "key2": "value2",
        },
    };
</script>
```

值可以是各种类型，甚至可以是json类型本身，以及json数组

其中json其实是一个object对象，json中的key可以理解为是对象中的一个属性，json中的key访问就和访问对象的属性一样，jsonObj.key

```html
alert(jsonObj.key1);
alert(jsonObj.key2);
alert(jsonObj.key3);
alert(jsonObj.key4[1]);
alert(jsonObj.key5.key1);
```

访问和对象基本没什么不同



### 常用方法

:::note

json的存在有两种形式

1.对象的形式，json对象（需要操作的时候使用）

2.字符串的形式存在，称为json字符串（在数据交换的时候使用）

这两种格式可以相互转换

:::

JSON.stringify()：将json对象转换为json字符串（类似toString）

JSON.parse()：把json字符串转为json对象

```html
var stringify = JSON.stringify(jsonObj);
alert(stringify);
```

string输出结果

```json
{"key1":12,"key2":"value2","key3":true,"key4":[11,33,"arr",false],"key5":{"key1":"value1","key2":"value2"}}
```

json输出结果

```java
var json = JSON.parse(stringify);
alert(json);//[object Object]
```



### Java使用JSON

我们在Java中使用JSON之前需要先导入Googel的Gson库，使用Maven进行导入

```html
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>
```

测试使用

建立一个Person对象作为Bean

```java
public class Person {
    private String name;
    private int age;

    public Person() {}
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



#### 普通类转化

toJson方法可以将Bean对象转为字符串

fromJson方法传入对应的json字符串以及类对象可以转为类对象的实例

```java
public class JsonTest {
    public static void main(String[] args) {
        Person person = new Person("Lory", 18);
        Gson gson = new Gson();//创建对象实例
        String json = gson.toJson(person);
        System.out.println(json);//{"name":"Lory","age":18}
        Person another = gson.fromJson(json, Person.class);//放入转为的类型
        System.out.println(another.getAge() + " " + another.getName());//18 Lory
    }
}
```



#### List类集合转化

对于LIst集合而言，我们也可以将其通过转化toJson方法转化为Json字符串，而将Json字符串转换为对应类型则较为复杂，需要在使用fromJson转化前，实现一个继承于TypeToken类的一个格式类，其中的泛型传入<List<Person>>（或者<ArrayList<Person>>），再将这个类型zhuanwei

```java
public class JsonTest {
    public static void main(String[] args) {
        List<Person> persons = new ArrayList<>();
        Person person1 = new Person("Lory", 18);
        Person person2 = new Person("Marry", 22);
        persons.add(person1);
        persons.add(person2);
        Gson gson = new Gson();
        String json = gson.toJson(persons);
        System.out.println(json);//[{"name":"Lory","age":18},{"name":"Marry","age":22}]

        List<Person> list = gson.fromJson(json, new PersionListType().getType());
        System.out.println(list);
    }
}

class PersionListType extends TypeToken<List<Person>> {
}
```



#### Map类集合转化

```java
public class JsonTest {
    public static void main(String[] args) {
        Map<String, Person> persons = new HashMap<>();
        Person person1 = new Person("Lory", 18);
        Person person2 = new Person("Marry", 22);
        persons.put("num1", person1);
        persons.put("num2", person2);
        System.out.println(persons);//{num1=Person{name='Lory', age=18}, num2=Person{name='Marry', age=22}}
        Gson gson = new Gson();
        String json = gson.toJson(persons);
        System.out.println(json);//{"num1":{"name":"Lory","age":18},"num2":{"name":"Marry","age":22}}
        Map<String, Person> map = gson.fromJson(json, new PersionMapType().getType());
        System.out.println(map.get("num1"));//Person{name='Lory', age=18}
    }
}

class PersionMapType extends TypeToken<Map<String, Person>> {
}
```

这里其实更推荐使用内部类，更加简洁一点
