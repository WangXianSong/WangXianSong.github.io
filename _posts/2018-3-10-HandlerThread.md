---
layout: post
title:  "HandlerThread 的深入学习"
date:  2018年3月11日13:49:13
categories: Android
tags: Android
---
* content
{:toc}


## 前言

在之前的文章中学习了多线程、Handler、AsyncTask 等等知识点，今天来了解一下什么是 HandlerThread。

## 为什么要使用 HandlerThread

问题：当我们遇到一些**耗时操作**的时候，这时候我们第一直觉就是开启**子线程**去进行耗时操作，这是非常方便的。

但是当线程执行完耗时操作并自动销毁后，如果**频繁地**重复开启和销毁线程的话，是很**消耗系统资源**的。


为了解决这个问题，我们可以构建一个*循环器 Looper* 来进行消息的轮循，当有耗时任务需要投放在该循环线程时，线程就会执行耗时任务。在执行完之后循环线程处于**等待阻塞**状态，直到下一耗时任务被投放进来，从而保证性能的最优。

**1.想要在子线程中创建Handler 的话，需要这样写：**
```java
class LooperThread extends Thread {
    public Handler mHandler;

      public void run() {
          Looper.prepare();

          mHandler = new Handler() {
              public void handleMessage(Message msg) {
                  // 处理消息
              }
          };
          Looper.loop();
      }
}
```
**2.子线程中为什么要构建 Looper ？**
因为 Handler 的 sendMessage 或 post(Runnable) 都需要 *MessageQueue 消息队列* 来保存它所发送的消息，而默认子线程中**没有开启**循环器 Looper，又因为消息队列是由 Looper 来管理的，所以子线程想要开启 Handler，发送消息是**无法关联**到 MessageQueue 来储存消息，导致抛出异常。

如果想在子线程当中创建 Handler，必须自己去初始化一个 Looper，然后再通过 Looper.loop() 方法开启循环才能创建 Handler。 


## HandlerThread 是什么？

为了更好的解决上面的所说的问题，Google 公司特意为我们封装了 HandlerThread 框架。那么我们就知道 HandlerThread 不单只是继承了 Thread，而且还是一个包含 Looper 的 Thread，可以直接使用这个 Looper 来创建 Handler。

![](https://i.imgur.com/zlt5isk.png)



## HandlerThread 的特点

- HandlerThread 本质上是一个线程类，它**继承**了 Thread；
- HandlerThread 有自己**内部**的 Looper 对象，可以进行 looper 循环；
- 通过获取 HandlerThread 的 looper 对象传递给 Hander 对象，可以在 handleMessage 方法中执行异步任务；
- 优点是不会有堵塞，减少了对性能的消耗，缺点是不能同时进行多任务处理，需要等待。
- 与线程池的并发不同，HandlerThread是一个**串行队列**，背后只有一个线程。



## HandlerThread 源码解析
```java
package android.os;

public class HandlerThread extends Thread {
    int mPriority;
    int mTid = -1;
    Looper mLooper; //在成员变量中，表明了它自己持有的Looper对象

    //构造方法，通过传入两个参数(线程的名称，线程的优先级)
    public HandlerThread(String name, int priority) {
        super(name);
        mPriority = priority;
    }

    //要在Looper.loop方法循环之前调用，可以做一些初始化的工作
    protected void onLooperPrepared() {
    }

    //是每一个HandlerThread必须复写的一个方法
    public void run() {
        mTid = Process.myTid();
        Looper.prepare();	//初始化Looper
	 //同步锁代码块
        synchronized (this) {	
            mLooper = Looper.myLooper();
            notifyAll();	//创建Looper成功，发出通知 getLooper
        }
	 //设定线程的优先级，可以解决一部分的数据安全和内存泄露
        Process.setThreadPriority(mPriority);		
        onLooperPrepared();	 //初始化工作
        Looper.loop();	//开启循环
        mTid = -1;
    }

    //获取当前线程的 Looper
    //如果线程不是正常运行的就返回 null
    public Looper getLooper() {
        if (!isAlive()) {
            return null;
        }
	 //同步代码块，通过阻塞的形式来创建 Looper
	 //如果 run 方法中 Looper 没有创建成功
	 //就会一直阻塞这里
	 //当 Looper 创建好了之后，notifyAll 形式通知去做相应的操作
        synchronized (this) {
            while (isAlive() && mLooper == null) {		//循环等待
                try {
                    wait();
                } catch (InterruptedException e) {
                }
            }
        }
        return mLooper;
    }
    ...
}
```
主要是看 **run** 方法中调用了 Looper.prepare() 和  Looper.loop() 两个方法。

其中 **Looper.prepare()** 是初始化 Looper，并且把该对象放到了 ThreadLocal 里面，在 Looper 对象的构造过程中，初始化了一个 MessageQueue，作为该 Looper 对象成员变量。

而 **Looper.loop()** 是开启循环，不断从 MessageQueue 消息队列中获取待处理的任务，否则一直阻塞在那，直到有新的任务。



## HandlerThread的例子

本次学习引用了 **鸿洋**博客里面的例子，只作了小小的改动。具体功能是模拟股票的走势。只有一个 Activity。
```java
public class HandlerThreadTest extends AppCompatActivity {

    private TextView mTvServiceInfo;

    private HandlerThread mCheckMsgThread;
    private Handler mCheckMsgHandler;
    private boolean isUpdateInfo;

    private static final int MSG_UPDATE_INFO = 0x110;

    //与UI线程管理的handler
    private Handler mHandler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handler_thread_test);
        mTvServiceInfo = (TextView) findViewById(R.id.id_textview);
        initBackThread();//创建后台线程
    }

    @Override
    protected void onResume() {
        super.onResume();
        //开始查询
        isUpdateInfo = true;
        mCheckMsgHandler.sendEmptyMessage(MSG_UPDATE_INFO);
    }

    @Override
    protected void onPause() {
        super.onPause();
        //停止查询
        isUpdateInfo = false;
        mCheckMsgHandler.removeMessages(MSG_UPDATE_INFO);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        //释放资源
        mCheckMsgThread.quit();
    }

    private void initBackThread() {
        mCheckMsgThread = new HandlerThread("check-message-coming");
        mCheckMsgThread.start();
        mCheckMsgHandler = new Handler(mCheckMsgThread.getLooper()) {
            @Override
            public void handleMessage(Message msg) {
                switch (msg.what) {
                    case MSG_UPDATE_INFO:
                        checkForUpdate();
                        if (isUpdateInfo) {
                            mCheckMsgHandler.sendEmptyMessageDelayed(MSG_UPDATE_INFO, 1000);
                        }
                        break;
                    default:
                        break;
                }
            }
        };
    }

    /**
     * 模拟从服务器解析数据
     */
    private void checkForUpdate() {
        try {
            //模拟耗时
            Thread.sleep(1000);
            mHandler.post(new Runnable() {
                @Override
                public void run() {
                    String result = "实时更新中，当前大盘指数：<font color='red'>%d</font>";
                    result = String.format(result, (int) (Math.random() * 3000 + 1000));
                    mTvServiceInfo.setText(Html.fromHtml(result));
                }
            });
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
> 可以看到我们在onCreate中，去创建和启动了 HandlerThread，并且关联了一个mCheckMsgHandler。然后我们分别在 onResume 和 onPause 中去开启和暂停我们的查询，最后在onDestory中去释放资源。
> 
> 这样就实现了我们每隔2s去服务端查询最新的数据，然后更新我们的UI，当然我们这里通过Thread.sleep()模拟耗时，返回了一个随机数，大家可以很轻易的换成真正的数据接口。

效果图片：
![](https://i.imgur.com/4FtKbAk.gif)

## 附录：参考资料

- 慕课网-BAT大咖助力 全面升级Android面试
- http://blog.csdn.net/u011240877/article/details/72905631
- http://blog.csdn.net/lmj623565791/article/details/47079737/