---
title: JavaWeb笔记
published: 2025-05-12
updated: 2025-05-12
description: 'JavaWeb文件传输，Session'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 

---

# JavaWeb笔记



## 文件传输

文件上传和下载是非常常见的功能，很多的系统中或者软件中都经常使用文件的上传和下载

### 文件上传

1.要有一个form标签，method=post请求

2.form标签的encType属性必须为multipart/form-data

3.在form标签中使用input type=file添加上传的文件

4.使用服务器代码接收上传的文件

以下是一个代码示例



1-3：前端显示界面完成上传功能

```jsp
<body>
    <form action="" method="post" enctype="multipart/form-data">
        用户名称：<input type="text" name="username"><br/>
        头像：<input type="file" name="password"><br/>
        <input type="submit" value="上传">
    </form>
</body>
```

4.服务器处理上传图片的代码

```java
@WebServlet("/photoUpload")
public class PhotoServer extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        System.out.println("图片上传处理");
    }
}
```
