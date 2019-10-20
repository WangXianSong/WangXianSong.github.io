---
layout: post
title:  "Android学习笔记之Service"
date:  2019-10-12 10:36:55
categories: Android
tags: Android

---
* content
{:toc}



### 1.Service 是什么？

Service 是 Android 中实现程序后台运行的解决方案，非常适用于去执行那些`不需要和用户交互`而且还要求`长期运行的任务`。Service默认运行在UI主线程中，所以`不能做耗时操作`，除非在Service中创建子线程来完成耗时操作。

---

### 2.Service 和 BroadcastReceiver 共同点

- 都是运行在主线程当中，都不能做长时间的`耗时操作`。

---

### 3.Service 和 Thread 的区别

- **定义**：
	- Thread 是程序执行的最小单元，它是分配CPU的最小单位。可以执行异步操作，是相对**独立**的。
	- Service 是 Android 的一种特殊机制，是运行在主线程当中的，是**依托于**所在的主线程。是由系统进程托管，也是一种轻量级IPC通信方式(Activity 和 Service 绑定，然后数据通信，并处于不同进程。）

- **关系**：Service 和 Thread 之间并没有什么关联，Service 翻译成中文是服务，同时服务可以理解为后台。Thread 是开启子线程执行耗时操作，而 Service 是在主线程中执行，但总被认为可以在后台处理耗时任务，容易混淆了两者之间的概念。

- **实际开发中**：在Android系统当中，线程一般指的是工作线程，主线程主要负责UI绘制，而Service 就是运行在主线程当中的。

- **应用场景**：
  - 当需要耗时的操作，比如网络请求，图片加载等等都应该使用工作线程。
  - 当需要在后台播放音乐、开启定位、数据统计等等应该使用Service。

- **区分**
  - 1.不要把后台和子线程联系在一起；
  - 2.服务和后台是不同的概念；
  - 3.Android的后台是指它的运行不依赖与UI线程，即使程序被销毁了、程序被关闭了，服务进程仍然在后台进行计算、统计等等。
  - 4.如果在 Service 执行耗时，也需要创建子线程，然后去做耗时逻辑。
  - 5.**Service是为了弥补 Activity 被销毁之后，无法获取之前所创建的子线程实例，并对后台进行控制的情况而产生的**。

---

### 4.开启子线程的方法

- 新建类继承自Thread，然后重写父类的run方法，并在里面编写耗时逻辑。
```java
class MyThread extends Thread{
    public void run(){
        //具体逻辑
    }
}
new MyThread().start();
```
- 实现Runnable接口可以降低继承的耦合性
```java
class MyThread implements Runnable{
    public void run(){
          //具体逻辑
    }
}
//启动线程的方法
MyThread my = new MyThread();
new Thread(my).start();
```
- 使用匿名类的方式，这种方式最常见
```java
new Thread(new Runnable(){
    public void run(){
        //具体逻辑
    }
}).start()
```

---

### 5.Service 的生命周期

![Service 的生命周期](https://github.com/WangXianSong/Wangxiansong.github.io/blob/master/images/ServiceLife.png)

- 回调方法含义：
	- **onCreate**：服务第一次被创建时调用；
	- **onStartComand**：服务启动时调用；
	- **onBind**：服务被绑定时调用；
	- **onUnBind**：服务被解绑时调用；
	- **onDestroy**：服务停止时调用；

- 生命周期：
	- 只是用 `startService()` 启动服务：onCreate() -> onStartCommand() -> onDestory()
	- 只是用 `bindService()` 绑定服务：onCreate() -> onBind() -> onUnBind() -> onDestory()
	- `同时使用startService()与bindService()`：onCreate() -> onStartCommnad() -> onBind() -> onUnBind() -> onDestory。

- 种类：
	- **startService**()：开启Service，调用者退出后Service仍然`存在`。
	- **bindService**()：开启Service，调用者退出后Service也随即`退出`。

- 启动方式：
     - **startService**()：通过`startService()`方法可以启动一个Service，并回调服务中的`onStartCommand()`。如果首次创建，那么回调的顺序是`onCreate()->onStartCommand()`。服务启动了之后会一直保持运行状态，直到`stopService()`或`stopSelf()`方法被调用，服务停止并自动回调`onDestroy()`。另外，无论调用多少次`startService()`方法，只需调用一次`stopService()`或`stopSelf()`方法，服务就会停止了。**onCreate()**只在第一次创建的时候调用，**onStartCommand()**每次启动服务都调用。
	- **bindService**()：通过`bindService()`可以绑定一个Service，并回调服务中的`onBind()`方法。类似地，如果该服务之前还没创建，那么回调的顺序是`onCreate()->onBind()`。之后，调用方可以获取到onBind()方法里返回的`IBinder对象`的实例，从而实现和服务进行通信。只要调用方和服务之间的连接没有断开，服务就会一直保持运行状态，直到调用了`unbindService()`方法服务会停止，回调顺序`onUnBind()->onDestroy()`。
	- 注意，这两种启动方法并不冲突，当使用startService()启动Service之后，还可再使用bindService()绑定，只不过需要同时调用 stopService()和 unbindService()方法才能让服务销毁掉。

---

### 6.Service的基本用法(startService、bindService和IntentService)

#### 6.1 startService

- 第一步：新建类并继承Service且必须重写onBind()方法，有选择的重写onCreate()、onStartCommand()及onDestroy()方法。
- 第二步：在配置文件AndroidManifest.xml中进行注册，否则不能生效。
- 第三步：在 Activity 中利用 startService(Intent) 可实现 Service 的启动。
- 第四步：在 Activity 中  stopService(Intent) 停止服务，或者在Service任意位置调用stopSelf()；

```java
public class MyService extends Service {

    public MyService() {
    }
    @Override
    public void onCreate() {
        super.onCreate();
    }
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                //处理具体的逻辑
                //必须调用stopService或者stopSelf方法才能停止
                stopSelf();
            }
        }).start();
        return super.onStartCommand(intent, flags, startId);
    }
    @Override
    public void onDestroy() {
        super.onDestroy();
    }
    @Override
    public IBinder onBind(Intent intent) {
        throw new UnsupportedOperationException("Not yet implemented");
    }
}
//在配置文件AndroidManifest.xml中进行注册
<service
    android:name=".MyService"
    android:enabled="true"
    android:exported="true" />

//启动
Intent intent = new Intent(this, MyService.class);
 startService(intent);
//停止
Intent intent = new Intent(this, MyService.class);
stopService(intent);
```

#### 6.2 bindService

- 第一步：创建BindService服务端，继承自Service，并在类中创建一个实例IBinder接口实现对象并提供公共方法给客户端调用。
- 第二步：从 onBind() 回调方法返回此Binder实例；
- 第三步：在客户端中，从onServiceConnected()回调方法接收Binder，并使用提供的方法调用绑定服务。
- 绑定服务提供了客户端和服务端接口，进行数据的交互(发送请求、获取结果、进程间通信)

```java
public class TestService extends Service {
    private Mybinder mBinder = new Mybinder();
    public TestService() {
    }
    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }
    public class Mybinder extends Binder {
        public void showToast() {
            Toast.makeText(getApplicationContext(), "BindService", Toast.LENGTH_SHORT).show();
        }
    }
}
```

```java
public class MainActivity extends AppCompatActivity {
    private TestService.Mybinder mBinder;
    private ServiceConnection mConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            //成功绑定
            mBinder = (TestService.Mybinder) service;
            mBinder.showToast();
        }
        @Override
        public void onServiceDisconnected(ComponentName name) {
            //断开连接
        }
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //绑定服务
        Intent bindIntent = new Intent(this, TestService.class);
        bindService(bindIntent, mConnection, BIND_AUTO_CREATE);
        //解绑服务
        unbindService(mConnection);
    }
}
```
#### 6.3 IntentService

```java
public class MyIntentService extends IntentService {

    public MyIntentService() {
        super("MyIntentService");
    }
    @Override
    protected void onHandleIntent(Intent intent) {
        //子线程 耗时操作
    }
    @Override
    public void onDestroy() {
        super.onDestroy();
    }
}
//调用方法
Intent intentService = new Intent(this, MyIntentService.class);
startService(intentService);
```

---

### 7.Activity如何与Service通信？

在 Activity 中可以通过 startService 和 bindService 方法启动 Service。一般情况下如果想获取 Service 的服务对象那么肯定需要通过`bindService()`方法，比如音乐播放器，第三方支付等。如果仅仅只是为了开启一个后台任务那么可以使用 startService() 方法。

1. 首先定义 **MyService** 来指定具体的操作。
2. 先在 Activity 里实现一个**ServiceConnection**类 connection，重写 onServiceConnected 和 onServiceDisconnected 方法，分别表示绑定成功与断开；
2. 并将 connection 传递给 **bindService**() 方法去启动 Service；
3. 在ServiceConnection的**onServiceConnected()**方法里启动 MyService 里的方法。
4. 最后通过 **unbindService** 来解绑服务。

---

### 8.说说 Activity、Intent 和 Service 是什么关系

他们都是 Android 开发中使用频率最高的类。其中 Activity 和 Service 都是 Android 四大组件之一。他俩都是 Context 类的子类 ContextWrapper 的子类，因此他俩可以算是兄弟关系吧。不过兄弟俩各有各自的本领，Activity 负责用户界面的**显示和交互**，Service 负责后台**任务的处理**。Activity 和 Service 之间可以通过 Intent **传递数据**，因此可以把 Intent 看作是通信使者。

---

### 9.Service 里面可以弹吐司么
可以的，弹吐司有个条件就是得有一个 Context 上下文，而 Service 本身就是 Context 的子类，因此在 Service 里面弹吐司是完全可以的。比如我们在 Service 中完成下载任务后可以弹一个吐司通知
用户。

---

### 10.IntentService是什么？

- IntentService 是一个特殊的 Service，它继承了 Service 并且它是一个抽象类，因此必须创建它的子类才能使用 IntentService。
- 它封装了 HandlerThread 和 Handler，可用于执行后台耗时的任务，当任务全部执行完毕后自动停止。
- 它的优先级比普通线程要高，所以适合执行高优先级的后台任务。
- （HandlerThread是继承了Thread，他的作用是 不用每次创建线程，他内部有循环机制可以重复利用，不用的时候要quit或者quitSafely。）

> **IntentService 启动方法**
>第一步：新建类并继承IntentService，在这里需要提供一个无参的构造函数且必须在其内部调用父类的有参构造函数，然后具体实现onHandleIntent()方法，在里可以去处理一些耗时操作而不用担心 ANR的问题，因为这个方法已经是在子线程中运行的了。
第二步：在配置文件中进行注册。
第三步：在活动中利用startService(Intent)实现IntentService的启动，和Service用的方法是完全一样的。

### 11.一个Activty先start一个Service后，再bind时会回调什么方法？此时如何做才能回调Service的destory()方法？
当对一个服务既调用 startService() 方法，又调用了bindService()方法，根据Android系统的机制，一个服务只要被启动或者被绑定了之后，就会一直处于运行状态，必须要让以上两种条件同时不满足，服务才能被销毁。所以，这种情况要同时调用是stopService()和unbindService方法，onDestroy才会执行。

### 12.前台服务是什么？和普通服务的不同？如何去开启一个前台服务？

前台服务和普通服务最大的区别是：前者会一直有一个正在运行的图标在系统的状态栏显示，下拉状态栏后可以看到更加详细的信息，非常类似于通知的效果。使用前台服务或者为了防止服务被回收掉，比如听歌，或者由于特殊的需求，比如实时天气状况。

想要实现一个前台服务非常简单，它和之前学过的发送一个通知非常类似，只不过在构建好一个Notification之后，不需要NotificationManager将通知显示出来，而是调用了startForeground()方法。

```java
//修改MyService的onCreate()方法：
public void onCreate(){
        Intent intent = new Intent(this,MainActivity.class);
        PendingIntent pi = PendingIntent.getActivity(this,0,intent,0);
        Notification notification = new NotificationCompat.Builder(this)
                .setContentTitle("This is content title")
                .setContentText("This is content text")
                .setWhen(System.currentTimeMillis())
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(),R.mipmap.ic_launcher))
                .setContentIntent(pi)
                .build();
        startForeground(1,notification);
}
```
### 13.是否了解ActivityManagerService，谈谈它发挥什么作用？

ActivityManagerService是Android中最核心的服务 ， 主要负责系统中四大组件的启动、切换、调度及应用进程的管理和调度等工作，其职责与操作系统中的进程管理和调度模块类似。

---

### 14.如何保证Service不被杀死？

- 在Service的onStartCommand()中设置flages值为START_STICKY，使得Service被杀死后尝试再次启动Service。
- 提升Service优先级，比如设置为一个前台服务。
- 在Activity的onDestroy()通过发送广播，并在广播接收器的onReceive()中启动Service。

---

### 15.是否能在Service进行耗时操作？如果非要可以怎么做？

Service默认并不会运行在子线程中，也不运行在一个独立的进程中，它同样执行在主线程中（UI线程）。换句话说，不要在Service里执行耗时操作，除非手动打开一个子线程，否则有可能出现主线程被阻塞（ANR）的情况。(开启子线程的方法有：继承、实现、匿名类)

--- 

### 16.用过哪些系统Service？

![系统服务](http://upload-images.jianshu.io/upload_images/6491732-8af3c469483279d4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

### 17.AlarmManager能实现定时的原理？

通过调用AlarmManager的 set() 方法就可以设置一个定时任务，并提供三个参数（工作类型，定时任务触发的时间，PendingIntent对象）。其中第三个PendingIntent对象是关键，一般会调用它的 getBroadcast() 方法来获取一个能够执行广播的PendingIntent。这样当定时任务被触发的时候，广播接收器的onReceive()方法就可以得到执行。即通过服务和广播的循环触发实现定时服务。

---

### 本文参考资料：
- 《Android开发艺术探索》
- 《第一行代码》
- [https://www.jianshu.com/p/718aa3c1a70b](https://www.jianshu.com/p/718aa3c1a70b)
