---
layout: post
title:  "Android之PagerSlidingTabStrip实现导航标题"
date:  2018-5-21 16:30:38
categories: 第三方框架
tags: 第三方框架
---
* content
{:toc}


## 用法

### 1.加载

- [Github]：[Android PagerSlidingTabStrip](https://github.com/astuetz/PagerSlidingTabStrip)
- [build.gradle]： implementation 'c​​om.astuetz：pagerslidingtabstrip：1.0.1'

![](https://i.imgur.com/MBSg2ES.png)

(效果图片)

![](https://i.imgur.com/qHSOJbP.png)

(项目文件)


### 2.使用

```xml
<com.astuetz.PagerSlidingTabStrip
    android:id="@+id/tabs"
    android:layout_width="match_parent"
    android:layout_height="60dp"
    android:background="#aaa"
    android:textColor="@android:color/white"
    app:pstsIndicatorColor="@android:color/white"
    app:pstsIndicatorHeight="5dp" />
<android.support.v4.view.ViewPager
    android:id="@+id/viewPager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

```java
ViewPager viewPager = findViewById(R.id.viewPager);
PagerSlidingTabStrip tabs = findViewById(R.id.tabs);
viewPager.setAdapter(new PagerAdapter(getSupportFragmentManager()));
tabs.setShouldExpand(true);//平分导航栏
tabs.setViewPager(viewPager);
```

```java
//（可选）如果您使用OnPageChangeListener视图分页器，您应该将其设置在小部件中，而不是直接在分页器上。
tabs.setOnPageChangeListener(mPageChangeListener);
```

### 3.可自定义参数

- pstsIndicatorColor: 滑动indicator的颜色
- pstsIndicatorHeight: 滑动indicator的高度
- pstsUnderlineColor: 底部分割线的颜色
- pstsUnderlineHeight: 底部分割线的高度
- pstsDividerColor: 两个tab之间分割线的颜色
- pstsDividerPadding: 两个tab之间分割线的上下padding
- pstsTabPaddingLeftRight: 每个tab的左右padding
- pstsScrollOffset: Scroll offset of the selected tab
- pstsTabBackground: Background drawable of each tab, should be a StateListDrawable
- **pstsShouldExpand**: 设为true时，每个tab的weight相同，默认false
- pstsTextAllCaps: 设为true时，所有tab标题为大写，默认true

### 4.关联ViewPager

```java
public class PagerAdapter extends FragmentPagerAdapter {
    private final String[] title = {"模块1", "模块2", "模块3"};
    private List<Fragment> fragments = new ArrayList<Fragment>();
    public PagerAdapter(FragmentManager fm) {
        super(fm);
        Fragment1 fragment1 = new Fragment1();
        Fragment2 fragment2 = new Fragment2();
        Fragment3 fragment3 = new Fragment3();
        //添加fragment到集合中时注意顺序
        fragments.add(fragment1);
        fragments.add(fragment2);
        fragments.add(fragment3);
    }
    @Override
    public CharSequence getPageTitle(int position) {
        return title[position];
    }
    @Override
    public Fragment getItem(int position) {
        return fragments.get(position);
    }
    @Override
    public int getCount() {
        return title.length;
    }
```

### 5.源码解析

[PagerSlidingTabStrip 源码解析](http://a.codekk.com/detail/Android/ayyb1988/PagerSlidingTabStrip%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90)

