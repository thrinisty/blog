---
title: JavaWeb笔记（文件上传）
published: 2025-05-12
updated: 2025-05-12
description: 'JavaWeb文件传输'
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



#### 实现方式

1-3：前端显示界面完成上传功能

```html
<body>
    <form action="" method="post" enctype="multipart/form-data">
        用户名称：<input type="text" name="username"><br/>
        头像：<input type="file" name="photo"><br/>
        <input type="submit" value="上传">
    </form>
</body>
```

4.服务器处理上传图片的代码

:::note
使用如下的Tomcat中的虚拟机选项设置字符集

```
-Dfile.encoding=UTF-8
```

:::

```java
@WebServlet("/photoUpload")
public class PhotoServer extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("图片上传处理");
    }
}
```

这里暂时没有实现图片的Servlet处理，我们接下来通过Servlet API实现

:::warning

文件传输request中的数据以流的形式传输，需要使用流对象来接收，而且流对象中的内容包括了请求头部分和图片二进制数据部分，需要我们手动处理，这里建议直接调用Servlet API

:::

#### 相关工具包

在使用相关API之前，我们需要使用Maven管理工具导入相关包

```html
<!-- Apache Commons FileUpload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.5</version> <!-- 最新稳定版 -->
</dependency>

<!-- Apache Commons IO（FileUpload 依赖它） -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.11.0</version> <!-- 最新稳定版 -->
</dependency>
```

其中有一些常用类与方法

ServletFileUpLoad：用于解析上传的数据

ServletFileUpLoad静态方法isMultipartContent：用于判断当前上传的数据格式是否是多段的格式

parseRequest：返回FileItem类List集合，解析上传数据

FileItem类方法isFormField:判断表单项是普通表单还是上传文件类型的表单

FileItem类方法getFieldName：获取表单项的name属性

FileItem类方法getString：获取当前表单项的值

FileItem类方法getName：获取上传的文件名

FileItem类方法write：传入file参数，将上传的文件写入到参数file指向的空间



#### 文件处理实现

```java
@WebServlet("/photoUpload")
public class PhotoServer extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        System.out.println("图片上传处理");
        if(ServletFileUpload.isMultipartContent(req)) {
            //创建FileItemFactory工厂实现类
            FileItemFactory fileItemFactory = new DiskFileItemFactory();
            //创建用于上传数据的工具类ServletFileUpload
            ServletFileUpload upload = new ServletFileUpload(fileItemFactory);
            try {
                List<FileItem> list = upload.parseRequest(req);
                for (FileItem item : list) {
                    if (item.isFormField()) {
                        //普通表单项
                        System.out.println(item.getFieldName() + " : " + item.getString());
                    } else {
                        //文件处理
                        String filePath = "e:\\" + item.getName();
                        item.write(new File(filePath));
                        System.out.println("存储至路径" + filePath);
                    }
                }
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```



### 文件下载

1.获取要下载的文件名

2.读取要下载的文件内容（通过ServletContext获取）

3.把下载的文件内容回传给客户端

4.回传前通过响应头告诉客户端返回的数据类型

5.告诉客户端收到的数据是用于下载使用



#### 页面get请求

```html
<form action="http://localhost:8080/EL/Download" method="get" enctype="multipart/form-data">
    输入下载的文件名<input type="text" name="filename"><br/>
    <input type="submit" value="下载文件">
</form>
```



#### Servlet服务器处理

```java
@WebServlet("/Download")
public class Download extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String fileName = req.getParameter("filename");
        //String fileName = "2.jpg";
        ServletContext context = getServletContext();
        String mimeType = context.getMimeType("/file/" + fileName);

        resp.setContentType(mimeType);
        resp.setHeader("Content-Disposition", "attachment; filename=" + fileName);

        InputStream resourceAsStream = context.getResourceAsStream("/file/" + fileName);//获取输入流
        OutputStream out = resp.getOutputStream();//获取响应输出流
        IOUtils.copy(resourceAsStream, out);//复制数据给输出流，输出到客户端
    }
}
```

:::warning

当文件路径中含有中文的时候，没有办法正常识别文件对象，我们需要处理一下头部，将文件名称进行url编码（谷歌浏览器、Edge）

```java
resp.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode(fileName, "UTF-8"));
```

:::



:::warning

如果是火狐浏览器需要Base64编码解码，较为麻烦，需要手动的将文件名转化为字节数组（UTF-8），再将字节数组使用编码器实例编码，解码也一样，先将Base64编码解为字节数组，再使用String指定UTF-8编码格式将其转化为字符串

```java
resp.setHeader("Content-Disposition", "attachment; filename==?UTF-8?B?" + new BASE64Encoder().encode(fileName.getBytes(UTF-8)) + "?=");
```

:::



#### 进一步改进

可以通过request的请求头User-Agent对于客户端浏览器进行判断，再根据不同的分支进行响应的编码处理

```java
if(req.getHeader("User-Agent").contains("Chrome")) {
    resp.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode(fileName, "UTF-8"));
} else {
    resp.setHeader("Content-Disposition", "attachment; filename==?UTF-8?B?" + new BASE64Encoder().encode(fileName.getBytes("UTF-8")) + "?=");
}
```

但是在实际使用中，我们采取更加健壮的编码方式

```java
@WebServlet("/Download")
public class Download extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String fileName = req.getParameter("filename");
        ServletContext context = getServletContext();
        String mimeType = context.getMimeType("/file/" + fileName);

        resp.setContentType(mimeType);

        // 改进的文件名编码处理（兼容所有现代浏览器）
        String encodedFileName = getEncodedFileName(fileName);
        resp.setHeader("Content-Disposition", "attachment; " + encodedFileName);

        try (InputStream resourceAsStream = context.getResourceAsStream("/file/" + fileName)) {
            IOUtils.copy(resourceAsStream, resp.getOutputStream());
        }
    }

    private String getEncodedFileName(String fileName) throws UnsupportedEncodingException {
        // 1. 简单ASCII文件名（兼容旧浏览器）
        String asciiFallback = "filename=\"" + escapeAscii(fileName) + "\"";

        // 2. RFC 5987编码（现代浏览器优先使用这个）
        String rfc5987 = "filename*=UTF-8''" + encodeRFC5987(fileName);

        return asciiFallback + "; " + rfc5987;
    }

    private String escapeAscii(String fileName) {
        // 转义ASCII文件名中的特殊字符
        return fileName.replace("\"", "\\\"")
                .replace("\r", "")
                .replace("\n", "");
    }

    private String encodeRFC5987(String fileName) throws UnsupportedEncodingException {
        // 使用Java标准库进行RFC 5987编码
        return URLEncoder.encode(fileName, "UTF-8")
                .replace("+", "%20")
                .replace("*", "%2A")
                .replace("%7E", "~")
                .replace("%27", "'");
    }
}
```

今天课多，学的内容较少，但是额外抽出时间整理了一下网络编程第二个大作业的思路（JSP+Servlet+Bean实现图书管理系统），准备攒一个简易版本网络程序，就当综合使用下JavaWeb部分的知识

明天打算，进入Cookie和Session部分的学习
