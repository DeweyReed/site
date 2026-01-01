---
layout: post
title: 一个人的Android开发可以走多远-26年1月版
date: 2026/1/1
categories: Android
---

一个人接着走啊走，

<!--more-->

## 2026年1月1日

与View相比，Jetpack Compose的`Text`关闭了`includeFontPadding`，所以英文的默认高度与`TextView`不同，打开就好了。

Jetpack Compose的`TextField`的高度则会根据中英文不同而变化，加上其他需求，[会很让人很抓狂](https://juejin.cn/post/7530088591395160106)。我发现`TextField`的大部分参数都不会变，所以直接用`AndroidView`包裹一个`AppCompatEditText`就可以解决问题喽。其中，用`DrawableCompat.setTint`给`textCursorDrawable`上色（旧系统的用户应该不会注意到吧）。
