---
layout: post
title: Android自动切换夜间主题
date: 2018/10/4
categories: Android
---

**2020年1月27日憋出了新方案，似乎修好了**

**2019年11月4日现在的实现有Bug，正在研究**

**2019年10月18日更新AppCompat 1.1.0的使用说明**

**2019年8月18日更新增强版**

网上搜怎么实现自动切换夜间主题都没资料的，全是夜间主题的实现，只能自己想办法了。

最终解决的方案倒是很简单，不过走了**很多、很多、很多**弯路。脑子不是很好使了。

<!--more-->

首先要判断某个时间点是否处于夜间模式的时间内:

```Kotlin
private fun LocalDateTime.isInTheNight(): Boolean {
    val theDay = this.toLocalDate()

    val nightRange = scheduleRange
    val nightStart =
        LocalDateTime.of(theDay, LocalTime.of(nightRange.fromHour, nightRange.fromMinute))
    val nightEnd =
        LocalDateTime.of(theDay, LocalTime.of(nightRange.toHour, nightRange.toMinute))

    return if (nightStart.isBefore(nightEnd)) {
        // |today start       |night start ----- |night end       | today end
        this.isAfter(nightStart) && this.isBefore(nightEnd)
    } else {
        // |today start ----- |night end       |night start ----- |today end
        this.isBefore(nightEnd) || this.isAfter(nightStart)
    }
}
```

用了threetenabp。`nightRnage`是储存的夜间模式的开启时间和结束时间。

接下来就在第一次进入夜间模式和第一次离开夜间模式时判断然后切换就可以了：

```Kotlin
/**
 * @return True if we should revert the current dark.
 */
fun calculateAutoDarkChange(
    currentIsDark: Boolean,
    nowMilli: Long,
    lastLaunchMilli: Long
): Boolean {
    val now = LocalDateTime.ofInstant(Instant.ofEpochMilli(nowMilli), ZoneId.systemDefault())
    val lastLaunch =
        LocalDateTime.ofInstant(Instant.ofEpochMilli(lastLaunchMilli), ZoneId.systemDefault())

    return (!lastLaunch.isInTheNight() && now.isInTheNight() && !currentIsDark) ||
            (lastLaunch.isInTheNight() && !now.isInTheNight() && currentIsDark)
}
```

然后在MainActivity里：

```Kotlin

val now = Instant.now().toEpochMilli()
val lastStartTime = prefs.getLong("last_start_time", now)
// Store now to prefs.

val currentIsDark = resources.isDarkTheme

if (darkTheme.calculateAutoDarkChange(
        currentIsDark = currentIsDark,
        nowMilli = now,
        lastLaunchMilli = lastStartTime
    )
) {
    updateDark(isDark = !currentIsDark)
}

val Resources.isDarkTheme: Boolean
    get() = configuration.uiMode and Configuration.UI_MODE_NIGHT_MASK == Configuration.UI_MODE_NIGHT_YES

```
