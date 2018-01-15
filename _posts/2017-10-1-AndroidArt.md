---
layout: post
title:  "第一章 Activity 的生命周期和启动模式"
date:  2017-10-1 17:08:56
categories: Android
tags: Android
---
* content
{:toc}


>最近学完Android开发的入门书籍《第一行代码》，为了更好的掌握知识点，接下来开始学习Android的进阶知识书籍《Android开发艺术探索》。

>此篇文章为《Android开发艺术探索》第一章“Activity 的生命周期和启动模式”的总结，内容比较易懂：Activity的生命周期分析、启动模式、IntentFilter的匹配规则。






![](https://i.imgur.com/kXgxhal.jpg)

**《Android开发艺术探索》是一本怎么样的书？**

> 《Android开发艺术探索》是一本Android进阶类书籍，采用理论、源码和实践相结合的方式来阐述高水准的Android应用开发要点。《Android开发艺术探索》从三个方面来组织内容。第一，介绍Android开发者不容易掌握的一些知识点；第二，结合Android源代码和应用层开发过程，融会贯通，介绍一些比较深入的知识点；第三，介绍一些核心技术和Android的性能优化思想。


> 《Android开发艺术探索》侧重于Android知识的体系化和系统工作机制的分析，通过《Android开发艺术探索》的学习可以极大地提高开发者的Android技术水平，从而更加高效地成为高级开发者。而对于高级开发者来说，仍然可以从《Android开发艺术探索》的知识体系中获益。


## 第1章 Activity的生命周期和启动模式

### 1.1  Activity的生命周期解析

####  1.1.1一般情况下的生命周期分析

关于Activity生命周期这个知识点，虽然在刚开始学习Android的时候就有接触，而且也专门对Activity生命周期作了总结，但是由于接触层面比较浅，印象并没有那么深刻[(当时的学习总结)](http://blog.csdn.net/qq_26849491/article/details/51241356)。通过这次的学习，使这知识点在我脑海里更深刻了些，同时也让我清楚在哪个环节应该做些什么后台处理。


- **onCreate**：表示Activity正在被创建，可以做一些初始化的工作。
- **onStart**：表示Activity正在被启动，Activity已经可见了，但仍在后台。
- **onResume**：表示Activity已经可见了，并且出现在前台，并开始活动。
- **onPause**：表示Activity正在停止，正常情况下，紧接着onStop就会被调用。在特殊情况下，如果快速回到当前Activity，那么onResume会被调用，所以此时可以做一些存储数据、停止动画等工作。(不能做耗时操作) 
- **onStop**：表示Activity即将停止，可以做一些稍微重量级的回收工作。(尽量不要太耗时) 
- **onDestroy**：表示Activity即将被销毁，可以做一些回收工作和最终资源释放。
- **onRestart**：表示Activity正在重新启动，当当前Activity从不可见重新变为可见状态时就会调用onRestart，切换过程为：onPause->onStop->(用户返回原Activity)->onRestart。


**重要笔记：**

　　1、当两个Activity进行相互跳转时，旧Activity的onPause先调用，然后才启动新的Activity的onCreate，所以不能在onPause执行耗时操作。 <br />
　　2、如果新Activity采用了透明主题，那么当前Activity的onStop方法不会被调用； 



####  1.1.2 异常情况下的生命周期分析

　　**情况1：资源相关的系统配置发生变化导致Activity被杀死并重新创建。**

　　(1)Activity在异常情况下被回收时，系统会调用onSaveInstanceState方法来保存当前Activity的状态，调用时机是在onStop之前。  <br />
　　(2)Activity被重新创建后，系统会调用onRestoreInstanceState，并且把Activity销毁时onSaveInstanceState方法所保存的Bundle对象作为参数同时传给onRestoreInstanceState和onCreate方法。


　　**情况2：资源内存不足导致低优先级的Activity被杀死。**

　　(1)前台Activity——正在和用户交互的Activity优先级最高； <br />
　　(2)可见但非前台Activity——比如Activity弹出的对话框； <br />
　　(3)后台Activity——已经被暂停的Activity，比如执行了onStop，优先级最低； 

**重要笔记：**

　　１、onSavedInstanceState和onRestoreInstanceState只会在Activity被异常终止的情况下被调用，正常情况下系统不会回调这两个方法，并且onRestoreInstanceState一旦被调用，其参数bundle必定为非空，不需要在方法内做空值判断； <br />
　　2、如果当系统配置中某项发生改变时，我们不想系统重新创建Activity，可以在AndroidManifest.xml中对应Activity标签声明时加上
` android:configChanges="orientation|screenSize"`即可。同时还可以onConfigurationChanged方法做一些自己的特殊处理。


### 1.2 Activity的启动模式

#### 1.2.1 Activity的四种LaunchMode

　　Android有四种启动模式：standard、singleTop、singleTask和singleInstance。

　　定义方法：

```xml
<activity
       android:name=".MainActivity"
       android:launchMode="singlelnstance">
</activity>   
```

- **standard**：标准模式，这也是系统的默认模式。每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在；
- **singleTop**：栈顶复用模式。<font color="#dd0000">(1)</font> 如果新Activity已经位于任务栈的栈顶，那么此Activity不会被重新创建，同时它的onNewIntent方法会被回调，通过此方法的参数我们可以取出当前的请求信息。<font color="#dd0000">(2)</font> 需要注意的是，这个Activity的onCreate、onStart不会被系统调用，因为它并没有发生改变。<font color="#dd0000">(3)</font> 如果新的Activity的实例已经存在但不是位于栈顶，那么新的Activity仍然会重新创建； 
- **singleTask**：栈内复用模式。这是一种单实例模式，在这种情况下，只要Activity在一个栈中存在，那么多次启动此Activity都不会重新创建实例，和singleTop一样，系统也会回调其onNewIntent；
- **singleInstance**：单实例模式，这是一种加强的singleTask模式，它除了具有singleTask模式的所有特性外，还加强一点，那就是具有此种模式的Activity只能单独地位于一个任务栈中。


#### 1.2.2 Activity的Flags

- **FLAG_ACTIVITY_NEW_TASK：**这个标记位的作用是为Activity指定"singleTask"启动模式，其效果和在XML中指定该启动模式相同；
- **FLAG_ACTIVITY_SINGLE_TOP：**这个标记位的作用是为Activity指定"singleTop"启动模式，其效果和在XML中指定该启动模式相同； 
- **FLAG_ACTIVITY_CLEAR_TOP：**具有此标记位的Activity，当它启动时，在同一个任务栈中所有位于它上面的Activity都要出栈；
- **FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS：**具有这个标记的Activity不会出现在历史Activity的列表中，当某些情况下我们不希望用户通过历史列表回到我们的Activity的时候这个标记比较有用。它等同于在XML中指定Activity的属性"android:excludeFromRecents="true""。

### 1.3 IntentFilter的匹配规则

- **action匹配规则：**要求intent中的action 存在 且 必须和过滤规则中的其中一个相同 区分大小写； 
- **category匹配规则：**系统会默认加上一个android.intent.category.DEAFAULT，所以intent中可以不存在category，但如果存在就必须匹配其中一个； 
- **data匹配规则：**data由两部分组成，mimeType和URI，要求和action相似。如果没有指定URI，URI但默认值为content和file（schema）







