---
layout: post
title: Android System UI
date: 2019/5/6
categories: Android
---

整理自[Becoming a master window fitter 🔧)](https://chris.banes.dev/talks/2017/becoming-a-master-window-fitter-lon/)，解释那复杂的Android状态栏与导航栏和应用界面的关系。

只是一些笔记。

<!--more-->

本文用“😲”代表“状态栏与导航栏”这七个字。

## [setSystemUiVisibility](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))

SYSTEM_UI_FLAG_

- VISIBLE：让😲回来，重新显示
- LOW_PROFILE：让😲暗淡
- HIDE_NAVIGATION
- FULLSCREEN

SYSTEM_UI_FLAG_

- IMMERSIVE：让😲消失，但一有操作就会回来。
- IMMERSIVE_STICKY：和上面一个一起用，但有操作时，让😲悬浮出现，稍等后再消失。

SYSTEM_UI_FLAG_

- LIGHT_STATUS_BAR
- LIGHT_NAVIGATION_BAR

让😲浅底深色。默认是深底浅色。

SYSTEM_UI_FLAG_

- LAYOUT_STABLE
- LAYOUT_HIDE_NAVIGATION
- LAYOUT_FULLSCREEN

跟应用内容的显示有关系。

让应用内容在😲后面显示：LAYOUT_STABLE or LAYOUT_FULLSCREEN or LAYOUT_HIDE_NAVIGATION，后文用WTFs代替。

```Kotlin
view.systemUiVisibility =
    // Tells the system that you wish to be laid out
    // as if the navigation bar was hidden
    View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
    // Optional, if we want you be laid out fullscreen,
    // behind the status bar
    View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
    // Tells the system that you wish to be laid out at
    // the most extreme scenario of any other flags
    View.SYSTEM_UI_FLAG_LAYOUT_STABLE
```

From `Dark Theme & Gesture Navigation (Google I/O'19)`

## `android:windowTranslucentStatus`和`android:windowTranslucentNavigation`

从KitKat引入。让😲把透明、渐隐。应用内容在它们后面显示。它们会自动应用WTFs。渐隐不能改变颜色。

## `android:windowDrawsSystemBarBackgrounds`

从Lollipop引入。默认已开启。

把😲放到了应用内容的Window中，好让我们可以`activity.window.setStatusColor`。

和`android:windowTranslucent`有冲突。后者会覆盖前者设置的😲的颜色，且会关闭`android:windowDrawsSystemBarBackgrounds`，这会导致😲不再是应用内容的一部分，从而可能导致涉及到😲SharedElement动画失效。

因此为了得到半透明的😲颜色，可以用一个半透明的`android:statusBarColor`或`android:navigationBarColor`，而避免使用`android:windowTranslucent`。

## `android:fitSystemWindows="true"`

让这个View自己使用Padding填充周围，让它的内容不和会😲重叠。几乎只有MaterialCompoents（CoordinatorLayout、DrawerLayout、CollapsingToolbarLayout等）才对这个属性有反应。

在和WTFs共同使用后，应用内容就可以既在😲后显示，也不和😲重叠。

`DrawerLayout`设置`android:fitSystemWindows="true"`后，会自动设置WTFs。

可以给子View设置，选择是否让子View出现在😲后。

## `WindowInsets`

正确的使用😲大小的方式。不许使用resources或hard-coded尺寸，得用这个。

```Kotlin
view.setOnApplyWindowInsetsListener { _, insets ->
    return insets.comsumeSystemWindowInsets
}
```

## Views

`CoordinatorLayout`

- `app:statusBarBackground`

`CollapsingToolbarLayout`

- statusBar: `app:statusBarScrim`。默认colorPrimaryDrak。
- contentScrim: `app:contentScrim`。默认@null。

## 参考与资源

- [Becoming a master window fitter 🔧)](https://chris.banes.dev/talks/2017/becoming-a-master-window-fitter-lon/)
- [WindowInsets — Listeners to layouts](https://medium.com/androiddevelopers/windowinsets-listeners-to-layouts-8f9ccc8fa4d1)
- [SystemUiHelper.java](https://gist.github.com/chrisbanes/73de18faffca571f7292)
- [SystemUiHelper](https://github.com/DreaminginCodeZH/SystemUiHelper)
- [Materialize](https://github.com/mikepenz/Materialize)
