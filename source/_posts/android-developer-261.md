---
layout: post
title: 一个人的Android开发可以走多远-26年1月版
date: 2026/1/1
categories: Android
---

一个人接着走啊走，

<!--more-->

## 2026年1月13日

面对AI时，总有一种既怕兄弟过的苦，又怕兄弟开路虎的感觉。不过这种感觉也算是动物本性了。针对一个复杂问题，看着AI给出的乱七八糟的方案，有时跑得起来，有时都编译不了，自己也哭笑不得。虽然现在AI真正帮得到忙的地方还是我很熟悉的地方，但我也想起了黑客帝国里有一段儿说，没人知道远处的一个好像是蓄水池是如何工作，但它依然一直在工作。

## 2026年1月12日

绝大部分时间，代码在完成后就再也不会被改动，所以与其想着未来再优化现在的半成品代码，不如多上点心，一开始就以完美为目标。

## 2026年1月11日

面对Legacy code，个人的原则是不坏不修也不碰。不然，在修改或优化的时候引入了新的问题，对用户来说就是体验降级，那么整件事就成没事找事了。想要优化代码，就等到未来必须通过优化来增加新功能或解决已有问题再开始。想要尝试新技巧，就在新功能里尝试。当然，这都是建立在时间有限的基础上，比如一些明显是太闲的公司，对于用新技术重构代码库热衷得很呐。

## 2026年1月10日

最近遇到了Room引用另外一个Module使用的Entity的过程中，当这个Entity有一个返回这个Module使用implementation引用的类时，无法编译的问题。由于编译错误让人摸不着头脑，花了不少时间检查。解决方法是要么改为api，让Room可以看到引用的类，或者把方法移出Entity。

## 2026年1月9日

SharedPreferences是会造成不少ANR的，但由于其难以被重现，所以很难在测试环境中被发觉。也没什么太好的解决方法，要么用网络上一些复杂的反射，要么换个框架，比如最近才算比较稳定的datastore。

## 2026年1月8日

Media3是个好项目，issue反应和功能迭代都很积极。虽然有不少UnstableApi，但用着都没什么问题。

## 2026年1月7日

Nav3看起来很好，但出货速度太快，以至于不敢信任。根据历史规律，再等等3.1吧。

## 2026年1月6日

我依旧不明白国内厂商提供的SDK如何可以这么差劲。这么多年下来，proguard是不会配置的，权限是乱申请的，接口设计都不是拍脑门的，简直是在梦游。

一定要把它们用依赖注入拆成单独的模块，这样至少不会污染开发坏境。

## 2026年1月5日

现在的[media-samples](https://github.com/android/media-samples)的玩具意味更大，看看乐子得了。

## 2026年1月4日

现阶段Metro作为依赖注入对多Module的KMP项目的支持还差点意思，所以暂时用Koin咯。只要架构选的好，切换依赖没烦恼。

## 2026年1月3日

[From Parcelable to @Serializable - Kuan-Ying Chou | droidcon London 2025](https://www.youtube.com/watch?v=SLs8drYogCc). `Serializable`是在`rememberSaveable`基础上可以跨平台的存活过进程死亡的实现。Android底层还是把对象转换为Bundle。

## 2026年1月2日

[Java versions in Android builds](https://developer.android.com/build/jdks)逻辑混乱，让人头大。怎样的配置是最佳实践，也是让人摸不着头脑。文档中的推荐也有着潜在的坑。保险起见，还是用传统的`sourceCompatibility` + `jvmTarget`吧。

## 2026年1月1日

与View相比，Jetpack Compose的`Text`关闭了`includeFontPadding`，所以英文的默认高度与`TextView`不同，打开就好了。

Jetpack Compose的`TextField`的高度则会根据中英文不同而变化，加上其他需求，[会很让人很抓狂](https://juejin.cn/post/7530088591395160106)。我发现`TextField`的大部分参数都不会变，所以直接用`AndroidView`包裹一个`AppCompatEditText`就可以解决问题喽。其中，用`DrawableCompat.setTint`给`textCursorDrawable`上色（旧系统的用户应该不会注意到吧）。
