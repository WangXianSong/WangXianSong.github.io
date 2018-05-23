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

## Retrofit入门

我们通过一个小案例来学习Retrofit，写一个网络请求获取豆瓣 Top250 榜单为例。

### 1.导包

- [**GitHub**]：[https://github.com/square/retrofit](https://github.com/square/retrofit)
- [**build.gradle**]：implementation 'com.squareup.retrofit2:retrofit:2.4.0'

### 2.实例

```java
public static final String BASE_URL = "https://api.douban.com/v2/movie/";
Retrofit retrofit = new Retrofit.Builder() 
       .baseUrl(BASE_URL) 
       .addConverterFactory(GsonConverterFactory.create())
       .build();
```

> 说明1：Retrofit2 的 baseUlr 必须以 /（斜线） 结束，不然会抛出一个 IllegalArgumentException。
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