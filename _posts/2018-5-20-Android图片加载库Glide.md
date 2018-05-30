---
layout: post
title:  "Android图片加载库Glide"
date:  2018-5-20 22:55:26
categories: 第三方框架
tags: 第三方框架

---
* content
{:toc}

本文参考自郭霖的《开始新的系列，Glide的基本用法》：[https://mp.weixin.qq.com/s/ccr1wqRYvZkVpeUT_V6fXQ](https://mp.weixin.qq.com/s/ccr1wqRYvZkVpeUT_V6fXQ)。

## Glide 介绍


Glide是一款由Bump Technologies开发的图片加载框架，使得我们可以在Android平台上以极度简单的方式加载和展示图片。

## Glide 三步走

```java
Glide.with(this).load(url).into(imageView);
```

- **with()**：**调用Glide.with()方法用于创建一个加载图片的实例**。 with()方法可以接收Context、Activity或者Fragment类型的参数。也就是说我们选择的范围非常广，不管是在Activity还是Fragment中调用with()方法，都可以直接传this。那如果调用的地方既不在Activity中也不在Fragment中呢？也没关系，我们可以获取当前应用程序的ApplicationContext传入到with()方法当中。注意with()方法中传入的实例会决定Glide加载图片的生命周期，如果传入的是Activity或者Fragment的实例，那么当这个Activity或Fragment被销毁的时候，图片加载也会停止。如果传入的是ApplicationContext，那么只有当应用程序被杀掉的时候，图片加载才会停止。

- **load()**：**这个方法用于指定待加载的图片资源**。Glide 支持加载各种各样的图片资源，包括网络图片、本地图片、应用资源、二进制流、Uri对象等等。因此load()方法也有很多个方法重载，除了我们刚才使用的加载一个字符串网址之外，你还可以这样使用load()方法：

```java
// 加载本地图片File file = getImagePath();
Glide.with(this).load(file).into(imageView);
// 加载应用资源
int resource = R.drawable.image;
Glide.with(this).load(resource).into(imageView);
// 加载二进制流
byte[] image = getImageBytes();
Glide.with(this).load(image).into(imageView);
// 加载Uri对象
Uri imageUri = getImageUri();
Glide.with(this).load(imageUri).into(imageView);
```

- **into()**：希望让图片显示在哪个ImageView上，把这个ImageView的实例传进去就可以了。当然，into()方法不仅仅是只能接收ImageView类型的参数，还支持很多更丰富的用法，不过那个属于高级技巧，我们会在后面的文章当中学习。


## Glide 其他方法

### 占位图 placeholder

占位图就是指在图片的加载过程中，我们先显示一张临时的图片，等图片加载出来了再替换成要加载的图片。

下面我们就来学习一下 Glide 占位图功能的使用方法，首先我事先准备好了一张loading.jpg图片，用来作为占位图显示。在刚才的三步走之间插入了一个placeholder()方法，然后将占位图片的资源id传入到这个方法中即可。

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .into(imageView);
```

### 禁用内存 diskCacheStrategy

可以看到，这里串接了一个diskCacheStrategy()方法，并传入DiskCacheStrategy.NONE参数，这样就可以禁用掉Glide的缓存功能。

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

### 异常占位图 error

异常占位图就是指，如果因为某些异常情况导致图片加载失败，比如说手机网络信号不好，这个时候就显示这张异常占位图。

异常占位图的用法相信你已经可以猜到了，首先准备一张error.jpg图片，然后修改Glide加载部分的代码，如下所示：

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

## 指定图片格式

Glide是支持加载GIF图片的，使用Glide加载GIF图并不需要编写什么额外的代码，Glide内部会自动判断图片格式。

### 静态图片 asBitmap

```java
Glide.with(this)
     .load(url)
     .asBitmap()
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

### 动态图片 asGif

```java
Glide.with(this)
     .load(url)
     .asGif()
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

## 指定图片大小

使用Glide，我们就完全不用担心图片内存浪费，甚至是内存溢出的问题。因为Glide从来都不会直接将图片的完整尺寸全部加载到内存中，而是用多少加载多少。Glide会自动判断ImageView的大小，然后只将这么大的图片像素加载到内存当中，帮助我们节省内存开支。

### 指定固定大小 override

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .override(100, 100)
     .into(imageView);
```