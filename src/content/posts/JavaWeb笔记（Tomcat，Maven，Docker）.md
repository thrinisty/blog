---
title: JavaWeb笔记(Tomcat，Maven，Docker)
published: 2025-04-29
updated: 2025-04-29
description: '服务器部署Tomcat，jar包管理Maven，环境Docker'
image: ''
tags: [JavaWeb]
category: 'JavaWeb'
draft: false 

---

# JavaWeb笔记



## Tomcat

JavaWeb：所用通过Java语言编写可以通过浏览器访问的程序的总称，基于请求和响应来开发的

Web资源：根据资源实现的技术和呈现的效果不同，分为静态资源和动态资源

静态资源：html，css，js，txt，mp4，jpg

动态资源：jsp页面，Servlet程序

常见的服务器：Tomcat，Jboss，GlassFish，Resin，WebLogic



### 安装启动

版本对应

![138](../images/138.png)

我使用的是tomcat9.0+Servlet4.0+JDK8

将Tomcat解压到你想要的路径下

在安装的bin目录下双击startup启动tomcat服务器，在浏览器中输入以下网址开启Tomcat服务页面

```
http://localhost:8080
```

![139](../images/139.png)

如果在启动的时候出现黑框立马闪退，有可能是没有设置系统变量

变量：JAVA_HOME   值：安装的JDK目录

关闭：双击shutdown.bat关闭

在config目录下有一个server.xml配置文件，在其中可以修改端口号8080，可以改为一个你想要用的其他端口号替换，再重启Tomcat服务器即可完成修改



### 部署Web

#### 第一种方式

是需要把Web工程目录放入Tomcat的webapps目录下即可，这种方式比较方便

```web
http://localhost:8080/test/123.html
```

输入路径即可访问，其中的 http://localhost:8080 代表的就是webapps目录

在其他的设备通过输入部署的网络IP地址可以远程访问Web页面，例如

```
http://192.168.104.29:8080/test/123.html
```



#### 第二种方式

找到Tomcat下的conf目录apache-tomcat-9.0.104\conf\Catalina\localhost，创建配置文件，在其中设置路径即可访问对应路径下的Web工程，这种方式较为灵活

访问服务器的时候，只填入网址端口默认访问ROOT工程，没有地址名，访问index.html



### 动态web工程

使用详见

https://blog.csdn.net/weixin_45791445/article/details/104574239

可以在IDEA上创建动态的Web项目，并使用Tomcat部署Web服务



## Maven

推荐教程：可以快速上手，理解Maven相关概念以及使用方式

【一小时Maven教程】https://www.bilibili.com/video/BV1uApMeWErY?p=12&vd_source=586d101621a2629c1796f59df9143dff

Maven是一个依赖管理工具

通过Maven可以实现批量编译，组织文件结构，批量复制jar包

![140](../images/140.png)



**依赖管理**

Maven可以管理项目依赖，包括自动下载依赖库，自动下载依赖之间没有冲突，以来版本管理，用户只需要编写配置即可

**构建管理**

项目构建是将源代码，配置文件，资源文件等转化为能够运行或者部署的应用程序或库的过程

![141](../images/141.png)



Package定义规则

jar：代表普通的java工程，打包为jar结尾的文件

war：代表java的web工程

porn：代表不会打包，用以做继承的父工程

而maven通过pom.xml配置执行打包以及构建，其中仓库存放jar包（仓库又分为本地仓库，私服仓库，中央仓库）

setting.xml配置

```
<settings>
  <!-- 原有镜像配置保持不变 -->
  <mirrors>
    <mirror>
      <id>aliyunmaven</id>
      <url>https://maven.aliyun.com/repository/public</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>

  <!-- 新增以下配置 -->
  <profiles>
    <profile>
      <id>disable-remote-archetype</id>
      <properties>
        <!-- 强制使用本地 archetype 缓存 -->
        <archetypeCatalog>internal</archetypeCatalog>
      </properties>
    </profile>
  </profiles>

  <!-- 激活配置 -->
  <activeProfiles>
    <activeProfile>disable-remote-archetype</activeProfile>
  </activeProfiles>
</settings>
```



父子模块管理

在父工程中的pom文件下添加的依赖可以被子模块中使用，所有将公共的依赖放置在父工程中可以简化依赖，也可以设置为不默认继承依赖，而是手动集成父工程依赖



## Docker

### 基本概念

Docker是一个用于构建运行传送应用程序的平台，可以解决环境配置的问题，将第三方软件库，依赖等打包，运行在任何的环境

Docker是一个容器的实现，容器是一个虚拟化技术是一个独立的环境，使用宿主机的OS操作系统，减少资源的浪费（运行多个操作系统和图形化界面）

容器是Docker的一个运行实例，提供一个可以移植的环境，可以在这个环境中运行应用程序，镜像和容器的关系类似Java中的类和实例，Docker仓库可以存储Docker镜像和上传，我们打包容器为镜像上传给别人，别人就可以使用镜像创建相应的容器



### 大致使用

Dockerfile是构建指令，包含了如何配置环境的一些指令，用于告诉如何构建镜像

```dockerfile
FROM node:14-alpine
COPY index.js /index.js
CMD ["node", "/index.js"]
```

构建镜像

```cmd
docker build -t hello-docker .
```

查看镜像

```
docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    9bea9f2796e2   5 months ago   192MB
```

运行镜像

```
docker run hello-docker
```

可以通过网页使用在线的play with docker来远程通过dockerhub下载镜像，运行容器

```
docker pull thrinisty/hello-docker
```



### Docker Compose

用于定义和运行多容器Docker应用程序的工具，使用yaml文件来设置应用程序服务

通过docker-compose.yaml配置文件将一组互相关联的容器组合在一起，形成项目，通过一条命令即可创建并启动所有的服务

```
docker compose up
```



### 实际使用

我们现在在Docker上下载一个redis的镜像，并且通过镜像创建运行redis容器，之后在宿主机上用客户端连接服务器

查找可用redis镜像

```
docker search redis
```

下载redis镜像

```
docker pull redis
```

运行redis容器，并且通过-p参数将容器端口映射到宿主机端口

```
docker run --name redis_test -p 6379:6379 -d redis
```

在宿主机连接docker运行中的redis容器对象

```
redis-cli -h 127.0.0.1 -p 6379
```

