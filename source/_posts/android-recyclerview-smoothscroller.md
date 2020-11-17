---
title: Android RecyclerView 将指定 Item 滑动到顶部
date: 2020-03-01 20:41:05
categories:
 - Android
tags: 
 - RecyclerView
---

只能在 `LayoutManager` 为 **LinearLayoutManager 及其子类**的情况下使用！！

<!-- more -->

在 `LinearLayoutManager` 中的 `smoothScrollToPosition` 是通过 `LinearSmoothScroller` 来实现滚动的，每次调用都会新建一个 LinearSmoothScroller 来执行操作：

```java
@Override
public void smoothScrollToPosition(RecyclerView recyclerView,
                                   RecyclerView.State state,
                                   int position) {
    LinearSmoothScroller linearSmoothScroller = new LinearSmoothScroller(recyclerView.getContext());
    linearSmoothScroller.setTargetPosition(position);
    startSmoothScroll(linearSmoothScroller);
}
```

那么查看 `LinearSmoothScroller` 的代码，里面有3个参数：

```java
/**
 * Align child view's left or top with parent view's left or top
 *
 * @see #calculateDtToFit(int, int, int, int, int)
 * @see #calculateDxToMakeVisible(android.view.View, int)
 * @see #calculateDyToMakeVisible(android.view.View, int)
 */
public static final int SNAP_TO_START = -1;

/**
 * Align child view's right or bottom with parent view's right or bottom
 *
 * @see #calculateDtToFit(int, int, int, int, int)
 * @see #calculateDxToMakeVisible(android.view.View, int)
 * @see #calculateDyToMakeVisible(android.view.View, int)
 */
public static final int SNAP_TO_END = 1;

/**
 * <p>Decides if the child should be snapped from start or end, depending
 * on where it currently is in relation to its parent.</p>
 * <p>For instance, if the view is virtually on the left of RecyclerView,
 * using {@code SNAP_TO_ANY} is the same as using {@code SNAP_TO_START}</p>
 *
 * @see #calculateDtToFit(int, int, int, int, int)
 * @see #calculateDxToMakeVisible(android.view.View, int)
 * @see #calculateDyToMakeVisible(android.view.View, int)
 */
 public static final int SNAP_TO_ANY = 0;
```
* **SNAP_TO_START** 表示将子视图对齐到 RecyclerView 的左边（水平滑动）或者顶部（垂直滑动）
* **SNAP_TO_END** 表示将子视图对齐到 RecyclerView 的右边（水平滑动）或者底部（垂直滑动）
* **SNAP_TO_ANY** 根据子视图的当前位置与父布局的关系，决定使用上面的哪种。


这3个常量在如下两个函数上使用：

```java
/**
 * When scrolling towards a child view, this method defines whether
 * we should align the leftor the right edge of the child with the 
 * parent RecyclerView.
 * @return SNAP_TO_START, SNAP_TO_END or SNAP_TO_ANY;
 * depending on the current target vector
 * @see #SNAP_TO_START
 * @see #SNAP_TO_END
 * @see #SNAP_TO_ANY
 */
protected int getHorizontalSnapPreference() {
    return mTargetVector == null || mTargetVector.x == 0 ? SNAP_TO_ANY :
            mTargetVector.x > 0 ? SNAP_TO_END : SNAP_TO_START;
}
/**
 * When scrolling towards a child view, this method defines whether
 * we should align the top or the bottom edge of the child with the
 * parent RecyclerView.
 * 
 * @return SNAP_TO_START, SNAP_TO_END or SNAP_TO_ANY;
 * depending on the current target vector
 * @see #SNAP_TO_START
 * @see #SNAP_TO_END
 * @see #SNAP_TO_ANY
 */
protected int getVerticalSnapPreference() {
    return mTargetVector == null || mTargetVector.y == 0 ? SNAP_TO_ANY :
            mTargetVector.y > 0 ? SNAP_TO_END : SNAP_TO_START;
}
```

当 RecyclerView 为水平滑动时会使用 `getHorizontalSnapPreference` ，为垂直滑动时会使用 `getVerticalSnapPreference` ，所以可以通过继承 `LinearSmoothScroller` 重写 `getHorizontalSnapPreference()` 或 `getVerticalSnapPreference` 来实现将指定 Item 滑动到顶部：

```java
public class ScrollToTopSmoothScroller extends LinearSmoothScroller {

    public ScrollToTopSmoothScroller(Context context) {
        super(context);
    }
    
    @Override
    protected int getHorizontalSnapPreference() {
        return SNAP_TO_START;
    }
    
    @Override
    protected int getVerticalSnapPreference() {
        return SNAP_TO_START;
    }
}
```

使用方式：

```java
ScrollToTopSmoothScroller smoothScroller = new ScrollToTopSmoothScroller(context);
smoothScroller.setTargetPosition(position);
layoutManager.startSmoothScroll(smoothScroller);
```

不可复用，每次滑动需要重新创建





