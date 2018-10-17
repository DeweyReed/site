---
layout: post
title: Android自动切换夜间主题
date: 2018/10/4
categories: Android
---

妈耶，网上搜怎么实现自动切换夜间主题都没资料的，全是夜间主题的实现，只能自己想办法了。

最终解决的方案倒是很简单，不过走了很多弯路。脑子不是很好使了。

<!--more-->

线索就是，应用会在任何时候启动，它该怎么确定自己是在某个时间（进入夜间或到了白天）之后第一次被启动。为了知道这个状态，就要记住上一次启动的时间，跟现在的时间比较。同时当然要有夜间模式的起始时间和结束时间。这样用四个时间来实现自动切换夜间主题。

```Kotlin
// 现在的时间
val now = System.currentTimeMillis()
// 上次启动的时间
val lastStartTime = prefs.getLong(keyLastStartTime, now)
// 保存好这次的启动时间，用于下一次使用
editor.putLong(keyLastStartTime, now).apply()
// 只有在自动切换打开时才进入逻辑
if (isAutoNightEnabled()) {
    // 获取自动切换的起始时间和结束时间
    val span = getNightSpan()

    // 将四个毫秒值转换为Date，方便之后的比较
    val lastDate = Date(lastStartTime)
    val nowDate = Date()
    val startDate = span.startTimeDate()
    val endDate = span.endTimeDate()

    // 获取现在是否是夜间模式，省去重复切换
    val isNightEnabled = isNightThemeEnabled

    if (lastDate.before(startDate) && nowDate.after(startDate) && !isNightEnabled) {
        // 进入夜间
        // 当且仅当，上一次启动时间是在夜间开始之前，并且现在时间是在夜间开始时候，同时夜间模式没有被打开
        configureNightTheme(true)
        restartWithFading(intent(this, showAutoNightMsg = true))
    } else if (lastDate.before(endDate) && nowDate.after(endDate) && isNightEnabled) {
        // 进入夜间
        // 当且仅当，上一次启动时间是在白天开始之前，并且现在时间是在白天开始时候，同时夜间模式已被打开
        configureNightTheme(false)
        restartWithFading(intent(this, showAutoNightMsg = true))
    }
}
// 通过在Intent中放Boolean，显示一个撤销按钮
if (intent.getBooleanExtra(EXTRA_SHOW_AUTO_NIGHT_MSG, false)) {
    if (isNightThemeEnabled) {
        rootLayout.longSnackbar(R.string.auto_night_to_night_done, R.string.action_undo) {
            configureNightTheme(false)
            restartWithFading(intent(this))
        }
    } else {
        rootLayout.longSnackbar(R.string.auto_night_to_day_done, R.string.action_undo) {
            configureNightTheme(true)
            restartWithFading(intent(this))
        }
    }
}
```

获取自动切换夜间模式时的方法，这里一定要确保秒和毫秒为0。
```Kotlin
fun startTimeDate(): Date = Calendar.getInstance().apply {
    set(Calendar.HOUR_OF_DAY, startHour)
    set(Calendar.MINUTE, startMinute)
    set(Calendar.SECOND, 0)
    set(Calendar.MILLISECOND, 0)
}.time
```

还有什么更好的实现方法吗？