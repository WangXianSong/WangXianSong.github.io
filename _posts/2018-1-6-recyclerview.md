---
layout: post
title:  " RecyclerView 的基本用法复习"
date:  2018-1-6 22:30:57
categories: Android
tags: Android
---
* content
{:toc}




# RecyclerView 的基本用法

- 简单使用步骤
- 实现横向排序
- 设置分割线
- 实现自定义分割线样式
- 实现 GridView 布局
- 实现瀑布流布局
- 自定义点击事件


## 简单使用步骤

1. 配置 build.gradle 文件夹
2. 定义 RecyclerView 控件
3. 创建 Adapter 适配器
4. 设置条目的样式文件
5.  修改 MainActivity 代码

**第一步：配置build.gradle文件夹，需要导入包。**

（File -> project structure -> 选择对应项目 -> Dependencies -> 增加Library dependency -> 输入包名。记得sync Now一下。

>     compile 'com.android.support:appcompat-v7:25.3.1'
>     compile 'com.android.support.constraint:constraint-layout:1.0.2'
>     testCompile 'junit:junit:4.12'
>     compile 'com.android.support:recyclerview-v7:25.3.1'


**第二步：定义 RecyclerView 控件**

```xml
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerview_id"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```

**第三步：创建 Adapter 适配器**
继承 RecyclerView.Adapter，并泛型指定为 SimpleAdapter.MyViewHolder，其中 MyViewHolder 是我们在 SimpleAdapter 定义的一个内部类。重写三个方法：onCreateViewHolder、onBindViewHolder、getItemCount。



```java

import android.content.Context;
import android.support.v7.widget.RecyclerView;
import android.support.v7.widget.RecyclerView.ViewHolder;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import java.util.List;


public class SimpleAdapter extends RecyclerView.Adapter<SimpleAdapter.MyViewHolder> {

    private Context mContext;
    private List<String> mDatas;

    //构造函数，用于把要展示的数据源传进来
    public SimpleAdapter(Context mContext, List<String> mDatas) {
        this.mContext = mContext;
        this.mDatas = mDatas;
    }

    //A、创建 ViewHolder，通过使用View来绑定条目样式文件
    public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewTpe) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_simple_textview, parent, false);
        MyViewHolder myViewHolder = new MyViewHolder(view);//要传入一个view值
        return myViewHolder;
    }


    //B、绑定 ViewHolder
    public void onBindViewHolder(MyViewHolder holder, int position) {
        holder.tv.setText(mDatas.get(position));

    }

    //C、数据的长度
    public int getItemCount() {
        return mDatas.size();
    }


    //定义一个 MyViewHolder
    //view参数，是RecyclerView最外层的子项，可以通过findViewbyId来获取布局中的控件
    class MyViewHolder extends ViewHolder {
        TextView tv;

        public MyViewHolder(View view) {
            super(view);
            tv = (TextView) view.findViewById(R.id.id_tv);//初始化TextView
        }
    }

}

```

**第四步：设置条目的样式文件。**

在layout目录下新建文件 item_simple_textview，在这个布局中我们指定高度为 72dp，只定义了一个 TextView。

```xml

<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="3dp"
    android:background="#00ffff">

    <TextView
        android:id="@+id/id_tv"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:gravity="center" />

</FrameLayout>

```

**第四步：修改 MainActivity 代码**

```java

public class MainActivity extends AppCompatActivity {

    private RecyclerView mRecyclerView;
    private List<String> mDatas;
    private SimpleAdapter mAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initDatas();
        initViews();

        //设置适配器,设置布局方向，
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(layoutManager);
        mAdapter = new SimpleAdapter(this,mDatas);
        mRecyclerView.setAdapter(mAdapter);
    }

    public void initViews() {
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerview_id);
    }

    //初始化数据
    private void initDatas() {

        mDatas = new ArrayList<String>();

        for (int i = 'A'; i <= 'z'; i++) {
            mDatas.add(""+(char)i);
        }
    }
}

```


## 实现横向排序

>   layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);//水平排序

```java

        //设置适配器,设置布局方向，
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);//水平排序
        mRecyclerView.setLayoutManager(layoutManager);
        mAdapter = new SimpleAdapter(this,mDatas);
        mRecyclerView.setAdapter(mAdapter);

```

## 设置分割线

使用 mRecyclerView.addItemDecoration() 方法来加入分割线。谷歌目前没有提供默认的分割线，所以我们可以导入一个网络上用得比较多的 DividerItemDecoration 类。

下载地址：[https://github.com/gabrielemariotti/RecyclerViewItemAnimators](https://github.com/gabrielemariotti/RecyclerViewItemAnimators/blob/master/app/src/main/java/it/gmariotti/recyclerview/itemanimator/demo/adapter/DividerItemDecoration.java)


```java

        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(layoutManager);
        mAdapter = new SimpleAdapter(this, mDatas);
        mRecyclerView.setAdapter(mAdapter);
        mRecyclerView.addItemDecoration (new DividerItemDecoration(this, DividerItemDecoration.VERTICAL_LIST));//添加分割线
```


如果想快速实现的话可以自己定义一个类 MyItemDecoration.java，本文主要是讲如何快速使用，具体的实现内容就不在这里说了。

```java

class MyItemDecoration extends RecyclerView.ItemDecoration {

    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
        //设定底部边距为1px
        outRect.set(0, 0, 0, 1);
    }
}

```

同样在 MainActivity.class 中使用：

```java

     mRecyclerView.addItemDecoration(new MyItemDecoration());

```


## 实现自定义分割线样式

利用系统自带的 listDivider 的属性，在 value 目录下的 styles 文件中 添加 android:listDivider 代码：

```xml
<resources>

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <item name="android:listDivider">@drawable/diviter_02</item>
    </style>

</resources>

```
在 drawable 目录下新建 diviter_02.xml 文件

```xml

<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <size android:height="4dp" />

    <gradient
        android:centerColor="#00ff00"
        android:endColor="#0000ff"
        android:startColor="#ff0000"
        android:type="linear" />
</shape>

```

## 实现 GridView

为了更好的展示效果，在 res 目录下创建 Menu 文件，并创建 menu_main.xml 文件，加入Item。

```xml

<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/action_recyclerView"
        android:orderInCategory="100"
        android:title="RecyclerView"
        app:showAsAction="never" />

    <item
        android:id="@+id/action_gridView"
        android:orderInCategory="100"
        android:title="GridView"
        app:showAsAction="never" />
    <item
        android:id="@+id/action_hor_gridview"
        android:orderInCategory="100"
        android:title="HorizontalGridView"
        app:showAsAction="never" />
    <item
        android:id="@+id/action_staggered"
        android:orderInCategory="100"
        android:title="StaggeredGridView"
        app:showAsAction="never" />	

</menu>

```
 
并在 MainActivity 中实现 onCreateOptionsMenu 和 onOptionsItemSelected，通过 mRecyclerView.setLayoutManager 来实现 GridView。

```java

  @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.action_recyclerView:
           
                break;
            case R.id.action_gridView:
                mRecyclerView.setLayoutManager(new GridLayoutManager(this, 3));//实现3列的GridView
                break;
            case R.id.action_hor_gridview:
               
                break;
            case R.id.action_staggered:

                break;
            default:
        }
        return true;
    }
}

```

![](https://i.imgur.com/1pf3e0G.jpg)


## 实现瀑布流

为了实现接下来的瀑布流功能，将会在上面的菜单中跳转到另一个页面 StaggeredGridLayoutActivity 和 适配器 StaggeredAdapter。

```java
 public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
           · · ·
            case R.id.action_staggered:
                Intent intent = new Intent(this, StaggeredGridLayoutActivity.class);
                startActivity(intent);
                break;
            · · ·
            default:
        }
        return true;
    }

```
StaggeredGridLayoutActivity.java 是复制 MainActivity.class 进行修改的。

```java


public class StaggeredGridLayoutActivity extends AppCompatActivity {

    private RecyclerView mRecyclerView;
    private List<String> mDatas;
    private StaggeredAdapter mAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initDatas();
        initViews();

        mRecyclerView.setLayoutManager(new StaggeredGridLayoutManager(3, StaggeredGridLayoutManager.VERTICAL));
        mAdapter = new StaggeredAdapter(this, mDatas);
        mRecyclerView.setAdapter(mAdapter);


        //回调StaggeredAdapter的点击事件
        mAdapter.setmOnItemClickListener(new SimpleAdapter.OnItemClickListener() {
            @Override
            public void onItemClick(View view, int pos) {

            }

            @Override
            public void onItemLongClick(View view, int pos) {
                mAdapter.deletedata(pos);
            }
        });
    }

    public void initViews() {
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerview_id);
    }


    private void initDatas() {

        mDatas = new ArrayList<String>();

        for (int i = 'A'; i <= 'z'; i++) {
            mDatas.add("" + (char) i);
        }
    }


    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
        }
        return true;
    }
}

```

由于 StaggeredAdapter 继承了 SimpleAdapter，在代码上很有所减少。

```java

public class StaggeredAdapter extends SimpleAdapter {

    private List<Integer> mHeight;//随机高度

    //构造函数，用于把要展示的数据源传进来
    public StaggeredAdapter(Context mContext, List<String> mDatas) {
        super(mContext, mDatas);

        //定义随机高度
        mHeight = new ArrayList<Integer>();
        for (int i = 0; i < mDatas.size(); i++) {
            mHeight.add((int) (100 + Math.random() * 300));
        }
    }

    //B、绑定 ViewHolder
    public void onBindViewHolder(MyViewHolder holder, int position) {
        //动态获取高度
        LayoutParams lp = holder.itemView.getLayoutParams();
        lp.height = mHeight.get(position);
        holder.itemView.setLayoutParams(lp);
        holder.tv.setText(mDatas.get(position));

        setUpItemEvent(holder);//调用adapter的事件
    }
}

```

![](https://i.imgur.com/YsXjkRr.jpg)


**增加和删除**

在actionBar中添加多两个Item，去实现增加与删除的点击效果：

```xml

    <item
        android:id="@+id/action_add"
        android:orderInCategory="100"
        android:icon="@mipmap/ic_menu_add"
        android:title="add"
        app:showAsAction="ifRoom" />
    <item
        android:id="@+id/action_delete"
        android:orderInCategory="100"
        android:icon="@mipmap/ic_menu_delete"
        android:title="delete"
        app:showAsAction="ifRoom" />

```


**添加增加和删除的动画**

>        mRecyclerView.setItemAnimator(new DefaultItemAnimator());//系统默认动画效果

还可以自定义动画或者**使用插件**：

参考网站：[https://github.com/gabrielemariotti/RecyclerViewItemAnimators](https://github.com/gabrielemariotti/RecyclerViewItemAnimators)




## 自定义点击事件

1、在 Adaper 中定义接口并提供回调：

```java

 //定义点击事件接口

    public interface OnItemClickListener {
        void onItemClick(View view, int pos);

        void onItemLongClick(View view, int pos);
    }

    private OnItemClickListener mOnItemClickListener;

    public void setmOnItemClickListener(OnItemClickListener listener) {
        this.mOnItemClickListener = listener;
    }

```


2、在 Adaper 中的 onBindViewHolder 设置回调：


```java

public void onBindViewHolder(final MyViewHolder holder, final int position) {
        holder.tv.setText(mDatas.get(position));


        //点击事件
        if (mOnItemClickListener != null) {
            //单击事件
            holder.itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    mOnItemClickListener.onItemClick(holder.itemView, position);
                }
            });
            //长击事件
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View v) {
                    mOnItemClickListener.onItemLongClick(holder.itemView, position);
                    return false;
                }
            });
        }

    }

```
3、在 MainActivity 中通过 Adaper 去监听单击和长击事件：

```java

 public void onBindViewHolder(final MyViewHolder holder, final int position) {
        holder.tv.setText(mDatas.get(position));
        
        //点击事件
        if (mOnItemClickListener != null) {
            //单击事件
            holder.itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    int layoutPosition = holder.getLayoutPosition();//获取布局上的位置
                    mOnItemClickListener.onItemClick(holder.itemView, layoutPosition);
                }
            });
            //长击事件
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View v) {
                    int layoutPosition = holder.getLayoutPosition();//获取布局上的位置
                    mOnItemClickListener.onItemLongClick(holder.itemView, layoutPosition);
                    return false;
                }
            });
        }
    }
```


以上这种方法比较复杂，适合频繁点击的应用，接下来还有一种比较简单的方法，同样在 onBindViewHolder 去写代码：

```java
 public void onBindViewHolder(final MyViewHolder holder, final int position) {
        holder.tv.setText(mDatas.get(position));

        final View view = holder.itemView;
        view.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(v.getContext(), "you chicked" + position, Toast.LENGTH_SHORT).show();
            }
        });
    }

```


## 总结

总觉得《论语十则》里面几句话说得特对：

> 子曰：“温故而知新，可以为师矣。”
> 
> 子曰：“学而不思则罔，思而不学则殆。”

哪怕最基础的知识，不去反复的学习与研究，总有一天会还回去的。

接下来还有更多的任务要完成，只有挺起不畏惧的胸膛往前走，才能离美好更近一步。


## 部分代码  

MainActivity.class

```java


public class MainActivity extends AppCompatActivity {

    private RecyclerView mRecyclerView;
    private List<String> mDatas;
    private SimpleAdapter mAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initDatas();
        initViews();

        //设置适配器,设置布局方向，
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);

//        layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);//水平排序

        mRecyclerView.setLayoutManager(layoutManager);
        mAdapter = new SimpleAdapter(this, mDatas);

        mRecyclerView.setAdapter(mAdapter);

        mRecyclerView.setItemAnimator(new DefaultItemAnimator());//系统默认动画效果

//        mRecyclerView.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL_LIST));//添加分割线


        //回调点击事件
        mAdapter.setmOnItemClickListener(new SimpleAdapter.OnItemClickListener() {
            @Override
            public void onItemClick(View view, int pos) {
                Toast.makeText(MainActivity.this, "short" + pos, Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onItemLongClick(View view, int pos) {

                Toast.makeText(MainActivity.this, "longClick" + pos, Toast.LENGTH_SHORT).show();
            }
        });

    }

    public void initViews() {
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerview_id);
    }

    private void initDatas() {

        mDatas = new ArrayList<String>();

        for (int i = 'A'; i <= 'z'; i++) {
            mDatas.add("" + (char) i);
        }
    }


    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.action_recyclerView:
                mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
                break;
            case R.id.action_gridView:
                mRecyclerView.setLayoutManager(new GridLayoutManager(this, 3));
                break;
            case R.id.action_hor_gridview:
                mRecyclerView.setLayoutManager(new StaggeredGridLayoutManager(5, StaggeredGridLayoutManager.HORIZONTAL));
                break;
            case R.id.action_staggered:
                Intent intent = new Intent(this, StaggeredGridLayoutActivity.class);
                startActivity(intent);
                break;
            case R.id.action_add:
                mAdapter.adddata(1);//添加
                break;
            case R.id.action_delete:
                mAdapter.deletedata(1);//删除
                break;
            default:
        }
        return true;
    }
}
```

SimpleAdapter.class

```java



public class SimpleAdapter extends RecyclerView.Adapter<SimpleAdapter.MyViewHolder> {

    private Context mContext;
    protected List<String> mDatas;




    //定义点击事件接口
    public interface OnItemClickListener {
        void onItemClick(View view, int pos);

        void onItemLongClick(View view, int pos);
    }

    private OnItemClickListener mOnItemClickListener;

    public void setmOnItemClickListener(OnItemClickListener listener) {
        this.mOnItemClickListener = listener;
    }





    //构造函数，用于把要展示的数据源传进来
    public SimpleAdapter(Context mContext, List<String> mDatas) {
        this.mContext = mContext;
        this.mDatas = mDatas;
    }


    //A、创建 ViewHolder，通过使用View来绑定条目样式文件
    public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewTpe) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_simple_textview, parent, false);
        final MyViewHolder myViewHolder = new MyViewHolder(view);//要传入一个view值
//        //点击事件
//        myViewHolder.tv.setOnClickListener(new View.OnClickListener() {
//            @Override
//            public void onClick(View v) {
//                int pos = myViewHolder.getAdapterPosition();
//                Toast.makeText(v.getContext(), "you chicked" + pos, Toast.LENGTH_SHORT).show();
//            }
//        });
        return myViewHolder;
    }


    //B、绑定 ViewHolder
    public void onBindViewHolder(final MyViewHolder holder, final int position) {
        holder.tv.setText(mDatas.get(position));

        setUpItemEvent(holder);//调用点击事件方法

        //简易的点击事件
//        final View view = holder.itemView;
//        view.setOnClickListener(new View.OnClickListener() {
//            @Override
//            public void onClick(View v) {
//                Toast.makeText(v.getContext(), "you chicked" + position, Toast.LENGTH_SHORT).show();
//            }
//        });
    }



    //C、数据的长度
    public int getItemCount() {
        return mDatas.size();
    }




    //定义一个 MyViewHolder
    //view参数，是RecyclerView最外层的子项，可以通过findViewbyId来获取布局中的控件
    class MyViewHolder extends ViewHolder {
        TextView tv;

        public MyViewHolder(View view) {
            super(view);
            tv = (TextView) view.findViewById(R.id.id_tv);//初始化TextView
        }
    }




    //添加Item
    public void adddata(int pos) {
        mDatas.add(pos, "Insert one");
        notifyItemInserted(pos);
    }
    //删除Item
    public void deletedata(int pos) {
        mDatas.remove("Insert one");
        notifyItemRemoved(pos);
    }



    //只是在onBindViewHolder里面提出来的方法，为了更好的提供给子类
    protected void setUpItemEvent(final MyViewHolder holder) {
        //点击事件
        if (mOnItemClickListener != null) {
            //单击事件
            holder.itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    int layoutPosition = holder.getLayoutPosition();//获取布局上的位置
                    mOnItemClickListener.onItemClick(holder.itemView, layoutPosition);
                }
            });
            //长击事件
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View v) {
                    int layoutPosition = holder.getLayoutPosition();//获取布局上的位置
                    mOnItemClickListener.onItemLongClick(holder.itemView, layoutPosition);
                    return false;
                }
            });
        }
    }
}
```















