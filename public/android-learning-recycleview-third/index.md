# Android学习RecyclerView笔记三

<!--more-->

> Android学习RecyclerView笔记一 ： [https://yqqy.top/android-learning-recycleview-first](https://yqqy.top/android-learning-recycleview-first)

> Android学习RecyclerView笔记二 ： [https://yqqy.top/android-learning-recycleview-second](https://yqqy.top/android-learning-recycleview-second)

### STEP

- 记载的是RecyclerView的瀑布流布局

- 瀑布流，在笔记二中与之不同的地方当时还是在 `setLayoutManager` 身上，那么瀑布流的布局是什么呢？

- 瀑布流布局： StaggeredGridLayoutManager()，这个方法中有两个参数，第一个是列数（要展示的为几列），第二个是方向（StaggeredGridLayoutManager.VERTICAL）为纵向

- 同样我们自定义一个函数 `MyDecoration` 来实现他的分割线，我们给上下左右都加一定的外边距，如下：

![mark](https://pic.yqqy.top/blog/20200111/qWcCNhMHeydf.png "MyDecoration")

- 复制一个笔记二中的 `GridAdapter` 将其命名为 `StaggeredGridAdapter` ，稍微改一下代码，如下：

![mark](https://pic.yqqy.top/blog/20200111/bG2uxYdwA0EU.png "StaggeredGridAdapter")

- 我们现在来看看瀑布流的效果吧：

![mark](https://pic.yqqy.top/blog/20200111/uEVkV6hinDtE.png "瀑布流效果")
