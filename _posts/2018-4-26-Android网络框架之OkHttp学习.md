---
layout: post
title:  "Android网络框架之OkHttp学习"
date:  2018-4-26 22:55:53
categories: 第三方框架
tags: 第三方框架

---
* content
{:toc}







## 前言



Github 地址：https://github.com/square/okhttp

Gradle：api 'com.squareup.okhttp3:okhttp:3.10.0'

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


## 1.异步 GET 请求

- 第一步：创建  OkHttpClient 对象，使用到了 Builder 设计模式
- 第二步：创建 Request 对象，封装了一些请求报文的信息
- 第三步：将 Request 封装成 Call 对象。
	调用 OkHttpClient 的 newCall 方法创建一个 call对象，
	把 request 作为参数传进来。call 是 request 和 response 之间的桥梁。
- 第四步：同步、异步请求。异步方法内容接收 new Callback 对象，Okhttp会让实际的网络请求在新的工作线程中执行。在请求成功之后会回调 onResponse 方法，会进行成功之后的数据处理，而请求失败或者请求取消就会回调 onFailure 方法。


```java
OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder().url("http://www.baidu.com/").build();
Call call = client.newCall(request);

//1.同步 execute 获取数据
Response response = call.execute();
String responseData = response.boby().string();
//Response response = client.newCall(request).execute()//简写第三四步

//2.异步 enqueue 获取数据
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

## 5.异步 POST 请求

```java
RequestBody requestBody = new FormBody.Builder()
        .add("username", "admin")
        .add("password", "123456")
        .build();
Request request = new Request.Builder()
        .url("https://www.imooc.com/")
        .post(requestBody)//添加进来
        .build();
```

## 2.Okhttp 需要注意的地方

- **同步请求**：发送请求后，就会进入阻塞状态，直到收到相应。(这是同步和异步请求的最大不同之处)

- **异步请求**：onResponse 和 onFailure 都是在工作线程中执行的。

## 3.Okhttp 同步和异步的区别

- 发起请求的方法调用：execute/enqueue(new Callback)；
- 阻塞线程与否：阻塞/开启新线程；


## 4.Okhttp 流程图

![](https://i.imgur.com/3FRJLeY.png)
 
