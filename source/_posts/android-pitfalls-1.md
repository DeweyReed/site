---
layout: post
title: Android开发踩坑和技巧（一）
date: 2018/8/7
categories: Android
tags: 不再维护
---

整理了一些开发过程中踩过的用于提醒自己的坑。

<!--more-->

## 1. 混淆前记得留下Models

比如Keep下Kotlin中，数据库用到的data class。这样可以方便Debug。尤其是保存数据时用到了Gson或其他工具，不keep的话，就会出现很多"a": "biubiubiu"之类的数据，可读性低，以后重构甚至是更新时还可能破坏掉名称一致性，或者直接崩溃。

## 2. 发布代码前最好Rebuild Project

最近就遇到了ButterKnife引起的崩溃，Rebuild之后才消除。

## 3. 谨慎使用StrictMode

我的使用经验是，它会破坏Instant Run、阻止在主线程中使用Shared Preferences、VmPolicy没有`penaltyDialog`、使用`penaltyLog`难以注意到等等。

比如，它会对[RintoneManager.getCursor()](https://developer.android.com/reference/android/media/RingtoneManager.html#getCursor())报错，提示Cursor未关闭。但文档中提到，我们并不需要亲自关闭它。

在测试过程中倒是可以打开检测各种问题，开发过程中就要谨慎使用了，因为很有可能浪费时间、得不偿失。

## 4. 重建当前Activity时的自然动画

主要用于切换主题时，模拟一个流畅的渐变动画。

{% asset_img day-night.gif %}

```Kotlin
// 方案一
startActivity(Intent(this, MainActivity::class.java)
finish()
overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out)

// 方案二
startActivity(Intent(this, MainActivity::class.java),
    ActivityOptions.makeCustomAnimation(this, android.R.anim.fade_in, android.R.anim.fade_out).toBundle())
finish()
```

注意：

- 代码顺序很重要。
- Activity的LaunchMode不能是Single系列的，因为旧Activity和新Activity在短暂的渐隐交替时，会同时有Activity的两个实例存在，single_task等会让新Activity建立不起来。

## 5. 自定义布局中，onSaveInstanceState和restore只有在当前View被赋予ID时才会被调用

要想让View在重建时保存状态，就得给它赋予一个ID。

[```Android didn't manage the onSaveInstanceState/onRestoreInstanceState if no id is set to the view.```](https://stackoverflow.com/a/28586444/5507158)

###### 这种特性（Bug）不遇到就难以避免呀。
