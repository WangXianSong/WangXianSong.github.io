---
layout: post
title:  " Android 的 Drawable"
date:  2017-12-13 21:59:36
categories: Android
tags: Android
---
* content
{:toc}


<br />


> 参考文章：
> [http://www.jianshu.com/p/7d2c88ca24fc](http://www.jianshu.com/p/7d2c88ca24fc)

> 参考书籍：
> 《Android开发艺术探索》
> 《Android进阶之光》

<br />



# 第 6 章 Android的Drawable

## Drawable 简介

- drawable 表示可以在 canvas（画布）中进行绘制的抽象概念，是个抽象类。

- 在 res/drawable/ 目录下新建 XML 文件，使用自定义 drawable 对应标签创建 drawable。

- 也可以直接通过代码创建 drawable 对象，但这方式使用起来比较复杂。

## Drawable 分类

### 1、BitmapDrawable

这是最简单的Drawable，它表示的是一张**图片**。

```xml
<?xml version="1.0" encoding="utf-8"?>
<bitmap
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/drawable_img"
    android:antialias="true"
    android:dither="true"
    android:filter="true"
    android:gravity="center"
    android:mipMap="false"
    android:tileMode="clamp" />
```

- **android:src :** 图片资源 id 。

- **android:antialias：**是否开启图片抗锯齿功能，开启后图片会变得平滑，也会轻微降低图片清晰度，建议开启。
- **android:dither：**是否开启抖动效果，防止失真，建议开启。
- **android:filter：**是否开启过滤效果，当图片尺寸被拉伸或压缩时，可以保持较好的显示效果，建议开启。
- **android:gravity：**对图片进行定位，用“ | ”来组合使用(top、bottom、left、right、center_vertical等等)。
- **android:mipMap：**是种图像相关的处理技术“纹理映射”，默认不开启。
- **android:titleMode：**平铺模式，有这几个选项[" disabled " | " repeat" | "mirror" | "clamp" ]。                                  
 - **disabled** 表示关闭平铺模式(默认值)，开启后 gravity 属性失效；
 - **repeat** 表示简单的水平和竖直方向上的平铺效果；
 - **mirror** 表示水平和竖直方向上的镜面投影效果；
 - **clamp**  表示图片四周的像素会扩展到周围区域。







### 2、NinePatchDrawable
它表示的是一张.9格式的**图片**，可以自动地根据所需的宽/高进行相应的缩放并保证不失真。与BitmapDrawable都是表示一张图片，所以和BitmapDrawable的对应属性的含义是相同的。

```xml
<?xml version="1.0" encoding="utf-8"?>
<nine-patch
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/drawable_img"
 />
```



### 3、ShapeDrawable

它既可以是**纯色的图形**，也可以是具有**渐变效果的图形**。

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <corners
        android:bottomLeftRadius="10dp"
        android:bottomRightRadius="10dp"
        android:radius="5dp"
        android:topLeftRadius="10dp"
        android:topRightRadius="10dp" />
    <gradient
        android:angle="0"
        android:centerColor="#3F3"
        android:centerX="100"
        android:centerY="20"
        android:endColor="#ccc"
        android:gradientRadius="100dp"
        android:startColor="#000000"
        android:type="linear"
        android:useLevel="false" />
    <padding
        android:bottom="10dp"
        android:left="10dp"
        android:right="10dp"
        android:top="10dp" />
    <size
        android:width="200dp"
        android:height="100dp" />
    <solid android:color="#428bca" />
    <stroke
        android:width="1dp"
        android:color="#cccccc"
        android:dashGap="2dp"
        android:dashWidth="50dp" />
</shape>
```

- **android:shape**：表示图形的形状，有四个选项：rectangle(矩形)、oval(椭圆)、line(横线)、ring(圆形)。
-  **< corners >**：表示shape的四个角度，只用于矩形shape，这里的角度是指圆角的程度。
 - android:radius：为四个角同时设定相同的角度，优先级比单向设定要低一点点。
 - android:topLeftRadius：设定左上角的角度；
 - android:topRightRadius：设定右上角的角度；
 - android:bottomRightRadius：设定右下角角的角度；
 - android:bottomLeftRadius：设定左下角的角度；

- **< gradient >**：表示渐变的效果，与< soild > 标签相互排斥的，其中 solid 表示纯色填充。
 - android:angle：渐变的角度，默认是0，其值必须是45的倍数，0表示从左到右，90表示从上到下。
 - android:centerX：渐变的中心点的横坐标。
 - android:centerY：渐变的中心点的纵坐标。
 - android:startColor：渐变的起始色。
 - android:centerColor：渐变的中间色。
 - android:endColor：渐变的结束色。
 - android:gradientRadius：渐变半径，当android:type="radial"时有效。
 - android:type：渐变的类别，有linear(默认线性渐变)、radial(径向渐变)、sweep(扫描线渐变)三者。

- **< solid >**：这个标签表示纯色填充，通过 android:color 指定 shape 中填充的颜色。

- **< stroke >**：表示 shape 的秒边。
 - android:width：描边的宽度。
 - android:color：描边的颜色。
 - android:dashWidth：组成虚线的线段的宽度。
 - android:dashGap：组成虚线的线段之间的间隔，越大则虚线空隙越大。

- **< padding >**：空白

- **< size >**：shape的大小。






### LayerDrawable

- 它对应XML标签是<layer-list>，他表示层次化的Drawable集合，也就是**叠加**效果。
- 在一个layer-list中可以包含多个item，每个item表示一个Drawable。
- 常用属性android:top、bottom、left、right表示Drawable相对View的上下左右的偏移量。
- Layes-list 有层次感的概念，下面的item会覆盖在上面的item，可以实现一些叠加效果。

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/res_haimei1"
        android:bottom="10dp"
        android:drawable="@drawable/drawable_img"
        android:left="10dp"
        android:right="10dp"
        android:top="10dp" />

    <item
        android:id="@+id/res_icon"
        android:width="30dp"
        android:height="30dp"
        android:drawable="@drawable/drawable_img"
        android:gravity="center" />
</layer-list>

```

### StateListDrawable

StateListDrawable对应于 <selector> 标签，它也表示 Drawable 集合，每个 Drawable 都对应着一种 View 的一种**状态**。(主要用于设置可单击的 View 的背景：比如 Button)

- **android:constantSize**：StateListDrawable的固有大小是否不随着其状态的改变而改变，默认false。
- **android:dither**：是否开启抖动效果，默认true。
- **android:variablePadding**：StateListDrawable的padding表示是否随着其状态的改变而改变。
- 每个item表示的都是一种状态下的Drawable信息：
 - android:state_pressed：按下，未松开；
 - android:state_focused：view获取焦点；
 - android:state_selected：选择了view；
 - android:state_checked：选中了view；
 - android:state_enabled：view处于可用状态；


```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android" 
    android:constantSize="false" 
    android:dither="true" 
    android:variablePadding="false">
    <item android:drawable="@drawable/drawable_img" android:state_pressed="true" />
    <item android:drawable="@drawable/drawable_img1" android:state_pressed="false" />
    <item android:drawable="@drawable/drawable_img3" />
</selector>
```

最后一条item是不附带任何状态的，当系统找不到匹配的view就会默认调用。




### LevelListDrawable


LevelListDrawable 对应于 <level-list> 标签，集合中的每个 Drawable 都会有一个**等级**的概念，根据等级不同来切换对于的 Drawable。当它作为 View 的背景时，可以通过 Drawable的setLevel 方法来设置不同的等级从而切换具体的 Drawable。level 的值从 0-10000。

```xml
<?xml version="1.0" encoding="utf-8"?>
<level-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/drawable_img" android:maxLevel="0" />
    <item android:drawable="@drawable/drawable_img1" android:maxLevel="1" />
</level-list>
```

### TransitionDrawable
TransitionDrawable对应于 <transition> 标签，实现两个Drawable 之间的**淡入淡出**效果。

```xml
<?xml version="1.0" encoding="utf-8"?>
<transition xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/drawable_img" />
    <item android:drawable="@drawable/drawable_img1" />
</transition>
```
然后在 startTransition 和 reverseTransition 方法中实现淡入淡出的效果。

```java
TransitionDrawable drawable = (TransitionDrawable) imageView.getBackground();
drawable.startTransition(1000);
```

### InsetDrawable

InsetDrawable 对应于 <Inset> 标签，它将其他 Drawable 嵌套到自己当中，并可以在四周设置间隔。当一个 View 希望自己的背景比自己的实际区域小的时候，可以采用 InsetDrawable 来实现。

android:insetTop、insetBottom、insetLeft、insetRight 表示**内凹**的大小。

```xml
<?xml version="1.0" encoding="utf-8"?>
<inset xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_i"
    android:insetBottom="10dp"
    android:insetLeft="10dp"
    android:insetRight="10dp"
    android:insetTop="10dp">
    <shape android:shape="rectangle">
        <solid android:color="#abcdef" />
    </shape>
</inset>
```



### ScaleDrawable

ScaleDrawable 对应于 <scale> 标签，它可以根据自己的等级将制定的 Drawable 缩放到一定的比例。xml 所定义的缩放比例越大，那么内部 Drawable 看起来越小。如果 level 设置的越大，那么Drawable看起来就越大。

```xml
<?xml version="1.0" encoding="utf-8"?>
<scale xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_i"
    android:scaleGravity="center"
    android:scaleHeight="70%"
    android:scaleWidth="70%">
</scale>
```

```java
ScaleDrawable scaleDrawable = (ScaleDrawable) imageView.getBackground();
caleDrawable.setLevel(1);
```


### ClipDrawable

ClipDrawable 对应于 <clip> 标签，它可以根据自己的等级来裁剪另一个Drawable，裁剪方向可以通过android:clipOrientation 和 android:gravity 这两个属性来共同控制。范围0~10000，等级0表示完全裁剪，8000表示裁剪了2000，即设置方向上裁剪了20% 。

```xml
<?xml version="1.0" encoding="utf-8"?>
<clip xmlns:android="http://schemas.android.com/apk/res/android"
    android:clipOrientation="vertical"
    android:drawable="@drawable/image1"
    android:gravity="bottom">
</clip>
```
从顶部开始剪：
```xml
<ImageView
android:id="@+id/test_clip"
android:layout_width="100dp"
android:height="100dp"
android:scr="@drawable/clip_drawable"
android:gravity="center" />
```
ClipDrawable的等级：

```java
ClipDrawable clipDrawable = (ClipDrawable) test_clip.getBackground();
clipDrawable.setLevel(5000);
```

## Drawable 自定义

- draw、setAlpha、setColorFilter和getOpacity这几个方法都是必须要实现的，其中draw是最重要的。
- 当自定义Drawable有固有大小的时候最好重写getIntrinsicWidth和getIntrinsicHeight这两个方法。
- 内部大小不等于Drawable的实际区域大小，Drawable实际区域的大小可以通过getBounds方法来得到，一般来说它和View的尺寸相同。
