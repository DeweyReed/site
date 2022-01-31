---
layout: post
title: Android Jetpack Paging3 从入门到入土
date: 2022/1/31
categories: Android
---

一言以蔽之：[Jack of all trades, master of none](https://en.wikipedia.org/wiki/Jack_of_all_trades,_master_of_none)。

本文记录了个人实现分页的一些弯路与经验。

<!--more-->

## 需求

1. 初始加载部分数据，然后在滑过一定距离后，自动加载更多。
1. Offline first。先显示缓存数据，本地缓存用光后再请求新数据。
1. 第一次请求和后续请求时根据是否加载与成功与否显示不同的布局。

人生中第一个分页方案是[FastAdapter的Endless scrolling](https://github.com/mikepenz/FastAdapter#8-infinite-endless-scrolling)。背后的原理是[Endless Scrolling with AdapterViews and RecyclerView](https://guides.codepath.com/android/endless-scrolling-with-adapterviews-and-recyclerview)。它们针对需求1，通过给RecyclerView增加OnScrollListener，进行了逐字逐句的实现。

由于业务不熟练，数据储存使用了MutableList，导致了很多多线程、缓存之类的问题。最后代码混乱，勉强能用。

之后在Jetpack Paging发布后，自然而然地决定迁移过去，吃Google软饭。

## 缝缝补补Paging2

Paging的官方Sample很复杂。可以看得出Google想要满足尽可能多的情况，但结果却是大伙儿都理解不了。

Sample中为了满足需求，需要[一个巨大的类](https://github.com/android/architecture-components-samples/blob/paging2/PagingWithNetworkSample/app/src/main/java/androidx/paging/PagingRequestHelper.java)，还要给BoundaryCheck[外挂LiveData来实现检测错误、实现刷新](https://github.com/android/architecture-components-samples/blob/paging2/PagingWithNetworkSample/app/src/main/java/com/android/example/paging/pagingwithnetwork/reddit/util/PagingRequestHelperExt.kt)。因此每一个PagedList的创建都很繁琐，为此开发者都得二次封装。加上几年前Unidirectional Data Flow的思想还未流行，从实现底层到UI层，自然处处都会见到PagedList的闪亮身影。

虽说代码库更乱了，但好在能用。FastAdapter的维护者也很勤奋，增加了对Paging2的支持。

## 竹篮打水Paging3

在Paging3公布时，看到了全程使用Kotlin Coroutines、一个Pager配置所有内容、支持分隔符、支持错误状态和重试……让人口水流了一地。

正式版发布后，我以为大版本不兼容，需要重构很多代码，所以在很长一段时间内都没正式使用过。

在某次偶然的机会，才发现Paging2到Paging3是二进制兼容的。细看之下，原来是Paging3用新API重新实现了Paging2！想到二者API差别之大，和确保行为相同所需的测试和努力，发自内心佩服维护者。

**可是**，

1. 初始加载部分数据，然后在滑过一定距离后，自动加载更多。

    即使是仅创建Pager，不交给Adapter，Paging3都会触发Refresh + Append或两次Append。如果我把分页设置为一个远大于屏幕可显示内容的数值，它依然会自动加载两次。

    自动加载一次可以理解为确保数据尽早到位，但为什么要加载第二次呢？为什么本地数据即使够多，也要加载更多呢？多次Debug后，发现Flow会初始化两次？

1. Offline first。先显示缓存数据，本地缓存用光后再请求新数据。

    由于需求1的原因，每次新建Pager后，它总会请求新的两页，即使本地数据还有很多未浏览。

1. 第一次请求和后续请求时根据是否加载与成功与否显示不同的布局。

    如果只用withLoadStateFooter，RecyclerView可能会自动滚动到第一页末尾。原因可能是为了保持没有数据时，第一项也就是Footer的位置，而在第一页数据加载完毕后，Footer可能还未清空。

    Paging3可能需要[类似如下的肮脏代码](https://developer.android.com/topic/libraries/architecture/paging/load-state#chain-operators)来解决问题：

    ```Kotlin
    lifecycleScope.launchWhenCreated {
        adapter.loadStateFlow
            // Only emit when REFRESH LoadState for RemoteMediator changes.
            .distinctUntilChangedBy { it.refresh }
            // Only react to cases where REFRESH completes, such as NotLoading.
            .filter { it.refresh is LoadState.NotLoading }
            // Scroll to top is synchronous with UI updates, even if remote load was
            // triggered.
            .collect { binding.list.scrollToPosition(0) }
    }
    ```

    最后是在Stackoverflow发现了可以增加一个Header来避免滚动。

    既然有了Header，那把初始刷新状态也显示了吧。

    那么这时RecyclerView的动画会在一瞬间同时显示Header和Footer，非常丑。为了解决这个，需要对ItemAnimator进行很复杂的Hack。

    于是决定把初始刷新状态单独成为View，和LoadStateAdapter协作。然而这么做依然无法彻底解决上述问题。

    同时，为了避免自动滚动，Header不能去掉。但如果把Header高度设置为0，会出现SwipeRefreshLayout失效的问题，因此最后只好把Header设置为空白的1px。

在花费很多天把玩Paging3，结果却依然不理想后，只能自己造轮子了。

[Reddit上也有很多开发者抱怨Paging3复杂难用](https://www.reddit.com/r/androiddev/search/?q=paging3)。

## 自制小饼干

为了自身情况量身定制的方法意外地简单。大体上还是参考了[Endless Scrolling with AdapterViews and RecyclerView](https://guides.codepath.com/android/endless-scrolling-with-adapterviews-and-recyclerview)，只不过使用ListAdapter中的getItem来确定已浏览的范围，进而决定何时请求更多。配合Room生成的Flow和自定义的加载状态，确保了Single source of truth。

## 总结

看起来像个多面手的Paging3到都来只是浪费了不少时间。虽然可以说一句简单的“凡事还得自己来”，但这件事，我还要再下一篇文章中继续反思。
