---
layout: post
title: 一个人的Android开发可以走多远-26年1月版
date: 2026/1/1
categories: Android
---

一个人接着走啊走，

<!--more-->

## 2026年1月6日

我依旧不明白国内厂商提供的SDK如何可以这么差劲。这么多年下来，proguard是不会配置的，权限是乱申请的，接口设计都不是拍脑门的，简直是在梦游。

一定要把它们用依赖注入拆成单独的模块，这样至少不会污染开发坏境。

## 2026年1月5日

现在的[media-samples](https://github.com/android/media-samples)的玩具意味更大，看看乐子得了。

## 2026年1月4日

现阶段Metro作为依赖注入对多Module的KMP项目的支持还差点意思，所以暂时用Koin咯。只要架构选的好，切换依赖没烦恼。

## 2026年1月3日

[From Parcelable to @Serializable - Kuan-Ying Chou | droidcon London 2025](https://www.youtube.com/watch?v=SLs8drYogCc). `Serializable`是在`rememberSaveable`基础上可以跨平台的存活过进程死亡的实现。Android底层还是把对象转换为Bundle。

## 2026年1月2日

[Java versions in Android builds](https://developer.android.com/build/jdks)逻辑混乱，让人头大。怎样的配置是最佳实践，也是让人摸不着头脑。文档中的推荐也有着潜在的坑。保险起见，还是用传统的`sourceCompatibility` + `jvmTarget`吧。

## 2026年1月1日

与View相比，Jetpack Compose的`Text`关闭了`includeFontPadding`，所以英文的默认高度与`TextView`不同，打开就好了。

Jetpack Compose的`TextField`的高度则会根据中英文不同而变化，加上其他需求，[会很让人很抓狂](https://juejin.cn/post/7530088591395160106)。我发现`TextField`的大部分参数都不会变，所以直接用`AndroidView`包裹一个`AppCompatEditText`就可以解决问题喽。其中，用`DrawableCompat.setTint`给`textCursorDrawable`上色（旧系统的用户应该不会注意到吧）。
