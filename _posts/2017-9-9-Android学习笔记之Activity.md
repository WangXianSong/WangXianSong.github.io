---
layout: post
title:  "Android学习笔记之Activity"
date:  2019-08-04 10:36:21
categories: Android
tags: Android

---
* content
{:toc}


> Activity 在应用中的表现主要是显示各种UI元素，并且为这些UI元素设置时间处理函数，使得用用户可以与这些UI进行交互。

### 1、生命周期全解析

1.典型情况下的Activity生命周期：

- **① onCreate**()：Activity 正在创建，可以做些初始化工作，如setViewContent界面资源、初始化数据，还可以恢复状态。 注意：此方法的传参Bundle为该Activity上次被异常情况销毁时保存的状态信息。
- **② onStart**()：Activity 正在启动，这时 Activity 处于可见状态，但不在前台，无法和用户交互。
- **③ onResume**()：Activity 获得焦点，此时 Activity 可见且在前台并处于栈顶。
- **④ onPause**()： Activity 正在停止，可做数据存储、停止动画等轻量级操作。注意：Activity切换时，旧Activity的onPause会先执行，然后才会启动新的Activity的onCreate，所以不能在onPause执行耗时操作。
- **⑤ onStop**()：Activity 即将停止，可做稍微重量级回收工作，如取消网络连接、注销广播接收器等。注意：新Activity是透明主题时，旧Activity都不会走onStop。
- **⑥ onDestroy**()：Activity 即将销毁，可以做回收工作、资源释放。
- **⑦ onRestart**()：Activity 重新启动，Activity由后台切换到前台，由不可见到可见。

---

### 2、Activity生命周期的切换过程

- **① 启动一个Activity**：onCreate()->onStart()->onResume()
- **② 打开一个新Activity**：旧的onPause() ->新的onCreate()->onStart()->onResume()->旧的onStop()
- **③ 返回到旧Activity**：新的onPause()->旧的onRestart()->onStart()->onResume()->新的onStop()->onDestory();
- **④ Activity1上弹出对话框Activity2**：Activity1的onPause()->Activity2的onCreate()->onStart()->onResume() (1不会stop)
- **⑤ 接着关闭屏幕/按Home键**：Activity2的onPause()-->onStop()-->Activity1的onStop()
- **⑥ 点亮屏幕/回到前台**：Activity2的onRestart()->onStart()->Activity1的onRestart()->onStart()->Activity2的onResume()
- **⑦ 关闭对话框Activity2**：Activity2的onPause()->Activity1的onResume()->Activity2的onStop()->onDestroy()
- **⑧ 销毁Activity1**：onPause()->onStop()->onDestroy()

---

### 3、onStart()和onResume()、onPause()和onStop()的区别：

- onStart与onStop是从Activity是否**可见**这个角度调用的，
- onResume和onPause是从Activity是否显示在**前台**这个角度来回调的，
- 在实际使用没其他明显区别。

---

### 4、Activity A启动另一个Activity B会回调哪些方法？如果Activity B是完全透明呢？如果启动的是一个Dialog呢？

- Activity A启动另一个Activity B会回调的方法：Activity A的onPause() ->Activity B的onCreate()->onStart()->onResume()->Activity A的onStop()；
- 如果Activity B是完全透明的，则最后不会调用Activity A的onStop()；
- 如果是Dialog，同后种情况。

---

### 5、谈谈onSaveInstanceState()方法？何时会调用？/ 在Activity中如何保存/恢复状态？

- 当非人为终止Activity时，比如系统配置发生改变时导致Activity被杀死并重新创建、资源内存不足导致低优先级的Activity被杀死，会调用 onSavaInstanceState() 来保存状态。该方法调用在onStop之前，但和onPause没有时序关系。

- **onSaveInstanceState 与 onPause 的区别**：
    - 前者适用于对**临时性**状态的保存，而后者适用于对数据的**持久化**保存。
    - onPause在Activity部分不可见的时候被调用，onSaveInstanceState在需要空出内存给当前Activity的时候执行。

- **如何恢复数据？**
    - Activity被重新创建时会调用onRestoreInstanceState（该方法在onStart之后），并将onSavaInstanceState保存的Bundle对象作为参数传到`onRestoreInstanceState`与`onCreate`方法，并取出数据进行恢复。
（在onCreate取出数据时一定要先判断savedInstanceState是否为空。onRestoreInstanceState发生在 onStart 之后。）

```java
//MainActivity 中添加代码进行临时保存
protected void onSaveInstanceState(Bundle outState){
    super.onSaveInstanceState(outState);
    String tempData = "123";
    outState.putString("data_key","tempData");
}
//MainActivity的onCreate()方法中修改如下：
protected void onCreate(Bundle saveInstanceState){
    ...
    if(saveInstanceState != null){
        String tempData = savedInstanceState.getString("data_key");
    }
}
```

---

### 6、Activity异常情况下生命周期分析

- a.**如何避免配置改变时导致Activity重建？**

可在AndroidManifest.xml中对应的Activity中设置**android:configChanges = "orientation|keyboardHidden|screenSize"**。此时再次旋转屏幕时，该Activity不会被系统杀死和重建，只会调用**onConfigurationChanged**。因此，当配置程序需要响应配置改变，指定configChanges属性，重写onConfigurationChanged方法即可。

- b.**由于系统资源不足，导致优先级低的Activity被回收。**
	- ①Activity优先级排序：前台可见Activity>前台可见不可交互Activity（前台Activity弹出Dialog)>后台Activity（用户按下Home键、切换到其他应用）
	- ②当系统内存不足时，会按照Activity优先级从低到高去杀死目标Activity所在的进程。
	- ③若一个进程没有四大组件在执行，那么这个进程将很快被系统杀死。

---

### 7、Activity 启动模式LaunchMode

- **设置Activity启动模式的方法**
	- a.在AndroidManifest.xml中给对应的Activity设定属性android:launchMode="standard\|singleInstance\|singleTask\|singleTop"。
	- b.通过标记位设定，方法是intent.addFlags(Intent.xxx)。

- **standard**：标准模式，这也是系统的默认模式。每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在；

- **singleTop**：栈顶复用模式。如果已经位于栈顶，就不会**重新创建**，会回调**onNewIntent**方法；适合推送消息详情页，比如新闻推送详情Activity;

- **singleTask**：栈内复用模式。如果存在栈内，则在其上所有Activity全部**出栈**，系统也会回调其onNewIntent；常用于主页和登录页。

- **singleInstance**：单实例模式，具有此模式的Activity只能单独位于一个任务栈中，且此任务栈中只有唯一一个实例。适用新开Activity和app能独立开的，如系统闹钟，微信的视频聊天界面。

- **常用的可设定Activity启动模式的标记位**
	- ①**FLAG_ACTIVITY_NEW_TASK**：这个标记位的作用是为Activity指定”singleTask”启动模式，其效果和在XML中指定该启动模式相同；
	- ②**FLAG_ACTIVITY_SINGLE_TOP**：这个标记位的作用是为Activity指定”singleTop”启动模式，其效果和在XML中指定该启动模式相同；
	- ③**FLAG_ACTIVITY_CLEAR_TOP**：具有此标记位的Activity，当它启动时，在同一个任务栈中所有位于它上面的Activity都要出栈；
	- ④**FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS**：具有这个标记的Activity不会出现在历史Activity的列表中，当某些情况下我们不希望用户通过历史列表回到我们的Activity的时候这个标记比较有用。它等同于在XML中指定Activity的属性”android:excludeFromRecents="true"。

---

### 8、IntentFilter匹配规则
- 原则：
	- ①一个intent只有同时匹配某个Activity的intent-filter中的action、category、data才算完全匹配，才能启动该Activity。
	- ② 一个Activity可以有多个 intent-filter，一个 intent只要成功匹配任意一组 intent-filter，就可以启动该Activity。

- a. **action匹配规则**：
	- 要求intent中的action 存在且必须和intent-filter中的其中一个 action相同。
	- 区分大小写。
	
- b. **category匹配规则**：
	- intent中的category可以不存在，这是因为此时系统给该Activity 默认加上了< category android:name="android.intent.category.DEAFAULT" />属性值。
	- 除上述情况外，有其他category，则要求intent中的category和intent-filter中的所有category 相同。
	
- c. **data匹配规则**：
	- 如果intent-filter中有定义data，那么Intent中也必须也要定义date。
	- data主要由mimeType(媒体类型)和URI组成。在匹配时通过intent.setDataAndType(Uri data, String type)方法对date进行设置。
	- 要求和action相似：如果没有指定URI，默认值为content和file; 有多组data规则时，匹配其中一组即可。

- 采用隐式方式启动Activity时，可以用PackageManager的resolveActivity方法或者Intent的resolveActivity方法判断是否有Activity匹配该隐式Intent。

---

### 9、Activity的四种状态：

- **running**：处于活动状态，用户可以点击屏幕，屏幕会做出响应，处于Activity 的栈顶。
- **paused**：处于失去焦点状态或者被一个非全屏的Activity占据，又或者被一个透明的Activity放置栈顶。Activity只是失去和用户的交互能力，处于内存紧张状态。
- **stopped**：被另外的 Activity 全屏覆盖，不再是可见的，
- **killed**：Activity 已经被系统回收掉了。
---

### 10、Activity之间的通信方式

- 1)在Intent跳转时携带数据 
- 2)借助类的静态变量
- 3)借助全局变量 Application
- 4)借助外部存储来实现通讯
  - 借助 SharedPreference 
  - 使用Android数据库 SQLite 
  - 赤裸裸的使用 File 
- 5)借助Service

---

### 11、Activity上有 Dialog 时按Home键的生命周期

- **生命周期是**：onCreate() -> onStart() -> onResume -> 启动Dialog-> home键 -> onPause() -> onStop() 

 - 其实就是一个很正常的 Activity 生命周期，并没有什么特别的地方，但是注意 onPause 方法和 onStop 方法是在我点击 Home 键之后才有的，这就说明对话框的出现并没有使 Activity 进入后台。而是点击Home键才使Activity进入后台工作。AlertDialog对话框实际上是Activity的一个组件。

---

### 12、Activity与Fragment之间生命周期比较

- **Activity**——onCreate->onStart->onResume->onPause->onStop->onDestroy
- **Fragment**——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume ->onPause->onStop->onDestroyView->  onDestroy->onDetach

---


### 13、Activity 进程优先级

- **前台进程**：处于与用户交互的 Activity，或者在前台绑定的 Service。
- **可见进程**：处于前台，但用户又不能点击的情况下。
- **服务进程**：在后台开启 Service服务。
- **后台进程**：用户按了Home键回到了桌面，根据内存情况作出相应的回收。
- **空进程**：优先级最低，处于缓存的目的而保留，系统随时杀掉。

---

### 14、scheme 跳转协议
**scheme** 是一种页面跳转协议，是一种非常好的实现机制，通过定义自己的scheme协议，可以非常方便跳转app的各个页面；通过scheme 协议，服务器可以定制化告诉App跳转哪个页面，可以通过通知栏消息定制化跳转页面，可以通过H5页面跳转页面。

- 1.服务端下发url，客户端根据url跳转到相应的页面。
- 2.H5跳转到App相应的Activity。
- 3.App根据url跳转到另一个App指定页面。

---

### 15、快速退出所有Activity(关闭多个Activity)？

1、**记录打开的Acitivity**，只需要用一个专门的收集类对所有的活动进行管理就可以了，新建 ActivityCollector 类作为活动管理器。在BaseActivity中的onCreate方法中调用 ActivityCollector.addActivity(this);即可
```java
public class ActivityCollector{
	public static List<Activity>activities = new ArrayList<>();
	public static void addActivity(Activity activity){
	activities.add(activity);
	}
	public static void remoeActivity(Activity activity){
	activities.remove(activity);
	}
	public static void finishAll(){
		for(Activity activity : activities){
			if(!activity.isFinishing()){
				activity.finish();
			}
		}
		activities.clear();
	}
}
```
2、**发送特定广播**，需要结束应用时，给每个Activity收到广播后，关闭即可。

3、**递归退出**，在打开新的 Activity 时使用 startActivityForResult，然后自己加标志，在 onActivityResult 中处理，递归关闭。

4、通过 intent 的 flag 来实现 intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)激活一个新的 activity。此时如果该任务栈中已经有该 Activity，那么系统会把这个 Activity 上面的所有 Activity 干掉。其实相当于给 Activity 配置的启动模式为 SingleTop。

---

### 16、如何将一个Activity设置成窗口样式

只需要给我们的 Activity 配置如下属性即可。
android:theme="@android:style/Theme.Dialog"

---

### 17、gravity 和 layout_gracity 的区别

- android:gravity：用于指定文字在控件中的对齐方式；
- android:layout_gracity：用于指定控件在布局中的对齐方式。
- android:layout_weight：使用比例方式来指定控件的大小。

---

### 18、Android 中的 Context, Activity，Appliction 有什么区别？

**相同**：Activity 和 Application 都是 Context 的子类。

Context 从字面上理解就是上下文的意思，在实际应用中它也确实是起到了管理上下文环境中各个参
数和变量的作用，方便我们可以简单的访问到各种资源。

**不同**：维护的生命周期不同。 Context 维护的是当前的 Activity 的生命周期，Application 维护
的是整个项目的生命周期。

使用 context 的时候，小心内存泄露，防止内存泄露，**注意一下几个方面**：

1. 不要让生命周期长的对象引用 activity context，即保证引用 activity 的对象要与 activity 本身
生命周期是一样的。
2. 对于生命周期长的对象，可以使用 application，context。
3. 避免非静态的内部类，尽量使用静态类，避免生命周期问题，注意内部类对外部对象引用导致
的生命周期变化。

---

### 19、Context 是什么？

- 1、它描述的是一个应用程序环境的信息，即上下文。
- 2、该类是一个抽象(abstract class)类，Android 提供了该抽象类的具体实现类（ContextIml）。
- 3、通过它我们可以获取应用程序的资源和类，也包括一些应用级别操作，例如：启动一个 Activity，发送广播，接受 Intent，信息等。

---

### 20、launchMode之singleTask与taskAffinity

taskAffinity是用来指示Activity属于哪一个Task的。taskAffinity能够决定以下两件事情（前提是Activity的launchMode为singleTask或者设置了FLAG_ACTIVITY_NEW_TASK），默认情况下，在一个app中的所有Activity都有一样的taskAffinity，但是我们可以设置不同的taskAffinity，为这些Activity分Task。甚至可以在不同的app之中，设置相同的taskAffinity，以达到不同app的activity公用同一个Task的目的。

---

### 21、Activity的布局文件是如何被加载的呢？
- Activity是使用setContentView来设置一个布局的。
- Activity通常包含Window对象，由PhoneWindow来实现Window对象。
- PhoneWindow将一个DecorView设置为整个应用窗口都根View。
- DecorView作为窗口界面都顶层视图，封装了一些窗口操作都通用方法。

---

### 22、如何启动其他应用的Activity？

思路：可从隐式Intent角度出发

 在保证有权限访问的情况下，通过隐式Intent进行目标Activity的IntentFilter匹配，原则是：
- 一个intent只有同时匹配某个Activity的intent-filter中的action、category、data才算完全匹配，才能启动该Activity。
- 一个Activity可以有多个 intent-filter，一个 intent只要成功匹配任意一组 intent-filter，就可以启动该Activity。

---

### 23、Activity的启动过程？

调用startActivity()后经过重重方法会转移到ActivityManagerService的startActivity()，并通过一个IPC回到ActivityThread的内部类ApplicationThread中，并调用其scheduleLaunchActivity()将启动Activity的消息发送并交由Handler H处理。Handler H对消息的处理会调用handleLaunchActivity()->performLaunchActivity()得以完成Activity对象的创建和启动。

---


### 本文参考资料：
- 《Android开发艺术探索》
- [https://www.jianshu.com/p/718aa3c1a70b](https://www.jianshu.com/p/718aa3c1a70b)