---
layout: post
title:  "《Android开发艺术探索》第二章总结"
date:  2017-11-10 21:59:36
categories: Android
tags: Android
---
* content
{:toc}




　　在上一章[《 Android 开发艺术探索》第一章总结](http://xsong.wang/2017/10/11/AndroidArt/)学习了 Activity 的生命周期以及启动模式的知识点，这一次我们就开始学习 Android 中的 IPC 机制，也就是进程间通信。

<br />


# 第1章 Activity 中的 IPC 机制

## 1、Android IPC 简单理解

　　IPC 是 Inter-Process Communication 的首字母缩写，也就是进程间通信，指的是两个进程之间进行数据交换的过程。

<br />

　　**使用场景：**由于某些原因应用 **自身需要** 采用多进程模式来实现，或者为了加大一个应用可使用的 **内存**，因为 Android 对当个应用可使用的最大内存做了限制。

<br />
<br />

## 2、Android 中的多进程模式
　　在 Android 中使用多进程只有一种方法，那就是给四大组件 ( Activity、Service、Receiver、ContentProvider ) 在 AndroidMenifest 中指定 android : process 属性，除此之外没有其他办法。
<br />

```xml
    <activity
           android:name=".MainActivity"
            android:process=":remote">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".SecondActivity"
            android:process="songsong.com.ipctest_2.remote">
        </activity>
```

<br />
**如何在Android中创建多进程：**

 - 进程名以 " : " 开头的进程前面自动加上包名，是一种简写的命名方式，是属于当前应用的**私有进程**，其他应用的组件不可以和它跑在同一个进程中。
 - 不以 "："  开头的进程名属于**全局进程**，是一种完整的命名方式，其他应用通过shareUID 方式可以和他跑在同一个进程中。
<br />

**使用多进程会造成如下几个方面的问题：**

　　所有运行在不同进程中的四大组件，只要它们之间需要通过内存来共享数据，都会共享失败。使用多进程会造成如下几个方面的影响：

- 静态成员和单例模式完全失效；
- 线程同步机制完全失效；
- SharedPreferences 的可靠性下降；
- Application 会多次创建。
<br />

**注意：**

　　在多进程模式中，不同进程的组件的确会拥有独立的虚拟机、Application 以及内存空间，这会给实际开发带来很多困扰，是尤其需要注意。

<br /><br />

## 3、IPC 基础概念介绍：
### Serializable 接口：
### Parcelable 接口：
### Binder ( Binder 的工作机制 )：

<br /><br />


## 4、Android 中的 IPC 的方式：

### 4.1 Bundle：

　四大组件中的三大组件( Activity、Service、Receiver )都是支持在Intent中传递 Bundle 数据的，由于 Bundle 实现了 Parcelable 接口，所以它可以方便地在不同的进程间传输。

**使用方法：**

- 装载数据：

```java
Bundle mBundle = new Bundle();
mBundle.putString("DataTag", "要传过去的数据");
Intent intent = new Intent();
intent.setClass(MainActivity.this, SecondActivity.class);
intent.putExtras(mBundle);
startActivity(intent); 
 ```

- 目标 Activity 解析数据

```java
Bundle bundle = getIntent().getExtras();   //得到传过来的bundle
String data = bundle.getString("DataTag");   //读出数据
```

<br /><br />

### 4.2 文件共享
　　两个进程通过读 / 写同一个文件来交换数据，A存进B读取；文件共享方式适合在对数据**同步要求性不高**的进程之间进行通信，并且要妥善处理**并发读写**的问题，或者处理好**线程同步**问题。

　　SharedPreferences 是个特例，虽然也是文件的一种，但系统在内存中有一份 SharedPreferences 文件的缓存，因此在多线程模式下，系统对他的读 / 写就变得不可靠，面对高并发读写 SharedPreferences 有一定几率会丢失数据，因此不建议在多进程通讯时采用 SharedPreferences 。


<br /><br />

### 4.3 Messenger

　　Messenger 是一种轻量级的 IPC 方案，底层实现是 AIDL，他对 AIDL 进行了封装，Messenger 服务端是以串行的方式来处理客户端的请求的，不存在并发执行的情形。

　　**实现的步骤：**

　　1.**服务端进程**：首先，创建 Service 来处理客户端的连接请求，同时创建 Handler 并通过它来创建 Messenger 对象，然后在 Service 的onBind 中返回这个 Messenger 对象底层的 Binder即可；

　　2.**客户端进程**：首先，绑定服务端的 Service，绑定成功后用服务端返回的 IBinder 对象创建一个 Messenger，通过这个 Messenger 就可以向服务器发送消息了，消息类型为 Message 对象。如果需要服务端能够回应客户端，就和服务端一样，我们还需要创建一个 Handler 并创建新的 Messenger，并把这个 Messenger 对象通过 Message 的 replyTo 参数传递给服务器，服务器通过这个 replyTo 参数就可以回应客户端。

　　**注意：**
　　Messenger 是以串行的方式处理客户端发来的消息，如果大量的消息同时发送到服务端，服务端仍然只能一个个处理，如果有大量的并发请求，那么用 Messenger 就不太适合了。

　　M**essenger 的工作原理：**
<br />
![](https://i.imgur.com/IayPy4M.jpg)
<br />
<br />

### 4.4 AIDL

### 4.5 ContentProvider
### 4.5 Socket
## 5、Binder连接池

## 6、选择合适的IPC方式
