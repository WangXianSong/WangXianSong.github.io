---
layout: post
title:  "Android数据解析Gson"
date:  2018-6-3 22:55:26
categories: 第三方框架
tags: 第三方框架

---
* content
{:toc}


## JSON 简介

### 1.JSON 的定义

- JSON 是一种与开发语言无关、轻量级的数据格式，全称为 JavaScript Object Notation。

- JSON 是行业内使用最为广泛的数据传输格式。当我们客户掉需要调用服务端 API 的时候，大多数使用 JSON 作为传输数据的格式。

- JSON 中文网站：[http://www.json.org/json-zh.html](http://www.json.org/json-zh.html)

### 2.JSON 的优点

**优点**：易于人的阅读和编写，易于程序解析与生产。

### 3.标准的数据表示

- **数据结构**：
	- **Object**：对象是一个无序的“名称/值对”的集合，对象由“{”和“}”表示开始和结束，名称和值对之间用“:”来表示关联，每对“名称/值对”之间用“,”来分割。
	- **Array**：数组是值对的有序集合。一个数组以“[”和“]”来表示开始和结束。值之间使用“,”（逗号）分隔。

- **基本类型**：string、number、true、false、null。
 - **array**：值是一个整数类型的数组；
 - **boolean**：值是一个布尔型的值；
 - **null**：JSON 数据格式中的值允许为 null；
 - **number**：值一个数值，可以是整数也可以是浮点数；
 - **object**：值是一个 JSON 对象；
 - **string**：值是一个字符串；

### 4.简单例子

```
{
	"name": "SongSong",
	"age": "25",
	"birthday": "2018-6-17",
	"major": ["JAVA", "PHP"],
	"has_job": false,
	"has_car": null
}
```



## Java中两种常用处理JSON的方式

## Gson的使用