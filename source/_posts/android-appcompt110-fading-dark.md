---
layout: post
title: Android AppCompat 1.1.0渐隐式切换夜间模式
date: 2019/9/7
categories: Android
---

在新出的1.1.0中，调用`AppCompatDelegate.setDefaultNightMode`会自动重启Activity，算是Breaking Change（还说是语义化版本？），但这个重启会让应用黑一下然后显示新的主题，让我很难受。今天就要给它加个渐隐动画。

<!--more-->

1. 根据[官方文档](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme#configuration_changes)添加拦截uiMode的变化

1. 然后手动重启Activity

    ```Kotlin
    override fun onConfigurationChanged(newConfig: Configuration) {
        super.onConfigurationChanged(newConfig)
        when (newConfig.uiMode and Configuration.UI_MODE_NIGHT_MASK) {
            Configuration.UI_MODE_NIGHT_NO -> {
                restartWithFading()
            }
            Configuration.UI_MODE_NIGHT_YES -> {
                restartWithFading()
            }
        }
    }
    ```

    [restartWithFading的代码在这里](https://aprildown.xyz/2019/06/15/android-code-snippets/#%E6%B8%90%E9%9A%90%E9%87%8D%E5%90%AFActivity)

没了，就这么简单。

之所以拉出来讲一讲，是因为之前的rc版本，这么做都是失效的。

另外，AppCompatActivity有这么一个可继承方法

```Java
/**
* Called when the night mode has changed. See {@link AppCompatDelegate#applyDayNight()} for
* more information.
*
* @param mode the night mode which has been applied
*/
protected void onNightModeChanged(@NightMode int mode) {
}
```

没啥用，是必须在已经处理过后才会调用。

另外的另外，发现了Google文档中，Dark Theme和Night Theme是混用的吗？
