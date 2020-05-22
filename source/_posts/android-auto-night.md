---
layout: post
title: Android自动切换深色主题
date: 2018/10/4
categories: Android
---

**2020年5月22日新方案又来了，希望能结束这个问题**

**2020年4月21日还有Bug，正在修。反正也没人看，估计也没坑到什么人**

**2020年1月27日憋出了新方案，似乎修好了**

**2019年11月4日现在的实现有Bug，正在研究**

**2019年10月18日更新AppCompat 1.1.0的使用说明**

**2019年8月18日更新增强版**

网上搜怎么实现自动切换深色主题都没资料的，全是实现，只能自己想办法了。

最终解决的方案倒是很简单，不过走了**很多、很多、很多、很多**弯路。脑子不是很好使了。

<!--more-->

已有数据：上一次的启动时间、本次的启动时间（就是现在）、自动启动深色模式的几点几分开始和几点几分结束。

举个例子，设置八点自动进入深色主题，九点退出深色主题，我们需要满足：1. 上一次启动是在今天八点前。2. 本次启动在今天八点后。3.本次启动在今天九点前。4. 现在是浅色主题。退出深色主题同理。

此外，还要注意判断进入时间和退出时间的先后顺序。八点到九点是一个小时，九点到八点是二十三个小时。

代码：

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

    val today = LocalDate.now()
    val nightStart = LocalDateTime.of(today, LocalTime.of(nightfromHour, nightfromMinute))
    val nightEnd = LocalDateTime.of(today, LocalTime.of(nighttoHour, nighttoMinute))

    return if (nightStart.isBefore(nightEnd)) {
        // |today start       |night start ----- |night end       | today end
        val enterDark = lastLaunch.isBefore(nightStart) &&
            now.isAfter(nightStart) &&
            now.isBefore(nightEnd) &&
            !currentIsDark
        val exitDark = lastLaunch.isBefore(nightEnd) &&
            now.isAfter(nightEnd) &&
            currentIsDark
        enterDark || exitDark
    } else {
        // |today start ----- |night end       |night start ----- |today end
        val exitDark = lastLaunch.isBefore(nightEnd) &&
            now.isAfter(nightEnd) &&
            now.isBefore(nightStart) &&
            currentIsDark
        val enterDark = lastLaunch.isBefore(nightStart) &&
            now.isAfter(nightStart) &&
            !currentIsDark
        exitDark || enterDark
    }
}
```

然后在MainActivity里：

```Kotlin

val now = Instant.now().toEpochMilli()
val lastStartTime = prefs.getLong("last_start_time", now)
prefs.edit().putLong("last_start_time", now).apply()

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
