---
title: RecyclerView ItemDecoration 均分 Item 间隔
date: 2020-03-16 21:49:35
categories: 
 - Android
tags: 
 - RecyclerView
---

在 RecyclerView 中使用 ItemDecoration 重写 **getItemOffsets** 设置每个 Item 的间隔，支持各种 LayoutManager:

{% codeblock "SpacingItemDecoration.java" lang:java %}
public class SpacingItemDecoration extends RecyclerView.ItemDecoration {

    private int spanCount;
    private int spacing;
    private boolean includeEdge;

    public SpacingItemDecoration(int spacing, int spanCount, boolean includeEdge) {
        this.spacing = spacing;
        this.spanCount = spanCount;
        this.includeEdge = includeEdge;
    }

    @Override
    public void getItemOffsets(@NotNull Rect outRect, @NotNull View view, RecyclerView parent,
                               @NotNull RecyclerView.State state) {
        int position = parent.getChildAdapterPosition(view);
        int column = position % spanCount;

        if (includeEdge) {
            outRect.left = spacing - column * spacing / spanCount;
            outRect.right = (column + 1) * spacing / spanCount;

            if (position < spanCount) {
                outRect.top = spacing;
            }
            outRect.bottom = spacing;
        } else {
            outRect.left = column * spacing / spanCount;
            outRect.right = spacing - (column + 1) * spacing / spanCount;
            if (position >= spanCount) {
                outRect.top = spacing;
            }
        }
    }
}
{% endcodeblock %}
