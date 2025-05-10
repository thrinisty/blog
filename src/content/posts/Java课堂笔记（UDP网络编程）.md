---
title: Java笔记
published: 2025-03-16
updated: 2025-03-16
description: 'UDP网络编程'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## UDP网络通信编程

不同于TCP通信，UDP不需要建立连接，UDP数据包中含有发送的目标地址

1.类 DatagramSocket 和 DatagramPacket【数据包/数据报】实现了基于UDP协议网络程序

2.UDP数据报通过数据报套接字 DatagramSocket 发送和接收，系统不保证UDP数据报有一定可以能够到达目的地，也不确定在什么时候可以到达

3.DatagramPacket 对象封装了UDP数据报，在数据包中包含了发送端和接收端的IP地址以及端口号

4.UDP协议中的每一个数据报都给出了完整的信息地址，因此无需建立发送方和接收方的链接吧

## UDP特殊说明

1.UDP中没有明确的服务端和客户端，演变为数据的发送端和接收端

2.接收数据和发送数据通过DatagramSocket对象完成

3.发送数据时会将数据封装在DatagramPacket对象中，称之为装包

4.当接收到数据包后，需要进行拆包取出数据

5.DatagramSocket可以指定在哪个端口接收数据

![65](../images/65.png)

## UDP通信流程

1.建立发送端和接收端

2.发送数据前建立数据包和数据报

3.调用数据包的发送和接受方法

4.关闭数据包

## 代码示例

发送端

```java
package com.udp;

import java.io.IOException;
import java.net.*;

public class Sender {
    public static void main(String[] args) throws IOException {
        DatagramSocket socket = new DatagramSocket(9998);//发送端的端口是9998
        byte[] bytes = "hello".getBytes();
        DatagramPacket packet = new DatagramPacket(bytes, bytes.length, InetAddress.getByName("127.0.0.1"), 9999);
        socket.send(packet);
        System.out.println("数据已发送");
        socket.close();
    }
}
```

接收端

```java
package com.udp;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

public class Receiver {
    public static void main(String[] args) throws IOException {
        DatagramSocket socket = new DatagramSocket(9999);
        byte[] buf = new byte[1024];
        DatagramPacket packet = new DatagramPacket(buf, buf.length);

        System.out.println("等待接收数据");
        socket.receive(packet);//将收到的数据填充进入packet对象

        int length = packet.getLength();//实际接收到的数据长度
        byte[] data = packet.getData();
        String s = new String(data, 0, length);
        socket.close();
        System.out.println(s);
    }
}
```

## 一个代码的综合习题

这一道题是聊天交互的最简情况，支持一次通信，可以加上循环完成消息的重复发送与接收

```java
package com.homework;

import java.io.*;
import java.net.InetAddress;
import java.net.Socket;
import java.net.UnknownHostException;
import java.util.Scanner;

public class Client {
    public static void main(String[] args) throws IOException {
        Scanner scanner = new Scanner(System.in);
        Socket socket = new Socket(InetAddress.getByName("127.0.0.1"), 6666);
        System.out.println("TCP链接建立");
        System.out.print("输入发送的消息:");
        String message = scanner.next();
        OutputStream outputStream = socket.getOutputStream();
        BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(outputStream));
        bufferedWriter.write(message);
        bufferedWriter.newLine();
        bufferedWriter.flush();
        System.out.println("消息发送完成");

        InputStream inputStream = socket.getInputStream();
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
        String response = bufferedReader.readLine();
        System.out.println("收到服务器消息：" + response);

        bufferedReader.close();
        bufferedWriter.close();
        socket.close();
        System.out.println("客户端关闭");
    }
}
```

```java
package com.homework;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(6666);
        System.out.println("服务器正在监听...");
        Socket socket = serverSocket.accept();

        OutputStream outputStream = socket.getOutputStream();
        InputStream inputStream = socket.getInputStream();
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
        BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(outputStream));

        String receive = bufferedReader.readLine();
        System.out.println("收到客户短消息" + receive);
        switch (receive) {
            case "name":
                bufferedWriter.write("我是nova");
                break;
            case "hobby":
                bufferedWriter.write("编写java程序");
                break;
            default:
                bufferedWriter.write("未知消息");
                break;
        }
        bufferedWriter.newLine();
        bufferedWriter.flush();
        bufferedWriter.close();
        System.out.println("回复消息已发送");

        bufferedReader.close();
        bufferedWriter.close();
        socket.close();
        serverSocket.close();
        System.out.println("服务器关闭");
    }
}
```
