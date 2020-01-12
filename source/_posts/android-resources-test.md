---
layout: post
title: Android Resources小研究
date: 2020/1/12
categories: Android
---

研究了一下如何获取各种资源，这里用颜色举例，其他类型没有尝试，但应该大同小异。

<!--more-->

我们在`styles.xml`设置`<item name="colorPrimary">@color/colorPrimary</item>`。

## `R.color.colorPrimary` -> Color Int

`ContextCompat.getColor(context, R.color.colorPrimary)` -> Color Int

## `R.attr.colorPrimary` -> Color Int

**这里的Context需要带Theme的Context，比如是一个`ContextThemeWrapper`或是Activity/View的Context，不然什么也拿不到。下个例子同理。**

```Kotlin
@ColorInt
fun Context.themeColor(@AttrRes attrRes: Int): Int {
    val a = obtainStyledAttributes(null, intArrayOf(attrRes))
    try {
        return a.getColor(0, Color.RED)
    } finally {
        a.recycle()
    }
}
```

然后`context.themeColor(R.attr.colorPrimary)` -> Color Int

## `R.attr.colorPrimary` -> `R.color.colorPrimary`(Color Res)

```Kotlin
@ColorRes
fun Context.themeRes(@AttrRes attrRes: Int): Int {
    val a = obtainStyledAttributes(null, intArrayOf(attrRes))
    try {
        return a.getResourceId(0, -1)
    } finally {
        a.recycle()
    }
}
```

然后`context.themeRes(R.attr.colorPrimary)` -> `R.color.colorPrimary`

一个更有用的例子是`context.themeRes(R.attr.colorControlActivated)` -> `R.color.colorAccent`(通常情况下是这个值)

## 拦截`View`

比如拦截`Button`的`android:textColor`和`app:backgroundTint`。

首先继承`AppCompatButton`或`MaterialButton`。

新建`attrs.xml`并塞入：

```XML
<declare-styleable name="Theme_Button">
    <attr name="android:textColor" />
    <attr name="backgroundTint" />
</declare-styleable>
```

然后就可以

```Kotlin
context.withStyledAttributes(set, attrs, defStyleAttr, defStyleRes) {
    getColorStateList(R.styleable.Theme_Button_android_textColor)
    getColorStateList(R.styleable.Theme_Button_backgroundTint)
}
```
