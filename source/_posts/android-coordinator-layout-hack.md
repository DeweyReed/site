---
layout: post
title: 巧用Android CoordinatorLayout实现复杂布局 + 滚动内容
date: 2019/10/2
categories: Android
---

一个使用情景是：页面上半部分是一个不算短的、复杂的Layout，后半部分是一个嵌套了RecyclerView的Fragment的横向ViewPager。使用效果是，上半部分会充当下半部分可滚动内容的Header。怎么做？

<!--more-->

## 常见的解决办法

如果下半部分只是RecyclerView而不是ViewPager的话，可以把上半部分当作RecyclerView的Header，使用多类型Adapter实现，除了每次找头部有点麻烦外，没问题。

但下面是ViewPager，这时如果用ScrollView/NestedScrollView，可能会发生一下情况：

1. 滚顿位置错误、初始位置不是最顶部

    从下往上滚动到ViewPager顶部时，滚动停止，需要再次下拉才能看到头部。记忆中SO上给出一种解决方案是，修改fousable，但这样会伤害到a11y，虽然不少人不在乎这个东西，但是还是有更优雅的解决方案的。

1. ViewPager双向滚动不流畅

    只要ViewPager嵌套RecyclerView都会多少出现这个问题。解决方案可以参考[Improving scrolling behavior of nested RecyclerViews - Part 1](https://rubensousa.com/2019/08/16/nested_recyclerview_part1/)

1. 使用ViewPager2来解决问题

    之前试了一下，似乎能解决不少问题，但遇到了横向滚动时位置错误/空白的问题，无奈只能放弃。另外，它的双向滚动不流畅问题比ViewPager还要严重。也许未来的Release会修好这些问题。

更严重的是，如果在SO上找答案，会根据不同Target API和不同AndroidX/Support版本，方案都不一样，有新有旧，有的有用有的没用。

## 要用CoordinatorLayout啊

前段时间找到了一个不错的解决办法，就是使用CoordinatorLayout，把上半部分塞到AppBarLayout中，下半部分放到正文中：

```XML
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@null"
        app:elevation="0dp">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_scrollFlags="scroll">

            <!-- Top Content -->

        </com.google.android.material.appbar.CollapsingToolbarLayout>

    </com.google.android.material.appbar.AppBarLayout>

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

CoordinatorLayouthe和AppBarLayout不一定非要用在根部嘛，无论哪里都可以用。

既然不放Toolbar，可以把AppBarLayout的颜色和elevation去掉：

```XML
<com.google.android.material.appbar.AppBarLayout
    android:background="@null"
    app:elevation="0dp">
```

让CollapsingToolbarLayout滚起来，可以让顶部内容成为Header：

```XML
<com.google.android.material.appbar.CollapsingToolbarLayout
        app:layout_scrollFlags="scroll">
```

这样，滚动位置、初始位置、a11y什么的都没问题啦。

## 奖励：带一个可滚动的TabLayout

```XML
<com.google.android.material.appbar.CollapsingToolbarLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:layout_scrollFlags="scroll">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</com.google.android.material.appbar.CollapsingToolbarLayout>
```

TabLayout是顶部内容最后一个，会跟随滚动，离开屏幕。

## 奖励励：带一个常驻的TabLayout

把TabLayout移到下半部分：

```XML
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    app:layout_behavior="@string/appbar_scrolling_view_behavior">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"" />

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

这样TabLayout一开始会跟随滚动，到达顶部后，就不动了，而下半部分的ViewPager会继续滚，相当于一个Sticky Header。如果一开始使用多类型Adapter + Header实现的话，要解决这个就需要点劳动了。

## 总结

浓眉大眼的CoordinatorLayout和它的一帮小弟有时候还有点用。希望material 1.1.0不会出现陨石坑吧。
