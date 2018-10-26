---
layout: post
title: Android切换RecyclerView的Item布局(Expandable RecyclerView?)
date: 2018/10/26
categories: Android
---

让一个RecyclerView中的一个Item有两种不同的布局，可以用来给Item赋予多个状态，当然也可以实现类似Expandable RecyclerView的效果。

<!--more-->

诀窍在于`notifyItemChanged`的时候，如果`getItemViewType`返回了不同的值，`onCreateViewHolder`就会被再次调用，这时新建一个新的ViewHolder就可以啦。

用实现一个Expandable RecyclerView来举例子：

1. 首先要使用不同的ViewType：

    ```Java
    @Override
    public int getItemViewType() {
        return isExpanded() ? ExpandedAlarmViewHolder.VIEW_TYPE : CollapsedAlarmViewHolder.VIEW_TYPE;
    }
    ```

1. 要让第一种ViewHolder变成第二种，要在点击了第一种ViewHolder中的一个Button或其他，触发展开事件：

    ```Java
    public void expand() {
        if (!isExpanded()) {
            mExpanded = true;
            notifyItemChanged();
        }
    }
    ```

1. 在`onCreateViewHolder`中根据不同的ViewType创建不同的ViewHolder

    ```Java
    @Override
    public ItemViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        final ItemViewHolder.Factory factory = mFactoriesByViewType.get(viewType);
        if (factory != null) {
            return factory.createViewHolder(parent, viewType);
        }
        throw new IllegalArgumentException("Unsupported view type: " + viewType);
    }
    ```

    这里也可以不用factory，直接判断`viewType`。

1. TaDa，完成。其实这篇文章最重要的就是第二句话。

来源：AOSP的DeskClock