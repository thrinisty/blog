---
title: JavaWeb笔记(ajax，i18n)
published: 2025-05-15
updated: 2025-05-15
description: 'ajax，i18n'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 

---

# JavaWeb笔记

## AJAX

Asynchronous Javascript And XML异步JavaScript与XML

是一种创建交互式网页应用的网页开发技术，通过这样一种技术，我们可以在不刷新网页的情况下进行数据的局部更新，提高网页运行的效率

### 基本使用

我们通过如下的js代码在jsp中设置ajax访问的方法，通过click事件调用

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>AJAX</title>
    <script type="text/javascript" charset="utf-8">
        function ajaxRequest() {
            var xhr = new XMLHttpRequest();
            xhr.open("GET", "http://localhost:8080/AJAX/ajax", true);//设置请求，如果是false则进行同步阻塞，直到完成返回之后，再执行后续代码
            xhr.onreadystatechange = function() {
                if (xhr.readyState === 4) {
                    if (xhr.status === 200) {
                        var Person = JSON.parse(xhr.responseText);
                        var name = Person.name;
                        var age = Person.id;
                        alert(name + " age is " + age);
                    }
                }
            }
            xhr.send();//发送请求
        }
    </script>
</head>
<body>
    <button onclick="ajaxRequest()">ajax请求</button>
</body>
</html>
```

而ajax访问的对象是AJAX的Servlet程序，

```java
@WebServlet("/ajax")
public class AJAX extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Person person = new Person(1, "Jack");
        Gson gson = new Gson();
        String json = gson.toJson(person);
        System.out.println(json);
        resp.getWriter().write(json);
    }
}
```

如此可以在不刷新页面的情况下获取到访问数据并且显示出来



**AJAX特点说明**

局部更新：浏览器地址栏不会发生变化，局部更新不会舍弃页面的内容



### jQuery使用Ajax

客户端JSP编程在实际使用的时候，Ajax请求，和数据的处理有些复杂，我们一般不使用原生的ajax请求，而是使用jQuery提供的封装来完成Ajax请求上传以及Ajax数据的获取

#### ajax方法

url：表示请求的地址

type：表示请求的类型（post/get）

data：表示发送给服务器的数据

success：请求响应，响应的回调函数

dataType：响应的数据类型

如果是设置响应类型为text需要手动运用JSON进行解析，但是如果是设置为json则会自动解析

```html
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script type="text/javascript" charset="utf-8">
    $(function() {
       $("#ajax1").click(function() {
          $.ajax({
              url:"http://localhost:8080/AJAX/ajax",
              data:"action=test",
              type:'GET',
              success:function(data){
                  alert(data);
                  var person = JSON.parse(data);
                  $("#msg").html(person.id + " " + person.name);
              },
              dataType:'text' //常用的有xml，text，json
          });
       });
    });
</script>
```

当数据类型dataType为json的时候，不需要手动的对于data进行转化，程序自动完成解析，直接调用data.field即可获取到对应的属性

```java
$("#ajax1").click(function() {
   $.ajax({
       url:"http://localhost:8080/AJAX/ajax",
       data:"action=test",
       type:'GET',
       success:function(data){
           $("#msg").html(data.id + " " + data.name);
       },
       dataType:'json' //常用的有xml，text，json
   });
```



#### get和post方法

url ：请求的url地址

data：发送的数据

callback：发送成功的回调函数

type：返回的数据类型 

相比于ajax方法少了请求类型的参数

对于get请求调用$.get参数	post请求替换即可

```html
<script type="text/javascript" charset="utf-8">
    $(function () {
        $("#ajax1").click(function () {
            var url = "http://localhost:8080/AJAX/ajax";
            var data = "action=test";
            function callback(data) {
                $("#msg").html(data.id + " " + data.name);
            }
            var type = "json";
            $.get(url, data, callback, type);
        });
    });
</script>
```



#### getJSON方法

相比较于get以及post方法不需要传入type参数

```html
<script type="text/javascript" charset="utf-8">
    $(function () {
        $("#ajax1").click(function () {
            var url = "http://localhost:8080/AJAX/ajax";
            var data = "action=test";
            function callback(data) {
                $("#msg").html(data.id + " " + data.name);
            }
            $.getJSON(url, data, callback);
        });
    });
</script>
```



#### serialize方法

serialize可以把表单中所有的表单项内容获取到，以name=value&name=value的形式进行拼接，提交发送给服务器

```html
<script type="text/javascript" charset="utf-8">
    $(function () {
        $("#ajax1").click(function () {
            var url = "http://localhost:8080/AJAX/ajax";
            var data = "action=test";
            function callback(data) {
                $("#msg").html(data.id + " " + data.name);
            }
            var serialize = $("#form1").serialize();
            alert(serialize);
            $.getJSON(url, data + "&" + serialize, callback);
        });
    });
</script>
```

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Person person = new Person(1, "Jack");
    Gson gson = new Gson();
    String json = gson.toJson(person);
    System.out.println(json);
    System.out.println(req.getParameter("name"));
    resp.getWriter().write(json);
}
```



## i18n国际化

:::note

i18n国际化是指同一个网站可以支持多种不同的语言，以方便不同的国家，不同语种用户的访问，最简单的方式是制作不同语言的的网页，例如苹果公司就是这么做的

然而这样一种方式不适合所有的公司，我们想要一个相同的网站，而不同人访问的时候可以根据用户所在的地区显示不同的语言文字，而网站的布局样式等不发生改变，就产生了国际化（internationalization）简称i18n。

:::

### 国际化三要素

**Locale对象**

Locale表示不同的时区，位置，语言

zh_CN	中国，中文

en_US 	英文，美国



**Properties配置**

将这样一些配置的属性放在Properties属性配置文件

```
国际化配置文件的命名规则
baseName_locale.properties
例如中文：i18n_zh_CN.properties
例如中文：i18n_en_US.properties
```



### ResourceBundle资源包

读取Locale对象和Properties配置

ResourceBundle.getBundle：根据给定的baseName和Locale读取相应的配置信息，得到文字信息

ResourceBundle.getString：通过key得到不同国家的语言信息



### 基础实例

```java
public class I18N {
    public static void main(String[] args) {
        Locale locale = Locale.getDefault();
        System.out.println(locale);//zh_CN
    }
}
```



:::note

注意将配置文件存放在resources文件夹下

:::

中文配置文件i18n_zh_CN.properties

```properties
username=用户名
password=密码
sex=性别
age=年龄
```

英文配置文件i18n_en_US.properties

```properties
username=username
password=password
sex=sex
age=age
```

通过Locale对象创建的资源包对象，我们可以通过键的形式获取值

```java
public class I18N {
    public static void main(String[] args) {
        Locale us = Locale.US;//获取美国的Locale对象
        ResourceBundle bundle = ResourceBundle.getBundle("i18n", us);
        //通过指定的basename和Locale兑现得到Bundle对象
        System.out.println(bundle.getString("username"));
    }
}
```

:::warning

在IDEA的配置中默认使用的是**ISO-8859-1**编码格式编码配置文件，但是该格式不支持中文，我们需要将其设置为UTF-8，通过工具进行Unicode转义

```cmd
native2ascii -encoding UTF-8 i18n_zh_CN.properties i18n_zh_CN_encoded.properties
```

替换为生成的配置文件，即可正常使用

:::

```java
public class I18N {
    public static void main(String[] args) {
        Locale us = Locale.CHINA;
        ResourceBundle bundle = ResourceBundle.getBundle("i18n", us);
        //通过指定的basename和Locale兑现得到Bundle对象
        System.out.println(bundle.getString("username"));//姓名
    }
}
```



### 请求头实现国际化

Accept-Language请求头可以通过浏览器设置各个语言的权重，JSP页面也可以通过这一些请求头调对应的资源包，加载对应的语言信息

```html
<body>
    <%
        Locale locale = Locale.getDefault();
        ResourceBundle i18n = ResourceBundle.getBundle("i18n", locale);
    %>
    <form action="/login" method="post">
        <%=i18n.getString("username")%><input type="text" name="username"><br/>
        <%=i18n.getString("password")%><input type="password" name="password"><br/>
        <%=i18n.getString("age")%><input type="password" name="password"><br/>
        <%=i18n.getString("sex")%><input type="password" name="password"><br/>
        <input type="submit">
    </form>
</body>
```



### 选择语言类型实现

通过超链接标签访问自身jsp页面并附带参数信息，按照获取到的信息设置locale对象，加载对应语言资源包

```html
<body>
    <%
        Locale locale = null;
        String country = request.getParameter("country");
        if("cn".equals(country)) {
            locale = Locale.CHINA;
        } else if ("usa".equals(country)) {
            locale = Locale.US;
        } else {
            locale = Locale.US;
        }
        ResourceBundle i18n = ResourceBundle.getBundle("i18n", locale);
    %>
    <a href="login.jsp?country=cn">中文</a>
    <a href="login.jsp?country=usa">english</a>
    <form action="/login" method="post">
        <%=i18n.getString("username")%><input type="text" name="username"><br/>
        <%=i18n.getString("password")%><input type="password" name="password"><br/>
        <%=i18n.getString("age")%><input type="text" name="age"><br/>
        <%=i18n.getString("sex")%><input type="text" name="sex"><br/>
        <input type="submit">
    </form>
</body>
```



### JSTL标签库实现

:::note

1.使用标签设置locale信息

2.使用标签设置baseName

3.使用标签输出国际化信息

:::

```html
<body>
    <fmt:setLocale value="${param.locale}"/>
    <fmt:setBundle basename="i18n"/>
    <a href="login.jsp?locale=zh_CN">中文</a>
    <a href="login.jsp?locale=en_US">english</a>
    <form action="/login" method="post">
        <fmt:message key="username"/><input type="text" name="username"><br/>
        <fmt:message key="password"/><input type="password" name="password"><br/>
        <fmt:message key="age"/><input type="text" name="age"><br/>
        <fmt:message key="sex"/><input type="text" name="sex"><br/>
        <input type="submit">
    </form>
</body>
```

:::warning

JavaWeb完结撒花！！！

:::
