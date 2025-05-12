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

客户端拥有Cookie后每次请求都发送给服务器



### 创建Cookie

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

实现createCookie方法

```java
@WebServlet("/CookieServlet")
public class Servlet01 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
        String action = req.getParameter("action"); // 获取 action 参数
        if ("createCookie".equals(action)) {
            createCookie(req, resp); // 调用 createCookie 方法
        } else {
            System.out.println("doGet (default)");
            resp.getWriter().write("Default GET request");
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost");
    }

    protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie cookie = new Cookie("name", "value");
        resp.addCookie(cookie);
        System.out.println("Cookie created");
        resp.getWriter().write("Cookie created");
    }
}
```

