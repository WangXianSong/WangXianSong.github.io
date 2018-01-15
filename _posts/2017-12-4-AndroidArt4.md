---
layout: post
title:  " 第四章 View 的工作原理"
date:  2017-12-4 21:59:36
categories: Android
tags: Android
---



此篇文章为《 Android 开发艺术探索》第四章“ View 的工作原理”的总结，学习后会更好的了解 View 的工作原理以及自定义 View 的方法。内容有：初识 ViewRoot 和 DecorView 、理解 MeasureSpec、View 的工作流程、自定义 View。


> 参考文章： [http://www.jianshu.com/p/7d2c88ca24fc](http://www.jianshu.com/p/7d2c88ca24fc)

> 参考书籍：《Android开发艺术探索》 《Android进阶之光》



## 4.1 初识 ViewRoot 和 DecorView 

View 对应 ViewRootImpl 类，它是连接 WindowManager 和 DecorView 的纽带，View 的三大流程均是通过 ViewRoot 来完成的。在 ActivityThread 中，当 Activity 对象被创建完毕后，会将 DecorView 添加到 Window 中，同时会创建 ViewRootImpl 对象，并将 ViewRootImpl 对象和 DecorView 建立关联。


View 的绘制流程从 ViewRoot 的 performTraversals 开始，经过 measure、layout 和 draw 三个过程才可以把一个 View 绘制出来。

performTraversals 会依次调用 performMeasure、performLayout 和 performDraw 三个方法，这三个方法分别完成顶级View的measure、layout和draw这三大流程。其中performMeasure中会调用measure方法，在measure方法中又会调用onMeasure方法，在onMeasure方法中则会对所有子元素进行measure过程，这样就完成了一次measure过程；子元素会重复父容器的measure过程，如此反复完成了整个View数的遍历。

![](https://i.imgur.com/EI0S2FP.jpg)

**measure** 过程决定了 View 的**宽高**，Measure 完成以后，可以通过 getMeasuredWidth 和getMeasuredHeight 方法来获取到 View 测量后的宽高。

**Layout** 过程决定了 View 的四个顶点的坐标和实际的 View 的宽高，完成以后，可以通过 getTop、getButton、getLeft、getRight 来拿到 View 的四个顶点的位置，并可以通过 getWidth 和 getHeight方法来拿到 View 的最终宽高。

**Draw** 过程则决定了 View 的显示，只有 draw 方法完成以后 View 的内容才能呈现到屏幕上。

**DecorView** 作为顶级View，一般情况下它内部包含了一个竖直方向的 LinearLayout，里面分为两个部分，上面是标题栏，下面是内容栏。在 Activity 通过 setContextView 所设置的布局文件其实就是被加载到内容栏之中的。内容栏的id是content，想要获取content可以这样写：ViewGroup content = findViewById(R.android.id.content); 想要获取View的话：Viewcontext.getChildAt(0); 
<br />
<br />
<br />
<br />
## 4.2 理解 MeasureSpec

### 4.2.1 MeasureSpec

MeasureSpec 在很大程度上决定了一个 View 的尺寸规格，在测量的过程中，系统会将 View 的 LayoutParams 根据父容器所施加的规则转换成对应的 MeasureSpec，然后再根据这个 measureSpec 来测量出 View 的宽高。

MeasureSpec 代表一个 32 位的 int 值，高 2 位为 SpecMode，低 30 位为 SpecSize（某种测量模式下的规格大小）。

>SpecMode是指测量模式：
>
>1. **UNSPECIFIED** 父容器不对 View 进行任何限制，要多大给多大，一般用于系统内部。
>
>2. **EXACTLY** 父容器检测到 View 所需要的精确大小，这时候 View 的最终大小就是 SpecSize 所指定的值，对应 LayoutParams 中的 match_parent 和具体数值这两种模式。
>
>3. **AT_MOST** 父容器指定了一个可用大小即 SpecSize，View 的大小不能大于这个值，不同 View 实现不同，对应 LayoutParams 中的 wrap_content。

### 4.2.2 MeasureSpec 和 LayoutParams 的对应关系

- 当 View 采用固定宽/高的时候，不管父容器的 MeasureSpec 是什么，View 的 MeasureSpec 都是精准模式并且其大小遵循 Layoutparams 中的大小。

- 当 View 的宽高是 match_parent 时，如果父容器的模式是精准模式，那么 View 也是精准模式并且其大小是父容器的剩余空间。如果父容器是最大模式，那么 View 也是最大模式并且其大小不会超过父容器的剩余空间。

- 当 View 的宽高 wrap_content 时，不管父容器的模式是精准还是最大化，View 的模式总是最大化并且大小不能超过父容器的剩余空间。

<br />
<br />
<br />
<br />

## 4.3 View 的工作流程

### 4.3.1 measure 过程

**View 的 measure 过程**

```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {          
     setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(),  widthMeasureSpec),     
     getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
}
```
![](https://i.imgur.com/Yj7fc9Y.jpg)

- **setMeasuredDimension** 方法会设置 View 的宽/高的测量值

- **getDefaultSize** 方法返回的大小就是 measureSpec 中的 specSize，也就是 View 测量后的大小，绝大部分情况和 View 的最终大小 ( layout 阶段确定) 相同。

- **getSuggestedMinimumWidth** 方法，作为 getDefaultSize 的第一个参数（建议宽度)。

- **getSuggestedMinimumHeight** 方法，作为 getDefaultSize 的第二个参数。

- 直接继承 View 的自定义控件，需要重写 onMeasure 方法并且设置 wrap_content 时的自身大小，否则在布局中使用了 wrap_content 相当于使用了 match_parent。解决方法：在 onMeasure 时，给 View 指定一个内部宽/高，并在 wrap_content 时设置即可，其他情况沿用系统的测量值即可。


**ViewGroup 的 measure 的过程**

- 对于ViewGroup来说，除了完成自己的 measure 过程之外，还会遍历去调用所有子元素的 measure 方法，个个子元素再递归去执行这个过程，和 View 不同的是，ViewGroup 是一个抽象类，没有重写 View的onMeasure方法，提供了 measureChildren 方法。

- measureChild 的思想就是 取出子元素的 LayoutParams，再通过 getChildMeasureSpec 方法来创建子元素的 MeasureSpec，接着将 MeasureSpec 传递给 View 的 measure 方法进行测量。

- measure 完成之后，通过getMeasureWidth/Height方法就可以获取View的测量宽/高，需要注意的是，在某些极端情况下，系统可能要多次measure才能确定最终的测量宽/高，比较好的习惯是在onLayout方法中去获取测量宽/高或者最终宽/高。

**Activity在启动过程中获取 View 的宽/高：**

- 1. Activity/View#onWindowFocusChanged。

　　onWindowFocusChanged这个方法的含义是：View已经初始化完毕了，宽/高已经准备好了，这个时候去获取宽/高是没问题的。但是！onWindowFocusChanged会在Activity的窗口得到焦点和失去焦点时均被调用一次，如果需要频繁的进行onResume和onPause，那么onWindowFocusChanged也会被频繁地调用。

- 2. view.post(runnable)。

　　通过post将一个runnable投递到消息队列的尾部，当Looper调用此runnable的时候，View也初始化好了。

- 3. ViewTreeObserver。

　　使用ViewTreeObserver的众多回调可以完成这个功能，比如OnGlobalLayoutListener这个接口，当View树的状态发送改变或View树内部的View的可见性发生改变时，onGlobalLayout方法会被回调。需要注意的是，伴随着View树状态的改变，onGlobalLayout会被回调多次。

- 4. view.measure(int widthMeasureSpec,int heightMeasureSpec)。通过手动对View 进行 measure 来得到 View 的宽/高。

  ** (1).match_parent**：直接放弃，无法measure出具体的宽/高。

  ** (2).具体的数值(dp / px)**：

```java
  int widthMeasureSpec = MeasureSpec.makeMeasureSpec(100,MeasureSpec.EXACTLY);
int heightMeasureSpec = MeasureSpec.makeMeasureSpec(100,MeasureSpec.EXACTLY);
view.measure(widthMeasureSpec,heightMeasureSpec);
```

  ** (3).wrap_content**：

```java
int widthMeasureSpec = MeasureSpec.makeMeasureSpec((1<<30)-1,MeasureSpec.AT_MOST);
int heightMeasureSpec = MeasureSpec.makeMeasureSpec((1<<30)-1,MeasureSpec.AT_MOST);
view.measure(widthMeasureSpec,heightMeasureSpec);
```


　　





### 4.3.2 layout 的过程

在View的默认实现中，View的测量宽/高和最终宽/高是相等的，测量宽/高形成于View的measure过程，而最终宽/高形成于View的layout过程。


### 4.3.3 draw的过程

将View绘制到屏幕上，大概的几个步骤：

- 1.绘制背景background.draw(canvas)
- 2.绘制自己（onDraw）
- 3.绘制children(dispatchDraw)
- 4.绘制装饰（onDrawScrollBars）

View的绘制过程是通过dispatchDraw来实现的，它会遍历所有子元素的draw方法。

如果一个View不需要绘制任何内容，那么设置setWillNotDraw为true后，系统会进行相应的优化；ViewGroup默认为true，如果我们的自定义ViewGroup需要通过onDraw来绘制内容的时候，需要显示的关闭它。


<br />
<br />
<br />
<br />

## 4.4 自定义 View
### 4.4.1 自定义 View 的分类

1. 继承 View 重写 onDraw 方法；
1. 继承 ViewGroup 派生特殊的 Layout；
1. 继承特定的 View，比如 TextView；
1. 继承特定的 ViewGroup，比如 LinearLayout；

### 4.4.2 自定义 View 须知

1. 让 View 支持 wrap_content；
2. 如果有必要，让你的 View 支持 padding；
3. 尽量不要在 View 中使用 Handler，没有必要，View 内部提供了 post 系列的方法；
4. View 中如果有线程或者动画，需要继续停止，参考View#onDetachedFromWindow；
5. View 带有滑动嵌套情形时，需要处理好滑动冲突；


### 4.4.4 自定义 View 的思想

1. 首先要掌握基本功，比如 View 的弹性滑动、滑动冲突、绘制原理等，这些都是自定义 View 所必须的。
2. 熟练掌握基本功后，在面对新的自定义 View 时，要能够对其分类并选择合适的实现思路。
3. 另外平时还要多积累一些自定义 View 相关的经验，并逐渐做到融会贯通。
