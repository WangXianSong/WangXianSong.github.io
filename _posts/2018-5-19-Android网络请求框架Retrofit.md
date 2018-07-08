---
layout: post
title:  "Android网络请求框架Retrofit"
date:  2018-5-17 20:19:52
categories: 第三方框架
tags: 第三方框架

---
* content
{:toc}



参考文章：

https://www.jianshu.com/p/5bc866b9cbb9
https://www.jianshu.com/p/308f3c54abdd

## 一、Retrofit入门

先了解Retrofit是个怎样的框架，它到底有什么神奇之处，然后通过一个小案例来获取豆瓣 Top250 榜单为例。

- Retrofit 是 Square 公司推出的 HTTP 框架，它将一个基本的Java接口通过动态代理的方式翻译成一个HTTP请求，同时 Retrofit 是 OKHttp 网络请求的封装。

- App 应用程序通过 Retrofit 请求网络，实际上是使用 Retrofit 接口层封装请求参数，完成各种转换和适配工作，之后 OKHttp 来完成剩余的网络请求工作。

- 在服务器返回数据之后，OKHttp 将原始的结果交给 Retrofit，再根据用户的需求对结果进行解析。

- Retrofit 使用到了非常多的设计模式，意味它有很好的扩展性，可以和Rxjava、Json和OKHttp这些主流库无缝对接，这就是大家热爱用 Retrofit 的原因。


## 二、Retrofit使用七步

### 1.依赖包导入、添加网络权限

- **Retrofit开源库**：implementation 'com.squareup.retrofit2:retrofit:2.4.0'
- **OKHttp网络库**：implementation 'com.squareup.okhttp3:okhttp:3.10.0'
- **Gson数据解析器**：implementation 'com.google.code.gson:gson:2.8.5'

- **申请网络权限**：<uses-permission android:name="android.permission.INTERNET"/>

### 2.创建接收服务器返回的数据的类

### 3.创建用于描述网络请求的接口

用于描述网络请求的接口，采用注解模式（动态代理机制）描述和配置网络请求参数。

- @GET、@POST：的确请求方式
- @Path：请求URL的字符替代
- @Query：要传递的参数
- @QueryMap：包含多个@Query注解参数
- @Body：添加实体类对象
- @ForUrlEncoded：URL编码

### 4.创建Retrofit实例

1.通过 Builder 构建 Retrofit；
2.使用 baseurl 设置网络请求的Url地址，与接口中的Path拼接成完整的Url；
3.设置数据解析器，Retrofit不仅支持一种解析模式，所以要添加转换器工厂给它支持不同类型的支持。
4.网络请求适配器，Retrofit也支持多种网络请求适配器方式，比较常用的Rxjava。

### 5.创建网络请求接口实例

1.通过retrofit的create方法创建接口实例，并传入接口的字节码文件。
2.创建后直接调用接口中的方法，获取call的实例。

### 6.发送网络请求(异步同步)

### 7.处理服务器返回的数据



## Retrofit 代理设计模式

- 代理模式：为其他对象提供一种代理，用以控制这个对象访问。(例子：海外代购)

### 静态代理

在静态代理中有三个角色：抽象对象、目标对象和代理对象。

![](https://i.imgur.com/ymw2QcF.png)

- 抽象对象AbstractObject：声明了目标类，以及代理类对象的共同接口。在抽象对象中的operation()方式是目标对象和代理对象共同需要继承的。
- 目标对象RealObject：
- 代理对象ProxyObject：





### 动态代理









### 2.实例

```java
public static final String BASE_URL = "https://api.douban.com/v2/movie/";
Retrofit retrofit = new Retrofit.Builder() 
       .baseUrl(BASE_URL) 
       .addConverterFactory(GsonConverterFactory.create())
       .build();
```

> 说明1：Retrofit2 的 baseUlr 必须以 /（斜线） 结束，不然会抛出一个 IllegalArgumentException。
> 
> 说明2：GsonConverterFactory 是默认提供的 Gson 转换器，Retrofit 也支持其他的一些转换器，详情请看官网Retrofit官网。


### 3.接口

```java
public interface MovieService { 
 //获取豆瓣Top250 榜单 
 @GET("top250")
 Call<MovieSubject> getTop250(@Query("start") int start,@Query("count")int count);
}
```

> 说明3：@GET 标签代表使用get请求方式，标签内的是尾址top250，完整的地址是(baseUrl+尾址)，
> 
> 说明4：参数是@Query，参数多的话可以用@QueryMap标签，接收一个Map。

### 4.接口调用

```java
//获取接口实例
MovieService movieService = retrofit.create(MovieService.class); 
//调用方法得到一个Call ,并传参数
Call<MovieSubject> call = movieService.getTop250(0,20);

 //进行网络请求 
call.enqueue(new Callback<MovieSubject>() {
       @Override 
       public void onResponse(Call<MovieSubject> call, Response<MovieSubject> response) { 
            mMovieAdapter.setMovies(response.body().subjects);     
            mMovieAdapter.notifyDataSetChanged(); 
       } 
      @Override 
      public void onFailure(Call<MovieSubject> call, Throwable t) { 
         t.printStackTrace(); 
      } 
});

//以上是异步方式请求，还有同步方式execute(),返回一个Response,代码如下
//Response<MovieSubject> response = call.execute();
```

以上是Get请求，还有Post请求：
```java
public interface MovieService { 
        //获取豆瓣Top250 榜单 
       @FormUrlEncoded
       @POST("top250") 
       Call<MovieSubject> getTop250(@Field("start") int start, @Field("count") int count);
```

> 注意：使用POST 方式时注意2点，1，必须加上 @FormUrlEncoded标签，否则会抛异常。2，使用POST方式时，必须要有参数，否则会抛异常。

## Retrofit注解详解

## Gson与Converter