---
layout: post
title:  "Android网络编程之HTTP协议"
date:  2018-4-27 15:39:20
categories: Android
tags: Android

---
* content
{:toc}







#  

## 一、Android与互联网交互的三种方式

- 数据上传：使用Get/Post等方式上传数据(图片、文本、XML、Json数据和音视频文件等等)，调用WebService数据；使用Socket上传大文件等。
- 数据下载：下载网络中的数据(图片、代码文本、XML、Json数据、音视频文件)，WebService的调用。
- 数据浏览：通过WebView浏览网页。



## 二、HTTP协议原理

### 1. 什么是HTTP协议

- **(1)协议**：指计算机通信网络中两台计算机之间进行通信所必须共同遵守的规定或规则。

- **(2)HTTP协议**：HyperText Transfer Protocol（超文本传输协议），TCP/IP 协议的一个应用层协议，用于定义 WEB 浏览器与 WEB 服务器之间交换数据的过程。
	- Client->请求->Server
	- Server->响应->Client

### 2. HTTP协议的主要特点

- **支持 C/S (客户/服务器)模式**。
- **简单快速**：客户向服务器请求服务时，只需传送请求方法好路径，就能获取相应的数据。请求方法有GET、POST和HEAD，由于HTTP服务器规模小，所以通信速度非常快。
- **无连接**：限制每次连接只处理一次请求，服务器处理完请求后，会收到客户的应答，即断开连接。采用这种方式可以节省传输时间。
- **无状态**：指协议对于事务处理没有记忆能力，如果后续处理需要前面的信息，则它必须重传，有可能导致传输量增大。另一方面，在服务器不需要先前信息时它的应答速度就较快。
- **灵活**：HTTP允许传输任意类型的数据对象，传输的类型由Content-Type加以标记。

### 3. HTTP协议的底层工作流程

1. 第一次握手：**建立连接**。客户端发送连接请求报文段，将SYN设置为1、Seq=x；接下来客户端进入SYN_SENT状态，等待服务器的确认。
2. 第二次握手：**服务器收到客户端的SYN报文段**，对SYN报文段进行确认，设置ACK为x+1；同时将 SYN 设置为1，Seq为 y。将 SYN+Seq+ACK放到报文段中，一并发送给客户端，此时服务端进入SYN_RCVD状态。
3. 第三次握手：

### URI 和 URL 的区别


- **URI**：是 Uniform Resource Identifier，统一资源标识符，用来唯一的标识一个资源。

- **URL**：是 Uniform Resource Locator，统一资源定位器，它是一种具体的 URI，即 URL 可以用来标识一资源，而还指明了如何 Locate 这个资源。

### Http的几种请求方式



### Request 和 Response 的原理


## 3. HTTP协议中比较容易混淆的知识点

### HTTP1.1和HTTP1.0的区别
### Get 和 Post 方法的区别
GET：在请求的URL地址后以?的形式带上交给服务器的数据，多个数据之间以&进行分隔， 但数据容量通常不能超过2K，比如:http://xxx?username=…&pawd=…这种就是GET
POST: 这个则可以在请求的实体内容中向服务器发送数据，传输没有数量限制
另外要说一点，这两个玩意都是发送数据的，只是发送机制不一样，不要相信网上说的 "GET获得服务器数据，POST向服务器发送数据"!!另外GET安全性非常低，Post安全性较高， 但是执行效率却比Post方法好，一般查询的时候我们用GET，数据增删改的时候用POST！！

### Cookie 和 Session 的区别

### Etag/if-None-Matoh、referer的区别





## 基本概念

**(1)协议**：

**(2)http协议**：

**(3)URI 和 URL 的区别：

- **URI**：是 Uniform Resource Identifier，统一资源标识符，用来唯一的标识一个资源。

URI 格式如下
```
http://host[":"port][abs_path]
```

URI 组成部分：
访问资源的命名机制；
存放


- URL**：是 Uniform Resource Locator，统一资源定位器，它是一种具体的 URI，即 URL 可以用来标识一资源，而还指明了如何 Locate 这个资源。

```
http://host[":"port][abs_path]
```
## HTTP协议的特点
简单快速：
无连接：
无状态：
## Request / Response 原理
