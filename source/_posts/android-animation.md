---
layout: post
title: Android Animation
date: 2018/12/11
categories: Android
---

整理自[Get Animated (Android Dev Summit '18)](https://www.youtube.com/watch?v=N_x7SV3I3P0)，解释那复杂的Android动画框架和工具。

<!--more-->

1. `android.view.animation`：过时的动画

    过时了，不好用了。包括创建在`R.anim.`中的资源（考虑使用`R.animator.`）。

    当且仅当处理Window Animation时或Fragment Transition的自定义动画需确保View已经measured不得不使用这个，

    ```XML
    <item name="android:windowEnterAnimation">@anim/foo</item>
    ```

1. `android.animation`：从API 11开始的Animator

    来自`R.animator.`的资源。

    Animator => ValueAnimator => ObjectAnimator

    1. ValueAniamtor

        ```Kotlin
        ValueAnimator.ofFloat(1f, 0f).apply {
            addUpdateListener { ... }
        }.start()
        ```

    1. ObjectAniamtor

        ```Kotlin
        ObjectAnimator.ofFloat(view, View.ALPHA, 1f, 0f).start()
        ```

        PropertyValueHolder是ValueAnimator的内部实现

        ```Kotlin
        ObjectAnimator.ofPropertyValueHolder(
            view,
            PropertyValueHolder.ofFloat(View.ALPHA, 1f, 0f),
            PropertyValueHolder.ofFloat(View.SCALE_X, 1f, 0f)
        ).start()
        ```

    1. AnimatorSet：属于Aniamtor，管理多个动画

    1. ViewPropertyAnimator：使用了ValueAnimator，但效率更高

        ```Kotlin
        view.animate()....start()
        ```

1. Transitions：在布局间中切换

    用于Shared Element Transition、Window Content Transition、模块化动画。

    `TransitionManager.beginDelayedTransition(viewGroup)`

    调用后，修改View，自动启动动画。

1. AnimatedVectorDrawable: VectorDrawable + Animator

    用于Icon Animation、一次性动画、性能要求高的情况。

    ```Kotlin
    val avd = AppCompatResources.getDrawable(context, R.drawable.avd) as AnimatedVectorDrawable
    imageView.drawable = avd
    avd.start()
    ```

1. Phycisc-based Animation：强交互时提供流畅体验

1. MotionLayout：饼，真香。
