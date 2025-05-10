---
title: JavaWeb笔记（jQuery）
published: 2025-04-27
updated: 2025-04-27
description: 'jQuery，XML'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 
---

# JavaWeb笔记

计划两天完成jQuery和XML部分的学习

## jQuery

定义：是JavaScript和Query查询的结合，是辅助JavaScript开发的js类库

核心思想：写的更少，做的更多，实现了很多浏览器的兼容问题

流行程度：流行程度高，约 **75% 的网站** 仍在使用 jQuery 进行 JavaScript 开发

优点：开源免费，语法便捷高效，简单易用（例如操作文档对象，选择DOM元素，制作动画效果，事件处理，使用Ajax等）



### 入门示例

如下是一个没有注册onclick事件的按钮，要求实现点击按钮输出Hello

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
</head>
<body><!--主体内容-->
    <button id="button">Say</button>
</body>
</html>
```

传统方式：通过获取标签对象绑定事件

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript">
        window.onload = function () {
            var Obj = document.getElementById("button");
            Obj.onclick = function () {
                alert("Hello");
            }
        }
    </script>
</head>
<body><!--主体内容-->
    <button id="button">Say</button>
</body>
</html>
```

通过jQuery实现

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {//表示onload事件
            var $button = $("#button");
            $button.click(function() {//绑定单击事件
                alert("Hello");
            });
        });
    </script>
</head>
<body><!--主体内容-->
    <button id="button">Say</button>
</body>
</html>
```

$是一个函数

对于框架而言jquery-3.7.1.min.js一般用于部署（快速，小），而jquery-3.7.1.js用于开发（可以方便看源码）

使用jQuery查询标签对象，使用标签对象.click绑定函数



### 核心函数

$是jQuery核心函数，可以完成jQuery很多功能

而$()就是在调用这个函数，根据传入的不同参数，实现不同的功能，以下是常见的使用方式

1.当传入函数的时候，功能相当于window.onload = function() {}，在文档加载完毕后自动调用函数

注意这里在加载的时间略微有区别，$内容在页面加载完毕的时候立马执行，但是原生的window.onload = function() {}还要等待标签显示内容显示完成后（例如来自网络中的图片）才会执行

```html
<script type="text/javascript">
    $(function() {alert("jQuery")});
</script>
```



2.当传入HTML字符串的时候，会对我们创建这个html标签对象

通过appendTo将字符串化为html标签对象添加到body，实现拼接，而非逐层添加

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {
           $("<div>"
               + "<span>div-span1</span>"
               + "<span>div-span2</span>"
               + "<div>").appendTo("body");
        });
    </script>
</head>
<body><!--主体内容-->
    <div>
        <span>div-span1</span>
        <span>div-span2</span>
    </div>
</body>
</html>
```



3.传入选择器字符串

```javascript
$("#id 属性值");
$("标签名");
$(".class 属性值");
```

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {
           var length = $("div").length;
           alert(length);
        });
    </script>
</head>
<body><!--主体内容-->
    <div>
        <span>div-span1</span>
        <span>div-span2</span>
    </div>
</body>
</html>
```



4.传入DOM对象，会将DOM对象转化为jQuery对象

```html
<script type="text/javascript">
    $(function() {
       var Obj = document.getElementById("test");
       $Obj = $(Obj);
       $Obj.click(function() {
          alert("test");
       });
    });
</script>
```

可以使用jQuery对象的方法，例如绑定单击

jQuery对象的本质其实是数组，其中存放的是DOM对象，可以通过访问数组的方式取出DOM对象

```javascript
$Obj = $(Obj);
var DOM = $Obj[0];
```



### jQuery选择器

#### 基础选择器

```
#ID
.class
element
*
selector1,selector2,selector3...
```

p.class表示要符合同时满足标签名和class名，使用上大体和css那部分相同



#### 层级选择器

取标签下的所有对应标签

```html
$("form input");
```

找到forn标签下的所有input标签



取标签下的所有子标签（不算孙子节点）

```
$("form>input");
```



取标签后面的对应标签（同一级别）

```
$("form+input");
```



取标签后面所有的对应标签（同一级别）

```
$("form~input");
```



#### 基本过滤选择器

first

筛选出集合中的第一个

```html
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
</ul>
```

```html
<script type="text/javascript">
    $(function() {
       alert($("li:first").text());
    });
</script>
```

查询到的结果是1，过滤出第一个li标签



not(selector)

除去集合中满足selector的元素

```html
<script type="text/javascript">
    $(function() {
       alert($("li:not(:first)").text());
    });
</script>
```

结果为234



even

匹配索引值位偶数的标签

```html
<script type="text/javascript">
    $(function() {
       alert($("li:even").text());
    });
</script>
```

结果为13

odd

匹配索引值位奇数的标签

```html
<script type="text/javascript">
    $(function() {
       alert($("li:odd").text());
    });
</script>
```

24



gt(index)

大于索引的标签

```html
<script type="text/javascript">
    $(function() {
       alert($("li:gt(0)").text());
    });
</script>
```

结果为234



lt(index)

小于索引的标签

```html
<script type="text/javascript">
    $(function() {
       alert($("li:lt(1)").text());
    });
</script>
```

结果为1



head：标题元素

animated：正在执行动画的元素

eq(Index)：等于对应索引的元素



#### 内容过滤器

contain(text)：匹配包含对应text的元素

empty：匹配不包含text的元素（为空）

has(selector)：匹配含有选择器匹配的元素的元素（外层元素）

parent：匹配含有子元素或者文本的元素（非空）



#### 属性过滤器

div[id]：含有id的div

div[id=123]：含有id为123的div

div[id!=123]：含有id不为123的div

div[id^=123]：含有id且id为123开头的div

div[id$=123]：含有id且id为123结尾的div

div[id*=123]：含有id且id包含123的div

复合选择器：

```javascript
input[id=123][name='hello'][class=.myclass]
```



#### 表单过滤器

input：匹配所有的input，extarea，select，button元素

以下都是其匹配对应表单的对应type的标签元素

```
text：
password：
radio：
checkbox：
submit：
image：
reset：
button：
file：
hidden：
```

enabled：匹配可用的元素

disabled：匹配不可用的元素（加上disabled="disabled"元素）

checked：匹配所有选中的checkbox元素

selected：匹配选中的option元素



#### 元素的筛选

大体上和Stream流处理的方式类似，在jQuery中其实就是将 :first  换了一种方式使用  .first()

```javascript
$(#123:fist)
$(#123).first();
```

如果你学过流式处理的话其实还是很熟悉的，这里就不再记录笔记了，等碰到不会的根据需求即查即用



### 属性操作

#### html

设置和获取其实标签和结束标签中的内容，和innerHTML一致

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {
            alert($("div").html());
        });
    </script>
</head>
<body><!--主体内容-->
    <div>div标签</div>
    <span>span标签</span>
</body>
</html>
```

```javascript
$(function() {
    $("div").html("<h1>重设值</h1>")
});
```

传入值代表设置值，而不传参数代表获取



#### text

设置和获取其实标签和结束标签中的文本，和innerText一致

```javascript
$(function() {
    $("div").text("text");
});
```



#### val

设置和获取表单项的value属性值，和dom中的balue一致val

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {
            var $btn = $("#btn");
            $btn.click(function() {
                alert($("#input").val());
            });
        });
    </script>
</head>
<body><!--主体内容-->
    <div>div标签</div>
    <input type="text" id="input"/>
    <button id="btn">click</button>
</body>
</html>
```

多选框中设置选中可以使用$().val(["num1","num2"])



#### attr

设置或者获取属性值，不推荐操作checked，readOnly，selected，disabled

```javascript
$(function() {
    var attr = $("#input").attr("name");
    alert(attr);
});
```

```javascript
$(function() {
    $("#input").attr("name","another");
    var attr = $("#input").attr("name");
    alert(attr);
});
```



#### prop

设置或者获取属性值，使用时建议与attr互补

当一个标签没有name的时候查询属性值会返回undifined，我们可以使用prop返回更为直观的false和true

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {
            $("#btn").click(function() {
               var prop = $("#check").prop("checked");
               alert(prop);
            });
        });
    </script>
</head>
<body><!--主体内容-->
    <input id="check" type="checkbox"/>
    <button id="btn">check</button>
</body>
</html>
```



### 增删改

#### 添加

appendTo(content)：将调用者插入到content的最后一个子元素之后

prependTo(content)：将调用者插入到content的最前一个子元素之前

```javascript
$(function() {
    $("<h1>h1<h1/>").appendTo("body");
});
```

#### 外部插入

```javascript
a.insertAfter(b);//得到da
a.insertBefore(b);//得到ab
```

#### 替换

```javascript
a.replaceWith(b);//用b替换掉a
a.replaceAll(b);//用a替换所有的b
```

#### 删除

```javascript
a.remove();//清除a标签
a.empty();//清除a标签中的内容
```



示例

```html
<!DOCTYPE html><!-- 约束声明 -->
<html lang="zh"><!--html表示html开始，lang=zh表示中文，分为head和body-->
<head><!--头部信息，一般包含title，css，js-->
    <meta charset="UTF-8"><!--字符集-->
    <title>标题</title><!--标题区域-->
    <script type="text/javascript" src="jquery-3.7.1.min.js"></script>
    <script type="text/javascript">
        $(function() {
            $("#btn1").click(function() {
                $("#select1 option:selected").appendTo($("#select2"));
            });
            $("#btn2").click(function() {
                $("#select1 option:selected").appendTo($("#select2"));
            });
        });
    </script>
</head>
<body><!--主体内容-->
    <select id="select1" multiple="multiple">
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
        <option value="4">4</option>
        <option value="5">5</option>
    </select>
    <select id="select2" multiple="multiple">
        <option value="6">6</option>
        <option value="7">7</option>
        <option value="8">8</option>
        <option value="9">9</option>
        <option value="10">10</option>
    </select><br/>
    <button id="btn1">1</button>
    <button id="btn2">2</button>
</body>
</html>
```



### 美化操作

#### CSS样式

addClass：添加样式

removeClass：移除样式

toggleClass：有就删除样式，没有就添加样式

offset：获取或者设置坐标

以下是ai生成的网页，便于对比理解

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery 类与坐标操作示例</title>
    <script src="jquery-3.7.1.min.js"></script>
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: #3498db;
            margin: 20px;
            position: relative;
            transition: all 0.3s ease;
        }

        .highlight {
            background-color: #e74c3c;
            border: 3px solid #f1c40f;
            transform: scale(1.1);
        }

        .rounded {
            border-radius: 50%;
        }

        .shadow {
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
        }

        button {
            margin: 5px;
            padding: 8px 15px;
            cursor: pointer;
        }

        .info {
            margin-top: 20px;
            padding: 10px;
            background-color: #f9f9f9;
            border: 1px solid #ddd;
        }
    </style>
</head>
<body>
<h1>jQuery 类与坐标操作示例</h1>

<div class="box" id="targetBox">可操作的盒子</div>

<div>
    <button id="addHighlight">添加高亮样式</button>
    <button id="removeHighlight">移除高亮样式</button>
    <button id="toggleRounded">切换圆形样式</button>
    <button id="toggleShadow">切换阴影样式</button>
</div>

<div>
    <button id="getOffset">获取坐标</button>
    <button id="setOffset">设置新坐标(右移50px)</button>
</div>

<div class="info" id="offsetInfo">
    坐标信息将显示在这里
</div>

<script>
    $(document).ready(function() {
        // 添加类
        $("#addHighlight").click(function() {
            $("#targetBox").addClass("highlight");
            updateOffsetInfo();
        });

        // 移除类
        $("#removeHighlight").click(function() {
            $("#targetBox").removeClass("highlight");
            updateOffsetInfo();
        });

        // 切换类
        $("#toggleRounded").click(function() {
            $("#targetBox").toggleClass("rounded");
            updateOffsetInfo();
        });

        // 切换另一个类
        $("#toggleShadow").click(function() {
            $("#targetBox").toggleClass("shadow");
        });

        // 获取偏移坐标
        $("#getOffset").click(function() {
            updateOffsetInfo();
        });

        // 设置新坐标
        $("#setOffset").click(function() {
            var currentOffset = $("#targetBox").offset();
            $("#targetBox").offset({
                top: currentOffset.top,
                left: currentOffset.left + 50
            });
            updateOffsetInfo();
        });

        // 更新坐标信息显示
        function updateOffsetInfo() {
            var offset = $("#targetBox").offset();
            var classes = $("#targetBox").attr("class") || "无";
            $("#offsetInfo").html(
                `当前坐标: top=${offset.top}px, left=${offset.left}px<br>
                     当前类: ${classes}`
            );
        }

        // 初始化显示坐标信息
        updateOffsetInfo();
    });
</script>
</body>
</html>
```



#### jQuery动画

show：显示，还可以传入显式的时间

hide：将元素隐藏

toggle：显示/隐藏，取决于隐藏/显示

fadeIn：淡入

fadeOut：淡出

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery 动画效果示例</title>
    <script src="jquery-3.7.1.min.js"></script>
    <style>
        .box {
            width: 200px;
            height: 200px;
            background-color: #3498db;
            margin: 20px;
            padding: 20px;
            color: white;
            text-align: center;
            line-height: 200px;
            font-size: 20px;
            border-radius: 10px;
        }

        .controls {
            margin: 20px;
            padding: 15px;
            background-color: #f5f5f5;
            border-radius: 5px;
        }

        button {
            margin: 5px;
            padding: 10px 15px;
            cursor: pointer;
            background-color: #2ecc71;
            color: white;
            border: none;
            border-radius: 4px;
            font-size: 14px;
        }

        button:hover {
            background-color: #27ae60;
        }

        .speed-control {
            margin-top: 15px;
        }
    </style>
</head>
<body>
<h1>jQuery 动画效果演示</h1>

<div class="controls">
    <h2>基本显示/隐藏</h2>
    <button id="btnShow">显示 (show)</button>
    <button id="btnHide">隐藏 (hide)</button>
    <button id="btnToggle">切换 (toggle)</button>

    <div class="speed-control">
        动画速度：
        <select id="speedBasic">
            <option value="fast">快速</option>
            <option value="normal" selected>正常</option>
            <option value="slow">慢速</option>
            <option value="1000">1秒</option>
            <option value="2000">2秒</option>
        </select>
    </div>
</div>

<div class="box" id="box1">显示/隐藏动画效果</div>

<div class="controls">
    <h2>淡入淡出效果</h2>
    <button id="btnFadeIn">淡入 (fadeIn)</button>
    <button id="btnFadeOut">淡出 (fadeOut)</button>
    <button id="btnFadeToggle">淡入淡出切换</button>

    <div class="speed-control">
        动画速度：
        <select id="speedFade">
            <option value="fast">快速</option>
            <option value="normal" selected>正常</option>
            <option value="slow">慢速</option>
            <option value="1500">1.5秒</option>
            <option value="3000">3秒</option>
        </select>
    </div>
</div>

<div class="box" id="box2">淡入淡出动画效果</div>

<script>
    $(document).ready(function() {
        // 基本显示/隐藏控制
        $("#btnShow").click(function() {
            $("#box1").show($("#speedBasic").val());
        });

        $("#btnHide").click(function() {
            $("#box1").hide($("#speedBasic").val());
        });

        $("#btnToggle").click(function() {
            $("#box1").toggle($("#speedBasic").val());
        });

        // 淡入淡出控制
        $("#btnFadeIn").click(function() {
            $("#box2").fadeIn($("#speedFade").val());
        });

        $("#btnFadeOut").click(function() {
            $("#box2").fadeOut($("#speedFade").val());
        });

        $("#btnFadeToggle").click(function() {
            $("#box2").fadeToggle($("#speedFade").val());
        });

        // 初始化隐藏第二个盒子
        $("#box2").hide();
    });
</script>
</body>
</html>
```



### 事件处理

在使用上和JS差不多，加了更多的常用事件



#### 常用的绑定方法

click：绑定单机事件以及触发的事件

mouseover：鼠标移入事件

bind：给元素一次性绑定一个或者多个事件

one：和bind类似，但是只会触发一次

live：用于绑定事件，可以绑定与选择其匹配的所有元素的事件，即使是动态创建后的元素

unbind：和bind相反，解除事件绑定



#### 冒泡

如果子元素和父元素都绑定了同一事件，当子元素被触发时，父元素也会响应，如果需要阻止则在子元素处理中返回false即可



#### 事件对象

是封装有触发事件信息的一个JavaScript对象

在给元素绑定事件的时候，在事件的function(event)参数列表中添加一个参数，这个event就是JavaScript传递参数事件处理函数的事件对象，其中包含了一些事件响应的信息

有的时候需要使用到事件对象中的内容，例如获取当前操作是什么事件，并将事件打印出来



## XML

xml是可扩展的标记性语言，用于保存数据，而且这些数据具有自我描述性，还可以作为项目的配置文件，另外还可以作为网络传输数据的格式（不过现在常使用json）



### 语法规则

XML元素命名规则：名称可以含有数字，字母，其他字符。名称不可以以数字，标点符号开始。名称不可以以xml开始。名称不可以包含空格

```html
<book sn="1124">
        <name>C++</name>
        <author>汉顺</author>
        <price>16</price>
</book>
```

分为单标签和双标签，和html类似，写的时候建议闭合

```xml
<br/>
<book></book>
```

属性：和html类似，提供额外的信息给元素

```xml
<book name="123" id="123" function="123"></book>
```

文档必须有唯一根元素，即没有父标签的元素，例如示例中的books标签

```xml
<?xml version="1.0" encoding="utf-8" ?>
<books>
    <book sn="1123">
        <name>时间简史</name><!--书名-->
        <author>霍金</author>
        <price>15</price>
    </book>
    <book sn="1124">
        <name>C++</name>
        <author>汉顺</author>
        <price>16</price>
    </book>
</books>
```

文本区域：不会解析其中xml格式的内容（CDATA）

格式

```xml
<![CDATA[内容部分，这个部分不会被解析]]>
<author><![CDATA[内容部分，这个部分不会被解析]]></author>
```



### XML解析

无论是html文件还是xml文件都是标记型文档都可以使解析取出操作内容



#### 常见的解析技术

DOM解析：标签在dom解析的时候生成各个对象，并以树形的格式连接

SAX解析：这种解析方式可以一行一行读取xml文件，不会创建大量的dom对象，性能和内存都优于dom解析

第三方解析：jdom在dom基础上封装，dom4j对jdom封装。pull主要用于安卓手机开发，和sax类似，是事件机制解析xml文件



#### dom4解析技术

引入dom4 jar包，添加到类库，以下是一个实例代码，获取document对象

```java
package com;

import org.dom4j.*;
import java.util.List;

public class Main {
    public static void main(String[] args) throws DocumentException {
        SAXReader reader = new SAXReader();
        Document document = reader.read("xml/test.xml");
        Element rootElement = document.getRootElement();
        //获取根标签对象
        List<Element> books = rootElement.elements("book");
        //通过根标签对象获取book子标签对象list集合
        for (Element book : books) {
            Element name = book.element("name");
            //取出book标签的name子标签对象
            System.out.println(name.asXML());
            //调用name标签对象的asXML输出标签+内容
        }
    }
}
```

```
<name>时间简史</name>
<name>C++</name>
```

其他常见的方法

```java
getText();//获取dom对象的内容
elementText("targetName");//获取子标签的内容
```

jQuery的学习时间拉的有点长了，明天会进入tomcat与servlet的学习
