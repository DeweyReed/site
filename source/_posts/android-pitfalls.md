---
layout: post
title: Android开发踩过的坑
date: 2018/2/1
updated: 2018/3/25
categories: Android
---

[更新内容](https://github.com/DeweyReed/site/commits/master/source/_posts/android-pitfalls.md)

### 谨慎使用StrictMode
我的使用经验是，它会破坏Instant Run，阻止在主线程中使用Shared Preferences等等。

比如，它会对[RintoneManager.getCursor()](https://developer.android.com/reference/android/media/RingtoneManager.html#getCursor())报错，提示Cursor未关闭。但文档中提到，我们不需要亲自关闭它。

<!--more-->

### 重建当前Activity时的自然动画
代码顺序很重要
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

### 自定义布局中，onSaveInstanceState和restore只有在当前View被赋予ID时才会被调用

[```Android didn't manage the onSaveInstanceState/onRestoreInstanceState if no id is set to the view.```](https://stackoverflow.com/a/28586444/5507158)
