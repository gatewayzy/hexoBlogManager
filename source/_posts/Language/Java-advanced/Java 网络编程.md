---
title: Java 网络编程
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
- Socket
---

**说明：**Java Socket网络编程：java.net.ServerSocket/Socket/InetSocket/...
<!-- more -->


参考文章：


## Java 网络编程

* Socket属于TCP、UDP等抽象封装，不是一个协议。
* socket常用类型：`SOCK_STREAM`(支持tcp)、`SOCK_DGRAM`(支持udp)。
* Socket实现TCP连接过程：三次握手、数据传输、四次挥手。
* 更多的详细的网络知识看计算机网络。

## ServerSocket
* java.net.ServerSocket 用于服务器listen
* java.net.Socket 用于client和server之间具体连接和数据传输
* java.net.InetAddress 表示网络协议地址
* java.io.DataInputStream/DataOutputStream 用于client与server读写数据


## Java用socket实现C/S示例demo

* ServerSocket.java：
    * 服务器启动一个ServerSocket类进行listen请求
    * accept()接收一个client的连接，如果setSoTimeout(10000)时间内没有新的连接会报异常
    * 每次新accept一个socket连接，建立一个新的线程处理请求
```
package javaAdvanced;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * Created by dell on 2017/9/7.
 */
public class _03ServerSocket {

    public static void main(String[] args) throws Exception {
        InetAddress inetAddress = InetAddress.getLoopbackAddress();
        ServerSocket serverSocket = new ServerSocket(8080, 50, inetAddress);
        serverSocket.setSoTimeout(10000);  // 不设置的话会一直阻塞等待连接，这里设置超时关闭
        System.out.println("=== serverSocket 需要一直处于监听状态：");
        System.out.println(serverSocket.getInetAddress() + "\t" + serverSocket.getLocalPort());
        while (true) {
            try {
                // 默认阻塞等待连接，如果用setSoTimeout设置超时限制，必须在时限内收到连接请求，否则超时异常
                Socket socket = serverSocket.accept();
                new Thread(() -> {
                    try {
                        // 获取输入流，并读取内容
                        DataInputStream dataInputStream = new DataInputStream(socket.getInputStream());
                        System.out.println(dataInputStream.readUTF());

                        // 获取输出流，向client发送消息
                        DataOutputStream dataOutputStream = new DataOutputStream(socket.getOutputStream());
                        dataOutputStream.writeUTF(String.format("【msg from server：%s】client你好~~", socket.getLocalAddress().toString()));

                        // 关闭资源
                        socket.close();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }).run();
            } catch (IOException e) {
                System.err.println("Accept timed out. 时限内没有收到连接请求...");
            }
        } // end of while
        // serverSocket.close();
    }
}
```

* Client用Socket连接服务器示例：
    * 客户端确定server的地址InetAddress和port
    * 新建一个socket连接服务器，client的地址和port默认是自动封装的，不用配置，socket默认是stream类型的
    * client与server使用java.io.DataInputStream/DataOutputStream相互通信
```
package javaAdvanced;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.Socket;

/**
 * Created by dell on 2017/9/7.
 */
public class _03ClientSocket {
    public static void main(String[] args) throws Exception {
        // 设置要连接的服务器的ip、port
        int serverPort = 8080;
        InetAddress inetAddress = InetAddress.getLoopbackAddress();
        System.out.println("===准备连接服务器，服务器信息为：");
        System.out.println(inetAddress.getHostAddress() + "\t" + serverPort);
        // 建立一个client的socket，一般不设置client使用的ip、端口号，会自动封装
        Socket socket = new Socket(inetAddress, serverPort);  // 默认使用stream类型，TCP。如果连接失败会立即抛出异常
        System.out.println("=== Client使用的socket信息为：");
        System.out.println(socket.getLocalAddress() + "\t" + socket.getLocalPort());


        System.out.println("===连接服务器成功，服务器地址为：" + socket.getRemoteSocketAddress());
        // 获取输出流，并向server发送数据
        OutputStream outputStream = socket.getOutputStream();
        DataOutputStream dataOutputStream = new DataOutputStream(outputStream);
        dataOutputStream.writeUTF(String.format("【msg from %s】Server你好，我是client！", socket.getLocalAddress().toString()));
        // 获取输入流，读取server的数据
        InputStream inputStream = socket.getInputStream();
        DataInputStream dataInputStream = new DataInputStream(inputStream);
        System.out.println(dataInputStream.readUTF());

        // 关闭资源
        socket.close();
    }
}
```







