---
layout: post
title:  "第二章 Activity 中的 IPC 机制"
date:  2017-10-3 21:59:36
categories: Android
tags: Android
---
* content
{:toc}


>此篇文章为《Android开发艺术探索》第二章“Activity 中的 IPC 机制”的总结，内容比较多，学起来比[第一章](http://xsong.wang/2017/10/11/AndroidArt/)要复杂得多！多很多！

>本章主要的内容有： IPC 简单介绍、Android 中的多进程模式、IPC 基础概念介绍、Android 中的 IPC 方式、Binder 连接池、选用合适的 IPC 方式。

  





## 1、Android IPC 简单理解

　　IPC 是 Inter - Process Communication 的首字母缩写，也就是进程间通信，指的是两个进程之间进行数据交换的过程。
<br />
　　**使用场景：** 由于某些原因应用<font color="#dd0000">自身需要</font>采用多进程模式来实现，或者为了<font color="#dd0000">加大一个应用可使用的内存</font>，因为 Android 对当个应用可使用的最大内存做了限制。
<br />
<br />
## 2、Android 中的多进程模式

**2.1 如何在Android中创建多进程：**

　　在 Android 中使用多进程只有一种方法，那就是给四大组件 ( Activity、Service、Receiver、ContentProvider ) 在 AndroidMenifest 中指定 android : process 属性，除此之外没有其他办法。
<br />

 - 进程名以 " : " 开头的进程前面自动加上包名，是一种简写的命名方式，是属于当前应用的**私有进程**，其他应用的组件不可以和它跑在同一个进程中。
 - 不以 "："  开头的进程名属于**全局进程**，是一种完整的命名方式，其他应用通过shareUID 方式可以和他跑在同一个进程中。
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

**2.2 使用多进程会造成如下几个方面的问题：**

　　所有运行在不同进程中的四大组件，只要它们之间需要通过内存来共享数据，都会共享失败。使用多进程会造成如下几个方面的影响：



>  - 静态成员和单例模式完全失效；
>  - 线程同步机制完全失效；
>  - SharedPreferences 的可靠性下降；
>  - Application 会多次创建。

<br />

**2.3 注意：**

　　在多进程模式中，不同进程的组件的确会拥有独立的虚拟机、Application 以及内存空间，这会给实际开发带来很多困扰，是尤其需要注意。
<br />
<br />
## 3、IPC 基础概念介绍：
### Serializable 接口：
### Parcelable 接口：
### Binder ( Binder 的工作机制 )：
 　　<font color="#dd0000">(待完善...)</font>

<br /><br />


## 4、Android 中的 IPC 的方式：

### 4.1 Bundle：

　　四大组件中的三大组件( Activity、Service、Receiver )都是支持在 Intent 中传递 Bundle 数据的，由于 Bundle 实现了 Parcelable 接口，所以它可以方便地在不同的进程间传输。

**4.1.1 使用方法：**

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
<br />
<br />
### 4.2 文件共享
　　两个进程通过读 / 写同一个文件来交换数据，A 存进 B 读取；文件共享方式适合在对数据**同步要求性不高**的进程之间进行通信，并且要妥善处理**并发读写**的问题，或者处理好**线程同步**问题。

　　SharedPreferences 是个特例，虽然也是文件的一种，但系统在内存中有一份 SharedPreferences 文件的缓存，因此在多线程模式下，系统对他的读 / 写就变得不可靠，面对高并发读写 SharedPreferences 有一定几率会丢失数据，因此不建议在多进程通讯时采用 SharedPreferences 。
<br />
<br />
### 4.3 Messenger

　　Messenger 是一种轻量级的 IPC 方案，底层实现是 AIDL，他对 AIDL 进行了封装，Messenger 服务端是以串行的方式来处理客户端的请求的，不存在并发执行的情形。

**4.3.1 实现的步骤：**

　　**服务端进程：**首先，创建 Service 来处理客户端的连接请求，同时创建 Handler 并通过它来创建 Messenger 对象，然后在 Service 的 onBind 中返回这个 Messenger 对象底层的 Binder 即可；

　　**客户端进程：**首先，绑定服务端的 Service，绑定成功后用服务端返回的 IBinder 对象创建一个 Messenger，通过这个 Messenger 就可以向服务器发送消息了，消息类型为 Message 对象。如果需要服务端能够回应客户端，就和服务端一样，我们还需要创建一个 Handler 并创建新的 Messenger，并把这个 Messenger 对象通过 Message 的 replyTo 参数传递给服务器，服务器通过这个 replyTo 参数就可以回应客户端。

**4.3.2 注意：**

　　Messenger 是以串行的方式处理客户端发来的消息，如果大量的消息同时发送到服务端，服务端仍然只能一个个处理，如果有大量的并发请求，那么用 Messenger 就不太适合了。

**4.3.3 Messenger 的工作原理：**
![](https://i.imgur.com/IayPy4M.jpg)
<br />
<br />
### 4.4 AIDL

**4.4.1 "感性"的实现过程：**

　　**服务端**首先创建一个 Service 用来监听客户端的连接请求，然后创建一个 AIDL 文件，将暴露给客户端的接口在 AIDL 文件中声明，最后在 Service 中实现这个 AIDL 接口即可。

　　**客户端**首先绑定服务端的 Service，绑定成功后，将服务端返回的 Binder 对象转化成AIDL接口所属的类型，调用相对应的 AIDL 中的方法。

　　<font color="#dd0000">之所以说是感性的实现过程是因为在实际过程中远不止这么简单，对于目前的我来说，暂时还是弄不清楚的。所以我决定先把这个知识点放在这里，回头再来学习。</font>

**4.4.2 AIDL支持的数据类型：**

> - 基本数据类型（int、long、char、boolean、double）；
> - String、CharSequence；
> - List：只支持 ArrayList ，里面的元素必须都能被 AIDL 所支持；
> - Map：只支持HashMap，里面的元素（key 和 value）必须都能被 AIDL 所支持；
> - Parcelable 所有实现了 Parcelable 接口的对象；
> - AIDL：所有 AIDL 接口本身也可以在 AIDL 文件中使用。

　　以上 6 种数据类型就是 AIDL 所支持的所有类型，其中，自定义的 Parcelable 对象和 AIDL 对象必须要显式 import 进来（即使在同一个包）。

 　　<font color="#dd0000">(待完善...)</font>
<br />
<br />
### 4.5 ContentProvider 

　　ContentProvider （内容提供器）是 Android 专门用于不同应用之间进行数据共享的方式，天生适合跨进程通讯，底层同样采用 Binder 实现。

　　ContentProvider 的用法一般两种，一种是现有的内容提供器来读取操作相应程序中的；另一种是创建自己的内容提供器给我们程序的数据提供外部的访问接口。

**4.5.1 读取系统联系人：**

```java

Cursor cursor = getContentResolver.query{
Uri,
projection,
selection,
selectionArgs,
sortOrder};

if(cursor!=null){
	while (cursor.moveToNext(){
	String column1 = cursor.getString(cursor.getColumnIndex("column1"));
	int column2 = cursor.getInt(cursor.getColumnIndex("column2"));	
	}
	cursor.close();
}


ContentValues values = new ContentValues();
values.put("column1","text");
values.put("column2","1");
getContentResolver().insert(uri,values)

```

**4.5.1 创建自己的内容提供器：**

　　自定义 ContentProvider 很简单，只需要继承 ContentProvider 类并实现六个抽象方法即可。

```java
    public boolean onCreate()
    public Uri insert(Uri uri, ContentValues values) 
    public int delete(Uri uri, String selection, String[] selectionArgs)
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) 
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) 
    public String getType(Uri uri)
```

> - onCreate： 代表 ContentProvider 的创建，一般可以做一些初始化的工作。除了 onCreate 由系统回调并运行在主线程里，其余五个方法都由外界回调并运行在 Binder 线程池中。
> - getType：用来返回一个 Uri 请求所对应的 MIME 类型(媒体类型)，比如图片、视频等。


　　接着需要注册这个 ContentProvider，其中  android:authorities 是 ContentProvider 的唯一标识，通过这个属性外部应用可以访问我们的 ContentProvider ，因此  android:authorities 必须是唯一的，这里建议在命名的时候加上包名前缀。

```xml
        <provider
            android:name=".MyContentProvider"
            android:authorities="com.songsong.MyContentProvider"
            android:enabled="true"
            android:exported="true">
        </provider>
```

　　其他的应用程序通过 ContentResolver 来操作 ContentProvider 所暴露的数据：

　　getContentResolver()：一旦在程序中获得 ContentResolver 对象之后，接下来就可调用 ContentResolver 的如下方法来操作数据：

> - insert(Uri uri ,ContentValues values ):
> - delete(Uri uri , String where , String[] selectionArgs):
> - update( Uri uri , ContentValues values ,  String where , String[] selectionArgs):
> - query(Uri uri , ContentValues values ,  String where , String[]selectionArgs,String sortOrder):

<br />
<br />
### 4.6 Socket

参考网站：[https://www.cnblogs.com/mq0036/p/3812755.html](https://www.cnblogs.com/mq0036/p/3812755.html)

　　Socket 称为“套接字”，是**网络通信**中的概念，它分为“流式套接字”和“用户数据报套接字”两种，分别对应网络的传输控制层中的 TCP 和 UDP 协议。

- TCP 协议是面向连接的协议，提供稳定的双向通信功能，TCP 连接的建立需要经过“三次握手”才能完成，为了提供稳定的数据传输功能，其本身提供了超时重传机制，因此具有很高的稳定性。
- UDP 协议是无连接的，提供不稳定的单向通信功能，虽然 UDP 也可以实现双向通信功能。它的缺点是不保证数据一定能够正确传输，尤其是在网络赌堵塞的情况下。



**4.6.1使用 TCP 协议通讯的 Socket 的主要流程：**

![](https://i.imgur.com/bGuJIFE.png)


**4.6.2 TCP 协议通过三次握手建立一个可靠的连接：**

![](https://i.imgur.com/vcjM2qs.jpg)

> - 第一次握手：客户端尝试连接服务器，向服务器发送 syn 包（同步序列编号 Synchronize Sequence Numbers），syn = j，客户端进入 SYN_SEND 状态等待服务器确认。

> - 第二次握手：服务器接收客户端 syn 包并确认（ack = j +1），同时向客户端发送一个 SYN 包（syn = k），即 SYN + ACK 包，此时服务器进入 SYN_RECV 状态。

> - 第三次握手：第三次握手：客户端收到服务器的 SYN +ACK 包，向服务器发送确认包 ACK( ack = k + 1），此包发送完毕，客户端和服务器进入 ESTABLISHED 状态，完成三次握手。

**4.6.3 服务器 Socket 与客户端 Socket 建立连接的部分其实就是大名鼎鼎的三次握手**

![](https://i.imgur.com/xjuvsGu.png)

## 5、Binder连接池

　　通过 BinderPool 的方式将 Binder 的控制与Service 本身解耦，同时只需要维护一份 Service 即可。这里用到了 CountDownLatch，大概解释下用意：线程在 await 后等待，直到 CountDownLatch 的计数为 0，BinderPoo l里使用它的目的是为了保证 Activity 获取 BinderPool 的时候 Service 已确定 bind 完成。


## 6、选择合适的IPC方式

![](https://i.imgur.com/3JCtdoM.png)

## 7、总结

　　说实话，刚学完第一章比较基础的知识，突然急转弯来到进程间通信 IPC ( 知识点的小高坡 )，难免会有点吃不消的感觉。虽然难，但也学到很多以往没注意到知识。希望，在接下来的 View 事件的学习能吸收得更好！