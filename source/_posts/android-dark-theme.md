---
layout: post
title: Android Dark Theme/深色模式使用体验
date: 2020/5/7
categories: Android
---

虽说一行代码`AppCompatDelegate.setDefaultNightMode`就能搞定的问题，但实际上坑不少哩。

<!--more-->

## 命名问题

之前Google自己在文档中和API中混用Night Theme/Dark Theme，后来才统一用Dark Theme，毕竟不一定非得晚上才可以用这个模式嘛。同样的，在Dark Theme的中文文档里，将其翻译为深色/浅色主题，个人觉得比之前大伙儿用的夜间模式、黑色模式、黑夜模式、暗夜模式、暗黑模式等好听不少。

## 自带的坑

AppCompat 1.1.0给我们带来了深色模式，也带来了不少问题。像是[因为从rc到正式版时只改了一行关于深色模式的代码，导致棒棒糖的WebView全线崩溃的问题](https://issuetracker.google.com/issues/141351441)。

`setDefaultNightMode`在1.1.0中会自动重启所有Activity，这导致不少应用的深色主题的逻辑被打乱不说，这个实现也很难自定义。当然，你别动它，自然什么毛病没有。

## 自挖的坑

`setDefaultNightMode`会调用[`ActivityCompat.recreate()`](https://developer.android.com/reference/kotlin/androidx/core/app/ActivityCompat#recreate(android.app.Activity))。

这个方法没有任何动画不说，而且在某些设备上会黑屏闪一下。在之前`setDefaultNightMode`不会自动重启的日子里，我的办法是：

```Kotlin
fun Activity.restartWithFading() {
    startActivity(intent)
    finish()
    overridePendingTransition(R.anim.fade_in_short, R.anim.fade_out_short)
}
```

但现在不行了。那么如何拦截`recreate()`呢？

我的第一次尝试是根据文档中[对`onConfigurationChanged()`进行拦截](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme#configuration_changes)，在拦截后手动重启Activity。

这么做用了没问题，但如果我从一个没有拦截`onConfigurationChanged()`的Activity中切换深色主题（比如通过省电模式），那这时会切换失效。

这是因为文档中有一句话：

> An app can handle the implementation of Dark theme itself by declaring that **each** Activity can handle the uiMode configuration change:

我们需要对每个Activity都进行拦截。但这时如果我用了第三方的Activity（像CustomTabs），这依然会失效。至此我只能另寻他路。

我的第二次尝试是重写`recreate()`：

```Kotlin
override fun recreate() {
    if (lifecycle.currentState.isAtLeast(Lifecycle.State.STARTED)) {
        // 不在前台时执行此处会导致Activity成为栈顶。
        restartWithFading()
    } else {
        super.recreate()
    }
}
```

使用后皆大欢喜，但当我在另一台机器上测试时，发现失效了。原因是：

```Java
public static void recreate(@NonNull Activity activity) {
    // On Android P and later we can safely rely on the platform recreate()
    if (Build.VERSION.SDK_INT >= 28) {
        activity.recreate();
    } else {
        if (!ActivityRecreator.recreate(activity)) {
            // If ActivityRecreator did not start a recreation, we'll just invoke the platform
            activity.recreate();
        }
    }
}
```

`ActivityCompat.recreate`在派之后才会调用`recreate`，而在之前只是**可能**调用，比如我在27的设备上没调用，但在21的模拟器上调用了。

至此我没办法了，只能委屈部分老用户闪一下了。╮(╯▽╰)╭

## 我都不知道我不知道的坑

我的一个应用里有一个指定时间段内自动打开/关闭深色主题的功能。具体如下，

一个Switch显示是否使用了深色主题：

```Kotlin
switchDark.isChecked = resources.isDarkTheme

val Resources.isDarkTheme: Boolean
    get() = configuration.uiMode and Configuration.UI_MODE_NIGHT_MASK == Configuration.UI_MODE_NIGHT_YES
```

切换Switch时切换深色模式：

```Kotlin
switchDark.setOnCheckedChangeListener { _, isChecked ->
    toggleDark(toDark = isChecked)
}

private fun toggleDark(toDark: Boolean) {
    AppCompatDelegate.setDefaultNightMode(
        if (toDark) {
            AppCompatDelegate.MODE_NIGHT_YES
        } else {
            AppCompatDelegate.MODE_NIGHT_NO
        }
    )
}
```

此外，在onCreate中根据需要自动切换深色模式：

```Kotlin
if (shouldToggleAutoDark) {
    toggleDark(toDark = !resources.isDarkTheme)
}
```

有啥问题，有啥问题呀？

如果使用之前在`recreate`中重启应用的方法，那么在派及之后的设备上没有任何问题。否则，在所有设备上 + 使用之前在`recreate`中重启应用的方法且在派之前的设备上，都会出现自动切换失效的问题。

比如现在是浅色主题，Switch为关，在启动时触发自动深色主题，`setDefaultNightMode`呼叫`recreate()`，应用重启，Switch为开，但这时Switch会经历一次`onSaveInstanceState`，将它的状态设置为之前的关，同时触发`OnCheckedChangeListener`，把应用设置为浅色模式，然后我们回到了原点。╮(╯▽╰)╭

最后我把Switch在切换前把所有可能调用`onSaveInstanceState`的地方（`toggleDark`和`recreate`中）删掉View后，修好了。

也可以在那些地方清空掉Listener，再把isChecked设为新的值。

完。
