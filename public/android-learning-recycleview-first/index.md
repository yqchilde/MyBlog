# Android学习RecyclerView笔记一

<!--more-->

> Android学习RecyclerView笔记二 ： [https://yqqy.top/android-learning-recycleview-second](https://yqqy.top/android-learning-recycleview-second)

> Android学习RecyclerView笔记三 ： [https://yqqy.top/android-learning-recycleview-third](https://yqqy.top/android-learning-recycleview-third)

> 记录一下RecyclerView的一些写法和特点，毕竟是用了adapter，赶紧来梳理一下。RecyclerView是Android 5.0推出的，是support-v7包中的新组件。

### STEP

1. 记录的是RecyclerView的列表布局

2. 整体写法如下 LinearAdapter->LinearRecyclerViewAvtivity->RecyclerActivity

3. 整体思路如下 LinearAdapter<-LinearRecyclerViewAvtivity<-RecyclerActivity

![mark](https://pic.yqqy.top/blog/20200111/QFELwtBJSEBq.png?imageMogr2/format/webp/interlace/1)

- 首先我们先引入V7包，在build.gradle中写这么一行代码

`implementation 'com.android.support:design:28.0.0'`

- 然后我们重新创建一个空的 `activity` 并将其命名为 `RecyclerViewActivity.java` 然后我们给其布局一个xml 命名为 `activity_recycler_view.xml` 画一个按钮用来点击跳转到演示页面，界面如下：

![mark](https://pic.yqqy.top/blog/20200111/c3DeNp91sxdb.png?imageMogr2/format/webp/interlace/1 "activity_recycler_view.xml")

代码如下：

```Java
package com.helloworld.recyclerview;

import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;

import com.helloworld.R;

public class RecyclerViewActivity extends AppCompatActivity {

    private Button mBtnLinear;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_recycler_view);
        mBtnLinear = findViewById(R.id.btn_linear);
        mBtnLinear.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(RecyclerViewActivity.this,LinearRecyclerViewActivity.class);
                startActivity(intent);
            }
        });
    }
}
```

- 然后我们在LinearRecyclerViewAvtivity对应的创建其布局文件，命名为 `activity_linear_recycler_view.xml` 布局RecyclerView

![mark](https://pic.yqqy.top/blog/20200111/XhAQAOK9t3Oo.png?imageMogr2/format/webp/interlace/1 "activity_linear_recycler_view.xml")

- 回到LinearRecyclerViewAvtivity
-- 这里介绍一下RecyclerView的两个属性属性
--RecyclerView.setLayoutManager(LinearLayoutManger()) // 线性布局管理
--RecyclerView.addItemDecoration() //添加分割线

- 然后我们去设置adapter

先贴代码：

```Java
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

public class LinearRecyclerViewActivity extends AppCompatActivity {

    private RecyclerView mRvMain;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_linear_recycler_view);
        mRvMain = findViewById(R.id.rv_main);
        mRvMain.setLayoutManager(new LinearLayoutManager(LinearRecyclerViewActivity.this)); // 设置线性布局管理
        mRvMain.addItemDecoration(new MyDecoration()); // 设置分割线，调用MyDecoration这个类
        mRvMain.setAdapter(new LinearAdapter(LinearRecyclerViewActivity.this, new LinearAdapter.OnItemClickListener() {
            @Override // 从LinearAdapter中传入索引pos
            public void onClick(int pos) {
                Toast.makeText(LinearRecyclerViewActivity.this,"click..." + pos,Toast.LENGTH_SHORT).show();
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
            outRect.set(0,0,0,getResources().getDimensionPixelOffset(R.dimen.dividerHeight)); // 设置分割线在下面，线宽为1dp
        }
    }
}
```

- 接着就是 `LinearAdapter` ，这里注意创建class时，要让其继承在 `RecyclerView.Adapter` 下

- 点开继承的类查看方法，发现是一个泛型 继承于 ViewHolder

![mark](https://pic.yqqy.top/blog/20200111/iOyNdjqGSuY1.png?imageMogr2/format/webp/interlace/1 "ViewHolder")

- 我们自己写一个类继承 RecyclerView.ViewHolder

![mark](https://pic.yqqy.top/blog/20200111/DaKx6OBeGE7P.png?imageMogr2/format/webp/interlace/1 "自定义")

- 然后将泛型传进来

![mark](https://pic.yqqy.top/blog/20200111/anTPlxxGOusC.png?imageMogr2/format/webp/interlace/1 "泛型")

- 这里再记录一下 `LayoutInflater` 的使用
--LayoutInflater这个类的作用类似于findViewById()。不同点是LayoutInflater是用来找res/layout/下的xml布局文件，并且实例化；而findViewById()是找xml布局文件下的具体widget控件(如Button、TextView等)。

- onCreateViewHolder（）方法，负责承载每个子项的布局。它有两个参数，第一个按照我的理解来说是视图组件，第二个是索引;

- onBindViewHolder()方法，负责将每个子项holder绑定数据。俩参数分别是绑定的视图组件，索引；

- 为什么是按照我的理解呢，因为现在这俩参数全部更新了，也就是百度到的都不一样，之前的第二个参数是type，组件类型，现在是i,so,是按照我的理解

![mark](https://pic.yqqy.top/blog/20200111/lVaq553s27gP.png?imageMogr2/format/webp/interlace/1)

- 在LinearAdapter中返回点击的组件的索引，LinearRecyclerViewActivity接口，并用Toast返回提示信息

- 写一个接口实现回调方法，目的是为了传入我们点击的索引

![mark](https://pic.yqqy.top/blog/20200111/sVYernB0Sdgm.png?imageMogr2/format/webp/interlace/1 "1")
![mark](https://pic.yqqy.top/blog/20200111/904SALh7V2XE.png?imageMogr2/format/webp/interlace/1 "2")
![mark](https://pic.yqqy.top/blog/20200111/5D2rSHXQTup9.png?imageMogr2/format/webp/interlace/1 "3")

- 在 `getItemCount` 方法中先写死，创建30个

代码如下：
```Java
package com.helloworld.recyclerview;

import android.content.Context;
import android.support.annotation.NonNull;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;
import android.widget.Toast;

import com.helloworld.R;

public class LinearAdapter extends RecyclerView.Adapter<LinearAdapter.LinearViewHolder> {

    private Context mContext;
    private OnItemClickListener mListener;

    public LinearAdapter (Context context,OnItemClickListener listener) {
        this.mContext = context;
        this.mListener = listener;
    }

    @NonNull
    @Override
    public LinearAdapter.LinearViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {

        return new LinearViewHolder(LayoutInflater.from(mContext).inflate(R.layout.layout_linear_item,viewGroup,false));
    }

    @Override
    public void onBindViewHolder(@NonNull LinearAdapter.LinearViewHolder viewHolder, final int i) {
        viewHolder.textView.setText("Hello World！");
        viewHolder.textView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.onClick(i);
            }
        });
    }

    @Override
    public int getItemCount() {
        return 30;
    }

    class LinearViewHolder extends RecyclerView.ViewHolder {

        private TextView textView;

        public LinearViewHolder (View itemView) {
            super(itemView);
            textView = itemView.findViewById(R.id.tv_title);

        }
    }

    public interface OnItemClickListener {
        void onClick (int pos);
    }
}

```

看下RecyclerView实现的线性布局效果，下个笔记写水平滚动布局和网格布局

![mark](https://pic.yqqy.top/blog/20200111/t806wb1bRrPV.png?imageMogr2/format/webp/interlace/1 "效果图")
