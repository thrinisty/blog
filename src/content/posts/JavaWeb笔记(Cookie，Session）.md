---
title: JavaWeb笔记(Cookie，Session)
published: 2025-05-13
updated: 2025-05-13
description: 'Cookie，Session'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 

---

# JavaWeb笔记

## Cookie

Cookie大小不超过4KB

Cookie是服务器通知客户端保存键值对的一种技术

客户端拥有Cookie后每次请求都发送Cookie信息给服务器



### Cookie使用

通过请求连接访问createCookie方法

```html
<body>
<div>
    <a href="http://localhost:8080/Cookie/CookieServlet?action=createCookie"
       target="_blank"
       style="text-decoration: none; color: blue;">
        Cookie创建
    </a>
</div>
</body>
```

Servlet get方法逻辑重写

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.setContentType("text/html;charset=UTF-8");
    System.out.println("doGet");
    String action = req.getParameter("action"); // 获取 action 参数
    if ("createCookie".equals(action)) {
        createCookie(req, resp); // 调用 createCookie 方法
    } else if ("getCookie".equals(action)) {
        getCookie(req, resp);

    } else if ("updateCookie".equals(action)) {
        updateCookie(req, resp);
    }else {
        System.out.println("doGet (default)");
        resp.getWriter().write("Default GET request");
    }
}
```



### 方法实现

#### 实现createCookie方法

```java
protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie1 = new Cookie("name1", "value");
    resp.addCookie(cookie1);//通知客户端保存Cookie
    Cookie cookie2 = new Cookie("name2", "value");
    resp.addCookie(cookie2);//通知客户端保存Cookie
}
```

请求头中得到的Cookie

```
cookie
name=value; JSESSIONID=4F532626BFB4F9BD1E10FB5AD9B48B8B; name1=value; name2=value; Idea-edca08ae=bf13885b-ced0-462f-82b1-c1e0b2a26978
```



#### 服务器获取Cookie对象

```java
protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        resp.getWriter().write(cookie.getName() + ":" + cookie.getValue() +"<br/>");
    }
}
```

```
name1:value
name2:value
JSESSIONID:A8B31B0AD41D5E89238669FF5CA83DB6
Idea-edca08ae:bf13885b-ced0-462f-82b1-c1e0b2a26978
```

我们在方法中还可以用逻辑判断取出我们想要的cookie使用

```java
for (Cookie cookie : cookies) {
    if(cookie.getName().equals("name1")) {
        System.out.println("Cookie name1 found");
        target = cookie;
    }
}
if (target != null) {
    System.out.println("Cookie function");
    resp.getWriter().write(target.getName() + ":" + target.getValue() + "<br/>");
}
```

:::tip

为了方便可以构造一个Cookie工具类，使用起来会方便一些

:::

```java
public class CookieUtils {
    public static Cookie findCookie(Cookie[] cookies, String name) {
        if(cookies != null || cookies.length == 0 || name != null) {
            for (Cookie cookie : cookies) {
                if(cookie.getName().equals(name)) {
                    return cookie;
                }
            }
        }
        return null;
    }
}
```

```java
protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    Cookie target = CookieUtils.findCookie(cookies, "name1");
    if (target != null) {
        System.out.println("Cookie function");
        resp.getWriter().write(target.getName() + ":" + target.getValue() + "<br/>");
    }
}
```



#### Cookie值修改

方案一

1.创建一个要修改的同名Cookie对象

2.在构造器，同时赋予新的Cookie值

3.调用response.addCookie(Cookie)

```java
protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("name1", "newValue");
    resp.addCookie(cookie);
    resp.getWriter().write("修改cookie完成 <br/>");
}
```

方案二

1.查找到需要修改的Cookie对象

2.调用setValue()方法赋予新值

3.调用resp.addCookie(cookie)保存修改

```java
protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    Cookie target = CookieUtils.findCookie(cookies, "name1");
    if (target != null) {
        target.setValue("newValue");
        resp.addCookie(target);
        resp.getWriter().write("修改cookie完成 <br/>");
    }
}
```

:::warning

Cookie中对应的值不可以包含空格 方括号 圆括号 逗号 等号 双引号 斜杠 问好 冒号 分号 空值 中文，如果需要使用则要使用Base64编码处理

:::



#### Cookie生命控制

Cookie的生命控制指的是如何管理Cookie什么时候被销毁

setMaxAge()

正数，表示指定秒数后过期

负数，表示浏览器关闭的时候Cookie被删除（默认为-1）

零，表示马上删除Cookie

创建一个10秒有效的Cookie

```java
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("name", "default");
    cookie.setMaxAge(10);
    resp.addCookie(cookie);
}
```

删除对应的Cookie

```java
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    Cookie target = CookieUtils.findCookie(cookies, "name1");
    if (target != null) {
        target.setMaxAge(0);
        resp.addCookie(target);
        System.out.println("目标Cookie已被删除");
    }
}
```
