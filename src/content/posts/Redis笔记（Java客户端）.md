---
title: Redis
published: 2025-04-24
updated: 2025-04-24
description: 'Java客户端，待学部分，等到Java框架学习完成后进行'
image: ''
tags: [Redis]
category: ''
draft: false 
---

# Redis笔记

## Jedis

我们学Redis最终还是要回到编码上，我们需要使用到Redis提供的客户端，推荐的有Jedis、lettuce、Redisson

Jedis，以Redis命令作为方法名称，学习成本较低，简单实用，但是Jedis实例线程不安全，多线程环境下需要基于连接池来使用

Lettuce，是基于Netty实现的，支持同步，异步，响应式编程方式，线程安全，支持Redis的哨兵模式，集群模式，管道模式

Redisson，是基于Redis实现的分布式，可伸缩的Java数据结构集合，包含了Map、Queue、Lock、Semaphore、AtomicLong等强大功能

而其中Spring Data Redis支持jedis和lettuce

### Jedis使用

用Maven添加如下的依赖

```xml
<dependencies>
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>5.2.0</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.8.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

全部配置文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>JedisTest</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>JedisTest</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>5.2.0</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
      <scope>test</scope>
    </dependency>

  </dependencies>
</project>
```



### 测试代码

```java
package org.example;

public class Main {
    public static void main(String[] args) {
        JedisTest jedis = new JedisTest();
        jedis.setUP();
        jedis.testString();
        jedis.tearDown();
    }
}
```

```java
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

public class JedisTest {
    private Jedis jedis;

    public void setUP(){
        jedis = new Jedis("192.168.104.52", 6379);
        jedis.auth("654321");
        jedis.select(0);
    }

    public void testString() {
        jedis.hset("hash", "test", "value");
        Map<String, String> hash = jedis.hgetAll("hash");
        Set<Map.Entry<String, String>> entrySet = hash.entrySet();
        Iterator<Map.Entry<String, String>> iterator = entrySet.iterator();
        while(iterator.hasNext()) {
            Map.Entry<String, String> entry = iterator.next();
            System.out.println(entry.getKey() + " " + entry.getValue());
        }
    }

    public void tearDown() {
        if(jedis != null) {
            jedis.close();
        }
    }
}
```

```
fieldString1 HelloWorld1
test value
fieldInt 1
fieldString HelloWorld
```

可以看到，所有的jedis指令名称和参数都和redis中的对应，使用起来异常的方便，Hash数据结构中的hgetAll也使用HashMap的形式返回，方便用迭代器进行处理



### Jedis连接池

Jedis本身是线程不安全的，而且频繁地创建和销毁连接会有性能上的损耗，推荐使用Jedis地连接池来替代Jedis地直接连接方式

封装连接池类

```JAVA
public class JedisConnectionFactory {
    private static final JedisPool jedisPool;

    static {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        //设置最大连接数
        jedisPoolConfig.setMaxTotal(8);
        //设置最大空闲连接
        jedisPoolConfig.setMaxIdle(8);
        //设置最小空闲连接
        jedisPoolConfig.setMinIdle(8);
        //设置最长等待时间ms
        jedisPoolConfig.setMaxWait(Duration.ofMillis(2000));
        jedisPool = new JedisPool(jedisPoolConfig, "192.168.104.52", 6379, 1000, "654321");
    }
    //1000 是连接 Redis 服务器的超时时间（毫秒）

    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Jedis jedis = JedisConnectionFactory.getJedis();
        jedis.set("key", "value");
        String s = jedis.get("key");
        System.out.println(s);
    }
}
```



## SpringDataRedis

SpringData是Spring中数据操作的模块，包含对于各种数据库的集成，其中对Redis的集成模块就是SpringDataRedis，总的来说包含了以下的功能

1.提供了对不同Redis客户端的整合（Lettuce和Jedis）

2.提供了RedisTemplate统一API操作Redis

3.支持Redis的发布订阅模型

4.支持Redis哨兵和Redis集群

5.支持基于Lettuce的响应式编程

6.支持基于JDK，JSON，字符串，Spring对象的数据序列化和反序列化

7.支持基于Redis的JDKCollection实现



### 快速入门

![131](../images/131.png)



### 问题解决

IDEA下创建项目SpringBoot发生如下问题，而没有Java更低版本

```
项目 SDK '1.8' 不支持所选版本的 Java 17。请选择较低版本的 Java，或设置较高版本的 SDK
```

可以尝试替换源，选择更低版本Java

```
https://start.aliyun.com
```



Maven获取依赖

如果在使用Maven获取依赖项的时候用时太久可以尝试替换

配置阿里云镜像

在 `~/.m2/settings.xml` 中添加：

```xml
<mirrors>
    <mirror>
        <id>aliyunmaven</id>
        <url>https://maven.aliyun.com/repository/public</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```



感觉学到这里有点寸步难行，redis的各种相关配置，用到的SpringBoot，Maven之类，我都没有接触过，还是得回到Java的开发框架进行学习，Redis教程的进度先耽搁一下

```java
@SpringBootTest
class RedisDataApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;
    @Test
    void testString() {
        redisTemplate.opsForValue().set("dataTest", "李四");
        Object dataTest = redisTemplate.opsForValue().get("dataTest");
        System.out.println(dataTest);
    }

}
```
