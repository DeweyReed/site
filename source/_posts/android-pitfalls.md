---
layout: post
title: Android技巧与窍门
date: 2018/2/1
categories: Android
---

[2019年4月2日 更新内容](https://github.com/DeweyReed/site/commits/master/source/_posts/android-pitfalls.md)

这里存放了一些技巧和窍门，代码相关的放到了[这里](https://github.com/DeweyReed/AndroidCodeSnippets)。

<!--more-->

## 临时禁用一些类和文件

在IDEA对着类文件右键-Mark As Plain Text。这样在重构时就不需要删除它们，影响VS了。

## `region`和`endregion`

```Kotlin
// region code
...
// endregion code
```

IDE就会出现折叠两者间的提示。折叠后就会只显示一个`code`。

## Kotlin `in` and `out`

```Kotlin
// RecyclerView.ViewHolder的某个子类的list
// 因为不知道是哪个子类的list，所以无法安全地set或add(Nothing)
// 但肯定能get到一个RecyclerView.ViewHolder
val outList: MutableList<out RecyclerView.ViewHolder> = mutableListOf()

// RecyclerView.ViewHolder的某个父类的list
// 因为不知道是哪个父类的list，所以只能get到最顶层的Any?
// 但可以set或add任意RecyclerView.ViewHolder及其子类
val inList: MutableList<in RecyclerView.ViewHolder> = mutableListOf()
```

## XML中的<TextView> vs <AppCompatTextView>

前者是默认的使用的方法，后者是运行时实际替换后的类。前者写起来快，但后者有代码补全。

或者可以取个折中，编辑时AppCompatTextView，编辑完换成TextView。

## `ResourcesCompat.getFont`

它并不检查API并会使用灰名单反射，所以要自己检查版本，在28及之后使用`Resources.getFont`。

## 在`Fragment`使用Context、Activity等

使用自带的requireContext等，而不是手动判断是否为`null`。

## ?attr, ?android:attr, ?colorPrimary, ?attr/colorPrimary......

[官方文档](https://developer.android.com/guide/topics/resources/providing-resources#ResourcesFromXml)下的`Referencing style attributes`小节。

## Firebase的Testlab和Google Play的Pre-launch测试

这更算一个技巧。

新建一个项目，建立对应的Firebase项目，但并不在代码中引用Firebase。这样就可以薅到每天免费的5台真机 + 10台模拟器的测试机会。

其中有一种Robo测试，类似于Monkey Test，可以用来确保在不同设备上的兼容性。

另外在Google Play发布时，先发布到Beta，再发布到Release，可以自动得到免费pre-launch的10次测试。

## `<merge>`还可用于自定义ViewGroup

`<merge>`不仅可以用于`<include>`Tag中减小View Hierarchy。还可以在使用自定义ViewGroup时，把需要的Layout XML的顶级Tag设置为`<merge>`, 然后添加`tools:parentTag="FrameLayout..."`，这样就也可以减小一层View Hierarchy。

## 谨慎挑选使用RecyclerView的Library

其实所有的Library在使用前都要小心挑选。

因为RecyclerView的可扩展性很强，很多Library会试图将它们所有功能囊括起来，这样看起来方便了开发者，但实际上，却南辕北辙了。

RecyclerView的初衷是为了避免将什么`setItemClickListener`，Footer，EmptyView等都包括在一个类中，而把他们剥离了开来。表面上看起来开发者在RecyclerView中需要的代码更多了，但实际上，在除去各种多余的组件后并不是。

最近尝试了很多RecyclerView Library，其中很多只是将RecyclerView又包装成一个ListView用。开发者爽到啊，但代码量和可扩展性却一塌糊涂。

其实根据自己的需要撸一个小RecyclerView框架，用起来也绰绰有余。复杂情况下就用Epoxy，FastAdapter之类维护有保障的Library。

PS. 好像几乎所有现有的RecyclerView Library对Kotlin支持都不给力啊。

## FragmentManager.setCustomAnimations()的四个参数

注意它们不同情况下是作用于不同Fragment的。

`@AnimatorRes @AnimRes int enter`: 作用于此Transition发生时进入屏幕的Fragment
`@AnimatorRes @AnimRes int exit`: 作用于此Transition发生时离开屏幕的Fragment
`@AnimatorRes @AnimRes int popEnter`: 作用于稍后调用popBackStack时，此Transition涉及的Fragment的进入屏幕动画
`@AnimatorRes @AnimRes int popExit`: 作用于稍后调用popBackStack时，此Transition涉及的Fragment的离开屏幕动画

## `BottomSheetDialog.setContentView(layoutResId)`Style丢失问题

在我的使用中，控件会找不到AccentColor。

解决方案是使用`setContentView(LayoutInflater.from(context).inflate(layoutResId, rootView, false))`

必须用`LayoutInflater.from`，使用Dialog自带的`getInflater()`也会丢Style。

## 重写`onSupportNavigateUp`而不是判断`android.R.id.home`

这才是想要截取点击左上角返回键的正确方式，我估计这还涵盖了从键盘返回时的操作。在其中调用`onBackPressed`或者其他操作。

没用Support中的组件就重写`onNavigateUp`/`onSupportNavigateUp`。

## .*Compat

我发现大多数需要判断API Level的情况，都有对应的Support Compat类。

比如AlarmManager.set就有AlarmManagerCompat，类似的还有TextViewCompat、ContextCompat、DrawableCompat等等。名字一猜一个准。

## PackageName和ApplicationId

这里不讨论Java Package Name

他们俩可以不一致，在代码中不使用反射之类的奇技淫巧的话，并不会产生什么影响。

Build.gradle中的ApplicationId会在生成APK时，把Manifest中的package name替换成ApplicationId，但整个代码库依赖另外的一个Package Name也可以（测试过，更换包名不影响Sqlite的使用）。

## `.gitignore`文件的创建

IDEA的`.gitignore`插件在使用过程中并不怎么好用，很多东西并忽略不了。相比之下，[gitignore.io](https://www.gitignore.io)就好用得多。

[Android项目的`.gitignore`](https://www.gitignore.io/api/java%2Cmacos%2Clinux%2Ckotlin%2Candroid%2Cwindows%2Cintellij%2Candroidstudio)

## 创建快捷方式的正确姿势

**快捷方式的名字和应用名字一致时，在有的系统上会创建不出来**

在StackOverflow和其他搜索结果中，高票答案往往是用`Intent`的老方法；在[官方文档](https://developer.android.com/guide/topics/ui/shortcuts.html)中上来就讲的是牛扎糖以后的指南。也没人告诉我说，时代不同啦，正确方案在官方文档的中间

> Note: See also the support library APIs, isRequestPinShortcutSupported() and requestPinShortcut(), which work on Android 7.1 (API level 25) and lower. The support library falls back to the deprecated EXTRA_SHORTCUT_INTENT extra to attempt the pinning process.

新时代应该用`ShortcutManagerCompat`创建快捷方式。

## Can't resolve symbol ?attr/colorPrimary

**现在这个方法也不怎么好使了**

好几次在更新Android Studio后，所有Support Design和其他中的资源都找不到了，用起来没问题，但会一直警告。
今天找到了[这个宝贝](https://stackoverflow.com/a/48734990/5507158)，方法就是删掉`.idea/libraries`文件夹，再Sync文件和Gradle。破费!。

## 混淆前记得留下Models

比如Kotlin中，数据库用到的data class。这样可以方便Debug。尤其是保存数据时用到了Gson或其他工具，不keep的话，就会出现很多"a": "biubiubiu"之类的数据，可读性低、以后重构时还可能破坏掉名称一致性。

## 发布代码前最好Rebuild Project

最近就遇到了ButterKnife引起的崩溃，Rebuild之后就自动没了。

## 谨慎使用StrictMode

我的使用经验是，它会破坏Instant Run，阻止在主线程中使用Shared Preferences等等。

比如，它会对[RintoneManager.getCursor()](https://developer.android.com/reference/android/media/RingtoneManager.html#getCursor())报错，提示Cursor未关闭。但文档中提到，我们不需要亲自关闭它。

## 自定义布局中，onSaveInstanceState和restore只有在当前View被赋予ID时才会被调用

[```Android didn't manage the onSaveInstanceState/onRestoreInstanceState if no id is set to the view.```](https://stackoverflow.com/a/28586444/5507158)
