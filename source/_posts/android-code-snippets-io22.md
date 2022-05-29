---
layout: post
title: 一些可能有用的Android代码 - IO22版
date: 2022/5/29
categories: Android
---

记录了一些可以直接拿来使用的内容。出处全是IO视频截图。

<!--more-->

## 搜索按钮的触发事件

```Kotlin
btnSearch.setOnEditorActionListener { _, actionId, _ ->
    if (actionId == EditorInfo.IME_ACTION_SEARCH) {
        performSearch()
        true
    } else {
        false
    }
}
btnSearch.setOnKeyListener { _, keyCode, event ->
    if (keyCode == KeyEvent.KEYCODE_ENTER && event.action == KeyEvent.ACTION_UP) {
        performSearch()
        true
    } else {
        false
    }
}
```

## ViewPager中的Fragment获取ViewModel

```Kotlin
// Scope the fragment to the parent fragment
private val sharedViewModel: PagerViewModel
  by viewModels(ownerProducer = { requireParentFragment() } )
```

## 获取硬盘剩余空间

[Stackoverflow上](https://stackoverflow.com/questions/8133417/android-get-free-size-of-internal-external-memory)说：

```Kotlin
val sf = StatFs(file.absolutePath)
val availableBytes = sf.availableBlocksLong * sf.blockSizeLong
```

IO上说：

Use `File.getUsableSpace` to check available space on storage.

二者数据似乎相等。

## 保存文件

```Kotlin
val target = if (filesDir.usableSpace > fileSize) {
    filesDir
} else {
    getExternalFilesDir(null)
}
requireNotNull(target) { "Unable to create to file" }
bigInputStream.use { input ->
    File(target, "big-file.zip").outputStream().use { output ->
        input.copyTo(output)
    }
}
```

## 获取屏幕大小

使用[Jetpack WindowManager](https://developer.android.com/jetpack/androidx/releases/window)：

```Kotlin
val current = WindowMetricsCalculator.getOrCreate().computeCurrentWindowMetrics(activity)
val max = WindowMetricsCalculator.getOrCreate().computeMaximumWindowMetrics(activity)
```

但如果没有Activity呢？先用古法凑活一下：

```Kotlin
val wm = context.getSystemService<WindowManager>()
requireNotNull(wm)
val point = Point()
val defaultDisplay = wm.defaultDisplay
defaultDisplay.getRealSize(point)
if (point.x <= 0 || point.y <= 0) {
    val rect = Rect()
    defaultDisplay.getRectSize(rect)
    point.set(rect.right, rect.bottom)
}
```

代码部分来自于`WindowMetricsCalculator.getOrCreate().computeCurrentWindowMetrics(activity)`。

注意：

1. 所有方法都会根据屏幕旋转进行数值调换。
2. `Resources.getSystem().displayMetrics.heightPixels`并不包括状态栏和（或）状态栏。

## 相机旋转和设备旋转

[Support resizable surfaces in your camera app](https://developer.android.com/codelabs/android-camera2-preview)，或者用1.1.0及以上的CameraX。

## 实现列表特殊Footer效果

1. 内容不满一屏幕时，Footer显示在屏幕底端。
1. 屏幕超过一屏幕时，Footer和其他条目一样排排坐。

使用Jetpack Compose来实现：

```Kotlin
LazyColumn(
    modifier = Modifier.fillMaxHeight(),
    verticalArrangement = TopWithFooter,
) {
    items(list) {
        Item()
    }
    item {
        Footer()
    }
}
```

默认情况下，LazyColumn不满一屏幕会`WRAP_CONTENT`，用`fillMaxHeight()`来指定高度。

```Kotlin
object TopWithFooter : Arrangement.Vertical {
    override fun Density.arrange(totalSize: Int, sizes: IntArray, outPositions: IntArray) {
        var y = 0
        sizes.forEachIndexed { index, size ->
            outPositions[index] = y
            y += size
        }

        if (y < totalSize) {
            outPositions[outPositions.lastIndex] = totalSize - (sizes.lastOrNull() ?: 0)
        }
    }
}
```

`if`判断前内容就是默认的`verticalArrangement`（`Arrangement.Top`）的实现。

代码过于简单以至于不需要其他解释了。
