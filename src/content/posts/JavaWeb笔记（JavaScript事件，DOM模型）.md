---
title: JavaWeb笔记（JavaScript）
published: 2025-04-26
updated: 2025-04-26
description: 'JavaScript事件，DOM模型'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 
---

# JavaWeb笔记



## JavaScript事件

### 事件定义

事件是电脑输入设备与页面进行交互的响应，称之为事件，如点击滑动，长按等，一下是常用的事件

onload 加载完成事件（页面代码初始化）

onclick 点击事件（按钮点击响应操作）

onblur 失去焦点事件（用于输入框去焦点后验证输入内容是否合法）

onchange 内容发送改变事件（常用于下拉列表和输入框内容发送改变后）

onsubmit 表单提交事件（常用于表单提交后验证表单合法性）



### 事件注册

定义：又称为绑定，告诉浏览器，事件响应后需要执行的操作代码，又分为静态注册和动态注册

静态注册：通过html标签的时间属性直接赋予事件响应后的代码

动态注册：先通过js代码得到dom对象，再通过dom对象.事件名称=function(){}这种形式赋予事件响应后的代码。其基本的步骤为  1.获取标签对象  2.标签对象.事件名称 = function() {}



### 常用事件

#### onload

表单初始化事件

静态注册示例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function onLoadFun() {
            alert('静态注册');
        }
    </script>
</head>
<body onload="onLoadFun();">

</body>
</html>
```

动态注册实例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        window.onload = function() {
            alert("动态注册");
        }
    </script>
</head>
<body>

</body>
</html>
```



#### onclick

按钮1为静态注册

按钮2为动态注册：获取标签对象，通过标签对象.事件名称 = function(){}

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function onclickFun() {
            alert("Hello World");
        }

        window.onload = function() {
            var obj = document.getElementById("Button");
            obj.onclick = onclickFun;
        }
    </script>
</head>
<body>
    <button onclick="onclickFun()">按钮1</button>
    <button id="Button">按钮2</button>
</body>
</html>
```



#### onblur

失去焦点：常用于输入框

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function funOnblur(){
            //console是控制台对象，用于向浏览器控制台打印
            console.log("静态注册失去焦点事件");
        }
        window.onload = function() {
            var obj = document.getElementById("password");
            obj.onblur = function(){
                console.log("动态注册失去焦点事件");
            }
        }
    </script>
</head>
<body>
用户名：<input type="text" onblur="funOnblur();"><br/>
密 码：<input id="password" type="text"><br/>
</body>
</html>
```



#### onchange

内容改变：常用于下拉列表，和输入框

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function funOnChange(){
            //console是控制台对象，用于向浏览器控制台打印
            console.log("静态注册内容发生更改");
        }
        window.onload = function() {
            var obj = document.getElementById("password");
            obj.onchange = function(){
                console.log("动态注册内容发生更改");
            }
        }
    </script>
</head>
<body>
用户名：<input type="text" onchange="funOnChange();"><br/>
密 码：<input id="password" type="text"><br/>
</body>
</html>
```



#### onsubmit

表单提交事件：用于验证表单项是否合法

注意当return false的时候不提交，其中在函数中可以加入自己的判断逻辑

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function funOnSubmit(){
            alert("表单提交不合法")
            return false;
        }
        window.onload = function() {
            var obj = document.getElementById("submit");
            obj.onsubmit = function(){
                alert("表单提交不合法")
                return false;
            }
        }
    </script>
</head>
<body>
    <form method="get" action="https://www.baidu.com"  onsubmit="return funOnSubmit();">
        用户名：<input type="text"><br/>
        密 码：<input type="text"><br/>
        <input type="submit" value="静态注册"/><br/>
    </form>
    <form method="get" action="https://www.baidu.com" id="submit">
        用户名：<input type="text"><br/>
        密 码：<input type="text"><br/>
        <input type="submit" value="静态注册"/>
    </form>
</body>
</html>
```



## DOM模型

Document Object Model文档对象模型

将文档中的标签，属性，文本，转化为对象来管理



### Document对象

一个html文档被Document对象存储，并交由Document对象管理

1.Document管理了所有的HTML文档内容

2.Document是一种树结构的文档，有层级关系

3.Document使得所有的标签都对象化

4.我们可以通过Document访问所有的标签对象



### 入门案例

验证输入框中内容是否符合某一个形式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function onclickFunction() {
            var userObj = document.getElementById("username");//获取输入框对象
            var value = userObj.value;//获取对象（文本框）数据
            //运用正则表达式判断数据是否合法
            var patt = /^\w{5,12}$/;
            if (!patt.test(value)) {
                Warning.innerHTML = "不合法"
            } else {
                Warning.innerHTML = "合法"
            }
        }
    </script>
</head>
<body>
        用户名：<input type="text" id="username">
        <span id="Warning" style="color: red"></span>
        <button onclick="onclickFunction();">校验</button>

</body>
</html>
```



### 正则表达式

以上案例中我们使用到了正则表达式，正则表达式可以为我们判断字符串是否满足某一个模板要求

```html
<script type="text/javascript">
    var patt = new RegExp("e");//判断是否含有e
    var str = "still";
    alert(patt.test(str));
</script>
```

```html
<script type="text/javascript">
    var patt = /e/;//判断是否含有e
    var str = "still";
    alert(patt.test(str));
</script>
```

以下是一些常见的正则表达式，我觉得在这里随用随取即可

邮箱验证

```java
/^[^\s@]+@[^\s@]+\.[^\s@]+$/
```

手机号验证

```
/^1[3-9]\d{9}$/
```

身份证号码验证

```
/^[1-9]\d{5}(18|19|20)\d{2}(0[1-9]|1[0-2])(0[1-9]|[12]\d|3[01])\d{3}[\dXx]$/
```

URL验证

```
/^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/
```

密码验证：包含数字字母至少八位

```
/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/
```

复杂密码（至少1个大写字母、1个小写字母、1个数字和1个特殊字符，至少8位）

```
/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/
```



### Document相关方法

#### getElementById

通过标签id获取标签对象，注意只获取第一个符合条件的标签对象

在上面有演示这里不再过多说明



#### getElementsByName

通过标签名称获取标签对象，注意返回所有的标签对象，是一个集合

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function onclickAll() {
            var hobbies = document.getElementsByName("hobby");
            for (var i = 0; i < hobbies.length; i++) {
                hobbies[i].checked = true;
            }
        }
    </script>
</head>
<body>
    <input type="checkbox" name="hobby" value="C++">C++
    <input type="checkbox" name="hobby" value="Java">Java
    <input type="checkbox" name="hobby" value="Python">Python<br/>
    <button onclick="onclickAll();">ALL</button>
</body>
</html>
```



#### getElementsByTagName

按照标签名获取多个标签对象，返回集合

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        function onclickAll() {
            var hobbies = document.getElementsByTagName("input");
            for (var i = 0; i < hobbies.length; i++) {
                hobbies[i].checked = true;
            }
        }
    </script>
</head>
<body>
    <input type="checkbox" value="C++">C++
    <input type="checkbox" value="Java">Java
    <input type="checkbox" value="Python">Python<br/>
    <button onclick="onclickAll();">ALL</button>
</body>
</html>
```

#### 注意事项

1.以上的三个方法范围从小到大，在实际使用的时候尽量从上到下使用（id，name，Tag）

2.以上方法需要在页面加载完毕后才可以使用，从上到下依次执行，在没有body加载的时候是没有办法获取得到没有创建的id的



### 节点

节点就是标签对象（狭义上讲，程序员可以操作的部分），其中有一些方法供我们使用

以下是其常用方法和属性



#### 方法

getElementsByTagName：获取当前节点的指定标签名孩子节点

appendChild：添加一个子节点，参数是要添加的孩子节点



createElement

创建标签对象，再在body中添加子节点标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript</title>
    <script type="text/javascript">
        window.onload = function () {
            var divObj = document.createElement("div");
            divObj.innerHTML = "Hello World";
            document.body.appendChild(divObj);//通过body对象（自动创建调用方法，添加子节点）
        }
    </script>
</head>
<body>

</body>
</html>
```

由于文本也算是节点对象，我们还可以通过如下的方式加入节点内容

```html
<script type="text/javascript">
    window.onload = function () {
        var divObj = document.createElement("div");
        var text = document.createTextNode("Hello World");
        divObj.appendChild(text);
        document.body.appendChild(divObj);
    }
</script>
```

#### 属性

childNodes：当前节点的所有子节点

firstChild：当前节点的第一个子节点

lastChild：当前节点的最后一个子节点

parentNode：父节点

nextSibling：下一个节点

previousSibling：上一个节点

className：获取或设置class属性值

innerText：获取或设置起始标签和结束标签中的文本

