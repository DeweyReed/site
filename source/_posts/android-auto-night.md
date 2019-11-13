---
layout: post
title: Android自动切换夜间主题
date: 2018/10/4
categories: Android
---

**2019年11月4日现在的实现有Bug，正在研究**

**2019年10月18日更新AppCompat 1.1.0的使用说明**

**2019年8月18日更新增强版**

妈耶，网上搜怎么实现自动切换夜间主题都没资料的，全是夜间主题的实现，只能自己想办法了。

最终解决的方案倒是很简单，不过走了很多弯路。脑子不是很好使了。

<!--more-->

之前的一版有些问题，这里是修改后的。只在本地测试了一下，还没经过用户和时间的考验，但看起来没什么问题。

前提：需要用户设置的夜间起始时分和夜间结束时分。

时间线👇

-|---------------------------|----------------------|-------------------------|--

宇宙时间的起点           夜间起始时分            夜间结束时分              宇宙时间的终点

我们的任务是要在用户跨过中间两个时间点后，第一次启动应用时，切换夜间主题。

首先确保结束时分在起始时分之后👇代码很简单，不解释了。

```Kotlin
fun getStartEndRange(): Pair<Date, Date> {
    val start = Calendar.getInstance().apply {
        set(Calendar.HOUR_OF_DAY, startHour)
        set(Calendar.MINUTE, startMinute)
        // 一定要记得设置秒和毫秒为0
        set(Calendar.SECOND, 0)
        set(Calendar.MILLISECOND, 0)
    }

    val end = Calendar.getInstance().apply {
        set(Calendar.HOUR_OF_DAY, endHour)
        set(Calendar.MINUTE, endMinute)
        set(Calendar.SECOND, 0)
        set(Calendar.MILLISECOND, 0)
    }

    if (end.before(start)) {
        end.add(Calendar.DAY_OF_YEAR, 1)
    }

    return start.time to end.time
}
```

然后在启动时判断👇需要注意对lastDate和nowDate的严格判断，其他没得说。

```Kotlin
val now = System.currentTimeMillis()
val lastStartTime = getLastStartTime()
saveStartTime(now)
if (isAutoNightEnabled) {

    val lastDate = Date(lastStartTime)
    val nowDate = Date()
    val (startDate, endDate) = getStartEndRange() // 上文的方法

    when {
        lastDate.before(startDate)
                && nowDate.after(startDate)
                && nowDate.before(endDate)
                && !isNightEnabled -> {
            // last |  now  |
            //    start    end
            // Day to Night
        }
        lastDate.after(startDate)
                && lastDate.before(endDate)
                && nowDate.after(endDate)
                && isNightEnabled -> {
            //      |  last |  now
            //    start    end
            // Night to Day
        }
    }
}
```

上述代码一般在onCreate里执行，如果使用的是AppCompat 1.1.0及以后的夜间模式，就要rootView.post { // Day to night }，来确保AppCompatDelegate会在onStart后重启应用。
