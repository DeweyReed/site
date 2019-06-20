---
layout: post
title: 一些可能有用的Android代码
date: 2019/6/15
categories: Android
---

没地儿放它们。

<!--more-->

## 渐隐重启Activity

```Kotlin
// 1
startActivity(Intent(this, MainActivity::class.java))
finish()
overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out)

// 2
startActivity(Intent(this, MainActivity::class.java),
    ActivityOptions.makeCustomAnimation(this, android.R.anim.fade_in, android.R.anim.fade_out).toBundle()
)
finish()
```

## [RingtoneManager](https://developer.android.com/reference/android/media/RingtoneManager.html)

> Note that the list of ringtones available will differ depending on whether the caller has the {@link android.Manifest.permission#READ_EXTERNAL_STORAGE} permission.

```Kotlin
val intent = Intent(RingtoneManager.ACTION_RINGTONE_PICKER)
        .putExtra(RingtoneManager.EXTRA_RINGTONE_TITLE, "Hello")
        .putExtra(RingtoneManager.EXTRA_RINGTONE_TYPE, RingtoneManager.TYPE_ALL)
        .putExtra(RingtoneManager.EXTRA_RINGTONE_SHOW_SILENT, true)
        .putExtra(RingtoneManager.EXTRA_RINGTONE_SHOW_DEFAULT, true)
        .putExtra(RingtoneManager.EXTRA_RINGTONE_DEFAULT_URI, Uri.EMPTY)
        .putExtra(RingtoneManager.EXTRA_RINGTONE_EXISTING_URI, Uri.EMPTY)
startActivityForResult(intent, 0)
```

`onActivityResult`,

```Kotlin
val uri = data?.getParcelableExtra<Uri>(RingtoneManager.EXTRA_RINGTONE_PICKED_URI)
val name = if (uri != null) {
    val rng = RingtoneManager.getRingtone(this, uri)
    if (rng != null) {
        rng.stop()
        rng.getTitle(this)
    } else {
        getString(R.string.unknown)
    }
} else {
    getString(R.string.unknown)
}
```

## 文件选择

[来自Codelab的一段代码](https://github.com/googlecodelabs/android-storage-permissions/blob/master/app/src/main/java/com/google/samples/dataprivacy/page/importimage/ImageImportActivity.java#L125).

需要`READ_EXTERNAL_STORAGE`权限。

## RecyclerView头部视差显示

使第一个View渐隐退出Window。

```Kotlin
list.addOnScrollListener(object :RecyclerView.OnScrollListener(){
    override fun onScrolled(recyclerView: RecyclerView, dx: Int, dy: Int) {
        super.onScrolled(recyclerView, dx, dy)
        val view = recyclerView.getChildAt(0)
        if (view != null && recyclerView.getChildAdapterPosition(view) == 0) {
            view.translationY = view.top / 3f
            view.alpha =  1 - -view.top.toFloat() / view.height
        }
    }
})
```

## ADB查看当前Activity

1. `adb shell "dumpsys window w | grep name="`
1. 栈顶：`adb shell dumpsys activity | grep "mFocusedActivity"`
1. 栈顶Fragment：`adb shell dumpsys activity your.package.name`

## ADB截屏并保存

`adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen.png`

或者

`adb exec-out screencap -p > screen.png`

[Grab Android screenshot to computer via ADB](https://blog.shvetsov.com/2013/02/grab-android-screenshot-to-computer-via.html)

## 让一个Activity有类似Android O或P彩蛋一样的背景。

设置theme为`android:theme="@android:style/Theme.Wallpaper.NoTitleBar.Fullscreen"`。

## 给Layout添加Ripple

1. 创建`ripple_press.xml`

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="@android:color/white">
        <item android:id="@android:id/mask">
            <shape android:shape="rectangle">
                <solid android:color="@android:color/white" />
            </shape>
        </item>
    </ripple>
    ```

1. 在Layout中使用

    ```XML
    <com.google.android.material.card.MaterialCardView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clickable="true"
        android:focusable="true">

        <!-- ... -->

        <View
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:foreground="@drawable/ripple_press" />

        <!-- ... -->

    </com.google.android.material.card.MaterialCardView>
    ```

    `clickable`和`focusable`很重要，`View`之前的View会有Ripple效果，之后的View则没有。

## AutoClearedValue

https://github.com/googlesamples/android-architecture-components/blob/master/GithubBrowserSample/app/src/main/java/com/android/example/github/util/AutoClearedValue.kt

## 带清除按钮的EditText

来自[Best Practices for Using Text in Android (Google I/O'19)](https://youtu.be/fpSfCvP36aA?t=1672)

```XML
<LinearLayout
    android:addStatesFromChildren="true"
    android:background="?editTextBackground">

    <EditText android:background="@null" />

    <ImageButton ... />

</LinearLayout>
```

## Custom View中的`onSaveInstanceState`

[Android didn't manage the onSaveInstanceState/onRestoreInstanceState if no id is set to the view.](https://stackoverflow.com/a/28586444/5507158)

## ?attr, ?android:attr, ?colorPrimary, ?attr/colorPrimary...

https://developer.android.com/guide/topics/resources/providing-resources#ResourcesFromXml => Referencing style attributes`

## File - Mark As Plain Text

重构时很有用，不会影响VS。

## `FragmentManager.setCustomAnimations()`'s Parameters

- `@AnimatorRes @AnimRes int enter`: 应用到动画中进入屏幕的Fragment
- `@AnimatorRes @AnimRes int exit`: 应用到动画中退出屏幕的Fragment
- `@AnimatorRes @AnimRes int popEnter`: 应用到`popBackStack`时动画中进入屏幕的Fragment
- `@AnimatorRes @AnimRes int popExit`: 应用到`popBackStack`时动画中退出屏幕的Fragment

## Kotlin `in` and `out`

```Kotlin
// 一个RecyclerView.ViewHolder子类的List
// 因为不知道到底是哪个子类，所以不能安全的添加或修改
// 但肯定能拿到一个RecyclerView.ViewHolder
// 只能拿，所以叫out，Java里叫extends
val outList: MutableList<out RecyclerView.ViewHolder> = mutableListOf()

// 一个RecyclerView.ViewHolder父类的List
// 因为不知道到底是哪个父类，所以只能强行拿到顶层的Any?
// 但是可以随便添加或修改RecyclerView.ViewHolder及其子类
// 只有添加或修改方便，所以叫in，Java里叫super
val inList: MutableList<in RecyclerView.ViewHolder> = mutableListOf()
```

## `clipToPadidng`

`Enables our items to still be drawn within the parent's padding.`
