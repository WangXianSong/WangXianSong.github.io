---
layout: post
title:  "Android网络框架之OKHttp学习"
date:  2018-5-16 22:55:53
categories: 第三方框架
tags: 第三方框架

---
* content
{:toc}


参考的网站：

- http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html
- https://www.jianshu.com/p/f5320b1e0287
- [okhttp框架解析与应用](https://www.imooc.com/video/13048)




上篇笔记里讲到 HTTP 协议的相关内容，这一篇笔记是关于 OkHttp 网络框架的简单使用。而在学习网络框架之前对基础知识的了解是非常有必要的，特别在面试的过程中。

> 目前比较热门的网络框架有：android-async-http、xUtils、Volley、Retrofit和OKhttp，为什么要学习 OKhttp 呢？

### OKhttp的好处

> - 支持 SPDY，共享同一个 Socket 来处理同一个服务器的所有请求。
> - 如果 SPDY 不可用，则通过连接池来减少请求延迟。
> - 无缝的支持 GZIP 来减少数据流量。
> - 缓存响应数据来减少重复的网络请求。
> - 可从很多常用的连接问题中自动恢复。
> - 还会处理代理服务器问题和 SSL 失败的问题。 
> - 使用起来非常简单。

## OKHttp 的使用
> 
> **Github 地址**：https://github.com/square/okhttp
> 
> **配置方法**：
> 
> - api 'com.squareup.okhttp3:okhttp:3.10.0'
> - compile 'com.squareup.okio:okio:1.14.0'


### OKHttp的类型和方法

- 请求的类型：GET 和 POST;
- 请求的方法：同步 execute 和 异步 enqueue；

### 1.异步 GET 请求

- 第一步：创建  OkHttpClient 对象，使用到了 Builder 设计模式
- 第二步：创建 Request 对象，封装了一些请求报文的信息
- 第三步：创建 Call 对象，调用 execute/enqueue。
- 第四步：获取返回对象 Response

```java
OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder().url("http://www.baidu.com/").build();
Call call = client.newCall(request);

//同步 execute 获取数据
Response response = call.execute();
String responseData = response.boby().string();
//Response response = client.newCall(request).execute()//简写第三四步

//异步 enqueue 获取数据
call.enqueue(new Callback){
    public void onFailure(Call call,IOException e){
         //失败
    }
    public void onResponse(Call call,Response response)throws IOException{
	//runOnUiThread
        sysout(response.boby().string());//成功
    }
}
```

### 2.异步 POST 请求

From表单形式：

```java
OkHttpClient client = new OkHttpClient();
RequestBody requestBody = new FormBody.Builder()
        .add("username", "admin")
        .add("password", "123456")
        .build();
Request request = new Request.Builder()
        .url("https://www.imooc.com/")
        .post(requestBody)//添加进来
        .build();
client.newCall(request).enqueue(new Callback() {...});
```
JSON参数形式

```java
OkHttpClient client = new OkHttpClient();
RequestBody requestBody = RequestBody.create(MediaType.parse("application/json; charset=utf-8"), json);
Request request = new Request.Builder()
         .post(requestBody)
         .url(url)
        .build();
client.newCall(request).enqueue(new Callback() {...});
```

### 3.异步上传文件

```java
//创建一个test.txt文件
String fillepath = Environment.getExternalStorageDirectory().getAbsolutePath();
File file = new File(fillepath, "test.txt");
//请求体
RequestBody requestBody =RequestBody.create(MediaType.parse("text/x-markdown; charset=utf-8"), file)

OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder()
        .url("https//www.baidu.com/")
        .post(requestBody)
        .build();
client.newCall(request).enqueue(new Callback() {....});
```
### 4.异步下载文件
```java
String url = "https://www.imooc.com/static/img/index/logo.png";
Request request = new Request.Builder().url(url).build();
OkHttpClient client = new OkHttpClient();
client.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
        //失败
    }
    @Override
    public void onResponse(Call call, Response response) throws IOException {
        //开启文件流
        InputStream inputStream = response.body().byteStream();
        FileOutputStream fileOutputStream = null;
        String filepath = "";
        // 判断SD卡是否存在，并且是否具有读写权限
        if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
            // 获得存储卡的路径
            filepath = Environment.getExternalStorageDirectory().getAbsolutePath();
        } else {
            //获取/data/data//files目录
            filepath = getFilesDir().getAbsolutePath();
        }
        //新建文件
        File file = new File(filepath, "songsong.jpg");
        if (null != file) {
            fileOutputStream = new FileOutputStream(file);
            byte[] buffer = new byte[2048];
            int len = 0;
            while ((len = inputStream.read(buffer)) != -1) {
                fileOutputStream.write(buffer, 0, len);
            }
            fileOutputStream.flush();
            inputStream.close();
            fileOutputStream.close();
        }
    }
```
### 5.异步上传Multipart文件
```java
OkHttpClient client = new OkHttpClient();

RequestBody requestBody = new MultipartBody.Builder()
        .setType(MultipartBody.FORM)
        .addFormDataPart("title", "songsong")//key-value
        .addFormDataPart("image", "songsong.jpg",
                RequestBody.create(MediaType.parse("image/png"),
                        new File("/sdcard/songsong.jpg")))//key-file,类型-来源
        .build();
Request request = new Request.Builder()
        .url(url)
        .post(requestBody)//
        .build();
client.newCall(request).enqueue(new Callback() {....});
```

[在这里不做过多的使用介绍，推荐一篇关于OKHttp的详细使用教程](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0106/2275.html)

## 实现机制

### OKhttp 需要注意的地方

- **同步请求**：发送请求后，就会进入阻塞状态，直到收到相应。(这是同步和异步请求的最大不同之处)
- **异步请求**：onResponse 和 onFailure 都是在工作线程中执行的。

### OKhttp 同步和异步的区别

- 发起请求的方法调用：execute/enqueue(new Callback)；
- 阻塞线程与否：阻塞/开启新线程；

### OKhttp 设计思路

![](https://i.imgur.com/YG9e42U.png)

> 1. 通过 Builder(构建者) 构建 Request。
> 1. Diapatcher(任务调度器) 不断从 RequestQueue(请求队列) 中取出 Request(请求)。
> 1. Diapatcher 把 Request 分发到 HttpEngine 中。
> 1. 根据当前请求是否有 Cache(缓存) 来返回 Response。
> 1. 没有就把请求发到 ConnectionPool 连接池中。
> 1. 从 ConnectionPool 连接池中获取到 Connection 之后去发起真正的请求。
> 1. 请求成功后通过 Route(路由) 和 Platform 找到合适平台。
> 1. 通过 Server(Socket) 去获取到 data。 

### OKhttp 流程图

![](https://i.imgur.com/xrZQlFM.png)


### 多路复用机制

![](https://i.imgur.com/g3fELS6.png)

> 1. HttpEngine 在发起请求之前，先调用 nextConnection() 方法来获取 Connection 对象。
> 1. 如果 nextConnection 能从 ConnectionPool 里面获取 Connection 对象，那就不会新建 Connection。
> 1. 如果获取不到，它才调用自己的 CreateNextConnection() 方法，去获取 Connection 对象。

(以上则是多路复用的核心，接着就是网络请求)

### 重连机制

![](https://i.imgur.com/Pmsgepr.png)

> 1. 当 Call 发送到 HttpEngine 的时候，每次都会判断能否 getResponse()。
> 1. 如果可以获取 getResponse()，就会跳出 while 死循环。
> 1. 如果不行，就会执行 recover()，然后开启 retry 机制，一直重连。

### 核心类

- Route：路由，其实就是对地址的一个封装类。
- Platform：这个类主要是做平台适应性，针对Android2.3到5.0后的网络请求的适配支持。同时，在这个类中能看到针对不同平台，通过java反射不同的class是不一样的。
- ResponseBody
- Response
- RequestBody
- FormBody：通过Key-Value形式发送 post 请求。
- MultipartBody：文件上传的时候使用，通过addFormDataPart添加Key-Value对象，以及RequestBody封装的对象。
- Requset
- Call：任务
- RealCall：实现Call接口，执行请求的任务。
- HttpUrl：url的工具类。
- Headers：请求响应头。
- ConnectionPool：管理Connection。
- Connnection：
- Cache：缓存块。
- HttpEngine：引擎，真正去发起请求的类。(重发机制)
- Dispatcher：任务分发器，就是一个线程池，有三种请求队列类型：异步将要、异步正在、同步正在。
- OKhttpClient：综合各种模块，完成整改请求的过程。
- Interceptor：拦截器，能够监控、重写、重试调用的机制。用来添加、移除、转换请求和响应的头部信息。


## 网络封装模块

> 具体内容可以在慕课网查看教程：[https://www.imooc.com/learn/732](https://www.imooc.com/learn/732)

### 好处

> - 强大的复用性
> - 与业务逻辑完全隔离
> - 强大的扩展性

### 思路

> - 封装公共的OkhttpClient
> - 封装通用的请求创建类CommonRequest
> - 封装通用的响应解析类JsonCommonRequest


### 源码学习

具体源码我就不在这里说了，作为一个小菜鸟，大家可以利用网上的资源进行学习。


### 注意

网络请求都要在子线程中执行。

```java
new Thread(new Runnable() {
            @Override
            public void run() {
                   //...网络请求
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                           //...UI更新
                        }
                    });
            }
```


