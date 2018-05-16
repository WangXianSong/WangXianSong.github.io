---
layout: post
title:  "Android网络框架之OkHttp学习"
date:  2018-5-16 22:55:53
categories: 第三方框架
tags: 第三方框架

---
* content
{:toc}





上篇笔记里讲到 HTTP 协议的相关内容，这一篇笔记是关于 OkHttp 网络框架的简单使用。而在学习网络框架之前对基础知识的了解是非常有必要的，特别在面试的过程中。

目前比较热门的网络框架有：android-async-http、xUtils、Volley、Retrofit和Okhttp，为什么要学习 Okhttp 呢？

### Okhttp的好处

- 支持 SPDY、HTTP2.0，共享同一个 Socket 来处理同一个服务器的所有请求。
- 如果 SPDY 不可用，则通过连接池来减少请求延迟。
- 无缝的支持 GZIP 来减少数据流量。
- 缓存响应数据来减少重复的网络请求。
- 可从很多常用的连接问题中自动恢复。
- 使用起来非常简单。

## OkHttp 的使用
> 
> **Github 地址**：https://github.com/square/okhttp
> 
> **配置方法**：api 'com.squareup.okhttp3:okhttp:3.10.0'


### OkHttp的类型和方法

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

### Okhttp 需要注意的地方

- **同步请求**：发送请求后，就会进入阻塞状态，直到收到相应。(这是同步和异步请求的最大不同之处)
- **异步请求**：onResponse 和 onFailure 都是在工作线程中执行的。

### Okhttp 同步和异步的区别

- 发起请求的方法调用：execute/enqueue(new Callback)；
- 阻塞线程与否：阻塞/开启新线程；


### Okhttp 流程图

![](https://i.imgur.com/xrZQlFM.png)
 
### Interceptor 拦截器


















## 网络封装模块

**好处**：

- 强大的复用性
- 与业务逻辑完全隔离
- 强大的扩展性

**思路**：

- 封装公共的OkhttpClient
- 封装通用的请求创建类CommonRequest
- 封装通用的响应解析类JsonCommonRequest


## 源码

网络请求客户端okhttpClient，通过Builder创建Request对象，Client.newCall返回的是RealCall对象。在


## 参考

[okhttp框架解析与应用](https://www.imooc.com/video/13048)












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



## 多路复用机制

## 重连机制

## 核心类

### Route 路由类

Address 包含所有要请求的端口号、服务器IP地址等等。

### ResponseBody 响应体

所有的存储都是通过byte字节

### Response 响应

headers 响应头 
ResponseBody 响应体

### RequestBody 请求体
FormBody 上传Key-Value类型
MultipartBody  除了KV类型，还可以上传对象类型

### Request 请求

### Call 任务

### newCall 
实现Call接口
调用HttpEngine发送请求

### Callback 
回调结果给onResponse

### HttpEngine

### Dispatcher 任务调度

### Intetceptor 拦截器