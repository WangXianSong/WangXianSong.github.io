---
layout: post
title:  "Android面试题笔记"
date:  2018-4-5 19:58:15
categories: 面试
tags: 面试

---
* content
{:toc}





## 前言：面试前的准备：

### 面试过程中的要求：

- 1.礼貌：态度谦虚
- 2.听懂问题直接回答

### 面试技巧

- 1.根据简历来面试、根据项目需求来面试、根据牛人来面试。
- 2.遇到难题时，可以向面试官提出，“我没有听清楚，刚出可能外面有些噪音，你能不能重复一下问题”，来为自己争取时间。实在回答不出，也不能说不知道，可以将题目拆分来成123小点来逐一回答。可以离题、可以扯别的问题。
- 3.实在不会就可以说下想法就好，绝对不可以说不知道。

### 面试建议：

- 1.选一个自己相对比较擅长的领域。（源码深入）
- 2.基础要背（灵活背）
- 3.试着去了解这个领域市面上的技术。
- 4.如果有时间 研究其中一个众所周知的库的源码。




## 一、基本概念

### Android 四大组件是什么

- **Activity 活动**：Activity用于显示界面，它上面可以显示控件、监听控件并处理用户的事件做出响应。

- **Service 服务**：是Android 中实现程序后台运行的解决方案，适合不需要和用户交互而且还要求长期运行的任务。依赖于创建服务的程序，程序被杀掉，服务也停止运行。服务不会自动开启线程，需要服务内部创建子线程。

- **Broadcast Receiver 广播接收器**：应用程序可以使用它对感兴趣的外部事件(如当电话呼入时，或者数据网络可用时)进行接收并做出响应。广播接收器没有用户界面。然而，它们可以启动一个 Activity 或 Serice 来响应它们收到的信息，或者用 NotificationManager 来通知用户。通知可以用很多种方式来吸引用户的注意力──闪动背灯、震动、播放声音等。

- **Content Provider内容提供者**：主要用于在不同的应用程序之间实现数据共享的功能，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。与其他存储方式不同的是：可以选择哪部分数据分享，从而保证隐私数据的安全性。

### Android平台的framework的层次结构

- Linux Kernel(**Linux内核**)
- Hardware Abstraction Layer(**HAL硬件抽象层**)
- Native C/C++Libraies(**原生C/C++库**) Android Runtime(**安卓运行时**)
- Java API Framework(**Java API 框架** )
- Applications(**系统应用**)

### Android中常用的布局。

答：常用四种布局方式，分别是：FrameLayout（帧布局），LinearLayout （线性布局），AbsoluteLayout（绝对布局），RelativeLayout（相对布局）。

（1）**FrameLayout**：所有东西依次都放在左上角，会重叠，这个布局比较简单，也只能放一点比较简单的东西。

（2）**LinearLayout**：线性布局，每一个LinearLayout里面又可分为垂直布局（android:orientation="vertical"）和水平布局（android:orientation="horizontal" ）。当垂直布局时，每一行就只有一个元素，多个元素依次垂直往下；水平布局时，只有一行，每一个元素依次向右排列。

（3）**AbsoluteLayout**：绝对布局用X,Y坐标来指定元素的位置，这种布局方式也比较简单，但是在屏幕旋转时，往往会出问题，而且多个元素的时候，计算比较麻烦。

（4）**RelativeLayout**：相对布局可以理解为某一个元素为参照物，来定位的布局方式。主要属性有：相对于某一个元素android:layout_below、 android:layout_toLeftOf相对于父元素的地方android:layout_alignParentLeft、android:layout_alignParentRigh；

### 动画有哪几类，它们的特点和区别是什么

**(1) 视图动画**：定义了透明度、平移、缩放、旋转动画。实现原理：是每次绘画视图时，View 所在的 ViewGroup 中的 drawChild 函数获取该 View 的 Animation 的 Transformation 值，然后调用 canvas.concat(transformToApply.getMatrix())，通过矩阵运算完成动画帧。缺点：是不具备交互性，当某个元素发生 View 动画后，其响应事件的位置仍然在动画前的地方。


**(2) 属性动画**：在一定时间间隔内，通过不断对值进行改变，并不断将该值赋给对象的属性，从而实现该对象在该属性上的动画效果，响应点击事件的有效区域也会发生改变。比较常用的几个动画类是：**ValueAnimator**(注重过程)、**ObjectAnimator**(具体操作) 和 **AnimatorSet**(组合)，其中 ObjectAnimator 继承自 ValueAnimator，AnimatorSet 是动画集合。

### Drawable

### Android 中有哪几种解析xml的类

官方推荐哪种？以及它们的原理和区别。

Android 提供了三种解析XML的方式：**SAX(Simple API XML)** ，**DOM(Document Object Model)**， **Pull解析** 

- **SAX解析方式**：(Simple API for XML)解析器是一种基于事件的解析器，事件驱动的流式解析方式是，从文件的开始顺序解析到文档的结束，不可暂停或倒退。 

- **DOM解析方式**：

- **Pull解析方式**：







## 二、Activity 面试详解

### 1、Activity的四种状态：

- **running**：处于活动状态，用户可以点击屏幕，屏幕会做出响应，处于Activity 的栈顶。
- **paused**：处于失去焦点状态或者被一个非全屏的Activity占据，又或者被一个透明的Activity放置栈顶。Activity只是失去和用户的交互能力，处于内存紧张状态。
- **stopped**：被另外的 Activity 全屏覆盖，不再是可见的，
- **killed**：Activity 已经被系统回收掉了。

### 2、Activity 在各种情况下的生命周期：

- **Activity启动** ：onCreate()初始化 -> onstart()可见了 -> onResume()可交互了
- **锁屏或Home键或新覆盖**： onPause() -> onStop()有可能被杀掉
- **解锁或回到前台**：onRestart()重新启动 -> onStart() -> onResume() 
- **退出 或Back键** ：onPause() -> onStop() -> onDestroy()回收，资源释放。
- **弹出对话框**：不会执行任何生命周期(注：对话框如果是Activity(Theme为Dialog)，还是会执行生命周期的)
- **从A跳转到B**：当B的主题为透明时，A只会执行onPause（A-onPause->B-(onCreate->onStart->onResume)）
- **从A跳转到B**：A-onPause->B-(onCreate->onStart->onResume)-A-onStop  (注意是B执行onResume后，A才执行onStop，所以尽量不要在onPause中做耗时操作)
- **从B返回到A**：B-onPause->A-(onRestart->onStart->onResume)-B-(onStop->onDestroy)

### 3、Activity之间的通信方式

- 1)在Intent跳转时携带数据 
- 2)借助类的静态变量
- 3)借助全局变量 Application
- 4)借助外部存储来实现通讯
  - 借助 SharedPreference 
  - 使用Android数据库 SQLite 
  - 赤裸裸的使用 File 
- 5)借助Service

### 4、Activity上有 Dialog 时按Home键的生命周期

**生命周期是**：onCreate() -> onStart() -> onResume -> 启动Dialog-> home键 -> onPause() -> onStop() 

 其实就是一个很正常的 Activity 生命周期，并没有什么特别的地方，但是注意 onPause 方法和 onStop 方法是在我点击 Home 键之后才有的，这就说明对话框的出现并没有使 Activity 进入后台。而是点击Home键才使Activity进入后台工作。AlertDialog对话框实际上是Activity的一个组件。

### 5、横竖屏切换时Activity的生命周期；如何将横竖屏切换对应的影响降至最低？

1、不设置 Activity的android:configChanges 时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次。

2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次。

3、设置 Activity的 android:configChanges="orientation\|keyboardHidden" 时，切屏不会重新调用各个生命周期，只会执行 onConfigurationChanged 方法。

### 6、Activity与Fragment之间生命周期比较

- **Activity**——onCreate->onStart->onResume->onPause->onStop->onDestroy
- **Fragment**——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume ->onPause->onStop->onDestroyView->  onDestroy->onDetach

### 7、Activity 进程优先级

- **前台进程**：处于与用户交互的 Activity，或者在前台绑定的 Service。
- **可见进程**：处于前台，但用户又不能点击的情况下。
- **服务进程**：在后台开启 Service服务。
- **后台进程**：用户按了Home键回到了桌面，根据内存情况作出相应的回收。
- **空进程**：优先级最低，处于缓存的目的而保留，系统随时杀掉。

### 8、Activity 启动模式

- **standard**：标准模式，这也是系统的默认模式。每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在；

- **singleTop**：栈顶复用模式。
  - (1) 如果新Activity已经位于任务栈的栈顶，那么此Activity不会被重新创建，同时它的**onNewIntent**方法会被回调；
  - (2) 需要注意的是，这个Activity的onCreate、onStart不会被系统调用，因为它并没有发生改变；
  - (3) 如果新的Activity的实例已经存在但不是位于栈顶，那么新的Activity仍然会重新创建；

- **singleTask**：栈内复用模式。这是一种单实例模式，在这种情况下，只要Activity在一个栈中存在，那么多次启动此Activity都不会重新创建实例，和singleTop一样，系统也会回调其onNewIntent；

- **singleInstance**：单实例模式，这是一种加强的singleTask模式，它除了具有singleTask模式的所有特性外，还加强一点，那就是具有此种模式的Activity只能单独地位于一个任务栈中。

### 9、scheme 跳转协议

**scheme** 是一种页面跳转协议，是一种非常好的实现机制，通过定义自己的scheme协议，可以非常方便跳转app的各个页面；通过scheme 协议，服务器可以定制化告诉App跳转哪个页面，可以通过通知栏消息定制化跳转页面，可以通过H5页面跳转页面。

- 1.服务端下发url，客户端根据url跳转到相应的页面。
- 2.H5跳转到App相应的Activity。
- 3.App根据url跳转到另一个App指定页面。

### 10、Activity状态保存于恢复

在《第一行代码》的 63 页中有详细介绍：
Activity中提供了一个onSaveInstanceState()回调方法，只要在代码中将临时数据保存在Bundle类型中，在Activity的onCreate方法中去获取数据即可。

### 快速退出所有Activity(关闭多个Activity)？

只需要用一个专门的收集类对所有的活动进行管理就可以了，在《第一行代码》的72页中有详细介绍：

新建ActivityCollector类作为活动管理器。









## 三、Fragment面试详解

### Fragment 是什么？

碎片是一种可以嵌入在Activity当中的UI片段，它能让程序更加合理和充分地利用在大屏幕的空间，因而在平板上应用非常广泛，是Android自3.0版本开始引入的。

### Fragment 为什么被称为 第五大组件

- 首先在使用频率上，Fragment是不属于四大组件的范畴，他有自己的生命周期。
- 同时它可以灵活动态加载到 Activity 当中去。
- 而且 Fragment 并不像 Activity 那样独立的，虽然有自己的生命周期，但需要依附Activity。

### Fragment 加载到Activity的两种方式

- **静态加载**：作为XML标签添加到 Activity 的布局文件当中。(过程省略...)
- **动态加载**：动态在 Activity 中添加 Fragment 。 (常用)

```java
//前提1：先创建 Fragment1.java，并继承 Fragment，在onCreatview中初始化fragment的XML布局文件
//前提2：在Fragment1的XML布局文件中准备好容器R.id.lin_id

//步骤1：添加FragmentTransaction的实例
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction transaction = fragmentManager.beginTransaction();
//步骤2：用add()加Fragment对象fragment1，并绑定到其XML页面里的容器内
Fragment1 fragment1 = new Fragment1();
transaction.add(R.id.lin_id, fragment1, "fragment1");
//步骤3：调用commit方法使得FragmentTransaction实例的改变生效
transaction.commit();
```

### FragmentPagerAdapter 与 FragmentStatePagerAdaper 区别：

- **FragmentPagerAdapter** ：FragmentPagerAdapter 在切换 ViewPager 的时候，只是把 Fragment的UI 与 Activity的UI 脱离开来，并不回收内存。所以它适用页面**减少**的情况。(在其源码 DestroyItem 方法中的最后一行，mCurTransaction.**detach** 可知)

- **FragmentStatePagerAdaper** ：由于 FragmentStatePagerAdaper 在每次切换 ViewPager 的时候，它是回收内存的。又因为在页面较多的情况下会更耗内存，所以它适合页面**较多**的情况。(在源码 DestroyItem 方法中的最后一行，mCurTransaction.**remove** 可知 )

### Fragment 的生命周期

**onAttach**:表示在 Fragment 与 Activity 关联之后所回调的。

**onCreate**:表示初次创建 Fragment 的时候调用。(仅用来创建，但为完成)

**onCreateView**:系统在 Fragment 的首次**绘制**用户界面的时候调用。

**onViewCreated**:表示 Fragment 的UI界面已经绘制好了，可以初始化里面的控件资源。

*Activity - onCreate*:初始化Activity 的布局和数据之类。

**onActivityCreated**:在 Activity - onCreate()调用完成后才可以被调用，表示Activity被渲染绘制成功以后的调用方法。

*Activity - onStart*:表明 Activity 可见了。

**onStart**:表明 Fragment 也可见了。

*Activity - onResume*:表示 Activity 可以和用户交互了。

**onResume**:表示 Fragment 也可以和用户交互了。

  ↑  (到此为止已经完成了 Fragment 从启动到展现的操作。)

  ↓ (回退Fragment)

**onPause**: Fragment 无法和用户交互。

*Activity - onPause*:整个Activity 也无法和用户交互。

**onStop**:做一些相应的保存与释放。

*Activity - onStop*:做一些相应的保存与释放。

**onDestroyView**:对应创建的onCreateView，表示 Fragment 即将结束，然后会被保存。

**onDestroy**:表示 Fragment 不再被使用。

**onDetach**: Fragment 的最后一个方法，Fragment不再被使用。

*Activity - onDetach*:整个Activity被回收了。

简单的Fragment流程图—— onAttach->onCreate -> onCreateView -> onActivityCreated -> onStart -> onResume  ->创建成功-> onPause->onStop->onDestroyView -> onDestroy->onDetach

### Fragment 之间的通信  (课后复习)

- 在 Fragment 中调用 Activity 中的 **getActivity** 方法；
- 在 Activity(实现) 中调用 Fragment(创建接口) 中的方法 **接口回调；**
- 在 Fragment 中调用 Fragment 中的 **findFragmentById** 方法；

### Fragment 的 replace、add、remove 方法

- **replace** 是 FragmentManager 的方法，是将 Activity 最上层的 Fragment **替换**掉。
- **add** 将 Fragment 的实例添加到 Activity 的**最上层**；
- **remove** 将 Fragment 的实例从 Activity 的队列中**删除**；

### Fragment 特点

- Fragment 可以作为 Activity 界面的一部分组成出现；
- 可以在一个 Activity 中同时出现多个 Fragment，并且一个 Fragment 也可以在多个 Activity 中使用；
- 在 Activity 运行过程中，可以添加、移除或者替换 Fragment；
- Fragment 可以响应自己的输入事件，并且有自己的生命周期，它们的生命周期会受宿主 Activity 的生命周期影响。
- Fragment 可以轻松得创建动态灵活的 UI 设计，可以适应于不同的屏幕尺寸。从手机到平板电脑。
- Fragment 解决 Activity 间的切换不流畅，轻量切换。
- Fragment 替代 TabActivity 做导航，性能更好。
- Fragment 做局部内容更新更方便，原来为了达到这一点要把多个布局放到一个 activity 里面，现在可以用多 Fragment 来代替，只有在需要的时候才加载 Fragment，提高性能。

### Fragment嵌套多个Fragment会出现bug吗？

参考：http://blog.csdn.net/megatronkings/article/details/51417510

### 怎么理解Activity和Fragment的关系？

- Fragment 拥有和 Activity 一致的生命周期，它和 Activity 一样被定义为 Controller 层的类。有过中大型项目开发经验的开发者，应该都会遇到过 Activity 过于臃肿的情况，而 Fragment 的出现就是为了缓解这一状况，可以说 它将屏幕分解为多个「Fragment（碎片）」（这句话很重要），但它又不同于 View，它干的实质上就是 Activity 的事情，负责控制 View 以及它们之间的逻辑。
- 将屏幕碎片化为多个 Fragment 后，其实 Activity 只需要花精力去管理当前屏幕内应该显示哪些 Fragments，以及应该对它们进行如何布局就行了。这是一种组件化的思维，用 Fragment 去组合了一系列有关联的 UI 组件，并管理它们之间的逻辑，而 Activity 负责在不同屏幕下（例如横竖屏）布局不同的 Fragments 组合。
- 这种碎片不单单能管理可视的 Views，它也能执行不可视的 Tasks，它提供了 retainInstance 属性，能够在 Activity 因为屏幕状态发生改变（例如切换横竖屏时）而销毁重建时，依然保留实例。这示意着我们能在 RetainedFragment 里面执行一些在屏幕状态发生改变时不被中断的操作。例如使用 RetainedFragment 来缓存在线音乐文件，它在横竖屏切换时依然维持下载进度，并通过一个 DialogFragment 来展示进度。

### 遇到过哪些关于Fragment的问题，如何处理的？

- **getActivity()空指针**：这种情况一般发生在在异步任务里调用getActivity()，而Fragment已经onDetach()，此时就会有空指针，解决方案是在Fragment里使用 一个全局变量mActivity，在onAttach()方法里赋值，这样可能会引起内存泄漏，但是异步任务没有停止的情况下本身就已经可能内存泄漏，相比直接crash，这种方式 显得更妥当一些。

- **Fragment视图重叠**：在类onCreate()的方法加载Fragment，并且没有判断saveInstanceState==null或if(findFragmentByTag(mFragmentTag) == null)，导致重复加载了同一个Fragment导致重叠。（PS：replace情况下，如果没有加入回退栈，则不判断也不会造成重叠，但建议还是统一判断下）

```java
@Override 
protected void onCreate(@Nullable Bundle savedInstanceState) {
// 在页面重启时，Fragment会被保存恢复，而此时再加载Fragment会重复加载，导致重叠 ;
    if(saveInstanceState == null){
    // 或者 if(findFragmentByTag(mFragmentTag) == null)
       // 正常情况下去 加载根Fragment 
    } 
}
```
### Fragment 管理器 FragmentManager
### fragment各种情况下的生命周期
### Fragment状态保存 startActivityForResult 是哪个类的方法，在什么情况下使用？
### 如何实现Fragment的滑动？
ViewPager + Fragment
### fragment之间传递数据的方式？


## 三、Service面试详解

### Service 是什么？

Service 是一种可以在后台执行长时间运行操作而没有用户界面的应用组件。(Service 不能做耗时操作)

### Service 和 BroadcastReceiver 共同点

都是运行在主线程当中，都不能做长时间的耗时操作。

### Service 和 Thread 的区别

- **定义**：Thread 是程序执行的最小单元，它是分配CPU的最小单位。可以执行异步操作，是相对**独立**的。
Service 是Android的一种特殊机制，Service是运行在主线程当中的，是**依托于**所在的主线程。是由系统进程托管，也是一种轻量级IPC通信方式(Activity 和 Service 绑定，然后数据通信，并处于不同进程。 )

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
  - 5.Service是为了弥补 Activity 被销毁之后，无法获取之前所创建的子线程实例，并对后台进行控制的情况而产生的。

### 开启 Service 的两种方式以及区别

- **StartService**

1.定义一个类继承 Service
2.在Manifest.xml文件中配置该 Service
3.使用Context的startService(Intent)方法启动Service
4.不再使用时，调用 stopService(Intent) 方法停止该服务。

- **bindService**:

绑定服务提供了客户端和服务端接口，进行数据的交互(发送请求、获取结果、进程间通信)
1.创建BindService服务端，继承自Service，并在类中创建一个实例IBinder接口实现对象并提供公共方法给客户端调用。
2.从onBind()回调方法返回此Binder实例
3.在客户端中，从onServiceConnected()回调方法接收Binder，并使用提供的方法调用绑定服务。


### Service 的生命周期

- startService()：开启Service，调用者退出后Service仍然存在。
- bindService()：开启Service，调用者退出后Service也随即退出。

**Service生命周期**：

- 只是用startService()启动服务：onCreate() -> onStartCommand() -> onDestory
- 只是用bindService()绑定服务：onCreate() -> onBind() -> onUnBind() -> onDestory
- 同时使用startService()启动服务与bindService()绑定服务：onCreate() -> onStartCommnad() -> onBind() -> onUnBind() -> onDestory


### Activity如何与Service通信？

可以通过bindService的方式，先在Activity里实现一个ServiceConnection接口，并将该接口传递给bindService()方法，在ServiceConnection接口的onServiceConnected()方法 里执行相关操作。

### Activity 怎么和 Service 绑定？
### 怎么在Activity 中启动自己对应的Service？











## 四、Content Provider

### Content Provider是什么？

**内容提供者**：主要用于在不同的应用程序之间实现数据共享的功能，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。与其他存储方式不同的是：可以选择哪部分数据分享，从而保证隐私数据的安全性。

### ContentProvider 的特点

1. 四大组件之一，需要在Mainifest.xml文件中进行注册。
2. 接口的统一，并不能用于存储数据，只是为数据的获取、添加、修改的操作提供统一的接口。
3. 跨进程供多个应用程序共享数据；
4. 设备存储数据：通讯录、图片；
5. 数据更新监听：UI更新；

### ContentProvider 的优缺点

1. 数据访问统一接口（存储方式都不用管）优点。 
2. 跨进程数据的访问 优点。
3. 无法单独使用，必须与其他的存储方式结合使用 缺点。

### 如何实现数据的访问？

- ContentResolver   Contex.getContentResolver()。
    - 提供了CRUD操作；
    - Transport implements；
    - IContentProvider；

- Uri，ContentResolver中的增删查改都是用Uri来代替表名参数。
    - 概念：统一资源标识符；
    - 组成：协议、域名authority、路径path；
    - content://com.example.app.authority/path；
    - 作用：访问ContentProvider；
    - 不同：与其他组件交互不同的地方(Intent)；

### ContentProvider工作流程

1. 应用程序A(提供者)定义了 ContentProvider，定义了一些数据库和文件来存放数据。
2. 应用程序B(访问者)获得 ContentResolver 对象，根据Uri访问指定的 ContentProvider。
3. ContentProvider 就会访问底层的存储方式，并返回数据给 ContentProvider。
4. ContentProvider 将数据返回给 ContentResolver，ContentResolver 再转到应用B。

### 访问设备数据

在访问数据之前要了解：存放的位置、特点(数据库形式存联系人、File存头像)、Uri(地址)。

### 更新设备数据

1、ContentResoler：注册内容观察者
2、ContentService：通过handler来更新UI
3、ContentObserver

```java
public class Main2Activity extends AppCompatActivity {

    Handler mHandler = new Handler() {
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            //在主线程更新UI  adapter.notifyChangeSet();
        }
    };
    ContentObserver observer = new ContentObserver(mHandler) {
        public void onChange(boolean selfChange) {
            super.onChange(selfChange);
            //Uri数据改变，非Ui线程不能直接更新
            //发送消息
        }
    };
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //注册内容观察者
        this.getContentResolver().registerContentObserver(uri, notifyForDescendants, observer);
    }
}
```
### ContentResolver 访问数据

```java
List<String> contactsList = new ArrayList<>();
Cursor mCursor = getContentResolver().query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
        null, null, null, null);
if (mCursor != null) {
    while (mCursor.moveToNext()) {
        String displayName = mCursor.getString(mCursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME));
        String number = mCursor.getString(mCursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
        contactsList.add(displayName + "\n" + number);
    }
}
mCursor.close();
```
### ContentProvider 提供数据

1. 新建类去继承ContentProvider，并实现6个抽象方法
2. 借助UriMather实现匹配内容URI的功能。

### ContentObserver 内容观察者

### 谈谈你对 ContentProvider 的理解

### 说说ContentProvider、ContentResolver、ContentObserver 之间的关系

- **ContentProvider**：管理数据，提供数据的增删改查操作，数据源可以是数据库、文件、XML、网络等，ContentProvider为这些数据的访问提供了统一的接口，可以用来做进程间数据共享。
- **ContentResolver**：ContentResolver可以不同URI操作不同的ContentProvider中的数据，外部进程可以通过ContentResolver与ContentProvider进行交互。
- **ContentObserver**：观察ContentProvider中的数据变化，并将变化通知给外界。















## 五、Broadcast Receiver

### 1、广播的定义

- 在 Android 中，Broadcast 是一种在应用程序之间传输信息的机制，Android 中我们要发送的广播内容是一个 Intent，这个 Intent 中可以携带我们要传送的数据。

- 广播实现了不同程序之间的**(1)信息传输与共享**，只要和发送广播的 action 相同的接收者，都能接收到这个广播。还可以作为**(2)通知**的作用，发送消息给 service 来更新 UI。

- 类似设计模式中的“观察者模式”，当被观察者数据发生变化的时候，会去相应的通知观察者做相应的数据处理。

### 2、广播的使用场景

- 同个 App 具有多个进程的不同组件之间的消息通信。
- 不同 App 之间的组件之间消息通信。

### 3、广播的种类

- **普通广播 Normal Broadcast**：异步执行的广播，所有接收者在同一时刻收到这条广播消息。效率高，没有先后顺序，无法截断。属于全局广播。调用 sendBroadcast() 发送，最常用的广播。

- **有序广播 Ordered Broadcast**：同步执行的广播，发出去的广播会被广播接受者按照顺序接收，广播接收者按照Priority属性值从大-小排序，Priority属性相同者，动态注册的广播优先，广播接收者还可以选择对广播进行截断和修改。调用sendOrderedBroadcast()发送。

### 4、注册广播接收 (静态和动态)

- **(1)静态注册** : 将广播写在 AndroidMainifest.xml 文件当中，特点是：常驻系统，不受组件生命周期影响，即便应用退出，广播还是可以被接收，耗电、占内存。

```java
//首先创建 Broadcast Receiver文件，Exported 属性表示是否允许这个广播接收本程序以外的广播，Enabled 属性表示是否启用用这个广播接收器。
public class MyReceiver extends BroadcastReceiver {
        //onReceive 不能做过多的耗时操作，因为它不能开启子线程。
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "boot complete", Toast.LENGTH_SHORT).show();
    }
}
```

在AndroidMainifest.xml中进行修改：

```xml
<receiver
    android:name=".MyReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
```

- **(2)动态注册** : 在代码中调用 registerReceiver() 注册来进行广播的注册。必须在 onDestroy 中调用 unregisterReceiver() 方法，否则会引起内存泄露，特点是：不常驻，跟随组件的生命变化，组件结束，广播结束。在组件结束前，需要先移除广播，否则容易造成内存泄漏。

```java
       IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
        registerReceiver(ChangeReceiver, intentFilter);

    //在onDestroy()中要取消注册，否则会引起内存泄漏。
    protected void onDestroy() {
        unregisterReceiver(networkChangeReceiver);
    }
    //ChangeReceiver就是接收后会怎么样怎么样
```

### 5、广播的发送：(标准、有序、本地广播)

- **(1)标准广播**：

```xml
    <intent-filter>
        <action android:name="com.example.broadcasttest.LOCAL_BROADCAST" />
    </intent-filter>
```

```java
//通过sendBroadcast发送标准合家欢广播
sendBroadcast(new Intent("com.example.broadcasttest.LOCAL_BROADCAST"));
```

- **(2)有序广播**：
	1. 给广播接收器设置优先级：

```xml
    <intent-filter android:priority="100">
        <action android:name="com.example.broadcasttest.LOCAL_BROADCAST" />
    </intent-filter>
```
	 2. 广播接收器截断：
```java
public void onReceive(Context context, Intent intent) {
    abortBroadcast();
}
```
	3. 发送广播：
```java
//通过sendOrderedBroadcast发送传递广播
sendOrderedBroadcast(new Intent("com.example.broadcasttest.LOCAL_BROADCAST"),null);
```

- **(3)本地广播**：

```java
//获取LocalBroadcastManager实例
LocalBroadcastManager lbm = LocalBroadcastManager.getInstance(this);
//注册本地广播监听器(接收广播,intent)，以下是简介版
lbm.registerReceiver(new BroadcastReceiver() {
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(Main2Activity.this, "本地广播简介版", Toast.LENGTH_SHORT).show();
    }
}, new IntentFilter("com.example.broadcasttest.LOCAL_BROADCAST"));
//通过sendBroadcast来发送广播
lbm.sendBroadcast(new Intent("com.example.broadcasttest.LOCAL_BROADCAST"));
```

### 6、广播内部实现机制

1. 自定义广播接收者 BroadcastReceiver，并复写 onRecvice();
2. 通过 Binder 机制向 AMS(Activity Manager Service) 注册广播；
3. 通过 Binder 机制向 AMS(Activity Manager Service) 发送广播。
4. AMS 查找符合相应条件(IntentFilter/Permission等)的BroadcastReceiver，将广播发送到BroadcastReceiver 所在的消息循环队列中。
5. BroadcastReceiver 所在消息队列拿到此广播后，回调它的 onReceive() 方法。

### 7、AMS 是什么？

 AMS(Activity Manager Service)：是贯穿Android系统组件的核心服务，负责启动四大组件启动切换调度。

### 8、本地广播 LocalBroadcastManager 详解

 - 发送的广播只能够在自己 App 的内部传递，不会泄露给其他 App，确保隐私数据不会泄露；
 - 广播接收器只能接收来自本 App 发出的广播；
 - 其他App也无法向你的App发送该广播，不用担心其他App会来搞破坏；
 - 比系统的全局广播更加高效。

1. LocalBroadcastManager 高效的原因主要因为它内部是通过Handler实现的，它的sendBroadcast()方法是通过handler()发送一个Message实现的。
2. 相比系统广播是通过Binder实现的，本地广播会更加高效。别人应用无法向自己的App发送广播，而自己App发送的广播也不会离开自己的App。
3. LocalBroadcastManager 内部协作主要是靠两个Map集合：mReceivers和mActions，当然还有一个List集合mPendingBroadcasts，主要是存储待接收的广播对象。

### 9、全局广播的缺点

- App被反编译获得Action后，会被植入广告、数据泄露。

### 10、BroadcastReceiver 和 LocalBroadcastReceiver 区别

- **BroadcastReceiver** 是跨应用广播，利用Binder机制实现。
- **LocalBroadcastReceiver** 是应用内广播，利用Handler实现，利用了IntentFilter的match功能，提供消息的发布与接收功能，实现应用内通信，效率比较高。

### 广播传输的数据是否有限制，是多少，为什么要限制？


## 六、webview安全漏洞

### WebView优化了解吗，如何提高WebView的加载速度？
为什么WebView加载会慢呢？

> 这是因为在客户端中，加载H5页面之前，需要先初始化WebView，在WebView完全初始化完成之前，后续的界面加载过程都是被阻塞的。

优化手段围绕着以下两个点进行：

1. 预加载WebView。
1. 加载WebView的同时，请求H5页面数据。

因此常见的方法是：

1. 全局WebView。
1. 客户端代理页面请求。WebView初始化完成后向客户端请求数据。
1. asset存放离线包。

除此之外还有一些其他的优化手段：

- 脚本执行慢，可以让脚本最后运行，不阻塞页面解析。
- DNS与链接慢，可以让客户端复用使用的域名与链接。
- React框架代码执行慢，可以将这部分代码拆分出来，提前进行解析。

### Java和JS的相互调用怎么实现，有做过什么优化吗？

jockeyjs：[https://github.com/tcoulter/jockeyjs](https://github.com/tcoulter/jockeyjs)

对协议进行统一的封装和处理。

## 七、Binder

Android Binder是用来做进程通信的，Android的各个应用以及系统服务都运行在独立的进程中，它们的通信都依赖于Binder。

为什么选用Binder，在讨论这个问题之前，我们知道Android也是基于Linux内核，Linux现有的进程通信手段有以下几种：

1. 管道：在创建时分配一个page大小的内存，缓存区大小比较有限；
1. 消息队列：信息复制两次，额外的CPU消耗；不合适频繁或信息量大的通信；
1. 共享内存：无须复制，共享缓冲区直接付附加到进程虚拟地址空间，速度快；但进程间的同步问题操作系统无法实现，必须各进程利用同步工具解决；
1. 套接字：作为更通用的接口，传输效率低，主要用于不通机器或跨网络的通信；
1. 信号量：常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。6. 信号: 不适用于信息交换，更适用于进程中断控制，比如非法内存访问，杀死某个进程等；

既然有现有的IPC方式，为什么重新设计一套Binder机制呢。主要是出于以上三个方面的考量：

- **高性能**：从数据拷贝次数来看Binder只需要进行一次内存拷贝，而管道、消息队列、Socket都需要两次，共享内存不需要拷贝，Binder的性能仅次于共享内存。
- **稳定性**：上面说到共享内存的性能优于Binder，那为什么不适用共享内存呢，因为共享内存需要处理并发同步问题，控制负责，容易出现死锁和资源竞争，稳定性较差。而Binder基于C/S架构，客户端与服务端彼此独立，稳定性较好。
- **安全性**：我们知道Android为每个应用分配了UID，用来作为鉴别进程的重要标志，Android内部也依赖这个UID进行权限管理，包括6.0以前的固定权限和6.0以后的动态权限，传荣IPC只能由用户在数据包里填入UID/PID，这个标记完全 是在用户空间控制的，没有放在内核空间，因此有被恶意篡改的可能，因此Binder的安全性更高。

## 八、Handler

![](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/native/process/android_handler_structure.png)

主要涉及的角色如下所示：

- Message：消息，分为硬件产生的消息（例如：按钮、触摸）和软件产生的消息。
- MessageQueue：消息队列，主要用来向消息池添加消息和取走消息。
- Looper：消息循环器，主要用来把消息分发给相应的处理者。
- Handler：消息处理器，主要向消息队列发送各种消息以及处理各种消息。

整个消息的循环流程还是比较清晰的，具体说来：

1. Handler通过sendMessage()发送消息Message到消息队列MessageQueue。
1. Looper通过loop()不断提取触发条件的Message，并将Message交给对应的target handler来处理。
1. target handler调用自身的handleMessage()方法来处理Message。

## 九、AsyncTask
## 十、HandlerThread
## 十一、IntentService

## 十二、View绘制
### 描述一下View的绘制原理？
View的绘制流程主要分为三步：

- **onMeasure**：测量视图的大小，从顶层父View到子View递归调用measure()方法，measure()调用onMeasure()方法，onMeasure()方法完成测量工作。
- **onLayout**：确定视图的位置，从顶层父View到子View递归调用layout()方法，父View将上一步measure()方法得到的子View的布局大小和布局参数，将子View放在合适的位置上。
- **onDraw**：绘制最终的视图，首先ViewRoot创建一个Canvas对象，然后调用onDraw()方法进行绘制。onDraw()方法的绘制流程为：① 绘制视图背景。② 绘制画布的图层。 ③ 绘制View内容。 ④ 绘制子视图，如果有的话。⑤ 还原图层。⑥ 绘制滚动条。

## 十三、View事件分发
### 描述一下Android的事件分发机制？

Android事件分发机制的本质：事件从哪个对象发出，经过哪些对象，最终由哪个对象处理了该事件。此处对象指的是Activity、Window与View。

Android事件的分发顺序：Activity（Window） -> ViewGroup -> View

Android事件的分发主要由三个方法来完成，如下所示：

```java
// 父View调用dispatchTouchEvent()开始分发事件
public boolean dispatchTouchEvent(MotionEvent event){
    boolean consume = false;
    // 父View决定是否拦截事件
    if(onInterceptTouchEvent(event)){
        // 父View调用onTouchEvent(event)消费事件，如果该方法返回true，表示
        // 该View消费了该事件，后续该事件序列的事件（Down、Move、Up）将不会在传递
        // 该其他View。
        consume = onTouchEvent(event);
    }else{
        // 调用子View的dispatchTouchEvent(event)方法继续分发事件
        consume = child.dispatchTouchEvent(event);
    }
    return consume;
}
```

## 十四、Listview缓存
## 十五、Android目录结构
## 十六、Android目录构建
## 十七、git版本控制器
## 十八、gradle
## 十九、proguard代码混淆
## 二十、volley网络框架
## 二一、okhttp网络框架
## 二二、retrofit网络框架
## 二三、butterknife注解框架
## 二四、glide图片框架
## 二五、ANR异常
## 二六、OOM异常

**1、什么是OOM**：OutOfMemoryError(OOM)内存泄露，当 JVM 因为没有足够的内存来为对象分配空间并且垃圾回收器也已经没有空间可回收时，就会抛出这个异常。

**2、为什么会OOM**：

- **(1)瞬时加载了一些资源**，例如，视频、图片、音频等等的内存申请大小超过了App的额定内存值，解决方案：对资源可能需要申请大内存的地方做压缩处理。
- **(2)调用registerRecevier() 后未调用 unregisterReceiver()**, 解决方案：在每一次动态注册的时候，记得在在适当的地方（Activity的OnDestory()）取消注册。
- **(3)数据库cursor没有关闭**，解决方案：使用完cursor及时关闭。
- **(4)构造Adapter没有使用缓存contentview**， 解决方案：在构造Adapter的时候，使用ContentView缓存页面，节省内存。
- **(5)未关闭InputStream/OutputStream** , 解决方案：在使用到IO Stream 的时候，及时关闭。
- **(6)Bitmap使用后未调用recycle() **，解决方案：在Bitmap不在需要被加载到内存中的收获，做回收处理。
- **(7)Context泄露，内部类持有外部类的引用。** 解决方案：  第一： 将线程的内部类，改为静态内部类  第二：在线程内部采用弱引用保存Context引用。
- **(8)static 原因**。 解决方案： 
	1. 应该尽量避免static成员变量引用资源耗费过多的实例，比如Context。
	1. Context尽量使用Application Context，因为Application的Context的生命周期比较长，引用它不会出现内存泄露的问题。
	1. 使用WeakReference代替强引用。比如可以使用WeakReference<Context> mContextRef;
- 查找内存泄漏可以使用Android Profiler工具或者利用LeakCanary工具。

**3、为什么Android会有APP的内存限制**：

- **(1)要开发者使用内存更加合理。**限制每个应用可用内存上限，避免恶意程序或单个程序使用过多内存导致其他程序的不可运行。有了限制，开发者就必须合理使用资源，优化资源使用
- **(2)屏幕显示内容有限，内存足够即可**。即使有万千图片千万数据需要使用到，但在特定时刻需要展示给用户看的总是有限的，因为屏幕显示就那么大，上面可以放的信息就是很有限的。大部分信息都是处于准备显示状态，所以没必要给予太多heap内存。必须一个ListView显示图片，打个比方这个ListView含有500个item，但是屏幕显示最多有10调item显示，其余数据是处于准备显示状态。
- **(3)Android多个虚拟机Davlik的限制需要。**android设备上的APP运行，每打开一个应用就会打开至少一个独立虚拟机。这样可以避免系统崩溃，但代价是浪费更多内存。

## 二七、bitmap

### 如何计算一个Bitmap占用内存的大小，怎么保证加载Bitmap不产生内存溢出？
Bitamp 占用内存大小 = 宽度像素 x （inTargetDensity / inDensity） x 高度像素 x （inTargetDensity / inDensity）x 一个像素所占的内存

注：这里inDensity表示目标图片的dpi（放在哪个资源文件夹下），inTargetDensity表示目标屏幕的dpi，所以你可以发现inDensity和inTargetDensity会对Bitmap的宽高 进行拉伸，进而改变Bitmap占用内存的大小。

在Bitmap里有两个获取内存占用大小的方法。

- getByteCount()：API12 加入，代表存储 Bitmap 的像素需要的最少内存。
- getAllocationByteCount()：API19 加入，代表在内存中为 Bitmap 分配的内存大小，代替了 getByteCount() 方法。

在不复用 Bitmap 时，getByteCount() 和 getAllocationByteCount 返回的结果是一样的。在通过复用 Bitmap 来解码图片时，那么 getByteCount() 表示新解码图片占用内存的大 小，getAllocationByteCount() 表示被复用 Bitmap真实占用的内存大小（即 mBuffer 的长度）。

为了保证在加载Bitmap的时候不产生内存溢出，可以受用BitmapFactory进行图片压缩，主要有以下几个参数：

- BitmapFactory.Options.inPreferredConfig：将ARGB_8888改为RGB_565，改变编码方式，节约内存。
- BitmapFactory.Options.inSampleSize：缩放比例，可以参考Luban那个库，根据图片宽高计算出合适的缩放比例。
- BitmapFactory.Options.inPurgeable：让系统可以内存不足时回收内存。

### Android如何在不压缩的情况下加载高清大图？
使用BitmapRegionDecoder进行布局加载。

## 二八、UI卡顿
## 二九、内存泄露
## 三十、内存管理
## 三一、冷启动优化
## 三二、其他优化
## 三三、MVC架构设计模式
## 三四、MVP架构设计模式
## 三五、MVVM架构设计模式
### MVC、MVP与MVVM之间的对比分析？

![](https://github.com/BeesAndroid/BeesAndroid/raw/master/art/practice/project/module/mvp_structure.png)

- **MVC**：PC时代就有的架构方案，在Android上也是最早的方案，Activity/Fragment这些上帝角色既承担了V的角色，也承担了C的角色，小项目开发起来十分顺手，大项目就会遇到 耦合过重，Activity/Fragment类过大等问题。
- **MVP**：为了解决MVC耦合过重的问题，MVP的核心思想就是提供一个Presenter将视图逻辑I和业务逻辑相分离，达到解耦的目的。
- **MVVM**：使用ViewModel代替Presenter，实现数据与View的双向绑定，这套框架最早使用的data-binding将数据绑定到xml里，这么做在大规模应用的时候是不行的，不过数据绑定是 一个很有用的概念，后续Google又推出了ViewModel组件与LiveData组件。ViewModel组件规范了ViewModel所处的地位、生命周期、生产方式以及一个Activity下多个Fragment共享View Model数据的问题。LiveData组件则提供了在Java层面View订阅ViewModel数据源的实现方案。

## 三六、android插件化
### 了解插件化和热修复吗，它们有什么区别，理解它们的原理吗？
- 插件化：插件化是体现在功能拆分方面的，它将某个功能独立提取出来，独立开发，独立测试，再插入到主应用中。依次来较少主应用的规模。
- 热修复：热修复是体现在bug修复方面的，它实现的是不需要重新发版和重新安装，就可以去修复已知的bug。
利用PathClassLoader和DexClassLoader去加载与bug类同名的类，替换掉bug类，进而达到修复bug的目的，原理是在app打包的时候阻止类打上CLASS_ISPREVERIFIED标志，然后在 热修复的时候动态改变BaseDexClassLoader对象间接引用的dexElements，替换掉旧的类。

目前热修复框架主要分为两大类：

- Sophix：修改方法指针。
- Tinker：修改dex数组元素。


## 三七、android热更新
## 三八、进程保活

### 进程保活如何做，如何唤醒其他进程？
进程保活主要有两个思路：

1. 提升进程的优先级，降低进程被杀死的概率。
1. 拉活已经被杀死的进程。

如何提升优先级，如下所示：

监控手机锁屏事件，在屏幕锁屏时启动一个像素的Activity，在用户解锁时将Activity销毁掉，前台Activity可以将进程变成前台进程，优先级升级到最高。

如果拉活：利用广播拉活Activity。

## 三九、设计模式
观察者模式、动态代理 、工厂、策略类、装饰、桥接、单例等常用设计模式
## 四十、网络协议
https／http、dns、tcp／ip以及加密算法
## 四一、java知识
GC/回收算法／堆栈／、反射／编译时vs运行时、注解（结合android annotation库）、范型、线程池／并发编程、Socket、IO/NIO、集合框架、类加载器、异常、继承／组合／多态、引用类型／内存泄漏、java虚拟机

## Intent
### Android里的Intent传递的数据有大小限制吗，如何解决？
Intent传递数据大小的限制大概在1M左右，超过这个限制就会静默崩溃。处理方式如下：

- 进程内：EventBus，文件缓存、磁盘缓存。
- 进程间：通过ContentProvider进行款进程数据共享和传递。

## APK的打包流程
### 了解APK的打包流程吗，描述一下？
Android的包文件APK分为两个部分：代码和资源，所以打包方面也分为资源打包和代码打包两个方面，这篇文章就来分析资源和代码的编译打包原理。

APK整体的的打包流程如下图所示：

![](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/native/vm/apk_package_flow.png)

具体说来：

1. 通过AAPT工具进行资源文件（包括AndroidManifest.xml、布局文件、各种xml资源等）的打包，生成R.java文件。
1. 通过AIDL工具处理AIDL文件，生成相应的Java文件。
1. 通过Javac工具编译项目源码，生成Class文件。
1. 通过DX工具将所有的Class文件转换成DEX文件，该过程主要完成Java字节码转换成Dalvik字节码，压缩常量池以及清除冗余信息等工作。
1. 通过ApkBuilder工具将资源文件、DEX文件打包生成APK文件。
1. 利用KeyStore对生成的APK文件进行签名。
1. 如果是正式版的APK，还会利用ZipAlign工具进行对齐处理，对齐的过程就是将APK文件中所有的资源文件举例文件的起始距离都偏移4字节的整数倍，这样通过内存映射访问APK文件 的速度会更快。


### 了解APK的安装流程吗，描述一下？

![](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/app/package/apk_install_structure.png)

1. 复制APK到/data/app目录下，解压并扫描安装包。
1. 资源管理器解析APK里的资源文件。
1. 解析AndroidManifest文件，并在/data/data/目录下创建对应的应用数据目录。
1. 然后对dex文件进行优化，并保存在dalvik-cache目录下。
1. 将AndroidManifest文件解析出的四大组件信息注册到PackageManagerService中。
1. 安装完成后，发送广播。



## 性能优化
### 如何做性能优化？

1. 节制的使用Service，当启动一个Service时，系统总是倾向于保留这个Service依赖的进程，这样会造成系统资源的浪费，可以使用IntentService，执行完成任务后会自动停止。
1. 当界面不可见时释放内存，可以重写Activity的onTrimMemory()方法，然后监听TRIM_MEMORY_UI_HIDDEN这个级别，这个级别说明用户离开了页面，可以考虑释放内存和资源。
1. 避免在Bitmap浪费过多的内存，使用压缩过的图片，也可以使用Fresco等库来优化对Bitmap显示的管理。
1. 使用优化过的数据集合SparseArray代替HashMap，HashMap为每个键值都提供一个对象入口，使用SparseArray可以免去基本对象类型转换为引用数据类想的时间。

### 如何防止过度绘制，如何做布局优化？

1. 使用include复用布局文件。
1. 使用merge标签避免嵌套布局。
1. 使用stub标签仅在需要的时候在展示出来。

### 如何提交代码质量？

1. 避免创建不必要的对象，尽可能避免频繁的创建临时对象，例如在for循环内，减少GC的次数。
1. 尽量使用基本数据类型代替引用数据类型。
1. 静态方法调用效率高于动态方法，也可以避免创建额外对象。
1. 对于基本数据类型和String类型的常量要使用static final修饰，这样常量会在dex文件的初始化器中进行初始化，使用的时候可以直接使用。
1. 多使用系统API，例如数组拷贝System.arrayCopy()方法，要比我们用for循环效率快9倍以上，因为系统API很多都是通过底层的汇编模式执行的，效率比较高。


























### Android中数据存储方式

Android中有5种数据存储方式，分别为文件存储、SQLite数据库、SharedPreferences、ContentProvider、网络。每种存储方式的特点如下：

- 1）**文件存储/SD卡**：文件存储方式是一种较常用的方法，在Android中读取/写入文件的方法，与Java中实现I/O的程序是完全一样的，提供openFileInput()和openFileOutput()方法来读取设备上的文件。

   - 将数据存储到文件中：

```java
String data = "data to save";
FileOutputStream out = openFileOutput("文件名", 覆盖:MODE_PRIVATE 追加:MODE_APPEND);
BufferedWriter writer = new BufferedWriter(new OutputStreamWriter());
writer.write(data);
writer.close();
```

   - 将数据存储到文件中：

```java
FileInputStream in = openFileInput("文件名");
BufferedReader reader = new BufferedReader(new InputStreamReader());
StringBuffer content = new StringBuffer();
String line = "";
while ((line.reader.readLine()) != null) {
    content.append(line);
}
reader.close();
return content.toString();
```


- 2）**SQLite数据库**：SQLite是Android所集成的一个轻量级的嵌入式数据库，它不仅可以使用Andorid API操作，同时它也支持SQL语句进行增删改查等操作。

```java

public class MyDatabaseHelper extends SQLiteOpenHelper {
    private Context mContext;
    public static final String CREATE_BOOK = "create table Book(" +
            "id integer primary key autoincrement," +
            "author text," +
            "price readl," +
            "price integer," +
            "name text)";
    public MyDatabaseHelper(Context context, String name,
                            SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
        mContext = context;
    }
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_BOOK);
    }
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        //升级数据库的话先修改版本号
        //drop删除表，delete是删除数据
        db.execSQL("drop table if exists Book");
        onCreate(db);
    }
}

public class Main2Activity extends AppCompatActivity {
    private MyDatabaseHelper dbHepler;
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        dbHepler = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        //都是打开和创建，区别在于空间满的情况w是出现异常，R是只读
        //dbHepler.getWritableDatabase();
        dbHepler.getReadableDatabase();

        //添加
        SQLiteDatabase db = dbHepler.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("name", "tom");
        values.put("pages", 464);
        values.put("price", 14.23);
        db.insert("Book", null, values);
        //更新
        SQLiteDatabase db = dbHepler.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("name", "HHH");
        db.update("Book", values, "price>?", new String[]{"10"});
        //删除
        //查询
    }
}
```

- 3）**SharedPreferences**：是Android提供的用于存储一些简单配置信息的一种机制，采用了XML格式将数据存储到设备中。不仅可以在同一个包下使用，还可以访问其他应用程序的数据，但是由于SharedPreferences的局限性，在实际操作中很少用来读取其他应用程序的数据。

	3.1 将数据存储到SharedPreferences：

	(1)Context类中的getSharedPreferences()方法：第一个参数是文件名，第二个参数是操作模式 MODE_PRIVATE只有当前程序才能读写。

	(2)Activity类中的getPreferences()方法：自动将当期活动类名作为SharedPreferences的文件名。

	(3)PreferenceManager类中的getDefaulSharedPreferences()方法：当前程序的包名作为前缀来命名SharedPreferences文件。

	步骤：

	(1)调用SharedPreferences对象的edit()方法来获取SharedPreferences.Editor对象。

	(2)向 SharedPreferences.Editor 对象中添加数据，比如putBoolean、putInt、putString

	(3)调用apply方法提交数据。

```java
SharedPreferences.Editor editor = getSharedPreferences("data", MODE_PRIVATE).edit();
editor.putString("name", "Tom");
editor.putInt("age", 28);
editor.putBoolean("married", false);
```

	3.2从SharedPreferences中读取数据：

```java
SharedPreferences pref  = getSharedPreferences("data",MODE_PRIVATE);
String name = pref.getString("name","默认值");
int age = pref.getInt("age",0);
boolean married = pref.getBoolean("married",false);
```

- 4）**ContentProvider**：并不能用户存储数据。主要用于不同应用程序之间共享数据，只是为我们存储以及添加数据制定统一的接口而已。

- 5）**网络存储数据**：通过网络上提供的存储空间来上传(存储)或下载(获取)我们存储在网络空间中的数据信息

### 共享数据的方式
1.File， 2.Sqlite，3.Content Provider，4.Service，5.Broadcast Receiver，6.Intent

### Binder

**Binder** 是Android系统进程间通信（IPC）方式之一。Linux已经拥有的进程间通信IPC手段包括(Internet Process Connection)： 管道（Pipe）、信号（Signal）和跟踪（Trace）、插口（Socket）、报文队列（Message）、共享内存（Share Memory）和信号量（Semaphore）。本文详细介绍Binder作为Android主要IPC方式的优势。

### ART、Dalvik和JVM的关系及区别是什么？

- **ART** 就是 Android Runtime ，是安卓4.4之后的系统的新的虚拟机模式，改模式提升了运行效率，启用该模式之后，系统在安装APP的时候，会进行一次预编译，把代码转成机器语言存储在本地，这样运行的时候效率就高了。

- **Dalvik** 是一种安卓系统在上面运行的虚拟机，因为安卓系统是以Linux 为底层构建的，为了更加高效的适配到各种不同的硬件设备上面，就创建了这个Dalvik 虚拟机，该虚拟机可以将程序的语言由java转成机器语言二进制运行，然而每次开启运用的时候都会执行一次编译，所以效率不是很高，所以我们需要ART，增加效率。

- **JVM** 是 java虚拟机，是实现java夸平台的主要方式，可以使得java这样的高级语言编译成机器可以识别的机器语言，这样使得java 一次编译，到处运行。


### Handler机制原理



### ANR

 - **什么是ANR**： 在Android中，如果应用程序有一段时间响应不够灵敏，系统会向用户显示**应用程序无响应**（ANR：Application Not Responding）对话框。用户可以选择让程序继续运行或者关闭程序。

 - **ANR产生的原因**：ANR产生的根本原因是APP阻塞了UI线程，不同的组件发生ANR 的时间不一样，主线程 Activity 是 5 秒，Service是 20 秒，BroadCastReceiver 是 10 秒。AsyncTask是5秒，Handler也是5秒。

 - **怎样避免ANR**：让耗时的工作（比如数据库操作，I/O，连接网络或者别的有可能阻碍UI线程的操作）把它放入单独的线程处理。


### Java GC 原理

**(1)GC 垃圾回收机制**(Gabage Collection) 在jvm运行时，由于所需要存放的对象越来越多，java堆就得想办法回收已不再用的对象来节省空间，那么垃圾回收机制在进行垃圾回收前就得判断哪些实例已不再使用，哪些实例还要使用，以便于回收不再使用的实例。

**(2)判断对象是否已死**：jvm在回收垃圾之前，得先判断哪些实例是不再需要的，不能乱回收。

- 引用计数算法
- 可达性分析算法

**(3)垃圾收集算法**：

- **标记--清除算法**：先标记再清除，当对象被统一标记后，再统一回收。

- **复制算法**：把内存按容量分为两个相同的块。每次只使用其中的一块，当一块用完后，就将存活的对象复制到另一块，然后再清理已使用的空间。

- **标记--整理算法**：标记--整理算法的思想和标记--清除算法类似。但后续步骤不是清除可回收的对象，而是把存活的对象都向一端移动，然后直接清除掉边界以外的内存。

- **分代收集算法**：根据对象存活的情况不同，把内存分为新生代和年老代，新生代对象存活率低，就用复制算法，年老代存活率高，就用标记--清除算法或者标记--整理算法。


### OOM原理()








### Volley
### RxJava+ Retrofit+okhttp

现在Android 市面上很火的当然是 Retrofit＋RxJava + OkHttp, 功能强大，简单易用。

- **Retrofit**：是 Square 公司开发的一款正对Android 网络请求的框架。底层基于OkHttp 实现。负责请求的数据和请求的结果，使用接口的方式呈现。

- **OkHttp**: 负责请求的过程。

- **RxJava**：负责异步，各种线程之间的切换。

### RxJava

> http://gank.io/post/560e15be2dca930e00da1083

- RxJava 的异步实现，是通过一种扩展的观察者模式来实现的。

- RxJava 的基本实现主要有三点：
1) 创建 Observer 观察者(或subscriber)，它决定事件触发的时候将有怎样的行为。 RxJava 中的 Observer 接口的实现方式：onNext、onCompleted、onError。

2) 创建 Observable 被观察者，它决定什么时候触发事件以及触发怎样的事件。RxJava 使用 create() 、just()、from()方法来创建一个 Observable ，并为它定义事件触发规则。

3) Subscribe 订阅：创建了 Observable 和 Observer 之后，再用 subscribe() 方法将它们联结起来，整条链子就可以工作了。代码形式很简单。observable.subscribe(observer);

### Retrofit


使用过程：第一步：

- 通过Builer构造者来创建 Retrofit 对象。
- 然后通过baseUrl来拼接URL(这里的URL不是完整的URL)。
- 通过.build()来完成对象的创建。

使用过程：第二步：

- 通过 Retrofit.create()方法创建好我们需要的网络请求接口。 
- 然后用 网络请求接口 调用他的方法contributorsBySimpleGetCall 来获取 Retrofit.call方法(其实他的底层也是用okhttp封装的)。
- 最后通过call.enqueue这个异步方法进行异步网络请求操作。

(都用接口的形式，做好了的封装。)

Retrofit源码剖析-动态代理：
1首先，通过method把它转换成 ServiceMethod；
2然后，通过 ServiceMethod，args 获取到okHttpCall对象；
3最后，再把okHttpCall进一步封装并返回call对象。 	






### Okhttp 原理

使用过程：

第一步：创建 **OKHttpClient** 的实例，
OkHttpClient client = new OkHttpClient()；

第二步：创建 **Request** 对象，封装了一些请求报文的信息。
Request request = new Request.Builder()
 .url("http://www.baidu.com")
 .build();

第三步：调用OkHttpClient的newCall方法创建一个 **call** 对象，并调用它的execute()/enqueue() 同步/异步来发送请求并获取服务器返回的数据。

**同步**获取数据：
Response response = client.newcall(request).execute();

**异步**获取数据：
 client.newcall(request).enqueue(new Callback){
onFailure()//失败
onResponse()//成功
}

### Butterknife

解决令人头疼的findViewById。

其实就是一个依托 **Java 的注解机制**来实现的辅助代码生成的框架。

使用简介：

1：绑定一个view(view不能为private 或者 stateic)
@BindView(R.id.mTxt)
Textview mTxt;

2：给一个View添加点击事件
@OnClick(R.id.mTxt)
public void onClick(){
  Toast...
}

3：给多个View添加点击事件：
@OnClick( { R.id.mTxt，R.id.Imageview } )
public void onClick(){
  Toast...
}

4.给ListView setItemClickListener
@OnItemClick(R.id.mListView)
public void onItemClick(){
  Toast...
}





### Android UI适配
### xml json gson区别
### Android内存泄露及管理

内存泄露：申请使用完的内存没有释放，导致虚拟机不能再次使用该内存，此时这段内存就泄露了。

内存溢出：申请的内存超出了JVM能提供的内存大小，此时称之为溢出。


### 设计模式

### MVC MVP 架构

> https://juejin.im/entry/56ebb4ad5bbb50004c440972

**MVC简介**：全名是Model View Controller，如图，是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。

- M层处理数据，业务逻辑等；
- V层处理界面的显示结果；
- C层起到桥梁的作用，来控制V层和M层通信以此来达到分离视图显示和业务逻辑层。


**Android中的MVC**：Android中界面部分也采用了当前比较流行的MVC框架，在Android中：

- (V)一般采用XML文件进行界面的描述，这些XML可以理解为AndroidApp的View。使用的时候可以非常方便的引入。同时便于后期界面的修改。逻辑中与界面对应的id不变化则代码不用修改，大大增强了代码的可维护性。

- (C)Android的控制层的重任通常落在了众多的 Activity 的肩上。这句话也就暗含了不要在Activity中写代码，要通过 Activity 交割 Model 业务逻辑层处理，这样做的另外一个原因是 Android 中的 Actiivity 的响应时间是5s，如果耗时的操作放在这里，程序就很容易被回收掉。

- (M)我们针对业务模型，建立的数据结构和相关的类，就可以理解为AndroidApp的Model，Model是与View无关，而与业务相关的。对数据库的操作、对网络等的操作都应该在Model里面处理，当然对业务计算等操作也是必须放在的该层的。




**MVP简介**：MVP从更早的MVC框架演变过来，与MVC有一定的相似性：Controller/Presenter 负责逻辑的处理，Model 提供数据，View负责显示。

 view < - - > Presenter < - - >Model

**MVP框架由3部分组成**：View负责显示，Presenter负责逻辑处理，Model提供数据。在MVP模式里通常包含3个要素（加上View interface是4个）：

- View：负责绘制UI元素、与用户进行交互(在Android中体现为Activity)

- Model：负责存储、检索、操纵数据(有时也实现一个Model interface用来降低耦合)

- Presenter：作为View与Model交互的中间纽带，处理与用户交互的负责逻辑。

- *View interface：需要 View 实现的接口，View 通过 View interface与Presenter进行交互，降低耦合，方便进行单元测试

**MVP的优点**：

- 1、模型与视图完全分离，我们可以修改视图而不影响模型；

- 2、可以更高效地使用模型，因为所有的交互都发生在一个地方——Presenter内部；

- 3、我们可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁；

- 4、如果我们把逻辑放在Presenter中，那么我们就可以脱离用户接口来测试这些逻辑（单元测试）。

举个简单的例子，UI层通知逻辑层（Presenter）用户点击了一个Button，逻辑层（Presenter）自己决定应该用什么行为进行响应，该找哪个模型（Model）去做这件事，最后逻辑层（Presenter）将完成的结果更新到UI层。

### handler
https://blog.csdn.net/wzhworld/article/details/78337641
### AsyncTask




## 六、

### AlertDialog，popupWindow，Activity区别
### Application 和 Activity 的 Context 对象的区别
### Android属性动画特性
### 如何导入外部数据库?
### LinearLayout、RelativeLayout、FrameLayout的特性及对比，并介绍使用场景。
### 谈谈对接口与回调的理解
### 回调的原理
### 写一个回调demo
### 介绍下SurfView
### RecycleView的使用
### 序列化的作用，以及Android两种序列化的区别
### 差值器
### 估值器
### 如何理解Activity，View，Window三者之间的关系？
### 运行时权限 p245页
### Android优化
### ListView优化方法
### 如何统计ListView加载速度

## 内存泄露
https://www.jianshu.com/p/f35ca324c285

## 网络
https://www.jianshu.com/p/97f77927db0f

### Http包头
### Get和Post区别
### Cache-control字段的作用
### Http和Https的区别
### Https怎么加密的
### 10亿数据找到出现最多次数的数字
