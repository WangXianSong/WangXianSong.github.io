---
layout: post
title:  "IntentServise 的深入学习"
date:  2018-3-13 14:03:17
categories: Android
tags:   学习笔记

---
* content
{:toc}



## 前言


在开启对 IntentServise 的学习之前，我们先来回顾 **Service** 的回调方法有：onCreate、onStart、onStartCommand、onBind、onDestroy，这些都是运行在**主线程**中的。

当我们通过 startService 启动 Service 之后，我们就需要在 Service 的 onStartCommand 方法中写代码完成工作，如果我们需要在此处完成一些网络请求或 I/O 等耗时操作，这样就会**阻塞主线程 UI 线程无响应**，从而出现 ANR 现象。

为了解决这种问题，最好的办法就是在 onStartCommand 中创建一个新的线程，并把耗时代码放到这个新线程中执行。Google 为此提供了 IntentService 来**简化**带有工作线程的 Service。
​
### 本文的内容有：
- 为什么要使用 IntentService
- IntentService 是什么？
- IntentService 的特点
- IntentService 的使用方法
- IntentService 源码解析

## IntentServise 是什么？

IntentService 是一个用于 Service 处理异步请求（以 Intents 表示）的基类。外界通过 startService(Intent) 呼叫发送请求。该服务根据需要启动，使用工作线程轮流处理每个 Intent，并在其停止工作时自行停止。

![](https://i.imgur.com/gNo6VcP.png)

## IntentServise 的特点

1. 它是一种特殊的 Service，继承了 Service 并且它是一个**抽象**类。
2. 它可以用于执行后台**耗时的任务**，当任务执行完毕自动停止。
3. 它拥有**较高的优先级**，不易被系统杀死（继承自Service的缘故）。
4. 它内部通过 HandlerThread 和 Handler 实现**异步**操作。
5. 可以多次启动 IntentServise，每个耗时操作都会以工作队列的方式在 **onHandleIntent** 回调方法中执行。

## IntentServise 使用方法

创建 IntentServise 时，只需实现 **onHandleIntent** 和**构造方法**，onHandleIntent 为异步方法，可以执行耗时操作。

```java
public class MyIntentService extends IntentService {

    public MyIntentService(String name) {
        super("MyIntentService");
    }

    protected void onHandleIntent(Intent intent) {
        //可以做些耗时操作
    }
}
```
## IntentServise 源码解析

IntentServise的源码只有 170+ 行，去掉边角料可能没有 100 行代码。所以还是老套路，通过源码来了解 IntentServise 背后的实现原理，还可以加深对 IntentServise 的认识。首先一下是 **onCreate** 方法的源码：
```java
public void onCreate() {
    super.onCreate();
    HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
    thread.start();

    mServiceLooper = thread.getLooper();
    mServiceHandler = new ServiceHandler(mServiceLooper);
}
```
当 IntentService 被启动，onCreate 方法会被调用，同时会创建一个 HandlerThread，然后使用它 Looper 来构造一个 Handler 对象 mServiceHandler，通过 mServiceHandler 发送消息最终都会在 HandlerThread 中执行。
```java
    public int onStartCommand(Intent intent, int flags, int startId) {
        onStart(intent, startId);
        return mRedelivery ? START_REDELIVER_INTENT : START_NOT_STICKY;
    }
```
IntentService 还会调用 onStartCommand 方法，并且在其中通过 **onStart** 处理每个后台任务Intent。
```java
    public void onStart(@Nullable Intent intent, int startId) {
        Message msg = mServiceHandler.obtainMessage();
        msg.arg1 = startId;
        msg.obj = intent;
        mServiceHandler.sendMessage(msg);
    }
```
IntentService 通过 mServiceHandler 发送消息，这个消息会在 HandlerThread中处理。以下是 **ServiceHandler** 的源码：
```java
private final class ServiceHandler extends Handler {
    public ServiceHandler(Looper looper) {
        super(looper);
    }
    public void handleMessage(Message msg) {
        onHandleIntent((Intent)msg.obj);
        stopSelf(msg.arg1);
    }
}
```
在 ServiceHandler 中的 handleMessage 回调了 **onHandleIntent** 这个抽象方法，它需要我们在子类中实现，它的作用是做一些**异步的操作**。
```java
protected abstract void onHandleIntent(Intent intent);
```
在 onHandleIntent 方法执行完这个任务后，IntentService 会主动调用** stopSelf(msg.arg1)**方法来尝试停止服务，并且保证执行完最后一个任务才销毁。

onHandleIntent 内部的原理也是通过 Handler 去发送消息给 HandlerThread，然后去处理耗时操作。

**stopSelf(msg.arg1) 与 stopSelf()的区别：**
- stopSelf(msg.arg1)：会等待所有消息都处理完毕后才终止服务，在尝试终止之前会判断最近启动服务的次数是否和 startId相等。如果相等就立刻停止，否则不停止。
- stopSelf()：会立即停止服务，而这个时候可能还可能有其他消息未处理。

## 总结

IntentService 的本质就是封装了 HandlerThread 可以在线程当中开启循环的一个线程，同时利用 Handler 来发送消息构成的异步框架。

 
## 结束语

IntentServise 是异步请求这个只是模块里面最后的一个知识点，接下来我会总结**网络编程**方面的内容，如果喜欢我的内容可以点赞支持！

## 附录：参考资料

- 《Android 开发艺术探索》任玉刚
- 慕课网-BAT大咖助力 全面升级Android面试
- http://www.jb51.net/article/76490.htm



