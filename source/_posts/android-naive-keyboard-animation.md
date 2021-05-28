---
layout: post
title: 简单的Android软键盘动画
date: 2021/5/28
categories: Android
---

随着`androidx.core`1.5.0的发布，我终于搞明白全屏、WindowInsets这些问题了。

<!--more-->

1. 设置`windowSoftInputMode`

    给Activity增加 `android:windowSoftInputMode="adjustResize"`

    根据[官方文档](https://developer.android.com/training/keyboard-input/visibility#Respond)：

    > To ensure the best behavior for your app, you should specify how you'd like the system to display your UI in the remaining space.

    而且不同的系统版本，默认的行为不同，所以这里加一个确保行为一致。

    根据[官方文档](https://developer.android.com/training/keyboard-input/visibility#ShowOnStart)，还可以加一个`stateHidden`来避免在一些系统版本上一打开Activity，软键盘就弹出的问题。

1. 全屏/让内容显示在系统界面之后

    `WindowCompat.setDecorFitsSystemWindows(window, false)`

1. 透明状态栏和导航栏

    ```Kotlin
    window.run {
        statusBarColor = Color.TRANSPARENT
        navigationBarColor = Color.TRANSPARENT
    }
    ```

    更完美的颜色参照[Gesture Navigation: going edge-to-edge (I)](https://medium.com/androiddevelopers/gesture-navigation-going-edge-to-edge-812f62e4e83e)。

1. 避免View和系统界面重叠

    如果顶部是`AppBarLayout`，就增加一个`android:fitsSystemWindows="true"`来让它自己处理顶部。底部也可以用默认处理，但通常效果不理想。

    如果是普通View：

    ```Kotlin
    ViewCompat.setOnApplyWindowInsetsListener(binding.root) { v, insets ->
        val bottomInsets = insets.getInsets(WindowInsetsCompat.Type.systemBars())
        v.updatePadding(bottom = bottomInsets.bottom)
        val keyboardShown = insets.isVisible(WindowInsetsCompat.Type.ime())
        insets
    }
    ```

    - 这里只用`systemBars`来避免键盘弹出时界面跳动。
    - 这里也是检测键盘是否弹出的地方。
    - 在键盘弹出时，导航栏高度算在软键盘高度的一部分中的。

1. 增加动画

    ```Kotlin
    ViewCompat.setWindowInsetsAnimationCallback(
        binding.root,
        object : WindowInsetsAnimationCompat.Callback(DISPATCH_MODE_STOP) {
            override fun onProgress(
                insets: WindowInsetsCompat,
                runningAnimations: MutableList<WindowInsetsAnimationCompat>
            ): WindowInsetsCompat {
                val bottomInsets = insets.getInsets(
                    WindowInsetsCompat.Type.ime() or
                        WindowInsetsCompat.Type.systemBars()
                )
                binding.root.updatePadding(bottom = bottomInsets.bottom)
                return insets
            }
        }
    )
    ```

1. 其他考虑

    - 如果要显示Snackbar，需要额外测试。Padding可能不是最好的方法，可能需要Margin或额外的`Space` View。
    - 显示软键盘，似乎[Showing the Android Keyboard Reliably](https://developer.squareup.com/blog/showing-the-android-keyboard-reliably/)比`WindowInsetsController`处理的情况更多且更好用。关闭的话，`WindowInsetsController`就可以。
