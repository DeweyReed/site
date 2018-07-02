---
layout: post
title: Android开发踩过的坑
date: 2018/2/1
categories: Android
tags: 保证维护
---

[2018年7月2日 更新内容](https://github.com/DeweyReed/site/commits/master/source/_posts/android-pitfalls.md)

<!--more-->

## 重写`onSupportNavigateUp`而不是判断`android.R.id.home`

这才是想要截取点击左上角返回键的正确方式，我估计这还涵盖了从键盘返回时的操作。在其中调用`onBackPressed`或者其他操作。

没用Support中的组件就重写`onNavigateUp`。

## 单Fragment的Activity

不需要新建一个XML，专门放一个FrameLayout或者Static Fragment。
使用

```Kotlin
supportFragmentManager.beginTransaction()
        .add(android.R.id.content, EvilFragment())
        .commit()
```

在[这个SO](https://stackoverflow.com/a/5161143/5507158)的评论中找到。

## .*Compat

我发现大多数需要判断API Level的情况，都有对应的Support Compat类。

比如AlarmManager.set就有AlarmManagerCompat，类似的还有TextViewCompat、ContextCompat、DrawableCompat等等。名字一猜一个准。

## PackageName和ApplicationId

这里不讨论Java Package Name

他们俩可以不一致，在代码中不使用反射之类的奇技淫巧的话，并不会产生什么影响。

Build.gradle中的ApplicationId会在生成APK时，把Manifest中的package name替换成ApplicationId，但整个代码库依赖另外的一个Package Name也可以（测试过，更换包名不影响Sqlite的使用）。

## `.gitignore`文件的创建

IDEA的`.gitignore`插件在使用过程中并不怎么好用，很多东西并忽略不了。相比之下，[gitignore.io](https://www.gitignore.io)就好用得多。

[Android项目的`.gitignore`](https://www.gitignore.io/api/java%2Cmacos%2Clinux%2Ckotlin%2Candroid%2Cwindows%2Cintellij%2Candroidstudio)

## 创建快捷方式的正确姿势

**快捷方式的名字和应用名字一致时，在有的系统上会创建不出来**

在StackOverflow和其他搜索结果中，高票答案往往是用`Intent`的老方法；在[官方文档](https://developer.android.com/guide/topics/ui/shortcuts.html)中上来就讲的是牛扎糖以后的指南。也没人告诉我说，时代不同啦，正确方案在官方文档的中间

> Note: See also the support library APIs, isRequestPinShortcutSupported() and requestPinShortcut(), which work on Android 7.1 (API level 25) and lower. The support library falls back to the deprecated EXTRA_SHORTCUT_INTENT extra to attempt the pinning process.

新时代应该用`ShortcutManagerCompat`创建快捷方式。

## Can't resolve symbol ?attr/colorPrimary

好几次在更新Android Studio后，所有Support Design和其他中的资源都找不到了，用起来没问题，但会一直警告。
今天找到了[这个宝贝](https://stackoverflow.com/a/48734990/5507158)，方法就是删掉`.idea/libraries`文件夹，再Sync文件和Gradle。破费!。

## 混淆前记得留下Models

比如Kotlin中，数据库用到的data class。这样可以方便Debug。尤其是保存数据时用到了Gson或其他工具，不keep的话，就会出现很多"a": "biubiubiu"之类的数据，可读性低、以后重构时还可能破坏掉名称一致性。

## 发布代码前最好Rebuild Project

最近就遇到了ButterKnife引起的崩溃，Rebuild之后就自动没了。

## 谨慎使用StrictMode

我的使用经验是，它会破坏Instant Run，阻止在主线程中使用Shared Preferences等等。

比如，它会对[RintoneManager.getCursor()](https://developer.android.com/reference/android/media/RingtoneManager.html#getCursor())报错，提示Cursor未关闭。但文档中提到，我们不需要亲自关闭它。

## 重建当前Activity时的自然动画
代码顺序很重要；Activity的LaunchMode不能是Single系列的，不然新的Activity建立不起来。
```Kotlin
// The first way
startActivity(Intent(this, MainActivity::class.java)
finish()
overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out)

// The second way
startActivity(Intent(this, MainActivity::class.java), 
    ActivityOptions.makeCustomAnimation(this, android.R.anim.fade_in, android.R.anim.fade_out).toBundle())
finish()
```

## 自定义布局中，onSaveInstanceState和restore只有在当前View被赋予ID时才会被调用

[```Android didn't manage the onSaveInstanceState/onRestoreInstanceState if no id is set to the view.```](https://stackoverflow.com/a/28586444/5507158)
