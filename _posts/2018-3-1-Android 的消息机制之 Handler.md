---
layout: post
title:  "Android 的消息机制之 Handler"
date:  2018-3-1 15:39:20
categories: Android
tags: Android

---
* content
{:toc}






## 前言

通过对Handler运行机制的简单回顾，同时也对知识点梳理，帮助我们在“金三银四”里找到自己喜欢的实习工作。

文中主要内容为：
-  什么是 Handler
- Handler 的使用方法
- Handler 机制的原理
- Handler 面试相关

## 什么是 Handler

1、Handler 是 Android 消息机制的上层接口，我们可以简单的理解 Handler 是*更新 UI 的一种机制*。但是，Handler 并不是专门用于更新 UI 的，它还可以用来做耗时的 I/O 操作，比如文件读取或者访问网络。

2、Handler 通过发送和处理 Message 和 Runnable 对象来关联相对应线程的消息队列(MessageQueue) ，可以让自己想要处理的耗时操作放在子线程，让更新 UI 的操作放在主线程。

## Handler 的使用方法

Handler提供了两种解决在子线程中无法更新 UI 的方法，一种是通过* post(Runnable) *方法，另一种是 发送 *sendMessage(message) *方法。

**(1) 传递 Runnable 对象**：

- post(Runnable)：立即执行Runnable对象。
- postAtTime(Runnable, long)：在指定的时间之内执行。
- postDelayed(Runnable, long)：在指定的时间之后执行。

```java
//在成员变量中创建Handler，自动绑定到主线程
private Handler handler = new Handler();

//开启一个子线程
new Thread(new Runnable() {
    @Override
    public void run() {

	  // ... 执行耗时任务操作

	 //通知handler在主线程中执行Runnable中的代码
        handler.post(new Runnable() {
            @Override
            public void run() {
                btn2.setText("this is handler.post");
            }
        });
        handler.postAtTime(new Runnable() {
            @Override
            public void run() {
                btn2.setText("this is handler.postAtTime");
            }
        }, 2000);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                btn2.setText("this is handler.postDelayed");
            }
        }, 2000);
     }
}).start();
```

**(2) 传递 Message**：

- sendEmptyMessage(int)：仅仅作为通知工作。
- sendEmptyMessageDelayed(int, long)：定时通知工作。
- sendMessage(Message)：允许你将 Message 对象添加到消息队列。
- sendMessageDelayed(Message, long)：定时将添加到消息队列

```java
//在成员变量中创建Handler，自动绑定到主线程
private Handler handler = new Handler() {
    //重写handleMessage
    public void handleMessage(Message msg) {
        //已经回到了主线程中
        switch (msg.what) {
            case 1:
		  //进行相应的 UI 线程的处理
                btn.setText("1234567890");
                break;
            case 2:
                //将obj强转为String类型，并得到这个字符串
                String s2 = (String) msg.obj;
                btn.setText(s2);
                break;
      }
}

//开启一个子线程
new Thread(new Runnable() {
    @Override
    public void run() {

	  // ... 执行耗时任务操作

        //第一种：仅仅作为通知工作
        handler.sendEmptyMessage(1);
        //第二种：定时通知工作
        handler.sendEmptyMessageDelayed(1, 1000);
        //第三种：在子线程发送一个消息。
        Message msg = new Message();
        String s2 = "Abc123";
        msg.what = 2; //message的命令为2
        msg.obj = s2; //传入任意类，将s2赋给obj
        handler.sendMessage(msg);
        //第四种：定时将添加到消息队列
        handler.sendMessageDelayed(msg,1000);
    }
  }).start();
};
```
**(3)两种方法之间的区别：** 

首先我们看看  post(Runnable) 和 sendMessage(msg) 两个方法的源码：
```java
public final boolean post(Runnable r)
{
   return  sendMessageDelayed(getPostMessage(r), 0);
}

public final boolean sendMessage(Message msg)
{
    return sendMessageDelayed(msg, 0);
}
```
其实质 post(Runnable) 这个方法只是进行了一层封装，通过 sendMessageDelayed 把 Runnable 对象添加到了 MessageQueue 中，而 sendMessage 也同样将 Message 加入到 MessageQueue，所以*两者内部实现是一样的*。



## Handler 机制的原理

在学习 Handler 机制的原理之前，我们先看看 Handler 机制的 4 大部分：Looper、MessageQueue、Message、Handler。

**1.Message**： 消息对象，是线程间通讯的消息载体。
**2.MessageQueue**：消息队列，它的内部存储了一组消息，以队列(先进先出)的形式对外提供*插入*和*读取*的操作。
**3.Looper**：轮播器，负责管理线程的消息队列和消息循环。
**4.Handler**：负责发送和处理消息，通过它可以实现其他线程与主线程之间的消息通讯。

接下来是 Handler 运行机制的工作流程图：

![工作流程图](https://i.imgur.com/eqoHX6s.png)
（图片来源网络）

工作流程：Handler 的 post/send 方法将一个 Runnabl/Message 投递或发送到 Handler 内部，然后 MessageQueue 的 enqueueMessage 方法将这个消息放入消息队列中，然后 Looper.loop() 方法发现有新消息来时，就会处理这个消息。最终消息中的 Runnabl 或者 handlerMessage 方法就会被调用。

在学习 Hnadler 的运行机制中的成员之前，先来学习一下 ThreadLocal 是什么？因为在后面会使用到。

### 1、ThreadLocal 的工作原理
 
**作用**：是不同的线程拥有该线程独立的变量，同名对象在不同线程中互不干扰地存储和修改数据。

**使用场景**：在当某些数据是以线程为作用域并且不同现场具有不同步的数据副本的时候，就可以考虑采用 ThreadLocal。

**方法**：它可以在线程中使用 mThreadLocal.*get()* 和 mThreadLocal.*set()* 来使用。若未被在当前线程中调用 set 方法，那么 get 时为空。

**工作原理**：在不同线程访问同一个 ThreadLocal 的 get 方法，ThreadLocal 内部会从各自的线程中取出一个数值，然后再从数据中根据当前 ThreadLocald 索引去查找对应的 value 值。这就是为什么在不同线程中互不干扰地存储和修改数据。

### 2、MessageQueue 的工作原理

MessageQueue 在 Android 中指的是消息队列，主要包含两个操作*插入*和*读取*。插入和读取对应的方法分别为 enqueueMessage 和 next。

**enqueueMessage**：往消息队列中插入一条消息。
**next**：从消息队列中取出一条并将其从消息队列中移除。

尽管 MessageQueue 叫消息队列，但它的内部实现不是用队列，而是用**单链表的数据结构**来维护消息列表，在插入和删除上有明显的优势。



### 3、Looper 的工作原理

Looper 扮演着消息循环的角色，会不停地从 MessageQueue 中查看是否有新消息，如果有就会立刻处理，否则就会一直阻塞在那里等待新消息。


**首先**在 Looper 的构造方法中，会创建一个 MessageQueue，将当前线程的对象保存起来。
```java
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```
**然后**通过 Looper.prepare() 来为当前线程创建一个 Looper，并把 Looper 对象设置给 ThreadLocal。
```java
private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed)); 
}
```
**紧跟着**然后通过 Looper.loop() 方法开启循环来消息的分发。
```java
public static void loop() {
    final Looper me = myLooper();
    ...
    for (;;) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            return;
        }
     msg.target.dispatchMessage(msg);
     ...
}
```
**最后**如果MessageQueue 的 next 方法返回了消息，Looper 就会处理这条消息：msg.target.dispatchMessage(msg)，这里的 msg.target 是发送这条消息的 Handler 对象，这样 Handler 发送的消息最终又交给它的 dispatchMessage 方法来处理了，成功地将代码逻辑切换到指定的线程中去执行了。


**另外 Looper.loop()**实质就是创建了一个 for 的死循环，然后从消息队列中逐个的去获取消息。唯一跳出循环的方法是 MessageQueue 的 next 方法返回 null。

**quit() 和 quitSafely() 方法不也能退出 Looper 吗？**

的确，当 Looper的 quit() 或 quitSafely() 被调用时，就会调用 MessageQueue 的 quit() 方法，分别对应的是 removeAllMessagesLocked() 和 removeAllFutureMessagesLocked() 方法。

区别在于，*quit() 会清空所有消息*(无论延时或非延时)，*quitSafely() 只清空延时消息*，最终 next 方法都是返回 null。

**注意**：无论调用哪个方法，当 Looper 退出后，Handler 发送的消息都会失败。这时候再通过 Handler 调用 sendMessage 或 post 等方法发送消息时均返回 false，表示消息没有成功放入消息队列 MessageQueue 中，因为消息队列已经退出了。



### 4、Handler 的工作原理

Handler 的工作主要有：接收与发送消息、处理消息。消息发送是通过 post 和 send 的一系列方法来实现的，由于 post 最终是通过 send 的方式来现实的，请自行理解 send 的 sendMessage、sendMessageDelayed、sendEmptyMessage、enqueueMessage 的源码，在此就不做过多解析。

**Handler 的消息发送过程**：向消息队列插入了一条消息，MessageQueue 的 next 的方法就会返回这条消息给 Looper，Looper 收到消息后就开始处理，最终消息由 Looper 交由 Handler 处理，即Handler 的 dispatchMessage 方法就会被调用。

以下是 dispatchMessage 的源码：
```java
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {
        handleCallback(msg);
    } else {
        if (mCallback != null) {
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        handleMessage(msg);
    }
}
```
**首先**检查 Message 的 callback 是否为空，不为空则通过 handleCallback 来处理消息。实际上，handleCallback 会通过 Message 的 callback 来处理 Runnable 的 run() 方法。
```java
private static void handleCallback(Message message) {
    message.callback.run();
}
```
**然后**检查 mCallback 是否为空，不为空则调用 mCallback 的 handleMessage 方法来处理消息。实际上 Callback 是个接口，提供另一种方式使用 Handler，当不想派生子类并重写 handleMessage 的时候，就可以通过 Callback 来实现。
```java
public interface Callback {
    public boolean handleMessage(Message msg);
}
``` 
**最后**调用 Handler 的 handleMessage 方法来处理消息。



## Handler 引起的内存泄露以及解决方法

由于我们创建的 Handler 不是静态内部类，它会隐匿持有 Activity 的引用。当 Activity 要回收的话，而 Handler 内部有可能在做耗时操作，所以 Handler 所持有的 Activity 的引用不能被释放。最终导致 Activity 没有被回收，停留在队列内存当中，造成内存泄露。

**总结**：静态内部类持有外部类的匿名引用，导致外部Activty 无法释放。

**解决方法**：

- 将Handler 设置为静态内部类。
- 在 Activity 的生命周期 onDestroy() 方法中，调用 handler.removeCallbacks() 方法。
- handler 内部持有外部 activity 的弱引用。





## Handler面试相关

**1.Android 消息机制为什么要提供 Handler？**
这是因为 Android 规定访问 UI 只能在主线程中进行，如果在子线程中访问 UI，那么线程就会抛出 ViewRootImpl 异常。这个时候通过 Handler 就可以将更新 UI 的操作切换到主线程中执行。

**2.为什么不允许在子线程中访问 UI?**
因为 Android 的 UI 控件不是线程安全的，如果在多线程中*并发*访问可能会导致 UI 控件处于不可预期的状态。

**3.为什么系统不对 UI 控件的访问加锁机制？**
首先加锁机制会让 UI 访问的逻辑变得复杂；其次锁机制会降低 UI 访问的效率，因为锁机制会阻塞某些线程的执行；鉴于这两个原因，所以最简单且高效的方法就是采用单线程模型来处理 UI 操作。

**4.Looper 死循环为什么不会导致应用卡死？**
线程默认没有 Looper 的，如果需要使用 Handler 就必须为线程创建 Looper。

在子线程中，如果手动为其创建了 Looper，那么在所有的事情完成以后应该调用 quit 方法来终止消息循环，否则这个子线程就会一直处于等待（阻塞）状态，而如果退出 Looper 以后，这个线程就会立刻（执行所有方法并）终止，因此建议不需要的时候终止 Looper。

**5.Handelr 与 AsyncTask 的对比**
- AsyncTask：简单，快捷，过程可控。在使用多个异步操作并且需要进行 UI 变更时，变得复杂。
- Handelr：结构清晰，功能明确。对于多个后台任务时，清晰简单。在单个后台任务时，显得代码过多，结构复杂。

**6.子线程有哪些更新UI的方法?**
- 主线程中定义 Handler，子线程通过 handler 发送消息，主线程 Handler 的 handleMessage 更新UI。
- View.post(Runnable r) 。
- 用 Activity 对象的 runOnUiThread 方法。

第一种在文中已经介绍过了。
**第二种 View 的 post() 方法：**
用法：
```java
btn2.post(new Runnable() {
    @Override
    public void run() {
        btn2.setText("this is view.post");
    }
});
```
查看view.post()的源码
```java
 //首先post接收的是一个Runnable对象
public boolean post(Runnable action) {
    Handler mHandler;  //局部 mHandler
    final AttachInfo attachInfo = mAttachInfo;
    if (attachInfo != null) {
        return attachInfo.mHandler.post(action);
	 //取当前UI控件所依附的线程里面自带的Handler
	 //然后通过局部的mHandler.post()来执行并且返回。
	 //本质上view.post()还是handler.post()
    }
    getRunQueue().post(action);
    return true;
}
```
**第三种 Activity 的runOniThread()方法：**
用法：
```java
runOnUiThread(new Runnable() {
    @Override
    public void run() {
        btn2.setText("this is runOnUiThread");
    }
});
```
源码：
```java
public final void runOnUiThread(Runnable action) {
    //如果当前线程不等于UI线程
    if (Thread.currentThread() != mUiThread) {
        //本质上还是handler.post()方法
        mHandler.post(action);
    } else {
	 //如果在UI线程就更直接的run
        action.run();
    }
```


## 附录：参考资料 

- 《Android 开发艺术探索》任玉刚
- 慕课网-BAT大咖助力 全面升级Android面试
- 麦子学院-Android 之多线程和异步任务
- http://mp.weixin.qq.com/s/Cum5znsb6dNs9n5Qx-vFBg
- http://mp.weixin.qq.com/s/JSrMjvBVBYeq6iBOWTGUng
