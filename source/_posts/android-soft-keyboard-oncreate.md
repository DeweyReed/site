---
layout: post
title: 在Activity启动时弹出软键盘并在其之上显示内容
date: 2023/1/11
categories: Android
---

想要在Activity一启动就显示如下状态，还要有一个不乱跳的动画，这比预想中要复杂不少。

|屏幕|
|:-:|
|状态栏<br>空白<br>内容<br>紧贴着<br>软键盘<br>导航栏|

<!--more-->

第一次尝试了`WindowCompat.setDecorFitsSystemWindows(window, false)` + `ViewCompat.setWindowInsetsAnimationCallback`，但问题在于：

1. 有的手机上，在动画结束后，软键盘的高度有时会少十几二十个dp。

    在onCreate中唤起动画时，一定几率出现。原因不明。

    如果是用户点击某个按钮后再唤起动画，是没问题的。

1. 软键盘从底部升起时，内容会先显示在底部，然后跟随动画升起。

    这样的效果还行，但很突兀。除非让内容跟随升起时，有一个从小变大动画。

无奈之下，不再使用Edge-to-edge。而使用Activity的`android:windowSoftInputMode="adjustResize"`。效果立竿见影，不会再出现高度缺少的问题了。

但第二个问题依然有。一直以为无能为力了，但直到前几天发现Todoist的一个快捷方式做到了。

在一顿反编译后，发现它们也用了`adjustResize`。虽然没能找到具体实现，但我有了新的想法。

`adjustResize`的实现是Activity自动给Root view增加Padding。那么如果我们记忆之前的Padding，并在之后的onCreate中重复使用，那么就不会跳跃了。

```Kotlin
// 这里用了JetpackCompose，所以override这个。
// 如果用XML，就override只有一个参数的setContentView。
override fun setContentView(view: View?, params: ViewGroup.LayoutParams?) {
    super.setContentView(view, params)
    if (view == null) return

    // 应用之前储存的底部Padding
    view.updatePadding(bottom = viewModel.bottomHeight)

    ViewCompat.setOnApplyWindowInsetsListener(view) { _, insets ->
        val bottom = insets.getInsets(
            WindowInsetsCompat.Type.ime() or WindowInsetsCompat.Type.navigationBars()
        ).bottom

        if (bottom > 0) {
            view.updatePadding(bottom = bottom)
            // 储存好底部Padding，供下次启动使用
            viewModel.setBottomPadding(bottom)
        }

        insets
    }
}
```

这个方案的优点是：

1. 内容只会在第一次时跳跃。
1. 可以跟随软键盘高度变化，比如切换语言时。
