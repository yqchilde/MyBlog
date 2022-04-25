---
title: "Android学习RecyclerView笔记二"
subtitle: ""
date: 2019-05-23T03:32:11+00:00
lastmod: 2019-05-23T03:32:11+00:00
author: ""
authorLink: ""
description: "记载RecycleView的水平滚动布局和网格布局"

tags: ["Android"]
categories: ["Android"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""
---
<!--more-->

> Android学习RecyclerView笔记一 ： [https://yqqy.top/android-learning-recycleview-first](https://yqqy.top/android-learning-recycleview-first)

> Android学习RecyclerView笔记三 ： [https://yqqy.top/android-learning-recycleview-third](https://yqqy.top/android-learning-recycleview-third)

### STEP

- 这节笔记记录RecycleView的另外两个布局 水平滚动布局 & 网格布局

- 和笔记一一样，先布局两个按钮，进行跳转操作

![mark](https://pic.yqqy.top/blog/20200111/jetgHAj72S1U.png "演示图1")
![mark](https://pic.yqqy.top/blog/20200111/yFwbIlsCl6Qy.png "演示图2")

#### 先说滚动布局吧

- 创建一个新的新的activity，命名为 `HorRecyclerViewActivity`，刚刚的recycleviewActivity就是跳转到这里，同理待会的网格布局也是一样，命名为 `GridRecyclerViewActivity` 两者相同，不同的地方会特别写出来

![mark](https://pic.yqqy.top/blog/20200111/2mXraNXjbthH.png "GridRecyclerViewActivity")

- ctrl + 左键 点进去，进行布局，也就是在 `activity_hor_recycler_view.xml` 文件中布局，布局如下：

![mark](https://pic.yqqy.top/blog/20200111/FxIqVleNxx3D.png "activity_hor_recycler_view.xml")

- 引入RecycleView,是v7包中的，笔记一有记录

- 回到 `HorRecyclerViewActivity` 中，首先再学习RecyclerView的一个属性 `RecyclerView.serOrientation` 设置方向（横向 or 纵向）

- 设置分割线的方法和笔记一中一直，这里不做记录了，之后也是通过回调adapter中返回的索引，来实现点击弹出提示

HorRecyclerViewActivity代码如下：

```java
package com.helloworld.recyclerview;

import android.graphics.Rect;
import android.support.annotation.NonNull;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.view.View;
import android.widget.Toast;

import com.helloworld.R;

public class HorRecyclerViewActivity extends AppCompatActivity {

    private RecyclerView mRvHor;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_hor_recycler_view);
        mRvHor = findViewById(R.id.rv_hor);
        LinearLayoutManager linearLayoutManager = new LinearLayoutManager(HorRecyclerViewActivity.this);
        linearLayoutManager.setOrientation(LinearLayoutManager.HORIZONTAL); // 设置水平方向
        mRvHor.setLayoutManager(linearLayoutManager);
        mRvHor.addItemDecoration(new MyDecoration()); // 设置分割线
        mRvHor.setAdapter(new HorAdapter(HorRecyclerViewActivity.this, new HorAdapter.OnItemClickListener() {
            @Override
            public void onClick(int pos) {
                Toast.makeText(HorRecyclerViewActivity.this,"click:" + pos,Toast.LENGTH_SHORT).show();
            }
}));
        }

    /**
     * 自定义MyDecoration类，继承RecyclerView.ItemDecoration
     */
    class MyDecoration extends RecyclerView.ItemDecoration {
        @Override
        public void getItemOffsets(@NonNull Rect outRect, @NonNull View view, @NonNull RecyclerView parent, @NonNull RecyclerView.State state) {
            super.getItemOffsets(outRect, view, parent, state);
            outRect.set(0,0,getResources().getDimensionPixelOffset(R.dimen.dividerHeight),0); // 设置分割线在下面，线宽为1dp
        }
    }
}

```

- 然后我们看adapter,水平滚动的 复制笔记一中的LinearAdapter，将其命名为 `HorAdapter` 同理 待会的网格布局命名为 `GridAdapter` Adapter实现的目的看笔记一就行

- 我们只需要改其中的布局就行，创建一个对应的adapter布局，将其命名为 `layout_hor_item.xml`，布局如下：

![mark](https://pic.yqqy.top/blog/20200111/4sEHIQ2eJuV7.png "layout_hor_item.xml")

- 将其刚刚复制的HorAdapter中的布局修改为这个布局引用

![mark](https://pic.yqqy.top/blog/20200111/6JT2hc3syeg8.png "HorAdapter")

- HorAdapter代码：

```java
package com.helloworld.recyclerview;

import android.content.Context;
import android.support.annotation.NonNull;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import com.helloworld.R;

public class GridAdapter extends RecyclerView.Adapter<GridAdapter.LinearViewHolder> {

    private Context mContext;
    private OnItemClickListener mListener;

    public GridAdapter(Context context, OnItemClickListener listener) {
        this.mContext = context;
        this.mListener = listener;
    }

    @NonNull
    @Override
    public GridAdapter.LinearViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {

        return new LinearViewHolder(LayoutInflater.from(mContext).inflate(R.layout.layout_gridrv_item,viewGroup,false));
    }

    @Override
    public void onBindViewHolder(@NonNull GridAdapter.LinearViewHolder viewHolder, final int i) {
        viewHolder.textView.setText("Hello");
        viewHolder.textView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.onClick(i);
            }
        });
    }

    @Override
    public int getItemCount() {
        return 80;
    }

    class LinearViewHolder extends RecyclerView.ViewHolder {

        private TextView textView;

        public LinearViewHolder (View itemView) {
            super(itemView);
            textView = itemView.findViewById(R.id.tv_title);

        }
    }

    public interface OnItemClickListener {
        void onClick(int pos);
    }
}

```

- 然后看下水平滚动的布局效果：

![mark](https://pic.yqqy.top/blog/20200111/1Ldh7myKuIhq.png "水平滚动布局效果")

#### 接着说下网格布局

- 只需要变更一下这里，其他都按水平滚动布局来写就行，注意文件截图位置，下面截图有

![mark](https://pic.yqqy.top/blog/20200111/3r6qKNMH4WSL.png "变更文件")

- 看下网格布局的效果：

![mark](https://pic.yqqy.top/blog/20200111/8lUlFKK1FiyY.png "网格布局效果")

- 笔记三记录一下很多的瀑布流布局，自己感觉笔记二写的比笔记三写的顺畅多了，看来记笔记加深理解真管用。

![mark](https://pic.yqqy.top/blog/20200111/2jxpneu8IOzJ.png)