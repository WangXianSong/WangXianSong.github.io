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

### 面试建议：

- 1.选一个自己相对比较擅长的领域。（源码深入）
- 2.基础要背（灵活背）
- 3.试着去了解这个领域市面上的技术。
- 4.如果有时间 研究其中一个众所周知的库的源码。




## 一、基本概念

### Android 四大组件是什么

- **Activity 活动**：Activity用于显示界面，它上面可以显示控件、监听控件并处理用户的事件做出响应。

- **Service 服务**：是Android 中实现程序后台运行的解决方案，适合不需要和用户交互而且还要求长期运行的任务。依赖于创建服务的程序，程序被杀掉，服务也停止运行。服务不会自动开启线程，需要服务内部创建子线程。

- **Broadcast Receiver 广播接收器**：主要用于接收系统或者 app 发送的广播事件，应用程序可以使用它对感兴趣的外部事件(如当电话呼入时，或者更改网络4Gwifi)进行接收并做出响应。广播接收器没有用户界面。然而，它们可以启动一个 Activity 或 Serice 来响应它们收到的信息，或者用 NotificationManager 来通知用户。通知可以用很多种方式来吸引用户的注意力──闪动背灯、震动、播放声音等。

- **Content Provider 内容提供者**：主要用于在不同的应用程序之间实现数据共享的功能，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。与其他存储方式不同的是：可以选择哪部分数据分享，从而保证隐私数据的安全性。

### Android平台的framework的层次结构

- Linux Kernel(**Linux内核**)
- Hardware Abstraction Layer(**HAL硬件抽象层**)
- Native C/C++Libraies(**原生C/C++库**) Android Runtime(**安卓运行时**)
- Java API Framework(**Java API 框架** )
- Applications(**系统应用**)

### Android中常用的布局。

答：常用四种布局方式，分别是：FrameLayout（帧布局），LinearLayout （线性布局），AbsoluteLayout（绝对布局），RelativeLayout（相对布局）。

（1）**FrameLayout**：所有东西依次都放在左上角，会重叠，这个布局比较简单，也只能放一点比较简单的东西。

（2）**LinearLayout**：线性布局，每一个LinearLayout里面又可分为垂直布局 vertical 和水平布局horizontal。

（3）**AbsoluteLayout**：绝对布局用 XY 坐标来指定元素的位置，这种布局方式也比较简单，但是在屏幕旋转时，往往会出问题，而且多个元素的时候，计算比较麻烦。

（4）**RelativeLayout**：相对布局可以理解为某一个元素为参照物，来定位的布局方式。主要属性有：相对于某一个元素android:layout_below、 android:layout_toLeftOf相对于父元素的地方android:layout_alignParentLeft、android:layout_alignParentRigh；

### 动画有哪几类，它们的特点和区别是什么

**(1) 视图动画**：定义了透明度、平移、缩放、旋转动画。实现原理：是每次绘画视图时，View 所在的 ViewGroup 中的 drawChild 函数获取该 View 的 Animation 的 Transformation 值，然后调用 canvas.concat(transformToApply.getMatrix())，通过矩阵运算完成动画帧。缺点：是不具备交互性，当某个元素发生 View 动画后，其响应事件的位置仍然在动画前的地方。


**(2) 属性动画**：在一定时间间隔内，通过不断对值进行改变，并不断将该值赋给对象的属性，从而实现该对象在该属性上的动画效果，响应点击事件的有效区域也会发生改变。比较常用的几个动画类是：**ValueAnimator**(注重过程)、**ObjectAnimator**(具体操作) 和 **AnimatorSet**(组合)，其中 ObjectAnimator 继承自 ValueAnimator，AnimatorSet 是动画集合。

### Drawable

### 1.res/raw和asserts的区别
这两个目录下的文件都会被打包进APK，并且不经过任何的压缩处理。
assets与res/raw不同点在于，assets支持任意深度的子目录，这些文件不会生成任何资源ID，只能使用AssetManager按相对的路径读取文件。如需访问原始文件名和文件层次结构，则可以考虑将某些资源保存在assets目录下。

### 2.图片放错目录会产生的问题吗？
高密度（density）的系统去使用低密度目录下的图片资源时，会将图片长宽自动放大以去适应高密度的精度，当然图片占用的内存会更大。所以如果能提各种dpi的对应资源那是最好，可以达到较好内存使用效果。如果提供的图片资源有限，那么图片资源应该尽量放在高密度文件夹下，这样可以节省图片的内存开支。

### 3.drawable-nodpi文件夹
这个文件夹是一个密度无关的文件夹，放在这里的图片系统就不会对它进行自动缩放，原图片是多大就会实际展示多大。但是要注意一个加载的顺序，drawable-nodpi文件夹是在匹配密度文件夹和更高密度文件夹都找不到的情况下才会去这里查找图片的，因此放在drawable-nodpi文件夹里的图片通常情况下不建议再放到别的文件夹里面。

### 4.Bitmap和Drawable
Bitmap是Android系统中的图像处理的最重要类。可以简单地说，Bitmap代表的是图片资源在内存中的数据结构，如它的像素数据，长宽等属性都存放在Bitmap对象中。Bitmap类的构造函数是私有的，只能是通过JNI实例化，系统提供BitmapFactory工厂类给我们从从File、Stream和byte[]创建Bitmap的方式。
Drawable官文文档说明为可绘制物件的一般抽象。View也是可以绘制的，但Drawable与View不同，Drawable不接受事件，无法与用户进行交互。我们知道很多UI控件都提供设置Drawable的接口，如ImageView可以通过setImageDrawable(Drawable drawable)设置它的显示，这个drawable可以是来自Bitmap的BitmapDrawable，也可以是其他的如ShapeDrawable。
也就是Drawable是一种抽像，最终实现的方式可以是绘制Bitmap的数据或者图形、Color数据等。理解了这些，你很容易明白为什么我们有时候需要进行两者之间的转换。

### 5.要加载很大的图片怎么办？
如果图片很大，比如他们的占用内存算下来就直接OOM了，那么我们肯定不能直接加载它。解决主法还是有很多的，系统也给我们提供了一个类BitmapRegionDecoder，可以用来分块加载图片。

### 6.图片圆角（或称矩形圆角）或者圆形头像的实现方式
除了把原图直接做成圆角外，常见有三种方式实现：
- 使用Xfermode混合图层；
- 使用BitmapShader；
- 通过裁剪画布区域实现指定形状的图形（ClipPath）

## Android 中有哪几种解析xml的类

官方推荐哪种？以及它们的原理和区别。

Android 提供了三种解析XML的方式：**SAX(Simple API XML)** ，**DOM(Document Object Model)**， **Pull解析** 

- **Dom解析**：将XML文件的所有内容读取到内存中（内存的消耗比较大），然后允许您使用DOM API遍历XML树、检索所需的数据。
- **Sax解析**：Sax是一个解析速度快并且占用内存少的xml解析器，Sax解析XML文件采用的是事件驱动，它并不需要解析完整个文档，而是按内容顺序解析文档的过程。
- **Pull解析**：Pull解析器的运行方式与 Sax 解析器相似。它提供了类似的事件，可以使用一个switch对感兴趣的事件进行处理。












## 二、Activity 面试详解

### 1、生命周期全解析

1.典型情况下的Activity生命周期：

- **onCreate**():
	- 状态：Activity 正在创建
	- 任务：做初始化工作，如setViewContent界面资源、初始化数据，还可以恢复状态。
	- 注意：此方法的传参Bundle为该Activity上次被异常情况销毁时保存的状态信息。
- **onStart**()：
	- 状态：Activity 正在启动，这时Activity 可见但不在前台，无法和用户交互。
- **onResume**()：
	- 状态：Activity 获得焦点，此时Activity 可见且在前台并开始活动。
- **onPause**()：
	- 状态： Activity 正在停止
	- 任务：可做 数据存储、停止动画等操作。
	- 注意：Activity切换时，旧Activity的onPause会先执行，然后才会启动新的Activity的onCreate，所以不能在onPause执行耗时操作。
- **onStop**():
	- 状态：Activity 即将停止
	- 任务：可做稍微重量级回收工作，如取消网络连接、注销广播接收器等。
	- 注意：新Activity是透明主题时，旧Activity都不会走onStop。
- **onDestroy**():
	- 状态：Activity 即将销毁
	- 任务：做回收工作、资源释放。
- **onRestart**()：
	- 状态：Activity 重新启动，Activity由后台切换到前台，由不可见到可见。

### 2、Activity生命周期的切换过程

- ①**启动一个Activity**：onCreate()-->onStart()-->onResume()

- ②**打开一个新Activity**：旧Activity的onPause() -->新Activity的onCreate()-->onStart()-->onResume()-->旧Activity的onStop()

- ③**返回到旧Activity**：新Activity的onPause（）-->旧Activity的onRestart()-->onStart()-->onResume()-->新Activity的onStop()-->onDestory();

- ④**Activity1上弹出对话框Activity2**：Activity1的onPause()-->Activity2的onCreate()-->onStart()-->onResume() (1不会stop)

- ⑤**关闭屏幕/按Home键**：Activity2的onPause()-->onStop()-->Activity1的onStop()

- ⑥**点亮屏幕/回到前台**：Activity2的onRestart()-->onStart()-->Activity1的onRestart()-->onStart()-->Activity2的onResume()

- ⑦**关闭对话框Activity2**：Activity2的onPause()-->Activity1的onResume()-->Activity2的onStop()-->onDestroy()

- ⑧**销毁Activity1**：onPause()-->onStop()-->onDestroy()

### 3、onStart()和onResume()、onPause()和onStop()的区别：

- onStart与onStop是从Activity是否**可见**这个角度调用的，
- onResume和onPause是从Activity是否显示在**前台**这个角度来回调的，
- 在实际使用没其他明显区别。

### 4、生命周期的各阶段

- **完整生命周期**：
Activity在onCreate()和onDestroy()之间所经历的。
在onCreate()中完成各初始化操作，在onDestroy()中释放资源。

- **可见生命周期**：
Activity在onStart()和onStop()之间所经历的。
活动对于用户是可见的，但仍无法与用户进行交互。

- **前台生命周期**：
Activity在onResume()和onPause()之间所经历的。
活动可见，且可交互。

### 5、onSaveInstanceState和onRestoreInstanceState的区别/在Activity中如何保存/恢复状态？

- a.**出现时机**：异常情况下Activity 重建，非用户主动去销毁。

- b.当系统异常终止时，调用onSavaInstanceState来保存状态。该方法调用在onStop之前，但和onPause没有时序关系。

- **onSaveInstanceState与onPause的区别**：前者适用于对**临时性**状态的保存，而后者适用于对数据的**持久化**保存。

- c.Activity被重新创建时，调用onRestoreInstanceState（该方法在onStart之后），并将onSavaInstanceState保存的Bundle对象作为参数传到onRestoreInstanceState与onCreate方法。

- 可通过onRestoreInstanceState(Bundle savedInstanceState)和onCreate((Bundle savedInstanceState)来判断Activity是否被重建，并取出数据进行恢复。但需要注意的是，在onCreate取出数据时一定要先判断savedInstanceState是否为空。另外，谷歌更推荐使用onRestoreInstanceState进行数据恢复。

- 需要注意的是，onSaveInstanceState()方法并不是一定会被调用的, 因为有些场景是不需要保存
状态数据的。比如用户按下 BACK 键退出 activity 时，用户显然想要关闭这个 activity，此时是没有必
要 保 存 数 据 以 供 下 次 恢 复  , 也 就 是 onSaveInstanceState() 方 法 不 会 被 调 用。如 果 调 用
onSaveInstanceState()方法，调用将发生在 onPause()或 onStop()方法之前。
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
### 6、Activity异常情况下生命周期分析

- a.**由于资源相关配置发生改变，导致Activity被杀死和重新创建。**
	- 例如屏幕发生旋转：当竖屏切换到横屏时，会先调用onSaveInstanceState来保存切换时的数据，接着销毁当前的Activity，然后重新创建一个Activity，再调用onRestoreInstanceState恢复数据。
	
	- 竖屏切换横屏执行一遍：onCreate–>onStart–>onResume–>onSaveInstanceState-->onPause（不定）-->onStop-->onDestroy-->onCreate-->onStart-->onRestoreInstanceState-->onResume
	
	- 横屏切换竖屏执行两遍：onSaveInstanceState–>onPause–>onStop–>onDestroy–> onCreate–>onStart–>onRestoreInstanceState–>onResume–> onSaveInstanceState–>onPause–>onStop–>onDestroy–> onCreate–>onStart–>onRestoreInstanceState–>onResume–>
	
	- 设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次。
	
	- **设置Activity的android:configChanges="orientation\|keyboardHidden\|screenSize"。此时再次旋转屏幕时，该Activity不会被系统杀死和重建，只会调用onConfigurationChanged。因此，当配置程序需要响应配置改变，指定configChanges属性，重写onConfigurationChanged方法即可。**
	-  .

- b.**由于系统资源不足，导致优先级低的Activity被回收。**
	- ①Activity优先级排序：前台可见Activity>前台可见不可交互Activity（前台Activity弹出Dialog)>后台Activity（用户按下Home键、切换到其他应用）
	- ②当系统内存不足时，会按照Activity优先级从低到高去杀死目标Activity所在的进程。
	- ③若一个进程没有四大组件在执行，那么这个进程将很快被系统杀死。


### 7、Activity 启动模式LaunchMode

- **设置Activity启动模式的方法**
	- a.在AndroidManifest.xml中给对应的Activity设定属性android:launchMode="standard\|singleInstance\|singleTask\|singleTop"。
	- b.通过标记位设定，方法是intent.addFlags(Intent.xxx)。

- **standard**：标准模式，这也是系统的默认模式。每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在；
	- (1) 注意：使用ApplicationContext去启动standard模式Activity就会报错。因为standard模式的Activity会默认进入启动它所属的任务栈，但是由于非Activity的Context没有所谓的任务栈。

- **singleTop**：栈顶复用模式。
	- (1) 如果新Activity已经位于任务栈的栈顶，那么此Activity不会被重新创建，同时它的**onNewIntent**方法会被回调；
	- (2) 需要注意的是，这个Activity的onCreate、onStart不会被系统调用，因为它并没有发生改变；
	- (3) 如果新的Activity的实例已经存在但不是位于栈顶，那么新的Activity仍然会新创建一个；

- **singleTask**：栈内复用模式。如果要启动的在栈内存在了，但不在栈顶，它会把上面的全部出栈。系统也会回调其onNewIntent；

- **singleInstance**：单实例模式，具有此模式的Activity只能单独位于一个任务栈中，且此任务栈中只有唯一一个实例。

- **常用的可设定Activity启动模式的标记位**
	- ①**FLAG_ACTIVITY_NEW_TASK**：这个标记位的作用是为Activity指定”singleTask”启动模式，其效果和在XML中指定该启动模式相同；
	- ②**FLAG_ACTIVITY_SINGLE_TOP**：这个标记位的作用是为Activity指定”singleTop”启动模式，其效果和在XML中指定该启动模式相同；
	- ③**FLAG_ACTIVITY_CLEAR_TOP**：具有此标记位的Activity，当它启动时，在同一个任务栈中所有位于它上面的Activity都要出栈；
	- ④**FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS**：具有这个标记的Activity不会出现在历史Activity的列表中，当某些情况下我们不希望用户通过历史列表回到我们的Activity的时候这个标记比较有用。它等同于在XML中指定Activity的属性”android:excludeFromRecents="true"。

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

### 9、Activity的四种状态：

- **running**：处于活动状态，用户可以点击屏幕，屏幕会做出响应，处于Activity 的栈顶。
- **paused**：处于失去焦点状态或者被一个非全屏的Activity占据，又或者被一个透明的Activity放置栈顶。Activity只是失去和用户的交互能力，处于内存紧张状态。
- **stopped**：被另外的 Activity 全屏覆盖，不再是可见的，
- **killed**：Activity 已经被系统回收掉了。

### 10、Activity之间的通信方式

- 1)在Intent跳转时携带数据 
- 2)借助类的静态变量
- 3)借助全局变量 Application
- 4)借助外部存储来实现通讯
  - 借助 SharedPreference 
  - 使用Android数据库 SQLite 
  - 赤裸裸的使用 File 
- 5)借助Service

### 11、Activity上有 Dialog 时按Home键的生命周期

- **生命周期是**：onCreate() -> onStart() -> onResume -> 启动Dialog-> home键 -> onPause() -> onStop() 

 - 其实就是一个很正常的 Activity 生命周期，并没有什么特别的地方，但是注意 onPause 方法和 onStop 方法是在我点击 Home 键之后才有的，这就说明对话框的出现并没有使 Activity 进入后台。而是点击Home键才使Activity进入后台工作。AlertDialog对话框实际上是Activity的一个组件。

### 12、Activity与Fragment之间生命周期比较

- **Activity**——onCreate->onStart->onResume->onPause->onStop->onDestroy
- **Fragment**——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume ->onPause->onStop->onDestroyView->  onDestroy->onDetach

### 13、Activity 进程优先级

- **前台进程**：处于与用户交互的 Activity，或者在前台绑定的 Service。
- **可见进程**：处于前台，但用户又不能点击的情况下。
- **服务进程**：在后台开启 Service服务。
- **后台进程**：用户按了Home键回到了桌面，根据内存情况作出相应的回收。
- **空进程**：优先级最低，处于缓存的目的而保留，系统随时杀掉。

### 14、scheme 跳转协议
**scheme** 是一种页面跳转协议，是一种非常好的实现机制，通过定义自己的scheme协议，可以非常方便跳转app的各个页面；通过scheme 协议，服务器可以定制化告诉App跳转哪个页面，可以通过通知栏消息定制化跳转页面，可以通过H5页面跳转页面。

- 1.服务端下发url，客户端根据url跳转到相应的页面。
- 2.H5跳转到App相应的Activity。
- 3.App根据url跳转到另一个App指定页面。


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

### 16、如何将一个Activity设置成窗口样式

只需要给我们的 Activity 配置如下属性即可。
android:theme="@android:style/Theme.Dialog"

### 17、gravity 和 layout_gracity 的区别

- android:gravity：用于指定文字在控件中的对齐方式；
- android:layout_gracity：用于指定控件在布局中的对齐方式。
- android:layout_weight：使用比例方式来指定控件的大小。


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

### 19、Context 是什么？

- 1、它描述的是一个应用程序环境的信息，即上下文。
- 2、该类是一个抽象(abstract class)类，Android 提供了该抽象类的具体实现类（ContextIml）。
- 3、通过它我们可以获取应用程序的资源和类，也包括一些应用级别操作，例如：启动一个 Activity，发送广播，接受 Intent，信息等。

### 20、launchMode之singleTask与taskAffinity

taskAffinity是用来指示Activity属于哪一个Task的。taskAffinity能够决定以下两件事情（前提是Activity的launchMode为singleTask或者设置了FLAG_ACTIVITY_NEW_TASK）,默认情况下，在一个app中的所有Activity都有一样的taskAffinity，但是我们可以设置不同的taskAffinity，为这些Activity分Task。甚至可以在不同的app之中，设置相同的taskAffinity，以达到不同app的activity公用同一个Task的目的。

















## 三、Fragment面试详解

### 1.Fragment  是什么？
Fragment 是 Android 自 3.0 版本开始引入的一种可以嵌入在 Activity 当中的UI片段，主要是为了能在不同分辩率屏幕上进行更为动态和灵活的UI设计，让程序更加合理和充分利用大屏幕空间。

### 2.Fragment 为什么被称为 第五大组件

1. 使用频率高；
1. 有自己的生命周期；
1. 可以灵活加载到Activity中；
1. 虽然 Fragment 有自己的生命周期，并不像 Activity 那样独立的，需要依附 Activity。

### 3.Fragment 加载到 Activity 的两种方式

- **静态加载**：作为XML标签添加到 Activity 的布局文件当中。

```java
//1.新建fragment.xml文件
//2.在Fragment中onCreateView中加载fragment的方法：
View view = inflater.inflate(R.layout.fragment,container,false);
return view;
//3.在主页面中添加fragment的包名
```

```xml
<fragment
	android:name="com.songsong.FragmentTest.fragment"
	...
/>
```

- **动态加载**：动态在 Activity 中添加 Fragment 。 (常用)

```java
//前提1：先创建 Fragment1.java，并继承 Fragment，在onCreatview中初始化fragment的XML布局文件
//前提2：在Activity的XML布局文件中准备好容器R.id.lin_id

//步骤1：添加FragmentTransaction的实例
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction transaction = fragmentManager.beginTransaction();
//步骤2：用add()加Fragment对象fragment1，并绑定到其XML页面里的容器内
Fragment1 fragment1 = new Fragment1();
//replace、add都可以
transaction.add(R.id.lin_id, fragment1, "fragment1");
//模拟返回栈
transaction.addToBackStack(null);
//步骤3：调用commit方法使得FragmentTransaction实例的改变生效
transaction.commit();
```
### 4.Fragment 和 Activity 之间的通信 
- 在 Activity 中调用 Fragment 中的 **findFragmentById** 方法；
```java
//通过FragmentManage的findFragmentById得到实例，然后就可以调用fragment里的方法
RightFragment rf = getFragmentManager().findFragmentById(R.id.right_fragment);
```
- 在 Fragment 中调用 Activity 中的 **getActivity** 方法；
```java
//fragment中调用Activity的方法，getActivity本身就是一个Context对象。
MainActivity act = getActivity();
```
- Fragment 与 Fragment 之间的通信：**
	- 先在一个碎片中可以得到与它相关联的活动，然后再通过这个活动去获取另外一个碎片的实例，这样实现了不同碎片之间的通信了 。

### 5.Fragment 的生命周期

![](https://i.imgur.com/EVTe4cq.png)

- **onAttach**:表示在 Fragment 与 Activity 关联之后所回调的。
- **onCreate**:表示初次创建 Fragment 的时候调用。(仅用来创建，但为完成)
- **onCreateView**:系统在 Fragment 的首次**绘制**用户界面的时候调用。
- **onViewCreated**:表示 Fragment 的UI界面已经绘制好了，可以初始化里面的控件资源。
- *Activity - onCreate*:初始化Activity 的布局和数据之类。
- **onActivityCreated**:在 Activity - onCreate()调用完成后才可以被调用，表示Activity被渲染绘制成功以后的调用方法。
- *Activity - onStart*:表明 Activity 可见了。
- **onStart**:表明 Fragment 也可见了。
- *Activity - onResume*:表示 Activity 可以和用户交互了。
- **onResume**:表示 Fragment 也可以和用户交互了。
- ↑  (到此为止已经完成了 Fragment 从启动到展现的操作。)
- ↓ (回退Fragment)
- **onPause**: Fragment 无法和用户交互。
- *Activity - onPause*:整个Activity 也无法和用户交互。
- **onStop**:做一些相应的保存与释放。
- *Activity - onStop*:做一些相应的保存与释放。
- **onDestroyView**:对应创建的onCreateView，表示 Fragment 即将结束，然后会被保存。
- **onDestroy**:表示 Fragment 不再被使用。
- **onDetach**: Fragment 的最后一个方法，Fragment不再被使用。
- *Activity - onDetach*:整个Activity被回收了。

简单的Fragment流程图—— onAttach->onCreate -> onCreateView -> onActivityCreated -> onStart -> onResume  ->创建成功-> onPause->onStop->onDestroyView -> onDestroy->onDetach

- **打开 Fragment**：onCreate->onCreateView->onActivityCreated->onStart->onResume
- **替换 Fragment**：onPause->onStop->onDestroyView
- **返回旧 Fragment**：onCreateView->onActivityCreated->onStart->onResume
- **退出 Fragment**：onPause->onStop->onDestroyView->onDestroy->onDetach



### 6.FragmentPagerAdapter 与 FragmentStatePagerAdaper 区别：

- **FragmentPagerAdapter** ：FragmentPagerAdapter 在切换 ViewPager 的时候，只是把 Fragment的UI 与 Activity的UI 脱离开来，并不回收内存。所以它适用页面**较少**的情况。(在其源码 DestroyItem 方法中的最后一行，mCurTransaction.**detach** 可知)

- **FragmentStatePagerAdaper** ：由于 FragmentStatePagerAdaper 在每次切换 ViewPager 的时候，它是回收内存的。又因为在页面较多的情况下会更耗内存，所以它适合页面**较多**的情况。(在源码 DestroyItem 方法中的最后一行，mCurTransaction.**remove** 可知 )


### 7.Fragment 的 replace、add、remove 方法

- **replace** 是 FragmentManager 的方法，是将 Activity 最上层的 Fragment **替换**掉。
- **add** 将 Fragment 的实例添加到 Activity 的**最上层**；
- **remove** 将 Fragment 的实例从 Activity 的队列中**删除**；

### 8.Fragment 特点

- Fragment 可以作为 Activity 界面的一部分组成出现；
- 可以在一个 Activity 中同时出现多个 Fragment，并且一个 Fragment 也可以在多个 Activity 中使用；
- 在 Activity 运行过程中，可以添加、移除或者替换 Fragment；
- Fragment 可以响应自己的输入事件，并且有自己的生命周期，它们的生命周期会受宿主 Activity 的生命周期影响。
- Fragment 可以轻松得创建动态灵活的 UI 设计，可以适应于不同的屏幕尺寸。从手机到平板电脑。
- Fragment 解决 Activity 间的切换不流畅，轻量切换。
- Fragment 替代 TabActivity 做导航，性能更好。
- Fragment 做局部内容更新更方便，原来为了达到这一点要把多个布局放到一个 activity 里面，现在可以用多 Fragment 来代替，只有在需要的时候才加载 Fragment，提高性能。

### 9.Fragment嵌套多个Fragment会出现bug吗？

参考：http://blog.csdn.net/megatronkings/article/details/51417510

### 10.怎么理解Activity和Fragment的关系？

- Fragment 拥有和 Activity 一致的生命周期，它和 Activity 一样被定义为 Controller 层的类。有过中大型项目开发经验的开发者，应该都会遇到过 Activity 过于臃肿的情况，而 Fragment 的出现就是为了缓解这一状况，可以说 它将屏幕分解为多个「Fragment（碎片）」（这句话很重要），但它又不同于 View，它干的实质上就是 Activity 的事情，负责控制 View 以及它们之间的逻辑。
- 将屏幕碎片化为多个 Fragment 后，其实 Activity 只需要花精力去管理当前屏幕内应该显示哪些 Fragments，以及应该对它们进行如何布局就行了。这是一种组件化的思维，用 Fragment 去组合了一系列有关联的 UI 组件，并管理它们之间的逻辑，而 Activity 负责在不同屏幕下（例如横竖屏）布局不同的 Fragments 组合。
- 这种碎片不单单能管理可视的 Views，它也能执行不可视的 Tasks，它提供了 retainInstance 属性，能够在 Activity 因为屏幕状态发生改变（例如切换横竖屏时）而销毁重建时，依然保留实例。这示意着我们能在 RetainedFragment 里面执行一些在屏幕状态发生改变时不被中断的操作。例如使用 RetainedFragment 来缓存在线音乐文件，它在横竖屏切换时依然维持下载进度，并通过一个 DialogFragment 来展示进度。

### 11.遇到过哪些关于Fragment的问题，如何处理的？

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
### Fragment状态保存 startActivityForResult 是哪个类的方法，在什么情况下使用？
### 如何实现Fragment的滑动？
ViewPager + Fragment
### 谈一谈Fragment的生命周期？
### Activity和Fragment的异同？
### Activity和Fragment的关系？
### 何时会考虑使用Fragment？










## 三、Service面试详解

### 1.Service 是什么？

Service 是 Android 中实现程序后台运行的解决方案，非常适用于去执行那些**不需要和用户交互**而且还要求**长期运行的任务**。服务的运行不依赖于任何用户界面，即使程序被切换到(Service 不能做耗时操作)

### 2.Service 和 BroadcastReceiver 共同点

- 都是运行在主线程当中，都不能做长时间的耗时操作。

### 3.Service 和 Thread 的区别

- **定义**：
	- Thread 是程序执行的最小单元，它是分配CPU的最小单位。可以执行异步操作，是相对**独立**的。
	- Service 是 Android 的一种特殊机制，是运行在主线程当中的，是**依托于**所在的主线程。是由系统进程托管，也是一种轻量级IPC通信方式(Activity 和 Service 绑定，然后数据通信，并处于不同进程。 )

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

### 4.开启子线程的方法

- 新建类继承自Thread，然后重写父类的run方法，并在里面编写耗时逻辑。
```java
class MyThread extends Thread{
  public void run(){
    //具体逻辑
  }
}
//只需要new出MyThread的实例
//调用start方法，run方法就会在子线程中运行
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

### Service 的生命周期

![](https://i.imgur.com/1Lf6nx0.png)

- 回调方法含义：
	- **onCreate**（）：服务第一次被创建时调用
	- **onStartComand**（）：服务启动时调用
	- **onBind**（）：服务被绑定时调用
	- **onUnBind**（）：服务被解绑时调用
	- **onDestroy**（）：服务停止时调用

- 生命周期：
	- 只是用 **startService**() 启动服务：onCreate() -> onStartCommand() -> onDestory()
	- 只是用 **bindService**() 绑定服务：onCreate() -> onBind() -> onUnBind() -> onDestory()
	- **同时使用startService()启动服务与bindService()绑定服务**：onCreate() -> onStartCommnad() -> onBind() -> onUnBind() -> onDestory。

- 种类：
	- **startService**()：开启Service，调用者退出后Service仍然**存在**。
	- **bindService**()：开启Service，调用者退出后Service也随即**退出**。

- 启动方式：
	- **startService**()：通过startService()方法可以启动一个Service，并回调服务中的onStartCommand()。如果首次创建，那么回调的顺序是onCreate()->onStartCommand()。服务启动了之后会一直保持运行状态，直到stopService()或stopSelf()方法被调用，服务停止并回调onDestroy()。另外，无论调用多少次startService()方法，只需调用一次stopService()或stopSelf()方法，服务就会停止了。onCreate只在第一次创建的时候调用，onStartCommand()每次启动服务都调用。
	- **bindService**()：通过bindService()可以绑定一个Service，并回调服务中的onBind()方法。类似地，如果该服务之前还没创建，那么回调的顺序是onCreate()->onBind()。之后，调用方可以获取到onBind()方法里返回的IBinder对象的实例，从而实现和服务进行通信。只要调用方和服务之间的连接没有断开，服务就会一直保持运行状态，直到调用了unbindService()方法服务会停止，回调顺序onUnBind()->onDestroy()。
	- 注意，这两种启动方法并不冲突，当使用startService()启动Service之后，还可再使用bindService()绑定，只不过需要同时调用 stopService()和 unbindService()方法才能让服务销毁掉。


### Service的基本用法

#### a.普通Service

- 第一步：新建类并继承Service且必须重写onBind()方法，有选择的重写onCreate()、onStartCommand()及onDestroy()方法。
- 第二步：在配置文件AndroidManifest.xml中进行注册，否则不能生效。
- 第三步：在 Activity 中利用 startService(Intent) 可实现 Service 的启动。
- 第四步：在 Activity 中  stopService(Intent) 停止服务，或者在Service任意位置调用stopSelf()；

```java
//启动
Intent intent = new Intent(this, MyService.class);
 startService(intent);
//停止
Intent intent = new Intent(this, MyService.class);
stopService(intent);
```



### 开启 Service 的两种方式以及区别


- **bindService**：绑定服务提供了客户端和服务端接口，进行数据的交互(发送请求、获取结果、进程间通信)
	1. 创建BindService服务端，继承自Service，并在类中创建一个实例IBinder接口实现对象并提供公共方法给客户端调用。
	2. 从 onBind() 回调方法返回此Binder实例
	3. 在客户端中，从onServiceConnected()回调方法接收Binder，并使用提供的方法调用绑定服务。

- 如果一个 service 通过 startService 被 start 之后，多次调用 startService 的话，service 会多次调
用 onStart 方法。多次调用 stopService 的话，service 只会调用一次 onDestroyed 方法。

- 如果一个 service 通过 bindService 被 start 之后，多次调用 bindService 的话，service 只会调用
一次 onBind 方法。多次调用 unbindService 的话会抛出异常。



### Activity如何与Service通信？

在 Activity 中可以通过 startService 和 bindService 方法启动 Service。一般情况下如果想获取
Service 的服务对象那么肯定需要通过 **bindService**（）方法，比如音乐播放器，第三方支付等。如
果仅仅只是为了开启一个后台任务那么可以使用 startService（）方法。

1. 首先定义 **MyService** 来指定具体的操作。
2. 先在 Activity 里实现一个 **ServiceConnection** 匿名类 connection，重写 onServiceConnected 和 onServiceDisconnected 方法，分别表示绑定成功与断开；
2. 并将该匿名类 connection 传递给 **bindService**() 方法去启动 Service；
3. 在ServiceConnection匿名类的**onServiceConnected()**方法里启动 MyService 里的方法。
4. 最后通过 **unbindService** 来解绑服务。

### 说说 Activity、Intent、Service 是什么关系

他们都是 Android 开发中使用频率最高的类。其中 Activity 和 Service 都是 Android 四大组件
之一。他俩都是 Context 类的子类 ContextWrapper 的子类，因此他俩可以算是兄弟关系吧。不过
兄弟俩各有各自的本领，Activity 负责用户界面的显示和交互，Service 负责后台任务的处理。Activity
和 Service 之间可以通过 Intent 传递数据，因此可以把 Intent 看作是通信使者。

### Service 里面可以弹吐司么
可以的。弹吐司有个条件就是得有一个 Context 上下文，而 Service 本身就是 Context 的子类，因此在 Service 里面弹吐司是完全可以的。比如我们在 Service 中完成下载任务后可以弹一个吐司通知
用户。

### IntentService是什么？

- IntentService 是一个特殊的 Service，它继承了 Service 并且它是一个抽象类，因此必须创建它的子类才能使用 IntentService。
- 它封装了 HandlerThread 和 Handler，可用于执行后台耗时的任务，当任务全部执行完毕后自动停止。
- 它的优先级比普通线程要高，所以适合执行高优先级的后台任务。
- （HandlerThread是继承了Thread，他的作用是 不用每次创建线程，他内部有循环机制可以重复利用，不用的时候要quit或者quitSafely。）

### Service调用方法

1. 手动调用startService()启动服务，自动调用内部方法：onCreate()、onStartCommand()，如果一个Service被startService()多次启动，那么onCreate()也只会调用一次。
2. 手动调用stopService()关闭服务，自动调用内部方法：onDestory()，如果一个Service被启动且被绑定，如果在没有解绑的前提下使用stopService()关闭服务是无法停止服务的。
3. 手动调用bindService()后，自动调用内部方法：onCreate()、onBind()。
4. 手动调用unbindService()后，自动调用内部方法：onUnbind()、onDestory()。
5. startService()和stopService()只能开启和关闭Service，无法操作Service，调用者退出后Service仍然存在；bindService()和unbindService()可以操作Service，调用者退出后，Service随着调用者销毁。



### 谈一谈Service的生命周期？
### Service的两种启动方式？区别在哪？
### 一个Activty先start一个Service后，再bind时会回调什么方法？此时如何做才能回调Service的destory()方法？
### Service如何和Activity进行通信？
### 用过哪些系统Service？
### 是否能在Service进行耗时操作？如果非要可以怎么做？
### AlarmManager能实现定时的原理？
### 前台服务是什么？和普通服务的不同？如何去开启一个前台服务？
### 是否了解ActivityManagerService，谈谈它发挥什么作用？
### 如何保证Service不被杀死？







## 四、Content Provider

### 1、Content Provider是什么？

**内容提供者**：主要用于在不同的应用程序之间实现数据共享的功能，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。与其他存储方式不同的是：可以选择哪部分数据分享，从而保证隐私数据的安全性。

### 2、ContentProvider 的特点

1. 四大组件之一，需要在 Mainifest.xml 文件中进行注册。
2. 接口的统一，并不能用于存储数据，只是为数据的获取、添加、修改的操作提供统一的接口。
3. 跨进程供多个应用程序共享数据；
4. 设备存储数据：通讯录、图片；
5. 数据更新监听：UI更新；

### ContentProvider 的优缺点

1. 数据访问统一接口（存储方式都不用管）优点。 
2. 跨进程数据的访问 优点。
3. 无法单独使用，必须与其他的存储方式结合使用缺点。

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
3、ContentObserver:

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


### 说说ContentProvider、ContentResolver、ContentObserver 之间的关系

- **ContentProvider**：**内容提供者**，用于对外提供数据，提供数据的增删改查操作，数据源可以是数据库、文件、XML、网络等，ContentProvider为这些数据的访问提供了统一的接口，可以用来做进程间数据共享。
- **ContentResolver**：**内容解析者**，用于获取内容提供者提供的数据。ContentResolver可以不同URI操作不同的ContentProvider中的数据，外部进程可以通过ContentResolver与ContentProvider进行交互。
- **ContentObserver**：**内容监听器**，可以监听数据的改变状态。


### 为什么要用 ContentProvider？它和 sql 的实现上有什么差别？

- **ContentProvider** 屏蔽了数据存储的细节,内部实现对用户完全透明,用户只需要关心操作数据的
uri 就可以了，ContentProvider 可以实现不同 app 之间共享。

- **Sql** 也有增删改查的方法，但是 sql 只能查询本应用下的数据库。而 ContentProvider 还可
以去增删改查本地文件. xml 文件的读取等。

### 请介绍下 ContentProvider 是如何实现数据共享的？

- 1、在 Android 中如果想将自己应用的数据（一般多为数据库中的数据）提供给第三发应用，那么我们只能通过 ContentProvider 来实现了。

- 2、ContentProvider 是应用程序之间共享数据的接口，使用的时候首先自定义一个类继承ContentProvider，然后覆写 query、insert、update、delete 等方法。因为它是四大组件之一因此必须在 AndroidManifest 文件中进行注册。

- 3、把自己的数据通过 uri 的形式共享出去，第三方可以通过 ContentResolver 来访问该 Provider。

```java
public class PersonContentProvider extends ContentProvider{
	public boolean onCreate(){}
	query(Uri, String[], String, String[], String)
	insert(Uri, ContentValues)	
	update(Uri, ContentValues, String, String[])
	delete(Uri, String, String[])
}
```









## 五、Broadcast Receiver

### 1、广播的定义

- 在 Android 中，Broadcast 是一种在应用程序之间传输信息的机制，Android 中我们要发送的广播内容是一个 Intent，这个 Intent 中可以携带我们要传送的数据。

- 广播实现了不同程序之间的**(1)信息传输与共享**，只要和发送广播的 action 相同的接收者，都能接收到这个广播。还可以作为**(2)通知**的作用，发送消息给 service 来更新 UI。

- 类似设计模式中的“观察者模式”，当被观察者数据发生变化的时候，会去相应的通知观察者做相应的数据处理。

### 2、广播的使用场景

- 同个 App 具有多个进程的不同组件之间的消息通信。
- 不同 App 之间的组件之间消息通信。

### 3、广播的种类

- **普通广播 Normal Broadcast**：异步执行的广播，所有接收者在同一时刻收到这条广播消息。效率高，没有先后顺序，无法截断。属于全局广播。调用 **sendBroadcast**() 发送，最常用的广播。

- **有序广播 Ordered Broadcast**：同步执行的广播，发出去的广播会被广播接收者按照顺序接收，广播接收者按照Priority属性值从大-小排序，Priority属性相同者，动态注册的广播优先，广播接收者还可以选择对广播进行截断和修改。调用 **sendOrderedBroadcast**() 发送。

### 4、注册广播接收 (静态和动态)

- **(1)静态注册** : 将广播写在 AndroidMainifest.xml 文件当中，特点是：常驻系统，不受组件生命周期影响，即便应用退出，广播还是可以被接收，耗电、占内存。

```java
//首先创建 Broadcast Receiver文件，Exported 属性表示是否允许这个广播接收本程序以外的广播，Enabled 属性表示是否启用用这个广播接收器。
public class MyReceiver extends BroadcastReceiver {
        //onReceive 不能做过多的耗时操作，10秒没响应就ANR
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
       ChangeReceiver = new BroadcastReceiver();
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

```xml
    /*1. 给广播接收器设置优先级 */
    <intent-filter android:priority="100">
        <action android:name="com.example.broadcasttest.LOCAL_BROADCAST" />
    </intent-filter>
```

```java
//2.广播接收器截断：
public void onReceive(Context context, Intent intent) {
    abortBroadcast();
}
```

```java
//3.通过sendOrderedBroadcast发送传递广播
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

### Broadcast Receiver能在onReceive中执行耗时任务吗？

BroadcastReceiver 在 10 秒内没有执行完毕，Android 会认为该程序无响应，所以在 onReceive 通常是不能开启线程的，一般是通过 service 或者 IntentService 来处理。


### BroadCastReceiver 的生命周期

- a. 广播接收者的生命周期非常短暂的，在接收到广播的时候创建，onReceive()方法结束之后销
- 毁；
- b. 广播接收者中不要做一些耗时的工作，否则会弹出 Application No Response 错误对话框；
- c. 最好也不要在广播接收者中创建子线程做耗时的工作，因为广播接收者被销毁后进程就成为了
- 空进程，很容易被系统杀掉；
- d. 耗时的较长的工作最好放在服务中完成；




















## 六、webview安全漏洞

### WebView优化了解吗，如何提高WebView的加载速度？

> 为什么WebView加载会慢呢？ 这是因为在客户端中，加载H5页面之前，需要先初始化WebView，在WebView完全初始化完成之前，后续的界面加载过程都是被阻塞的。

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

### 主要涉及的角色：

- Message：消息，分为硬件产生的消息（例如：按钮、触摸）和软件产生的消息。
- MessageQueue：消息队列，主要用来向消息池添加消息和取走消息。
- Looper：消息循环器，主要用来把消息分发给相应的处理者。
- Handler：消息处理器，主要向消息队列发送各种消息以及处理各种消息。

### 整个消息的循环流程

1. Handler通过sendMessage()发送消息Message到消息队列MessageQueue。
1. Looper通过loop()不断提取触发条件的Message，并将Message交给对应的target handler来处理。
1. target handler调用自身的handleMessage()方法来处理Message。

### 在一个工作线程中创建自己的消息队例应该怎么做

Looper.prepare（在每个线程只允许执行一次）

### handler
- handler通过post（Runnable）和sendMessage（Message）传递Runnable和Message，最后调用的是sendMessage；
- MessageQueue核心方法：enqueueMessage和next(实现原理：synchronized 和 for(;;))
- Looper核心方法：Looper.prepare()和Looper.loop()，其中loop()方法里面是一个for(;;)死循环
msg.target = this

### 为什么主线程不会因为Looper.loop()里的死循环卡死？

事实上，会在进入死循环之前便创建了新binder线程，在代码ActivityThread.main()中：thread.attach(false)；便会创建一个Binder线程（具体是指ApplicationThread，Binder的服务端，用于接收系统服务AMS发送来的事件），该Binder线程通过Handler将Message发送给主线程。对于主线程，我们是绝不希望会被运行一段时间，自己就退出，那么如何保证能一直存活呢？简单做法就是可执行代码是能一直执行下去的，死循环便能保证不会被退出，例如，binder线程也是采用死循环的方法，通过循环方式不同与Binder驱动进行读写操作，当然并非简单地死循环，无消息时会休眠。

主线程的死循环一直运行是不是特别消耗CPU资源呢？ 其实不然，这里就涉及到Linux pipe/epoll机制**，简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。

## 九、AsyncTask
## 十、HandlerThread
## 十一、IntentService







## 十二、View绘制

### view树的绘制流程

- **onMeasure**：测量视图的大小，从顶层父View到子View递归调用measure()方法，measure()调用onMeasure()方法，onMeasure()方法完成测量工作。
- **onLayout**：确定视图的位置，从顶层父View到子View递归调用layout()方法，父View将上一步measure()方法得到的子View的布局大小和布局参数，将子View放在合适的位置上。
- **onDraw**：绘制最终的视图，首先ViewRoot创建一个Canvas对象，然后调用onDraw()方法进行绘制。onDraw()方法的绘制流程为：① 绘制视图背景。② 绘制画布的图层。 ③ 绘制View内容。 ④ 绘制子视图，如果有的话。⑤ 还原图层。⑥ 绘制滚动条。

### measure

![](https://i.imgur.com/C3hEYc5.png)

重要的参数：

ViewGroup.LayoutParams：用来指定视图高度宽带(具体的长宽高、和父控件一样、包含即可)
MeasureSpec：测量规格，包括两种(测量模式、..)

measure方法调用：

- measure：为每一个View的宽高赋值
- onMeasure：
- setMeasuredDimension ：完成整个测量


### layout
### draw

### 描述一下View的绘制原理？









## 十三、View事件分发

### 为什么会有事件分发机制？

答： Android 的 View 是树形结构的，View 可能会重叠在一起，当我们点击的地方有多个 View 都可以响应的时候，这个点击事件由谁来触发呢？为了解决这个问题，就有了事件分发机制。 

- **Phonewindow**：最顶层的管理容器，作为window的唯一实现类。
- **DecorView**：Phonewindow的内部类，作为传递消息的使者。

### 事件分发流程

Activity-> PhoneWindow -> DecorView -> ViewGroup -> ... -> View

当最后一个View 没有消费事件，这个事件会依次返转回到最高位的Activity，如果这样都没消费的话才抛弃。

### 三个重要的事件分发方法

- **dispatchTouchEvent**：用来进行事件的分发。
- **onInterceptTouchEvent**：用来判断是否拦截某个事件。在 dispatchTouchEvent 方法内部调用，果当前 View 拦截了某个事件，那在同一个事件序列中，此方法不会再次调用，返回结果表示是否拦截当前事件。
- **onTouchEvent**：用来处理点击事件。在 dispatchTouchEvent 方法内部调用，返回结果表示是否消耗当前事件，如果不消耗，在同一事件序列里，当前 View 无法再次接收到事件。

```java
// 父View调用 dispatchTouchEvent() 开始分发事件
public boolean dispatchTouchEvent(MotionEvent event){
    boolean consume = false;
    // 父View决定是否拦截事件
    if(onInterceptTouchEvent(event)){
        // 父View调用onTouchEvent(event)消费事件，如果该方法返回true，表示
        // 该View消费了该事件，后续该事件序列的事件（Down、Move、Up）将不会在传递
        // 该其他View。
        consume = onTouchEvent(event);
    }else{
        //否则， 调用子View的dispatchTouchEvent(event)方法继续分发事件
        consume = child.dispatchTouchEvent(event);
    }
    return consume;
}
```






## 十四、Listview缓存

### 什么是listview 

ListView 就是一个能用数据集合以滚动的方法展示到用户界面的View，用列表来展示内容。

### ListView的使用步骤

1.新建javaBean类
2.新建Item布局
3.定义适配器
4.加载适配器

### ListView 的优化





### listview适配器模式

![](https://i.imgur.com/o7M1eU6.png)

### listview的 recycleBin机制














## 十五、Android目录结构
### 四种引用的区别
1. 强引用：通常我们编写的代码都是强引用，eg ：Object obj = new Object();当内存空间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。比如你创建一个很长的数组Object[] objArr = new Object[10000];当运行至这句时，如果内存不足，JVM会抛出OOM错误也不会回收数组中的object对象。不过要注意的是，当方法运行完之后，数组和数组中的对象都已经不存在了，所以它们指向的对象都会被JVM回收。
2. 软引用：只要有足够的内存，就一直保持对象。一般可用来实现缓存，通过java.lang.ref.SoftReference类实现。内存非常紧张的时候会被回收，其他时候不会被回收，所以在使用之前需要判空，从而判断当前时候已经被回收了。
3. 弱引用：通过java.lang.ref.WeakReference或java.util.WeakHashMap类实现，eg : WeakReference p = new WeakReference(new Person(“Rain”));不管内存是否足够，系统垃圾回收时必定会回收。
4. 虚引用：不能单独使用，主要是用于追踪对象被垃圾回收的状态。通过java.lang.ref.PhantomReference类和引用队列ReferenceQueue类联合使用实现。

## 十六、Android目录构建
## 十七、git版本控制器
## 十八、gradle
## 十九、Proguard代码混淆

**Proguard 定义**：是用于压缩、优化、混淆我们的代码。主作用是可以移除代码中的无用类、字段、方法和属性，同时还可以混淆代码。

- 压缩(Shrink)：在打包的时候，通过 Proguard 来移除没有用到的类、字段、方法和属性。
- 优化(Optimize)：通过javac编译后的字节码文件，对字节码进行优化，移除无用的指令。
- 混淆(Obfuscate)：通过无意义的命名(a b c)来混淆有意义的命名，加大反编译的难度。
- 预检测(Preveirfy)：在Java平台上对处理后的代码进行预检，确保加载的class文件是可执行的。

**Proguard 工作原理**：

- EntryPoint：在压缩过程中，ProGuard 会从上述的 EntryPoint 开始递归遍历，搜索哪些类和类的成员在使用，对于没有被使用的类和类的成员，就会在压缩段丢弃，在接下来的优化过程中，那些非 EntryPoint 的类、方法都会被设置为 private、static 或 final，不使用的参数会被移除，此外，有些方法会被标记为内联的，在混淆的步骤中，ProGuard 会对非 EntryPoint 的类和方法进行重命名。

![](https://i.imgur.com/QSGaSQp.png)


## 二十、volley网络框架
## 二一、okhttp网络框架

### OKhttp 的好处

- 支持 SPDY，共享同一个 Socket 来处理同一个服务器的所有请求。
- 如果 SPDY 不可用，则通过连接池来减少请求延迟。
- 无缝的支持 GZIP 来减少数据流量。
- 缓存响应数据来减少重复的网络请求。
- 可从很多常用的连接问题中自动恢复。
- 还会处理代理服务器问题和 SSL 失败的问题。
- 使用起来非常简单。

### OKHttp 的使用

- 第一步：创建 OkHttpClient 对象，使用到了 Builder 设计模式。
- 第二步：创建 Request 对象，封装了一些请求报文的信息。
- 第三步：创建 Call 对象，调用 execute/enqueue。
- 第四步：获取返回对象 Response。

```java
OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder().url("http://www.baidu.com/").build();
Call call = client.newCall(request);

//同步 execute 获取数据
Response response = call.execute();
String responseData = response.boby().string();
//Response response = client.newCall(request).execute()//简写第三四步

//异步 enqueue 获取数据
call.enqueue(new Callback){
    public void onFailure(Call call,IOException e){
         //失败
    }
    public void onResponse(Call call,Response response)throws IOException{
	//runOnUiThread
        sysout(response.boby().string());//成功
    }
}
```
### OKhttp 设计思路

- 通过 Builder(构建者) 构建 Request。
- Diapatcher(任务调度器) 不断从 RequestQueue(请求队列) 中取出 Request(请求)。
- Diapatcher 把 Request 分发到 HttpEngine 中。
- 根据当前请求是否有 Cache(缓存) 来返回 Response。
- 没有就把请求发到 ConnectionPool 连接池中。
- 从 ConnectionPool 连接池中获取到 Connection 之后去发起真正的请求。
- 请求成功后通过 Route(路由) 和 Platform 找到合适平台。
- 通过 Server(Socket) 去获取到 data。

## 二二、Retrofit网络框架 

- 第一步：
	- 通过Builer构造者来创建 Retrofit 对象。
	- 然后通过baseUrl来拼接URL(这里的URL不是完整的URL)。
	- 通过.build()来完成对象的创建。

```java
public static final String BASE_URL = "https://api.douban.com/v2/movie/";
Retrofit retrofit = new Retrofit.Builder() 
       .baseUrl(BASE_URL) 
       .addConverterFactory(GsonConverterFactory.create())
       .build();
```

- 第二步：
	- 通过 Retrofit.create() 方法创建好我们需要的网络请求接口。
	- 然后用 网络请求接口 调用他的方法来获取 Retrofit.call方法。
	- 最后通过call.enqueue这个异步方法进行异步网络请求操作。

```java
MovieService movieService = retrofit.create(MovieService.class); 
//调用方法得到一个Call ,并传参数
Call<MovieSubject> call = movieService.getTop250(0,20);

 //进行网络请求 
call.enqueue(new Callback<MovieSubject>() {
       @Override 
       public void onResponse(Call<MovieSubject> call, Response<MovieSubject> response) { 
            mMovieAdapter.setMovies(response.body().subjects);     
            mMovieAdapter.notifyDataSetChanged(); 
       } 
      @Override 
      public void onFailure(Call<MovieSubject> call, Throwable t) { 
         t.printStackTrace(); 
      } 
});
```

- 第三步：创建接口

```java
public interface MovieService { 
 //获取豆瓣Top250 榜单 
 @GET("top250")
 Call<MovieSubject> getTop250(@Query("start") int start,@Query("count")int count);
}
```

## 二三、butterknife注解框架
## 二四、Glide图片框架
## 二五、ANR异常

 1. **什么是ANR**： 在Android中，如果应用程序有一段时间响应不够灵敏，系统会向用户显示**应用程序无响应**（ANR：Application Not Responding）对话框。用户可以选择让程序继续运行或者关闭程序。


 2. **ANR产生的原因**：
   - ANR产生的根本原因是主线程做了耗时操作：IO操作、耗时计算、。
   - 应用程序的响应性是由 Activity Manager 和 WindowManager 系统服务监视的。
   - 不同的组件发生 ANR 的时间不一样，主线程 Activity 是 5 秒，Service是 20 秒，广播是 10 秒。


 3. **怎样避免ANR**：主线程的耗时工作通过 Handler 机制转移到子线程中执行，在子线程中将结果返回给主线程进行更新UI。
   - 使用Asynctask处理耗时IO操作。
   - 使用Thread或者HandlerThread 提高优先级。
   - 使用Handler来处理工作线程的耗时任务。
   - Activity的onCreate和onResume回调中尽量避免耗时的代码。
 

 4. **Android哪些操作是在主线程中**：
    - Activity的所有生命周期回调都是执行在主线程的。
    - Service默认是执行在主线程的。
    - BroadCastReceiver的onReceive回调是执行在主线程的。
    - 没有使用子线程的Looper的Handler的handleMessage、post(Runnable)是执行在主线程的。
    - AsyncTask的回调除了 doInBackground，其他都是执行在主线程。

## 二六、OOM异常

什么是oom？
一些容易混淆的概念：内存溢出oom、内存抖动(短时间大量对象被创建，然后被释放，会严重占用内存)、内存泄露
如何解决oom：



 1. **什么是OOM**：当前占用的内存加上申请的内存资源超过了 Dalvik 虚拟机的最大内存限制就会抛出的 Out of memory 异常。

 2. **为什么会OOM**：
   - **(1)瞬时加载了一些资源**，例如，视频、图片、音频等等的内存申请大小超过了App的额定内存值，解决方案：对资源可能需要申请大内存的地方做压缩处理。
   - **(2)调用 registerRecevier() 后未调用 unregisterReceiver()**, 解决方案：在每一次动态注册的时候，记得在在适当的地方（Activity的OnDestory()）取消注册。
   - **(3)资源对象没关闭造成的内存泄漏，如查询数据库后没有关闭游标cursor**，解决方案：使用完cursor及时关闭。
   - **(4)构造Adapter没有使用缓存 contentview重用**， 解决方案：在构造Adapter的时候，使用ContentView缓存页面，节省内存。
   - **(5)未关闭 InputStream/OutputStream** , 解决方案：在使用到IO Stream 的时候，及时关闭。
   - **(6)Bitmap对象不在使用时调用recycle()释放内存**，解决方案：在Bitmap不在需要被加载到内存中的收获，做回收处理。
   - **(7)Context泄露，内部类持有外部类的引用。** 解决方案：  第一： 将线程的内部类，改为静态内部类  第二：在线程内部采用弱引用保存Context引用。
   - **(8)static 原因**。 解决方案：（1）应该尽量避免static成员变量引用资源耗费过多的实例，比如Context。（2）Context尽量使用Application Context，因为Application的Context的生命周期比较长，引用它不会出现内存泄露的问题。（3）使用WeakReference代替强引用。比如可以使用WeakReference<Context> mContextRef；（4）查找内存泄漏可以使用Android Profiler工具或者利用LeakCanary工具。

 3. **为什么Android会有APP的内存限制**：
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

> https://juejin.im/entry/56ebb4ad5bbb50004c440972

**MVC简介**：全名是Model View Controller，如图，是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。

- M层处理数据，业务逻辑等；
- V层处理界面的显示结果；
- C层起到桥梁的作用，来控制V层和M层通信以此来达到分离视图显示和业务逻辑层。


**Android中的MVC**：Android中界面部分也采用了当前比较流行的MVC框架，在Android中：

- (V)一般采用XML文件进行界面的描述，这些XML可以理解为AndroidApp的View。使用的时候可以非常方便的引入。同时便于后期界面的修改。逻辑中与界面对应的id不变化则代码不用修改，大大增强了代码的可维护性。

- (C)Android的控制层的重任通常落在了众多的 Activity 的肩上。这句话也就暗含了不要在Activity中写代码，要通过 Activity 交割 Model 业务逻辑层处理，这样做的另外一个原因是 Android 中的 Actiivity 的响应时间是5s，如果耗时的操作放在这里，程序就很容易被回收掉。

- (M)我们针对业务模型，建立的数据结构和相关的类，就可以理解为AndroidApp的Model，Model是与View无关，而与业务相关的。对数据库的操作、对网络等的操作都应该在Model里面处理，当然对业务计算等操作也是必须放在的该层的。

## 三四、MVP架构设计模式

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

### MVP(要特别熟悉，能说出优点)

任何事务都存在两面性，MVP当然也不列外，我们来看看MVP的优缺点。

- 优点：
	- 1.降低耦合度，实现了Model和View真正的完全分离，可以修改View而不影响Modle
	- 2.模块职责划分明显，层次清晰
	- 3.Presenter可以复用，一个Presenter可以用于多个View，而不需要更改Presenter的逻辑（当然是在View的改动不影响业务逻辑的前提下）
	- 4.利于测试驱动开发。以前的Android开发是难以进行单元测试的（虽然很多Android开发者都没有写过测试用例，但是随着项目变得越来越复杂，没有测试是很难保证软件质量的；而且近几年来Android上的测试框架已经有了长足的发展——开始写测试用例吧），在使用MVP的项目中Presenter对View是通过接口进行，在对Presenter进行不依赖UI环境的单元测试的时候。可以通过Mock一个View对象，这个对象只需要实现了View的接口即可。然后依赖注入到Presenter中，单元测试的时候就可以完整的测试Presenter应用逻辑的正确性。
	- 5.View可以进行组件化。在MVP当中，View不依赖Model。这样就可以让View从特定的业务场景中脱离出来，可以说View可以做到对业务完全无知。它只需要提供一系列接口提供给上层操作。这样就可以做到高度可复用的View组件。
	- 6.代码灵活性
- 缺点：
	- 1.Presenter中除了应用逻辑以外，还有大量的View->Model，Model->View的手动同步逻辑，造成Presenter比较笨重，维护起来会比较困难。
	- 2.由于对视图的渲染放在了Presenter中，所以视图和Presenter的交互会过于频繁。
	- 3.如果Presenter过多地渲染了视图，往往会使得它与特定的视图的联系过于紧密。一旦视图需要变更，那么Presenter也需要变更了。
	- 4.额外的代码复杂度及学习成本。

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

## GC垃圾回收机制

**(1)GC 垃圾回收机制**(Gabage Collection) 在jvm运行时，由于所需要存放的对象越来越多，java堆就得想办法回收已不再用的对象来节省空间，那么垃圾回收机制在进行垃圾回收前就得判断哪些实例已不再使用，哪些实例还要使用，以便于回收不再使用的实例。

**(2)判断对象是否已死**：jvm在回收垃圾之前，得先判断哪些实例是不再需要的，不能乱回收。

- 引用计数算法
- 可达性分析算法

**(3)垃圾收集算法**：

- **标记--清除算法**：先标记再清除，当对象被统一标记后，再统一回收。

- **复制算法**：把内存按容量分为两个相同的块。每次只使用其中的一块，当一块用完后，就将存活的对象复制到另一块，然后再清理已使用的空间。

- **标记--整理算法**：标记--整理算法的思想和标记--清除算法类似。但后续步骤不是清除可回收的对象，而是把存活的对象都向一端移动，然后直接清除掉边界以外的内存。

- **分代收集算法**：根据对象存活的情况不同，把内存分为新生代和年老代，新生代对象存活率低，就用复制算法，年老代存活率高，就用标记--清除算法或者标记--整理算法。

## Intent
### Intent 传递的数据有大小限制吗，如何解决？
Intent传递数据大小的限制大概在1M左右，超过这个限制就会静默崩溃。处理方式如下：

- 进程内：EventBus，文件缓存、磁盘缓存。
- 进程间：通过ContentProvider进行款进程数据共享和传递。

### Intent 传递数据时，可以传递哪些类型数据？

Intent 可以传递的数据类型非常的丰富，java 的基本数据类型和 String 以及他们的数组形式
都可以，除此之外还可以传递实现了 Serializable 和 Parcelable 接口的对象。

### 请描述一下 Intent 和 IntentFilter

- Intent：Android 中通过 Intent 对象来表示一条消息，一个 Intent 对象不仅包含有这个消息的目的
地，还可以包含消息的内容，这好比一封 Email，对于一个 Intent 对象，消息“目的地”是必须的，而内容则是可选项。通过 Intent 可以实现各种系统组件的调用与激活。

- IntentFilter：可以理解为邮局或者是一个信笺的分拣系统，这个分拣系统通过 3 个参数来识别：
	- Action: 动作 view  用户定义的字符串，用于描述一个 Android 应用程序组件，可多个。
	- Data: 数据 uri uri   Intent 可以通过 URI 携带外部数据给目标组件，<data/>
 	- Category : 而外的附加信息。

### 隐式Intent

setData(Uri.parse(网址、电话))

putExtra(键，数据)，接收是 Intent intent = getIntent()；String data = intent.getStringExtra("键名")；还有getBoolExtra()方法。

###返回数据给上一个Activity

A通过startActivityForResult启动BActivity，期望在B销毁的时候能返回一个结果给上一个A活动。
```java
Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
startActivityForResult(intent,1);//请求码只要是一个唯一值就可以了。
```
B调用了setResult，第一个参数是返回处理结果(RESULT_OK或RESULT_CANCELED)，另一个是intent
```java
Intent intent = new Intent();
intent.putExtra("key","data"); 
setResult(RESULT_OK,intent);
finish();
```
A页面重写onActivityResult()方法来得到返回的数据
```java
protected void onActivityResult(int requestCode,int resultCode,Intent data){
  switch(requestCode){
  case 1:
     if(resultCode==RESULT_OK){
     String returnedData = data.getStringExtra(key);
     }
     break;
    default:
  }
}
```

###  Serializable 和 Parcelable 的区别

在使用内存的时候，Parcelable 类比 Serializable 性能高，所以推荐使用 Parcelable 类。

- 1．Serializable 在序列化的时候会产生大量的临时变量，从而引起频繁的 GC。
- 2．Parcelable 不能使用在要将数据存储在磁盘上的情况。尽管 Serializable 效率低点，但在这种情况下，还是建议用 Serializable 。

实现：

- 1．Serializable 的实现，只需要继承 Serializable 即可。这只是给对象打了一个标记，系统会自动将其序列化。
- 2．Parcelabel 的实现，需要在类中添加一个静态成员变量 CREATOR，这个变量需要继承Parcelable.Creator 接口。

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

### 如何打多渠道包
在AndroidMainfest.xml配置相应的渠道
```
 <meta-data android:value="UMENG_CHANNEL"         
      android:name="${UMENG_CHANNEL_VALUE}"/>  <!--动态更改渠道号-->
```
在build.gradle中配置渠道信息和自动替换脚本
```
  //多渠道打包
    productFlavors {
        xiaomi {}
        huawei {}
        yingyongbao {}
        wandoujia {}
    }
```
```
    //自动替换清单文件中的渠道号
    productFlavors.all {
        flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name]
    }
```
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

### 链式存储结构和顺序存储结构
顺序表适宜于做查找这样的静态操作；链表宜于做插入、删除这样的动态操作。
若线性表的长度变化不大，且其主要操作是查找，则采用顺序表；
若线性表的长度变化较大，且其主要操作是插入、删除操作，则采用链表。
顺序表平均需要移动近一半元素
链表不需要移动元素，只需要修改指针