---
layout: post
title:  "第十一章 Android 的线程与线程池"
date:  2018-2-14 12:34:46
categories: Android
tags: 学习笔记

---
* content
{:toc}



## 前言
这是一篇对Android多线程编程的简单学习，对于我们这样的初学者来说，如果一上来就那么难，恐怕接下来的路会很难走。总而言之，就是从简单到深入最后到放弃。

## 线程基础

### 1.进程(Process)与线程(Thread)

- **进程**：执行中的程序，一个进程可以包含一个或多个线程，一个进程至少要包含一个线程。
- **线程**：是操作系统调度的最小单元，同时线程又是一种受限的系统资源，即线程不可能无限制地产生，并且线程的创建和销毁都会有相应的开销。

### 2.主线程与子线程

- **主线程**：是指进程所拥有的线程，主要处理界面交互相关的逻辑，因为用户随时会和界面发生交互，因此主线程在任何时候都必须有较高的响应速度，否则就会产生一种界面卡顿的感觉。
- **子线程**：也叫工作线程，为了保持主线程有较高的响应速度，主线程中不能执行耗时的任务，这个时候就通过创建子线程来完成耗时任务。

### 3.线程的创建

在 Java 中实现多线程有两种手段，一种是继承 Thread 类，另一种就是实现 Runnable 接口。下面我们就分别来介绍这两种方式的使用。

- **(1)继承 Thead 类，重写 run() 方法**

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            Log.d("TAG", Thread.currentThread().getName() + " run: " + i);
        }
    }
}
```
```java
        MyThread mt1 = new MyThread();
        MyThread mt2 = new MyThread();
        mt1.start();
        mt2.start();
```

- **(2)实现 Runnable 接口的 run() 方法**

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            Log.d("TAG", Thread.currentThread().getName() + " run: " + i);
        }
    }
}
```
```java
public void Runnabletest() {
        MyRunnable rt1 = new MyRunnable();
        MyRunnable rt2 = new MyRunnable();
        Thread t1 = new Thread(rt1);
        Thread t2 = new Thread(rt2);
        t1.start();
        t2.start();
    }
```

**问题1：继承Thread 和实现Runnable 的区别：**

1. 实现的方式避免了单继承的局限性。
2. 适合多个相同的程序代码的线程去处理同一个资源。
3. Thread 类是 Runnable 接口的子类。

**问题2：Thread 对象可以重复使用吗？能否调用 start() 指定新的任务给它？**

答：不行，一旦线程的 run() 方法完成之后，该线程就不能重新启动，事实上过了该点线程就会死掉，Thread 对象可能还停留在堆上，如同活着的对象一般还能接受某些方法的调用，但已经永远地失去了线程的执行性，只剩下对象本身。 

### 4.线程的启动

- **run()**：是作为普通方法去调用，而且是在主线程中去执行。
- **start()**：是用来启动线程，通过 JVM，然后线程 Thread 会调用 run() 方法，去执行相应的代码。

### 5.线程的状态

![生命周期](https://i.imgur.com/x7X1IrV.png)

- **New**：创建状态。还没有调用 start 方法，在线程运行之前做些基础工作。
- **Runnable**：可运行状态。调用 start 方法后线程就会处于 Runnable状态，等待CPU的调度。
- **Blocked**：阻塞状态。表示线程被锁阻塞，暂时不活动(等待wait()阻塞、同步阻塞、sleep(),join(),I/O请求)。
- **Waiting**：等待状态。表示暂时不活动，而且不运行任何代码，直到线程调度器重新激活。
- **Timed waiting**：超时等待状态。与等待状态的区别是，它可以在指定时间自行返回。
- **Terminared**：终止状态。表示当前线程已经执行完毕，有两种情况，第一种是run方法完毕正常退出。第二种方法是一个没有捕获的异常而终止了run方法，导致线程进入终止状态。

![线程状态](https://i.imgur.com/n5kpmBe.jpg)

### 6.线程的主要函数：

- run()：线程运行时所执行的代码 。
- start()：启动线程。
- sleep()：线程的休眠，醒了也不会马上变成执行中的任务。
- join()：线程的强制执行。在 t1 线程中调用了 t2 线程中的join()，t1会进入阻塞状态，等待t2线程运行结束后 t1 才能继续往下运行，有点强制执行的感觉。
- yield()：线程的礼让。使当前线程交出CPU，让CPU去执行其他的任务，但不会是线程进入阻塞状态，而是重置为就绪状态，yield方法不会释放锁。
- wait()：让当前线程进入等待状态，同时释放它所持有的锁。直到其他线程调用此对象的 notify()/notifyAll() 唤醒方法。
- notify()/notifyAll()：唤醒单个线程/唤醒所有的线程。
- interrupt()：中断线程，通过interrupt方法和isInterrupted()方法来停止正在运行的线程，注意只能中断已经处于阻塞的线程。
- getId()：获取当前线程的ID。
- getName()：取得线程名称。
- currentThread()：静态函数取得当前线程对象。
- getPriority()/setPriority()：获取和设置线程的优先级。
- isAlive()：判断线程是否启动。

### 7.线程的优先级

- MIN_PRIORITY：优先级别 1 ；
- NORM_PRIORITY：优先级别 5 ；
- MAX_PRIORITY：优先级别 10 ；


## 多线程

### 1.多线程的意义

- 使用多线程可以提高体验或者避免 ANR (Application Not Responding)；
- **异步**，不需要等待返回结果就可以继续执行接下来的任务，比如加载图片。
- **同步**，需要等待返回结果才能继续执行接下来的任务，比如商品购买结果。
- 多任务，比如下载管理器。

### 2.同步

在多线程的应用中，两个或两个以上的线程需要共享对同一个数据的存取。如果两个线程存取相同的对象，并且每一个线程都调用了修改该对象的方法，这种情况通常会引发资源同步的问题。

Java线程同步的方式：同步方法、同步代码块、volatile、重入锁、wait与notify、ThreadLocal和阻塞队列。

由于同步的方式这个知识点比较重要，将会以单独的博客进行详细的学习，在此就不进行过多的描述。

### 3.异步

- Handler (包括 Handler、Looper、Message、MessageQueue)
- AsyncTask (封装了线程池和 Handler )

由于异步这个知识点十分重要，在面试中经常会出现，将会以单独的博客进行详细的学习，在此就不进行过多的描述。

### 4.并发( Concurrent ) 与 并行 ( Parallel )

- 并发：多个任务**交替**运行。
- 并行：多个任务**同时**运行。

![并发并行](https://i.imgur.com/tByzz1s.png)



## 线程池
### 1.线程池的好处
- 重用线程池中的线程，避免因为线程的创建和销毁所带来的性能开销。
- 控制线程并发数，避免大量的线程之间因为互相抢占系统资源而导致的阻塞现象。
- 有效的控制线程的执行，并提供定时执行以及指定间隔循环执行等功能。

### 2.ThreadPoolExecutor

Android 中的线程池来源于 Java，主要是通过 Executor 来派生特定类型的线程池，不同种类的线程池又具有各自的特性。

Executor 是一个接口，真正的线程池的实现为 ThreadPoolExecutor，它提供了一系列参数来配置线程池，通过不同的参数可以创建不同的线程池。其中，拥有最多参数的构造方法如下所示：

```java
    public ThreadPoolExecutor(int corePoolSize,
 int maximumPoolSize,
 long keepAliveTime,
 TimeUnit unit, 
BlockingQueue<Runnable> workQueue,
 ThreadFactory threadFactory, 
RejectedExecutionHandler handler) {
    }
```

- **corePoolSize**：线程池的核心线程数，默认情况下，核心线程会在线程池中一直存活，即使它们处于闲置状态。
- **maximumPoolSize**：线程池所能容纳的最大线程数，当活动数值达到这个数值后，后续的新任务将会被阻塞。
- **keepAliveTime**：非核心线程闲置时的超时时长，超过这个市场，非核心线程就会被回收。
- **unit**：用于指定 keepAliveTime 参数的时间单位，常用的有：TimeUnit.MICROSECONDS、TimeUnit.SECONDS 以及 TimeUnit.MINUTES 等。
- **workQueue**：线程池中的任务队列，通过线程池的 execute 方法提交的 Runnable 对象会存储在这个参数中。
- **threadFactory**：线程工厂，为线程池提供创建新线程的功能。ThreadFactory 是一个接口，它只有一个方法 Thread newThread(Runnable r)。



### 3.线程池的种类

- **FixedThreadPool**：是一个创建定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。

```java
    public void Fixedtest() {
        ExecutorService fixed = Executors.newFixedThreadPool(3);
        for (int i = 0; i < 10; i++) {
            final int finalI = i;
            fixed.execute(new Runnable() {
                @Override
                public void run() {
                    Log.d("TAG", Thread.currentThread().getName() + " " + finalI);
                }
            });
        }
    }
```

![](https://i.imgur.com/9xGy7XT.png)
	

- **CachedThreadPool**：是一个创建可缓存的线程池，如果线程长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。

```java
    public void Cachetest() {
        ExecutorService cache = Executors.newCachedThreadPool();
        for (int i = 0; i < 10; i++) {
            final int finalI = i;

            try {
                Thread.sleep(1000);//休眠1秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            cache.execute(new Runnable() {
                @Override
                public void run() {
                    Log.d("TAG", Thread.currentThread().getName() + " " + finalI);
                }
            });
        }
    }
```
![](https://i.imgur.com/2BjqjPX.png)

- **SingleThreadPool**：创建一个单个工作线程的线程池，保证所有任务按照指定顺序( FIFO,LIFO,优先级 )执行。

```java
public void SingleThreadExecutor() {
        ExecutorService single = Executors.newSingleThreadExecutor();
        for (int i = 0; i < 10; i++) {
            final int finalI = i;
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            single.execute(new Runnable() {
                @Override
                public void run() {
                    Log.d("TAG", Thread.currentThread().getName() + " " + finalI);
                }
            });
        }
    }
```

![](https://i.imgur.com/yWtd0o4.png)


- **ScheduledThreadPool**：是一个能实现定时和周期性任务的线程池。

```java
public void Scheduledtest() {
        Log.d("TAG", "begin: ");
        ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
        scheduledExecutorService.schedule(new Runnable() {
            @Override
            public void run() {
                Log.d("TAG", "delay 3 seconds");
            }
        }, 3, TimeUnit.SECONDS);
    }
```

![](https://i.imgur.com/Zmlj6k9.gif)


## 结束语

通过本次的学习，对 Java 的基础知识点进行了回顾，从中获得了不少过去没涉及到的知识点。相信在接下来的 线程同步、异步的学习中，能够更好的理解并且掌握。

## 参考文章

- 细说Java多线程之内存可见性：[https://www.imooc.com/learn/352](https://www.imooc.com/learn/352)
- 《Android开发艺术探索》
- 《Android进阶之光》