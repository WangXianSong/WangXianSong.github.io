---
layout: post
title:  "第七章 Android 动画深入分析"
date:  2018-1-15 16:50:54
categories: Android
tags: Android
---
* content
{:toc}





## 前言
此篇文章是对“Android 动画深入分析”的学习与总结，动画效果一直是人机交互中一个非常重要的部分。在提示、引导类的场景中，合理使用动画能让用户获得更加愉悦的使用体验。在学习过程中，参考资料来源有《Android 艺术开发探索》、《Android 群英传》、慕课网教学视频。

本章的主要内容：

- **View 动画**：定义了透明度、平移、缩放、旋转动画，实现原理是每次绘画视图时，View 所在的 ViewGroup 中的 drawChild 函数获取该 View 的 Animation 的 Transformation 值，然后调用 canvas.concat(transformToApply.getMatrix())，通过矩阵运算完成动画帧。缺点是不具备交互性，当某个元素发生 View 动画后，其响应事件的位置仍然在动画前的地方。

- **属性动画**：在一定时间间隔内，通过不断对值进行改变，并不断将该值赋给对象的属性，从而实现该对象在该属性上的动画效果，响应点击事件的有效区域也会发生改变。比较常用的几个动画类是：ValueAnimator、ObjectAnimator 和 AnimatorSet，其中 ObjectAnimator 继承自 ValueAnimator，AnimatorSet 是动画集合。


   ![](https://i.imgur.com/rWoHIEL.png)   



## 1. 视图动画

### 1.1 基本动画

1、View 动画的四种变换效果对应着 Animation 的以下四个子类，他们不仅可以用 XML 来定义，用代码来动态创建也可以，推荐使用可读性更好的 XML 来定义。

- TranslateAnimation（平移动画）
- ScaleAnimation（缩放动画）
- RotateAnimation（旋转动画）
-  AlphaAnimation（透明度动画）

2、**创建动画的 XML 文件**：< set > 标签表示动画集合，对应 **AnimationSet** 类，它可以包含若干个动画，并且他的内部也可以嵌套其他动画集合。

```xml

<?xml version="l.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
 android:interpolator="@[package:]anim/interpolator_resource"
android:shareInterpolator=["true" | "false"] >
	<alpha
		android:fromAlpha="float" 
		android:toAlpha="float" />
	<scale
		android:fromXScale="float" 
		android:toXScale="float" 
		android:fromYScale="float"
		android:toYScale="float" 
		android:pivotX="float" 
		android:pivotY="float" />
	<translate
		android:fromXDelta="float"
		android:toXDelta="float" 
		android: fromYDelta="float" 
		android: toYDelta="float" />
	<rotate
		android:fromDegrees="float"
		android: toDegrees=" float" 
		android:pivotX=" float" 
		android:pivotY="float" />
	<set>
	...
	</set>
</set>

```

- **android:interpolator**：表示动画集合所采用的插值器，作用是影响动画的速度，比如非匀速动画就需要通过插值器来控制，默认加速减速插值器：@android:anim/accelerate_decelerate_interpolator

- **android:shareInterpolator**：表示集合中的动画是否和集合共享同一个插值器，如果集合不指定插值器，那么子动画就需要单独指定所需的插值器或默认值。

- **< translate >** 标签为**平移**动画，对应 TranslateAnimation；

- **< scale >** 标签为**缩放**动画，对应 ScaleAnimation；

- **< rotate >** 标签为**旋转**动画，对应 RotateAnimation；

- **< alpha >** 标签为**透明度**动画，对应 AlphaAnimation；

- 还有一些常用的属性：
 - **android:duration**：动画持续的时间；
 - **android:fillAfter**：动画结束后是否停留在结束位置；


例子：如何去调用这些动画呢

XML 内容：
```xml

//res/anim/animation_test.xml 
<?xml version="l.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" 
	android:fillAfter="true" 
	android:zAdjustment="normal">
	<translate
		android:duration="100"
		android:fromXDelta="0"
		android:fromYDelta="0"
		android:interpolator="@android:anim/linear_interpolator"
		android: toXDelta="100" 
		android:toYDelta="100"/>
	<rotate
		android:duration="400" 
		android:fromDegrees="0" 
		android:toDegrees=="90"/>
</set>

```

java 代码：

```java

Button mButton = (Button) findViewById(R.id.button);
Animation animation = AnimationUtils.loadAnimation(this,R.anim.animation_test);
mButton.startAnimation(animation);

```

3、除了XML中定义，还可以通过 **Java 代码来实现应用代码**，以下代码中，创建了一个透明度动画，将一个 Button 在 300ms 内由 0 变为 1。

```java

AlphaAnimation alphaAnimation = new AlphaAnimation(0,1);
alphaAnimation.setDuration(300);
mButton.startAnimation(alphaAnimation);
```

4、另外还可以通过 Animation 的 setAnimationListener 方法可以给 View 动画添加监听。

```java

 public static interface AnimationListener {
	void onAnimationStart(Animation animation);
	void onAnimationEnd(Animation animation);
	void onAnimationRepeat(Animation animation);
}

```

### 1.2 自定义 View  动画

自定义 View  动画只要继承 Animation 这个抽象类，然会重写它的 initialize 和 applyTransformation 方法即可，在 initialize 方法中做初始化工作，在 applyTransformation 中进行相应的矩阵变换即可。



### 1.3 帧动画 AnimationDrawable

1、帧动画是顺序播放一组预先定义好的图片，类似于电影播放。对应的类是 AnimationDrawable 来使用帧动画。首先通过XML来定义一个 AnimationDrawable。

```xml

//res/drawable/frame_animation.xml
<?xml version="l.0" encoding="utf-8"?>
<animation-List xmlns:android="http://schemas.android.com/apk/res/android"
	android:oneshot="false">
	<item android:drawable="@drawable/image1" android:duration="500"/>
	<item android:drawable="@drawable/image2" android:duration="500"/>
	<item android:drawable="@drawable/image3" android:duration="500"/>
</animation-List>

```

2、然后通过上面的代码作为 View 的背景并通过 Drawable 来播放动画即可：

```java

Button mButton = (Button) findViewById(R.id.button);
mButton.setBackgroudResouce(R.drawable.frame_animation);
AnimationDrawable drawable = (AnimationDrawable) mButton.getBackground();
drawable.start();

```

3、注意的一点是：图片比较多或者图片比较大的情况下容易引起 OOM。



### 1.4 布局动画 LayoutAnimation

为 ViewGroup 指定一个动画，为其子元素出场时会具有这种动画。常常用作 ListView 上，当每个 item 出场都会以一定的动画形式出现。

方法1：通过XML实现为ViewGroup的子元素添加出场动画。

(1)定义LayoutAnimation，并指定子元素出场动画，//res/anim/anim_layout.xml

```xml

<?xml version="1.0" encoding="utf-8"?>
<layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android"
    android:animation="@anim/anim_item"
    android:animationOrder="normal"
    android:delay="0.5" />

```

(2)定义子元素具体的入场动画	//res/anim/anim_item.xml

```xml

<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="300"
    android:interpolator="@android:anim/accelerate_interpolator"
    android:shareInterpolator="true">
    <alpha
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />

    <translate
        android:fromXDelta="500"
        android:toXDelta="0" />
</set>

```

(3)给 ListView 控件指定 LayoutAnimation //activity_main.xml

```xml

    <ListView
        android:id="@+id/listview_id"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#fff4f7f9"
        android:cacheColorHint="#000"
        android:divider="#dddbdb"
        android:dividerHeight="1.0px"
        android:layoutAnimation="@anim/anim_layout"
        android:listSelector="@android:color/transparent" />

```

![](https://i.imgur.com/SFBucBR.gif)

方法2：通过LayoutAnimationController 来实现。

```java

        mListView = findViewById(R.id.listview_id);
        Animation mAnimation = AnimationUtils.loadAnimation(this, R.anim.anim_item);
        LayoutAnimationController controller = new LayoutAnimationController(mAnimation);
        controller.setDelay(0.5f);
        controller.setOrder(LayoutAnimationController.ORDER_NORMAL);
        mListView.setLayoutAnimation(controller);

```

### 2.2 Activity 的切换效果

1、Activity 之间的切换可以通过自定义来设定，主要用到 overridePendingTransition( int enterAnim , int exitAnim ) 这个方法，要用在 startActivity( Intent )，或者 在 finish() 之后被调用才能生效。

 - enterAnim：Actiivity 被打开时，所需的动画资源id；
 - exitAnim：Actiivity 被暂停时，所需的动画资源id；

```java

        Intent intent = new Intent(MainActivity.this, Main2Activity.class);
        startActivity(intent);
        overridePendingTransition(R.anim.enter_anim, R.anim.exit_anim);

```

```java

    @Override
    public void finish() {
        super.finish();
        overridePendingTransition(R.anim.enter_anim, R.anim.exit_anim);
    }

```

2、Fragment 也可以添加切换动画，通过FragmentTransaction 中的 setCustAnimation()方法来添加切换动画。






## 3. 属性动画

在属性动画中，用得最多的就是 AnimatorSet 和 ObjectAnimator 配合，ObjectAnimator 只控制一个对象的一个属性值，而 AnimatorSet 就是将 ObjectAnimator 组合起来，通过更精细化控制，如setFrameDelay 方法设置动画帧之间的间隙时间，调整时间，减少动画频繁的绘制，从而减少 CPU 资源的损耗。

属性动画通过调用属性的 get 、set 方法来真实地控制一个 View 的属性值，因此强大的属性动画框架能实现所有的动画效果。没有做不到，只有想不到。666

### 3.1 ObjectAnimator

1、简单使用方法

```java
                ObjectAnimator animator = ObjectAnimator.ofInt(btn_Object, "width", 1000);
                animator.setDuration(1000);
                animator.setInterpolator(new LinearInterpolator());
                animator.start();
```

 ![](https://i.imgur.com/d8Hgi8E.gif) 

通过 ObjectAnimator 的静态工厂方法，创建一个 ObjectAnimator 对象，需要返回三个参数，同时还可以设置显示时长、插值器等属性。

- 第一个参数：需要操控的 View；
- 第二个参数：要操纵的属性；
- 第三个参数：可变数组参数；


2、常用属性值

- **translationX 和 translationY**：控制着 View 对象从它的布局容器的左上角坐标偏移的位置；
- **rotation、rotationX 和 rotationY**：控制着 View 对象围绕它的支点进行 2D和3D 旋转；
- **scaleX 和 scaleY**：控制着 View 对象围绕它的支点进行 2D 缩放；
- **pivotX 和 pivotY**：控制着 View 对象的支点位置，默认是中心点。
- **x 和 y**：描述了 View 对象在它容器中的最终位置，他是最初的左上角坐标和 translationX、translationY 值的累计和；
- **alpha**：表示 View 对象的 alpha 透明度，默认是 1(不透明)，0 代表完全透明(不可见)。

3、属性做动画

在使用 ObjectAnimator 的时候，需要注意的是！那就是要操作的属性必须具有 get、set 方法，不然 ObjectAnimator 就无法起效，以下是三种解决方法：


- **第一种：给你的对象加上 get 和 set 方法，如果你有权限的话。**（最简单，但往往是不可行）
- **第二种：通过自定义一个属性类来包装原始对象，间接为其提供个 get 和set 方法**。(比较方便、好理解)：
- **第三种：采用 ValueAnimator 来实现，监听动画过程，自己实现属性的改变。**

第一种方法就不用太在乎，因为往往我们都是没有权限。

第二种的具体如何使用包装类的方法给一个属性增加 get、set 方法，代码如下：

```java

    private static class WrapperView {
        private View mTarget;

        public WrapperView(View mTarget) {
            this.mTarget = mTarget;
        }

        public int getWidth() {
            return mTarget.getLayoutParams().width;
        }

        public void setWidth(int width) {
            mTarget.getLayoutParams().width = width;
            mTarget.requestLayout();
        }
    }

```

通过以上代码给一个属性包装了一层，并提供了get、set 方法，接下来是间接去调用即可。

```java

            case R.id.btn_anim_object:
                WrapperView wrapperView = new WrapperView(btn_Object);
                ObjectAnimator.ofInt(wrapperView, "width", 1000).setDuration(1000).start();
                break;

```
第三种是采用 ValueAnimator 来实现，ValueAnimator 在属性动画中占有非常重要的地位，连 ObjectAnimator 也是继承自 ValueAnimator ，接下来就让我们去学习 ValueAnimator。

### ValueAnimator

> ValueAnimator 本身不提供任何动画效果，它更像一个数值发生器，用来产生具有一定规律的数字，从而让调用者来控制动画的实现过程。

这句话是来自《Android 群英传》的对 ValueAnimator 的介绍，我认为说得非常对！

通常情况下，在 ValueAnimator 的 AnimatorUpdateListener 中监听数值的转换，从而完成动画的变换，以下是简单地例子：

```java

 ValueAnimator valueAnimator = ValueAnimator.ofInt(0, 400);
                valueAnimator.setTarget(btn_Value);
                valueAnimator.setDuration(1000);
                valueAnimator.addUpdateListener(new AnimatorUpdateListener() {
                    @Override
                    public void onAnimationUpdate(ValueAnimator valueAnimator) {

                        int curValue = (int) valueAnimator.getAnimatedValue();
                        btn_Value.layout(curValue,curValue,curValue+btn_Value.getWidth(),curValue+btn_Value.getHeight());
                    }
                });
                valueAnimator.start();

```
<center>![](https://i.imgur.com/Z0rwtUm.gif)</center>

在上面的例子中，它会在 1000ms 内将一个数从 1 到 400，然后动画的每一帧会回调 onAnimationUpdate 方法，可以在其中进行具体的操作。


### PropertyValuesHolder

类似视图动画中的 AnimationSet，对一个对象的多个属性同时作用多种动画。其实，在属性动画中还有一个叫 AnimatorSet 类，相同的操作，但却有更牛逼的功能。

```java

                PropertyValuesHolder pvh1 = PropertyValuesHolder.ofFloat("translationX", 300f);
                PropertyValuesHolder pvh2 = PropertyValuesHolder.ofFloat("scaleX", 1f, 2f, 1f);
                PropertyValuesHolder pvh3 = PropertyValuesHolder.ofFloat("scaleY", 1f, 2f, 1f);
                ObjectAnimator.ofPropertyValuesHolder(imageView_id, pvh1, pvh2, pvh3)
                        .setDuration(1000).start();

```


### AnimatorSet

这个类用于将一个动画集合按特定的顺序播放。动画可以设置成同时播放、顺序播放或者在一定的延时后播放。

与 PropertyValuesHolder 的区别是 AnimatorSet 能更为精确的顺序来控制动画效果。

与 AnimationSet 的区别是父类不一样，然后是针对属性动画做效果，动画会导致对象的触发位置发生改变。

在属性动画中，AnimatorSet 正是通过 playTogether()、playSequentially()、AnimatorSet.play().with() 、before()、after()这些方法来控制多个动画的协同工作方式，从而做到对动画播放顺序的精确控制。 

```java

 //第一种写法
                AnimatorSet set = new AnimatorSet();
                set.playTogether(
                        ObjectAnimator.ofFloat(imageView_id, "rotationX", 0, 360),
                        ObjectAnimator.ofFloat(imageView_id, "rotationY", 0, 180),
                        ObjectAnimator.ofFloat(imageView_id, "rotation", 0, -90),
                        ObjectAnimator.ofFloat(imageView_id, "translationX", 0, 90),
                        ObjectAnimator.ofFloat(imageView_id, "translationY", 0, 90),
                        ObjectAnimator.ofFloat(imageView_id, "scaleX", 1, 1.5f),
                        ObjectAnimator.ofFloat(imageView_id, "scaleY", 1, 0.5f),
                        ObjectAnimator.ofFloat(imageView_id, "alpha", 1, 0.25f, 1)
                );
                set.setDuration(5 * 1000).start();


                //第二种写法
                ObjectAnimator animator1 = ObjectAnimator.ofFloat(imageView_id, "translationX", 300f);
                ObjectAnimator animator2 = ObjectAnimator.ofFloat(imageView_id, "scaleX", 1f, 2f, 1f);
                ObjectAnimator animator3 = ObjectAnimator.ofFloat(imageView_id, "scaleY", 1f, 2f, 1f);
                AnimatorSet set1 = new AnimatorSet();
                set1.play(animator1).with(animator2).before(animator3);
                set1.setDuration(1000);
                set1.start();

```


### 动画事件的监听

完整的动画具有 Start、Repeat、End、Cancel 四个过程，通过 Android 提供了接口，可以很方便地监听到这四个事件：

```java

ObjectAnimator objectAnimator = ObjectAnimator.ofFloat(view,"alpha",1f, 0, 2f);
                objectAnimator.addListener(new AnimatorListener() {
                    @Override
                    public void onAnimationStart(Animator animator) {
                    }

                    @Override
                    public void onAnimationEnd(Animator animator) {
                    }

                    @Override
                    public void onAnimationCancel(Animator animator) {
                    }

                    @Override
                    public void onAnimationRepeat(Animator animator) {
                    }
                });

```

更人性化的是，Android 为我们提供了一个 AnimatorListenerAdapter 来让我们选择必要的事件监听，代码如下：


```java

                ObjectAnimator objectAnimator = ObjectAnimator.ofFloat(imageView_id, "alpha", 1f, 0, 2f);
                objectAnimator.addListener(new AnimatorListenerAdapter() {
                    @Override
                    public void onAnimationEnd(Animator animation) {
                        super.onAnimationEnd(animation);
                        Toast.makeText(getApplicationContext(), "动画结束", Toast.LENGTH_SHORT).show();
                    }
                });
                objectAnimator.setDuration(1000);
                objectAnimator.start();

```

1. **AnimatorListener** 会监听动画的开始、结束、取消以及重复播放。

2. 为了方便开发，系统提供了 **AnimatorListenerAdapter** 类，它是 AnimatorListener 的适配器类，可以有选择的实现以上 4 个方法。

3. **AnimatorUpdateListener** 会监听整个动画的过程，动画由许多帧组成的，每播放一帧，onAnimationUpdate 就会调用一次。


### 插值器Interpolator与估值器Evaluator

属性动画中的插值器和估值器很重要，它们是实现非匀速动画的重要手段。

 1. **TimeInterpolator 时间插值器**，作用是根据时间流逝的百分比 来计算当前属性值改变的百分比，系统预置的有 LinearInterpolator (线性插值器：匀速动画)、AccelerateDecelerateInterpolator (加速减速插值器：动画两头慢中间快)、DecelerateInterpolator(减速插值器：动画越来越慢)。
<center>![](https://i.imgur.com/ivj29tO.jpg)</center>
 2. **TypeEvaluator 类型估值算法**，作用是根据当前属性改变的百分比来计算改变后的属性值，系统预置的有 IntEvaluator (针对整型属性)、FloatEvaluator (针对浮点型属性)、ArgbEvaluator (针对 Color 属性)。

 3. 插值器和估值器除了系统提供之外，我们还可以自定义。自定义插值器需要实现 Interpolator 或者 TimeInterpolator；自定义估值器算法需要实现 TypeEvaluator。









