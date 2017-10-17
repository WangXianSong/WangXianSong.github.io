---
layout: post
title:  "学习《Android开发艺术探索》的知识总结"
date:  2017-10-11 17:08:56
categories: Android
tags: Android
---
* content
{:toc}

最近学完Android开发的入门书籍《第一行代码》，为了更好的掌握知识点，接下来开始学习Android的进阶知识书籍《Android开发艺术探索》。





![](https://i.imgur.com/kXgxhal.jpg)

**《Android开发艺术探索》是一本怎么样的书？**

> 《Android开发艺术探索》是一本Android进阶类书籍，采用理论、源码和实践相结合的方式来阐述高水准的Android应用开发要点。《Android开发艺术探索》从三个方面来组织内容。第一，介绍Android开发者不容易掌握的一些知识点；第二，结合Android源代码和应用层开发过程，融会贯通，介绍一些比较深入的知识点；第三，介绍一些核心技术和Android的性能优化思想。


> 
《Android开发艺术探索》侧重于Android知识的体系化和系统工作机制的分析，通过《Android开发艺术探索》的学习可以极大地提高开发者的Android技术水平，从而更加高效地成为高级开发者。而对于高级开发者来说，仍然可以从《Android开发艺术探索》的知识体系中获益。

这是来自[百度百科](https://baike.baidu.com/item/Android%E5%BC%80%E5%8F%91%E8%89%BA%E6%9C%AF%E6%8E%A2%E7%B4%A2/18526051?fr=aladdin)的介绍，在我眼中已经不单是进阶书籍那么简单，而是突破我目前的樽颈位的学习阶段。为了更好的学习，我会在边学习边总结，将知识点整理出来，方便以后的复习。

## 第1章 Activity的生命周期和启动模式

- 1.1 Activity的生命周期解析
  - 1.1.1 一般情况下的生命周期分析
  - 1.1.2 异常情况下的生命周期分析
- 1.2 Activity的启动模式
  - 1.2.1 Activity的四种LaunchMode
  - 1.2.2 Activity的Flags
- 1.3 IntentFilter的匹配规则

### 1.1 一般情况下的生命周期分析

关于Activity生命周期这个知识点，虽然在刚开始学习Android的时候就有接触，而且也专门对Activity生命周期作了总结，但是由于接触层面比较浅，印象并没有那么深刻[(当时的学习总结)](http://blog.csdn.net/qq_26849491/article/details/51241356)。通过这次的学习，使这知识点在我脑海里更深刻了些，同时也让我清楚在哪个环节应该做些什么后台处理。

　　**onCreate**：表示Activity正在被创建，可以做一些初始化的工作。<br />
　　**onStart**：表示Activity正在被启动，Activity已经可见了，但仍在后台。<br />
　　**onResume**：表示Activity已经可见了，并且出现在前台，并开始活动。<br />
　　**onPause**：表示Activity正在停止，正常情况下，紧接着onStop就会被调用。在特殊情况下，如果快速回到当前Activity，那么onResume会被调用，所以此时可以做一些存储数据、停止动画等工作。(不能做耗时操作) <br />
　　**onStop**：表示Activity即将停止，可以做一些稍微重量级的回收工作。(尽量不要太耗时) <br />
　　**onDestroy**：表示Activity即将被销毁，可以做一些回收工作和最终资源释放。 <br />
　　**onRestart**：表示Activity正在重新启动，当当前Activity从不可见重新变为可见状态时就会调用onRestart，切换过程为：onPause->onStop->(用户返回原Activity)->onRestart。 <br />



**重要笔记：**

　　1、当两个Activity进行相互跳转时，旧Activity的onPause先调用，然后才启动新的Activity的onCreate，所以不能在onPause执行耗时操作。 <br />
　　2、如果新Activity采用了透明主题，那么当前Activity的onStop方法不会被调用； <br />


### 1.2 异常情况下的生命周期分析


　　**情况1：资源相关的系统配置发生变化导致Activity被杀死并重新创建。**

　　(1)Activity在异常情况下被回收时，系统会调用onSaveInstanceState方法来保存当前Activity的状态，调用时机是在onStop之前。  <br />
　　(2)Activity被重新创建后，系统会调用onRestoreInstanceState，并且把Activity销毁时onSaveInstanceState方法所保存的Bundle对象作为参数同时传给onRestoreInstanceState和onCreate方法。<br />


　　**情况2：资源内存不足导致低优先级的Activity被杀死。**

　　(1)前台Activity——正在和用户交互的Activity优先级最高； <br />
　　(2)可见但非前台Activity——比如Activity弹出的对话框； <br />
　　(3)后台Activity——已经被暂停的Activity，比如执行了onStop，优先级最低； <br />

**重要笔记：**

　　１、onSavedInstanceState和onRestoreInstanceState只会在Activity被异常终止的情况下被调用，正常情况下系统不会回调这两个方法，并且onRestoreInstanceState一旦被调用，其参数bundle必定为非空，不需要在方法内做空值判断； <br />
　　2、如果当系统配置中某项发生改变时，我们不想系统重新创建Activity，可以在AndroidManifest.xml中对应Activity标签声明时加上
`android:configChanges="orientation|screenSize"` 即可。同时还可以onConfigurationChanged方法做一些自己的特殊处理。<br />


### 1.2 Activity的启动模式

　　Android有四种启动模式：standard、singleTop、singleTask和singleInstance。

　　定义方法：
	```XML
	<activity
            android:name=".MainActivity3"
            android:launchMode="singlelnstance">
        </activity>
	```









