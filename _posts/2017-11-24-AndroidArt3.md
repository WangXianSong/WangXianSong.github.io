---
layout: post
title:  " View 的事件体系"
date:  2017-11-24 21:59:36
categories: Android
tags: Android
---
* content
{:toc}


<br />

> 第一章我们学习了 [Activity 的生命周期以及启动模式](http://xsong.wang/2017/10/01/AndroidArt/)的知识点，第二章学习了 [Android 中的 IPC 机制](http://xsong.wang/2017/10/03/AndroidArt2/)，明显第二章比第一章要吃力多了，那接下来就要学习 View 的事件体系。
> 
>
> 参考的文章：
> [http://www.jianshu.com/p/7d2c88ca24fc](http://www.jianshu.com/p/7d2c88ca24fc)
> 
> 参考的书籍：
> 《Android开发艺术探索》
> 《Android进阶之光》

<br />


# 第 3 章 View 的事件体系
## 3.1 View 的基础知识
### 3.1.1 什么是 View？

 View 是一种界面层的控件的一种抽象，一组 View 则称为 ViewGroup，同时 ViewGroup 基础了 View。意味着 View 可以是单个控件也可以是多个控件组成的组控件，通过这种关系形成了 View 树的结构。

![](https://i.imgur.com/eBxCWH7.jpg)

### 3.1.2 View的位置参数

![](https://i.imgur.com/lViaq1e.png)

x 和 y 是 View 左上角的坐标，translationX 和 translationY 是 View 左上角相对于容器的偏移量。getRawX / getRawY 是相对于手机屏幕左上角的 X 和 Y 坐标，这几个参数都是相对于父容器的坐标。

> - x = left + translationX.getX()
> - y = top + translationY.getY()

### 3.1.3 MotionEvent 和 TouchSlop

**MotionEvent滑动参数：**

> - ACTION_DOWM：手指刚接触屏幕。
> - ACTION_MOVE：手指在屏幕上移动。
> - ACTION_UP：手指在屏幕上松开的一瞬间。

**TouchSlop**

TouchSlop 是系统所能识别出的被认为是滑动的最小距离。获取这个常量：

```java
    ViewConfiguration.get(getContext()).getScaledTouchSlop();
```

### 3.1.4 VellocityTracker、GestureDetector 和 Scroller

**VellocityTracker 速度追踪：**

用于追踪手指在滑动过程中的速度，包括水平和竖直方向的速度。首先在 View 的 onTouchEvent 方法中跟踪当前单击事件的速度：

```java 
 VelocityTracker  mVelocityTracker = VelocityTracker.obtain();
  mVelocityTracker.addMovement(event);
```

接着要知道滑动速度就必须调用 computeCurrentVelocity 方法：

```java
mVelocityTracker.computeCurrentVelocity(1000);
int xVelocity = ( int ) mVelocityTracker.getXVelocity();
int yVelocity = ( int ) mVelocityTracker.getYVelocity()
```

时间间隔设为 1000 ms 时，在 1s 内，手指的水平方向从左向右划过 100 像素，那么水平速度就是 100。（当手指从右向左滑动时，水平方向速度即为负值）

**公式：速度 = ( 终点位置 - 起点位置 ) / 时间段**

最后可以在 MotionEvent.ACTION_CANCEL 调用clear方法来重置和回收内存：

```java
mVelocityTracker.clear();
mVelocityTracker.recycle();
```

** GestureDetector：**

用于辅助检测用户的单击、滑动、长按、双击等行为；建议：如果只是监听滑动相关的推荐在onTouchEvent中实现，如果需要监听双击，使用 GeststureDetector 。


** Scroller：**

　　用来实现 View 的弹性滑动，View 的 scrollTo / scrollBy 是瞬间完成的，使用 Scroller 配合 View的 computeScroll 方法配合使用达到弹性滑动的效果。

```java
Scroller mScroller = new Scroller(context);
  
 public void smoothScrollTo(int destX, int destY) {  
        int scrollX = getScrollX();
        int delta = destX - scrollX;
       mScroller.startScroll(scrollX , 0 , delta , 0 , 1000 );
        invalidate();
    }  

public void computeScroll() {
        if (mScroller.computeScrollOffset()){
        scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
        postInvalidate();
        }
}
```

## 3.2 View 的滑动

### 3.2.1 使用 scrollTo / scrollBy

- scrollTo(x,y) 表示移动到一个具体的坐标点，scrollBy(x,y) 则表示移动的增量为 dx，dy 。

- scrollTo 和 scrollBy 只能改变 View 内容而不能改变 View 本身的位置。

- scrollBy 内部也是调用了 scrollTo，它是基于当前位置的相对滑动，scrollTo 是基于所传递参数的绝对滑动。

![](https://i.imgur.com/SSP1WlK.jpg)

### 3.2.2 使用动画

**采用 View 动画**来移动，只是对View的影像做操作，它并不能真正改变View的位置参数，点击动画后的新位置无法触发点击事件的。fillAfter="true" 动画结束后停留在当前位置。

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
android:fillAfter="true" >
    <translate
        android:duration="1000"
        android:fromXDelta="0"
        android:toXDelta="300" />
</set>
 ```

**采用属性动画**会更简单，同时能解决点击事件。让 CustomView 在 1000ms 内沿着 X 轴向右平移 300 像素：

```java
ObjectAnimator.ofFloat(mCustomView,"translationX",0,300) .setDuration(1000).start();
```

传入的第一个参数是要操作的 Object target；第二个参数是要操作的属性 String propertyName；最后一个参数是一个可变的 float 类型数组 values 。 


### 3.2.3 改变布局参数 LayoutParams

改变布局参数实现滑动，即改变LayoutParams，如想将一个 View 右平移 100px，只需要将该 View 的 LayoutParams 里的 marginLeft 增加 100px 即可。

```java
ViewGroup.MarginLayoutParams params = (ViewGroup.MarginLayoutParams) getLayoutParams();
    params.leftMargin = getLeft () + offsetX;
    params.topMargin = getTop () + ofrrsetY 
    setLayoutParams ( layoutParams );
```

### 3.2.4 各种滑动方式的对比

- scrollTo / scrollBy：操作简单，适合对 View 内容的滑动；
- 动画：操作简单，适合没有交互的 View 和实现负责的动画效果；
- 改变布局参数：操作稍微复杂，适合有交互的 View；


## 3.3 弹性滑动

### 3.3.1 使用 Scroller

Scroller 不能直接完成 View 滑动，需要配合 View的computeScroll 方法才可以完成弹性滑动，它让 View 不断重绘，每一次重绘有一个时间间隔，通过这个时间间隔 Scroller 就可以得出 View 当前滑动的位置，知道了滑动位置就通过 scrollTo 来完成滑动。每一次滑动都会导致 View 小幅度滑动，多次小幅度滑动组成了弹性滑动，这就是 Scroller 的工作机制。


startScroll() 保存了我们传递的几个参数 —> invalidate() 会导致 View 重绘 —> draw() —> computeScroll() 该方法为空实现，我们内部调用 scrollTo(x,y) 实现滑动 和 postInvalidate() 继续重绘，反复下去完成弹性滑动。


### 3.3.2 通过动画

动画本身就是一种渐进的过程，通过动画可以直接实现弹性滑动。

### 3.3.3 使用延时策略

延时策略完成滑动，核心思想就是通过发送一系列延时消息从而达到一种渐进式的效果。用 Handler 或 View 的 postDelayed 方法，postDelayed 发送延时消息，然后消息中进行 View 滑动，接连不断的发送这种延时消息，达到弹性滑动的效果。也可以使用线程的 sleep 方法来实现。


## 3.4 View 的事件分发机制

### 3.4.1 点击事件的传递规则

- **dispatchTouchEvent(MotionEvent ev) ：**用来进行事件的分发。

- **onInterceptTouchEvent(MotionEvent ev)：**用来判断是否拦截某个事件。在 dispatchTouchEvent 方法内部调用，果当前 View 拦截了某个事件，那在同一个事件序列中，此方法不会再次调用，返回结果表示是否拦截当前事件。

- **onTouchEvent(MotionEvent ev)** ：用来处理点击事件。在 dispatchTouchEvent 方法内部调用，返回结果表示是否消耗当前事件，如果不消耗，在同一事件序列里，当前 View 无法再次接收到事件。

- 三者关系可以用如下伪代码表示：

```java
public boolean dispatchTouchEvent(MotionEvent ev){    
    boolean consume = false;
    if(onInterceptTouchEvent(ev)){
        consume = onTouchEvent(ev);
    }else{
        consume = child.dispatchTouchEvent(ev);   
    }
    return consume;
}
```
- 1、对于一个根 ViewGroup，点击事件产生后，首先会传递给它，这时他的dispatchTouchEvent会调用，如果它的 onInterceptTouchEvent 返回 true 表示要拦截当前事件，接下来事件会交给这个 ViewGroup 处理，它的 onTouchEvent 就会被调用。如果这个 ViewGroup 的 onInterceptTouchEvent 返回 false，则事件会继续传递给子元素，子元素的 dispatchTouchEvent 会调用，如此反复直到事件被处理。
![](https://i.imgur.com/xdbI13t.jpg)

- 2、当一个 View 需要处理事件时，如果设置了 OnTouchListener，那么 OnTouchListener 的 onTouch 方法会回调，如果 onTouch 返回 false，则当前 View 的 onTouchEvent 方法会被调用；如果返回 true，那么 onTouchEvent 方法将不会调用。由此可见，OnTouchListener 优先级高于 onTouchEvent。OnClickListener 优先级处在事件传递的尾端。

- 3、一个点击事件产生后，传递顺序：Activity->Window->View；如果一个View的onTouchEvent返回false,那么它的父容器的onTouchEvent会被调用，以此类推，所有元素都不处理该事件，最终将传递给Activity处理，即Activity的onTouchEvent会被调用。

- 4、“同一个事件序列”是指从手指接触屏幕的那一刻起，到手指离开屏幕的那一刻结束( down -> move ... move -> up )。

- 5、一个事件序列只能被一个 View 拦截且消耗，如果通过特殊手段可以将本该自己处理的事件通过 onTouchEvent 强行传递给其他 View 处理。

- 6、当一个 View 决定拦截一个事件后，那么系统会把同一个事件序列内的其他方法都直接交给它来处理，因此就不用再调用这个 View 的 onInterceptTouchEvent 去询问它是否要拦截了。

-7、事件一旦交给一个 View 处理，那么它必须消耗掉，否则同一个事件序列中剩下的事件就不会再交给它来处理了。

- 8、如果某个 View 不消耗除 ACTION_DOWN 以外的其他事件，那么这个点击事件会消失，此时父元素的 onTouchEvent 并不会被调用，并且当前 View 可以收到后续事件，最终这些消失的点击事件会传递给Activity处理。(个人理解：有点像手机轻轻点了一下屏幕，并没有什么实际操作)

- 9、ViewGroup 默认不拦截任何事件，ViewGroup 的 onInterceptTouchEvent 方法默认返回 false。

- 10、View 没有 onInterceptTouchEvent 方法，一旦有事件传递给它，那么它的 onTouchEvent 方法就会被调用。

- 11、View 的onTouchEvent 方法默认消耗事件（返回true），除非他是不可点击的（clickable 和longClickable 同时为false）。View 的 longClickable 属性默认都为 false，clickable 属性分情况，Button 默认为 true，TextView 默认为 false。

- 12、onClick 发生的前提是 View 可点击，并且它收到了 down 和 up 事件。

- 13、事件传递过程是由内而外，事件总是先传递给父元素，然后在由父元素分发给子 View，通过 requestDisallowInterceptTouchEvent 方法可以在子元素干预父元素的事件分发过程，但 ACTION_DOWN 事件除外。

### 3.4.2 事件分发的源码解析

——




## 3.5 View 的滑动冲突

### 3.5.1 常见的滑动冲突场景

>- 场景1 —— 外部滑动方向与内部滑动方向不一致（左右滑动：Fragment，上下滑动： ListView）。
>- 场景2 —— 外部滑动方向与内部滑动方向一致，（ScrollView 中包含 ListView )；
>- 场景3 —— 上面两种情况的嵌套；

### 5.2滑动冲突的处理规则

根据滑动是水平滑动还是竖直滑动来判断到底谁来拦截事件 ：

>- (1)滑动路径和水平方向所形成的**夹角**
>- (2)水平方向和竖直方向上的**距离差**
>- (3)水平和竖直方向的 **速度差** 
>- (4)从业务的需求上得出相应的处理规则。

### 5.3 滑动冲突的解决方式

>- **外部拦截法** —— 即点击事件先经过父容器的拦截处理，如果父容器需要此事件就拦截，不需要就不拦截，需要重写父容器的 onInterceptTouchEvent 方法；在 onInterceptTouchEvent 方法中，首先在 ACTION_DOWN 这个事件中，父容器必须返回 false，即不拦截 ACTION_DOWN 事件，因为一旦父容器拦截了 ACTION_DOWN，那么后续的 ACTION_MOVE / ACTION_UP 都会直接交给父容器处理；其次是 ACTION_MOVE，根据需求来决定是否要拦截；最后 ACTION_UP 事件，这里必须要返回 false，在这里没有多大意义。


>- **内部拦截法** —— 所有事件都传递给子元素，如果子元素需要就消耗掉，不需要就交给父元素处理，需要子元素配合 requestDisallowInterceptTouchEvent 方法才能正常工作；父元素需要默认拦截除 ACTION_DOWN 以外的事，这样子元素调用 parent.requestDisallowInterceptTouchEvent(false) 方法时，父元素才能继续拦截需要的事件。（ACTION_DOWN 事件不受 requestDisallowInterceptTouchEvent 方法影响,所以一旦父元素拦截 ACTION_DOWN 事件，那么所有元素都无法传递到子元素去）。




