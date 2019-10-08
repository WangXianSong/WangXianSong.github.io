---
layout: post
title:  "Android学习笔记之Broadcast"
date:  2019-10-09 10:36:21
categories: Android
tags: Android

---
* content
{:toc}


### 内容目录

1. 广播的定义
1. 广播的用途（`信息传输与共享`和`通知`）
1. 广播的使用场景
1. 广播主要的种类（`普通广播`、`有序广播`和`本地广播`）
1. 注册广播接收 (`静态接收`和`动态接收`)
1. 广播的发送（`sendBroadcast`和`sendOrderBroadcast`）
1. 广播内部实现机制
1. AMS 是什么？
1. 本地广播 LocalBroadcastManager
1. 全局广播的缺点
1. BroadcastReceiver 和 LocalBroadcastReceiver 区别
1. BroadCastReceiver 的生命周期
1. 广播传输的数据是否有限制，是多少，为什么要限制？

![BroadcastReceiver.png](https://upload-images.jianshu.io/upload_images/6491732-e0d0ffcb739bd2e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 1.广播的定义

- 在 Android 中，Broadcast 是一种在`应用程序之间传输信息的机制`，要发送的广播内容是一个 Intent，这个 Intent 中可以携带我们要传送的数据。(数据小于1MB)

### 2.广播的用途

1. 广播实现了不同程序之间的`信息传输与共享`，只要和发送广播的 action 相同的接收者，都能接收到这个广播。典型的应用就是 android 自带的短信，电话等等广播，只要我们实现了他们的 action 的广播，那么我们就能接收他们的数据了，以便做出一些处理。比如说拦截系统短信，拦截骚扰电话等。
2. 作为`通知`的作用，比如在 Service 中要通知主程序、更新主程序的 UI 等，因为 Service 是没有界面的，所以不能直接获得主程序中的控件，这样我们就只能在主程序中实现一个广播接收者专门用来接收service发过来的数据和通知了。

### 3.广播的使用场景

1. 同一app内部的同一组件内的消息通信（单个或多个线程之间）(可用handler解决)；
1. 同一app内部的不同组件之间的消息通信（单个进程）(可用EventBus)；
1. 同一app具有多个进程的不同组件之间的消息通信；
1. 不同app之间的组件之间的消息通信；
1. Android系统在特定情况下与App之间的消息通信。

### 4.广播主要的种类

- **普通广播Normal Broadcast**：`异步`执行的广播，所有接收者在同一时刻收到这条广播消息。效率高，没有先后顺序，无法截断。属于全局广播。调用 `sendBroadcast()`发送，最常用的广播。

- **有序广播Ordered Broadcast**：`同步`执行的广播，发出去的广播会被广播接收者按照顺序接收，广播接收者按照Priority属性值从大-小排序，Priority属性相同者，动态注册的广播优先，广播接收者还可以选择对广播进行截断和修改。调用`sendOrderedBroadcast()`发送。

- **本地广播Local  Broadcast**：App应用内广播可理解为一种`局部广播`，广播的发送者和接收者都同属于一个App。相比于全局广播（普通广播），App应用内广播优势体现在：安全性高 & 效率高。对于LocalBroadcastManager方式发送的应用内广播，只能通过LocalBroadcastManager`动态注册`，不能静态注册。调用`sendBroadcast`发送。

### 4、注册广播接收 (静态和动态)


#### 4.1两种注册方法的区别

- **动态注册**的接收器必须要在程序`启动之后才能`接收到广播；
- **静态注册**的接收器即便程序`未启动也能`接收到广播，比如想接收到手机开机完成后系统发出的广播就只能用静态注册了。

#### 4.2静态注册具体步骤

- **静态注册**：将广播写在 AndroidMainifest.xml 文件当中，特点是：常驻系统，不受组件生命周期影响，即便应用退出，广播还是可以被接收，耗电、占内存。
- 第一步：`创建继承 BroadcastReceiver`，然后重写具体实现onReceive()，由于生命周期短，耗时工作应该发给Service，onReceive()不要开启子线程。

```java
public class MyReceiver extends BroadcastReceiver {
        //onReceive 不能做过多的耗时操作，10秒没响应就ANR
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "boot complete", Toast.LENGTH_SHORT).show();
    }
}
```
- 第二步：`在 AndroidMainifest.xml 中添加<receiver>`，子标签intent-filter中添加需要监听的action。Exported 属性表示是否允许这个广播接收本程序以外的广播，Enabled 属性表示是否启用用这个广播接收器。

```xml
<receiver
    android:name=".MyReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
```

#### 4.3 动态注册

- **动态注册** : 自定义类继承 BroadcastReceiver，然后重写具体实现onReceive()。在代码中调用`registerReceiver()`注册来进行广播的注册。必须在 onDestroy 中调用`unregisterReceiver()`方法，否则会引起内存泄露。特点是：不常驻，跟随组件的生命变化，组件结束，广播结束。在组件结束前，需要先移除广播，否则容易造成内存泄漏。
```java
    protected void onCreate() {
       myBroadcastReceiver ChangeReceiver = new myBroadcastReceiver();
       IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
        registerReceiver(ChangeReceiver, intentFilter);
    }
    //在onDestroy()中要取消注册，否则会引起内存泄漏。
    protected void onDestroy() {
        unregisterReceiver(networkChangeReceiver);
    }
    //ChangeReceiver就是接收后会怎么样怎么样
    class myBroadcastReceiver extends BroadcastReceiver {
        public void onReceive(Context context, Intent intent){
		//to do
        }
     }
```

### 5、广播的发送

#### 1.标准广播(异步)
```java
//通过sendBroadcast发送标准合家欢广播
Intent intent = new Intent("com.example.songsong.MY_BROADCAST");
sendBroadcast(intent);
```
#### 2.有序广播(同步)

- 定义：发送出去的广播被广播接收者按照先后顺序接收
- 接收广播的顺序规则（同时面向静态和动态注册的广播接受者）
	- 按照`Priority`属性值从大-小排序；
	- `Priority`属性相同者，动态注册的广播优先；
- 特点：
	- 接收广播按顺序接收；
	- 先接收的广播接收者可以对广播进行`截断`，即后接收的广播接收者不再接收到此广播；
	- 先接收的广播接收者可以对广播进行`修改`，那么后接收的广播接收者将接收到被修改后的广播

```java
//通过sendOrderBroadcast发送
Intent intent = new Intent("com.example.songsong.MY_BROADCAST");
sendOrderBroadcast(intent,null);
```

```xml
    /*给广播接收器设置优先级 */
    <intent-filter android:priority="100">
        <action android:name="com.example.broadcasttest.LOCAL_BROADCAST" />
    </intent-filter>
```
```java
//广播接收器截断：
public void onReceive(Context context, Intent intent) {
    abortBroadcast();
}
```
#### 3.本地广播
```java
//发送1：实例化localBroadcastManager
LocalBroadcastManager localBroadcastManager = LocalBroadcastManager.getInstance(this);
//发送2：发送广播
Intent intent = new Intent("android.net.conn.CONNECTIVITY_CHANGE");
localBroadcastManager.sendBroadcast(intent);
//接收1：实例化IntentFilter和接收器LocalReceiver
IntentFilter intentFilter = new IntentFilter(); 
LocalReceiver localReceiver  = new LocalReceiver();
//接收2：设置广播接收类型
intentFilter.addAction(android.net.conn.CONNECTIVITY_CHANGE);
//接收3：进行动态注册本地广播
localBroadcastManager.registerReceiver(localReceiver, intentFilter);
//接收4：在onDestroy中取消注册
localBroadcastManager.unregisterReceiver(localReceiver);
//接收5：在localReceiver中继承BroadcastReceiver并重写onReceiver。
...
```

### 6.广播内部实现机制

1. 自定义广播接收者 BroadcastReceiver，并复写 onRecvice();
2. 通过 Binder 机制向 AMS(Activity Manager Service) 注册广播；
3. 通过 Binder 机制向 AMS(Activity Manager Service) 发送广播。
4. AMS 查找符合相应条件(IntentFilter/Permission等)的BroadcastReceiver，将广播发送到BroadcastReceiver 所在的消息循环队列中。
5. BroadcastReceiver 所在消息队列拿到此广播后，回调它的 onReceive() 方法。

### 7.AMS 是什么？

 AMS(Activity Manager Service)：是贯穿Android系统组件的核心服务，负责启动四大组件启动切换调度。

### 8.本地广播 LocalBroadcastManager 

- **背景**：Android中的广播可以跨App直接通信（exported对于有intent-filter情况下默认值为true）

- **冲突**：
	- 其他App针对性发出与当前App intent-filter相匹配的广播，由此导致当前App不断接收广播并处理；
	- 其他App注册与当前App一致的intent-filter用于接收广播，获取广播具体信息(即会出现安全性 & 效率性的问题)。

- **解决方案**：使用App应用内广播（Local Broadcast）
	- App应用内广播可理解为一种**局部广播**，广播的发送者和接收者都同属于一个App。
	- 相比于全局广播（普通广播），App应用内广播优势体现在：安全性高 & 效率高；

- **特点**：
	- 发送的广播只能够在自己 App 的内部传递，不会泄露给其他 App，确保隐私数据不会泄露；
	- 广播接收器只能接收来自本 App 发出的广播；
	- 其他App也无法向你的App发送该广播，不用担心其他App会来搞破坏；
	- 比系统的全局广播更加高效。
	
- **内部实现原理**：
	1. `LocalBroadcastManager` 高效的原因主要因为它内部是通过`Handler`实现的，它的`sendBroadcast()`方法是通过`handler()`发送一个`Message`实现的。
	2. 相比系统广播是通过`Binder`实现的，本地广播会更加高效。别人应用无法向自己的App发送广播，而自己App发送的广播也不会离开自己的App。
	3. LocalBroadcastManager 内部协作主要是靠两个Map集合：`mReceivers`和`mActions`，当然还有一个List集合`mPendingBroadcasts`，主要是存储待接收的广播对象。

### 9.全局广播的缺点

- App被反编译获得Action后，会被植入广告、数据泄露。

### 10.BroadcastReceiver 和 LocalBroadcastReceiver 区别

- **BroadcastReceiver** 是`跨应用`广播，利用`Binder`机制实现。
- **LocalBroadcastReceiver** 是`应用内`广播，利用`Handler`实现，利用了IntentFilter的match功能，提供消息的发布与接收功能，实现应用内通信，效率比较高。

### 11.Broadcast Receiver能在onReceive中执行耗时任务吗？

BroadcastReceiver 在 `10 秒`内没有执行完毕，Android 会认为该程序无响应ANR，所以在 onReceive 通常是不能开启线程的，一般是通过 service 或者 `IntentService` 来处理。

### 12.BroadCastReceiver 的生命周期

- a. 广播接收者的生命周期非常短暂的，在接收到广播的时候创建，onReceive()方法结束之后销毁；
- b. 广播接收者中不要做一些耗时的工作，否则会弹出 Application No Response应用无响应对话框；
- c. 最好也不要在广播接收者中创建子线程做耗时的工作，因为广播接收者被销毁后进程就成为了空进程，很容易被系统杀掉；
- d. 耗时的较长的工作最好放在服务中完成；

### 13.广播传输的数据是否有限制，是多少，为什么要限制？

- Broadcast广播通过Intent来传输数据，而Intent的数据大小限制为小于1MB，如果大于等于1MB都会出现异常。

- Intent携带信息的大小其实是受Binder限制，Binder传递缓存有一个限定大小，通常是1Mb。但同一个进程中所有的`传输共享缓存空间`。多个地方在进行传输时，即时它们各自传输的数据不超出大小限制，`TransactionTooLargeException`异常也可能会被抛出。

- 参考文章：[https://blog.csdn.net/u011033906/article/details/89316543](https://blog.csdn.net/u011033906/article/details/89316543)



### 本文参考资料：
- 《Android开发艺术探索》
- 《第一行代码》
- [https://www.jianshu.com/p/718aa3c1a70b](https://www.jianshu.com/p/718aa3c1a70b)
- Fragment通信参考文章：[https://juejin.im/entry/59746af7f265da6c34337d2e](https://juejin.im/entry/59746af7f265da6c34337d2e)
