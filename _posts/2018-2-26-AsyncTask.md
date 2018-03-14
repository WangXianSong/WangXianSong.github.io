---
layout: post
title:  "Android 异步加载之AsyncTask"
date:  2018-2-26 22:14:09
categories: Android
tags: 学习笔记

---
* content
{:toc}






![](https://i.imgur.com/2RPUzdS.jpg)

## 前言

### 1、为什么要使用异步加载

- 提高用户体验，在加载数据的时候不会明显感觉到卡顿。
- Android 是单线程模型。

### 2、异步加载最常用的两种方式

- 线程池
- AsyncTask (底层也是基于线程池实现的)


## AsyncTask 

1、AsyncTask 是一种轻量级的异步任务类，它可以在线程池中执行后台任务，然后把执行的进度和最终结果传递给主线程并在主线程中更新 UI。

2、AsyncTask 屏蔽掉了多线程和后面要讲的 Handler 的概念。

3、不过封装越好越高级的 API，对初级程序员反而越不利。

4、也要掌握功能更强大的与 Main 线程通讯的方法：Handler 的使用。

5、AsyncTask 并不适合进程特别耗时的后台任务，如果您需要保持线程长时间运行，强烈建议您使用java.util.concurrent 包提供的各种 API，例如 Executor 线程池。


## AsyncTask的核心方法

### 1、构建 AsyncTask 子类的参数：

AsyncTask 是一个抽象的泛型类，它提供了 Params、Progress 和 Result 这三个泛型参数，如果 AsyncTask 不需要传递具体的参数，那么三个参数可以用 Void 来代替。(v需要大写)

- **Params**：启动任务时输入参数的类型。
- **Progress**：后台任务执行中返回进度值的类型。
- **Result**：后台执行任务完成后返回结果的类型。

```java
public abstract class AsyncTask<Parms, Progress, Result>{ . . . }
```

### 2、构建 AsyncTask 子类的回调方法：

AsyncTask提供了 4 个核心方法，他们的含义如下所示：

- **onPreExecute()**：在主线程中执行、在异步任务执行之前，做一些准备工作。
- **doInBackground(Params...params)**：在线程池中执行，异步任务中要执行的内容。
- **onPostExecute(Result result)**：在主线程中执行，在异步任务执行之后，此方法会被调用。
- **onProgressUpdate(Progress...values)**：在主线程中执行，当后台任务的执行进度发生改变时会调用此方法。在doInBackground()方法中调用 publishProgress()方法即回调当前方法。

### 3、AsyncTask 使用条件限制：

1、AsyncTask 的类必须在主线程中加载；
2、AsyncTask 的对象必须在主线程中创建；
3、execute 方法必须在 UI 线程调用；
4、不要在程序中直接调用四个回调方法；
5、一个 AsyncTask 对象只能执行一次，execute 只能用一次。

以下是简单的例子：
```java
public class MyAsyncTask extends AsyncTask<Void, Void, Void> {

    //必须实现的方法
    protected Void doInBackground(Void... voids) {
        Log.d("ss", "doInBackground: ");
        onProgressUpdate();
        return null;
    }
    protected void onPreExecute() {
        super.onPreExecute();
        Log.d("ss", "onPreExecute: ");
    }
    protected void onPostExecute(Void aVoid) {
        super.onPostExecute(aVoid);
        Log.d("ss", "onPostExecute: ");
    }
    //获取进度，更新进度条
    protected void onProgressUpdate(Void... values) {
        super.onProgressUpdate(values);
        Log.d("ss", "onProgressUpdate: ");
    }
}
```
> 在doInBackground 和 onProgressUpdate 方法中参数包含 ... 的字样，在 Java 中...表示参数的数量不定，他说一种数组型参数。

当要执行上述下载任务时，可以通过如下方式来完成：
```java
new MyAsyncTask().execute();
```
通过这个案例就知道回调方法的调用顺序：
onPreExecute -> doInBackground -> onProgressUpdate -> onPostExecute 



## AsyncTask的工作原理

我们从 execute 方法开始分析，execute 方法又会调用 executeOnExecutor 方法，如下所示：

```java
    public final AsyncTask<Params, Progress, Result> execute(Params... params) {
        return executeOnExecutor(sDefaultExecutor, params);
	//1、sDefaultExecutor实际上是串行的线程池，一个进程中所有的 AsyncTask 全部都会在这个串行线程池中排队执行。
    }

    public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params) {
        if (mStatus != Status.PENDING) {
            switch (mStatus) {
                case RUNNING:
                    throw new IllegalStateException("Cannot execute task:"
                            + " the task is already running.");
                case FINISHED:
                    throw new IllegalStateException("Cannot execute task:"
                            + " the task has already been executed "
                            + "(a task can be executed only once)");
            }
        }
        mStatus = Status.RUNNING;
        onPreExecute();
	 //2、在executeOnExecutor中，AsyncTask 的onPreExecute 方法最先执行，然后线程池开始执行。
        mWorker.mParams = params;
	 //3、AsyncTask 的 params 才参数传给 WorkerRunnable，并作为参数传给了 FutureTask 对象，
	 //FutureTask 是一个并发类，在这里它充当Runnable的作用。
        exec.execute(mFuture);
	 //4、接下来 FutureTask 会交给 SerialExecutor 的 execute 方法去处理。
        return this;

    }
```
下面分享线程池的执行过程，如下所示：
```java
    public static final Executor SERIAL_EXECUTOR = new SerialExecutor();
    private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;
    private final WorkerRunnable<Params, Result> mWorker;
    private final FutureTask<Result> mFuture;

    private static class SerialExecutor implements Executor {
        final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
	 //5、SerialExecutor 的 execute 方法首先会把 FutureTask 对象插入到任务队列 mTasks 中。
        Runnable mActive;

        public synchronized void execute(final Runnable r) {
            mTasks.offer(new Runnable() {
                public void run() {
                    try {
                        r.run();
                    } finally {
                        scheduleNext();
                    }
                }
            });
            if (mActive == null) {
                scheduleNext();
            }
	     //6、如果这时候没有正在活动的 AsyncTask 任务，
	     //那么就会调用 SerialExecutor 的 scheduleNext 方法来执行下一个任务。
        }

        protected synchronized void scheduleNext() {
            if ((mActive = mTasks.poll()) != null) {
                THREAD_POOL_EXECUTOR.execute(mActive);
            }
	     //7、当一个 AsyncTask 任务执行完后，
	     //AsyncTask会继续执行其他任务直到所有的任务都被执行为止，
	     //从这一点可以看出，在默认情况下，AsyncTask 是串行执行的。
        }
    }
```
AsyncTask 中有两个线程池( SerialExecutor 和 THREAD_POOL_EXECUTOR )和一个 Handler( InternalHandler )，他们的用法是：

- **SerialExecutor**： 用于任务的排队；
- **THREAD_POOL_EXECUTOR**： 用于真正地执行任务；
- **InternalHandler**： 用于执行环境从线程池切换到主线程；


在AsyncTask 的构造方法中，由于 FutureTask 的 run 方法会调用 mWorker 的 call 方法，因此mWorker 的call 方法最终会在线程池中执行。

```java
    public AsyncTask(@Nullable Looper callbackLooper) {
        mWorker = new WorkerRunnable<Params, Result>() {
            public Result call() throws Exception {
                mTaskInvoked.set(true);
		  Result result = null;
                    Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
                return postResult(doInBackground(mParams););
            }
        };
    }
```
首先理清关系，mWorker 是 WorkerRunnable 的子类，WorkerRunnable 实现了 Callable 的接口，并实现了它的 call 方法，在call 方法中调用了 doInBackground(mParams)来处理任务并得到结果，并最终调用postResult将结果投出去。
```java
    private Result postResult(Result result) {
        @SuppressWarnings("unchecked")
        Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
                new AsyncTaskResult<Result>(this, result));
        message.sendToTarget();
        return result;
    }
```
在 postResult 方法中会创建 Message，将结果赋值给这个 Message，通过 getHandler 方法得到 Handler，并通过这个 Handler 发送消息。
```java
    private static Handler getMainHandler() {
        synchronized (AsyncTask.class) {
            if (sHandler == null) {
                sHandler = new InternalHandler(Looper.getMainLooper());
            }
            return sHandler;
        }
    }

    private Handler getHandler() {
        return mHandler;
    }
```
在getHandler 方法中创建了 InternalHandler，InternalHandler定义如下：
```java
private static class InternalHandler extends Handler {
        public InternalHandler(Looper looper) {
            super(looper);
        }

        @SuppressWarnings({"unchecked", "RawUseOfParameterizedType"})
        @Override
        public void handleMessage(Message msg) {
            AsyncTaskResult<?> result = (AsyncTaskResult<?>) msg.obj;
            switch (msg.what) {
                case MESSAGE_POST_RESULT:
                    // There is only one result
                    result.mTask.finish(result.mData[0]);
                    break;
                case MESSAGE_POST_PROGRESS:
                    result.mTask.onProgressUpdate(result.mData);
                    break;
            }
        }
    }
```
在收到 MESSAGE_POST_RESULT 消息后会调用 AsyncTask 的 finish 方法，代码如下：
```java
    private void finish(Result result) {
        if (isCancelled()) {
            onCancelled(result);
        } else {
            onPostExecute(result);
        }
        mStatus = Status.FINISHED;
    }
```
如果 AsyncTask 任务被取消了，则执行 onCancelled 方法，否则调用 onPostExecute 方法。可以看到doInBackground 的返回结果会传递给 onPostExecute 方法。
```java
    protected void onCancelled(Result result) {
        onCancelled();
    }
    protected void onCancelled() {
    }
```
具体的取消方法当然是由自己去重写，AsyncTask 的整个工作过程分析完毕。




## AsyncTask的面试相关

**Q1：什么是 AsyncTask**

AsyncTask 本质上封装了线程池和 Handler 的异步框架，主要是用来执行异步任务的。它可以方便的在主线程和子线程中灵活地切换。

**Q2：AsyncTask 内部原理**

面试中问到 AsyncTask 源码的这一块的还是比较少，所以记住它的框架整体结构就行。

1、AsyncTask 的本质是一个静态的线程池，AsyncTask 派生的子类可以实现不同的异步任务，这些任务都是提交到静态的线程池中执行。 

2、线程池中的工作线程通过 doInBackground 方法来执行异步任务。

3、当任务状态改变之后，工作线程会想UI线程发送消息，AsyncTask 内部的 InternalHandler相应这些消息，并调用相关的回调函数。

总结：内部封装了线程池，然后通过Handler来发送消息，在UI线程和子线程之间传递。



**Q3：内存泄露**

AsyncTask 和Handler都有，原因差不多，非静态内部类持有外部类的匿名引用，导致外部类Activity被回收时，由于非静态内部类还持有外部类的引用导致外部类无法被内存回收导致的内存泄露。

解决方法：
可以在外部的 Activity 生命周期的 onDestroy里面进行finish。

**Q4：生命周期**

AsyncTask 有一些后台任务在 doInBackground 中执行，如果没主动调用 AsyncTask 的 onCancelled 方法的话，它是不会被终止的，导致崩溃。所以必须在 Activity 生命周期的 onDestroy 里面销毁 AsyncTask 来保证程序的稳定。

**Q5：结果丢失**

在屏幕旋转、Activity 被重新创建，之前运行的 AsyncTask 会持有之前 Activity 的引用，但是这个引用已经无效了，所以这时调用 AsyncTask 的 onPostExecute方法去更新界面这是不会生效。

**Q6：并行or串行**

在android1.6之前都是串行，所有的任务在线程池中有序的进行执行。  在1.6 到 2.3 的版本中改成并行。在 2.3 之后为了系统的稳定，又改成了串行。但仍然能实现并行，可以调用 executeOnExecutor这个方法就可以并行。一般建议只用串行，这样能保证整个线程池的稳定。并行效率虽然高，但是 AsyncTask 做不了什么太耗时的工作。


## 附录：参考资料

- 《Android进阶之光》刘望舒
- 《Android开发艺术探索》任玉刚
- 慕课网 Android必学-AsyncTask基础：[https://www.imooc.com/learn/377](https://www.imooc.com/learn/377)















 








