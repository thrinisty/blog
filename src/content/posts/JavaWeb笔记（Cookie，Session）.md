---
title: JavaWeb笔记（Cookie，Session）
published: 2025-05-13
updated: 2025-05-13
description: 'Cookie，Session会话'
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
<%--
  Created by IntelliJ IDEA.
  User: 71460
  Date: 2025/5/12
  Time: 20:52
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Cookie&Session</title>
</head>
<body>
<div>
    <a href="http://localhost:8080/Cookie/CookieServlet?action=createCookie"
       target="_blank"
       style="text-decoration: none; color: blue;">
        Cookie创建
    </a>
    <a href="http://localhost:8080/Cookie/CookieServlet?action=getCookie"
       target="_blank"
       style="text-decoration: none; color: blue;">
        Cookie获取
    </a>
    <a href="http://localhost:8080/Cookie/CookieServlet?action=updateCookie"
       target="_blank"
       style="text-decoration: none; color: blue;">
        Cookie值修改
    </a>
    <a href="http://localhost:8080/Cookie/CookieServlet?action=defaultLife"
       target="_blank"
       style="text-decoration: none; color: blue;">
        Cookie生命期
    </a>
    <a href="http://localhost:8080/Cookie/CookieServlet?action=testPath"
       target="_blank"
       style="text-decoration: none; color: blue;">
        CookiePath测试
    </a>
</div>
</body>
</html>
```

Servlet get方法逻辑重写，通过action提交信息，来判断请求的功能

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
    } else if ("defaultLife".equals(action)) {
        defaultLife(req, resp);
    } else if ("testPath".equals(action)) {
        testPath(req, resp);
    } else {
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



#### 设置path属性

Cookie的path属性可以有效地过滤哪些Cookie可以发送给服务器，哪一些不发送，path属性是通过请求的地址来进行有效的过滤，例如有如下的两个Cookie

CookieA  	path=/工程路径

CookieB  	path=/工程路径/doc

请求地址为：http://localhost:8080/工程路径/a.html

这个时候发送CookieA，而CookieB不发送

请求地址为：http://localhost:8080/工程路径/abc/a.html

这个时候发送CookieA，CookieB都发送（只看是否包含path属性）



我们可以手动设置path属性

```java
protected void testPath(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie1 = new Cookie("path1", "value1");
    cookie1.setPath(req.getContextPath() + "/abc");
    resp.addCookie(cookie1);
    System.out.println("创建带有path路径的Cookie1");
    Cookie cookie2 = new Cookie("path2", "value2");
    resp.addCookie(cookie2);
    System.out.println("创建不有path路径的Cookie2");
}
```

当网址中没有路径/abc路径的时候不会使客户端获取到cookie1

我们新建一个在 http://localhost:8080/Cookie/abc/testPath.jsp 的jsp文件，发现这个页面可以获取到cookie1

:::tip

通过设置Cookie的path属性，我们可以实现一些功能

1. **隔离不同路径的Cookie**
   - 例如，网站有两个独立功能模块：
     - `/admin` 路径下的管理后台
     - `/user` 路径下的用户中心
       通过设置 `path=/admin` 和 `path=/user`，可防止两个模块的Cookie互相干扰，提升安全性。
2. **避免资源请求携带无关Cookie**
   - 静态资源（如图片、CSS）通常存放在 `/static` 路径。若Cookie的 `path` 不包含此路径，浏览器请求资源时不会附带这些Cookie，减少网络开销。
3. **多应用共享域名时的隔离**
   - 同一域名下部署多个应用（如 `example.com/app1` 和 `example.com/app2`），通过不同 `path` 隔离各自的会话Cookie，防止数据冲突。
4. **精细化权限控制**
   - 敏感路径（如 `/settings`）的Cookie可限制仅在该路径下发送，降低信息泄露风险。

:::



### 综合使用

用户免密登录

在我们日常访问各大网站的时候，我们在登陆过一次后，往往之后就不需要使用账号密码再次登录，我们可以使用Cookie实现

在LoginServlet程序中我们使用如下的流程实现

1.获取用户名和密码

2.判断用户名和密码是否正确

正确允许登录，错误不允许登录

如果正确将用户名和密码保存为Cookie发送给客户端



jsp客户端，将输入值默认输入cookie值

```html
<%--
  Created by IntelliJ IDEA.
  User: 71460
  Date: 2025/5/13
  Time: 16:51
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <form action="http://localhost:8080/Cookie/login" method="get">
        用户名称<input type="text" name="username" value="${cookie.username.value}"><br/>
        密码<input type="password" name="password" value="${cookie.password.value}"><br/>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

Servlet登录程序，当密码正确时账号密码存入Cookie，并重定向到对应页面，失败时，重写定位到登录界面

```java
@WebServlet("/login")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        if(username.equals("lory") && password.equals("123456")){
            Cookie cookie1 = new Cookie("username", username);
            cookie1.setMaxAge(60*60*24);
            resp.addCookie(cookie1);
            Cookie cookie2 = new Cookie("password", password);
            cookie2.setMaxAge(60*60*24);
            resp.addCookie(cookie2);
            System.out.println("Login Success");
            resp.sendRedirect("index.jsp");
        } else {
            System.out.println("Invalid username or password");
            resp.sendRedirect("login.jsp");
        }
    }
}
```



## Session

:::note

什么是**Session**

1. Session是会话，用于维护一个客户端和服务器之间关联的一种技术
2. Session是一个接口（HttpSession）
3. 每个客户端都有自己的Session会话
4. Session会话中经常用于保存用户登录之后的信息

:::

### 创建获取Session对象

#### 调用方法

创建和获取Session都是调用同一个API获取

```java
request.getSession()
```

第一次创建，第二次以及之后获取之前创建的Session



那么如何判断Session对象是创建出的还是获取的呢？

我们通过如下的方法获取

```java
session.isNew()
```

true代表初次创建的，而false代表是获取到的Session



每个会话都会有一个身份证号，即ID，这个ID是唯一的

```java
session.getId()
```



#### 代码实现

通过createSession获取到session对象

```java
protected void createSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    HttpSession session = req.getSession();
    System.out.println(session.isNew());
    System.out.println(session.getId());
}
```

```
false
E40645D73ABF4618B37B7FF0363A6E1C
```



### Session域对象存取数据

```java
protected void setAndGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    HttpSession session = req.getSession();
    session.setAttribute("key1", "value1");
    session.setAttribute("key2", "value2");
    Object key1 = session.getAttribute("key1");
    Object key2 = session.getAttribute("key2");
    System.out.println("key1: " + key1
    + " key2: " + key2);
}
```



### Session生命周期

session超时指的是客户端两次请求间隔的最大时长（负数不会超时）

setMaxnactiveInterval方法：设置Session的超时时间，超过指定的时长Session就会被销毁

getMaxnactiveInterval方法：获取Session的超时时间

```java
protected void SessionLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        session.getMaxInactiveInterval();//默认为1800s
        session.setMaxInactiveInterval(60*60);
        session.getMaxInactiveInterval();
    }
```

默认的超时时间长短可以在Tomcat的配置中进行修改

invalidate方法：使得Session被销毁

:::note

服务器在接收客户端的请求（无Cookie的情况下），会创建一个Session对象，并创建一个Cookie对象，装入一个JSESSIONID:sessionId键值对，最后将Cookie对象返回给服务器，此后每一次客户端在请求服务器的时候都会附带的Cookie中始终有Session的Id标识。

在需要Session会话的时候，服务器会通过Cookie的SessionId找到创建完成的Session对象返回使用。

所以当将对于客户端的Cookie的JSESSIONID键删除的情况下，即使服务器有客户端的Session对象，它任然会创建出一个新的Session对象

:::
