---
layout: post
title: 一个人的Android开发可以走多远-26年3月版
date: 2026/3/1
categories: Android
---

该不会继续走啊走，

<!--more-->

## 2026年3月20日

[Remote Compose: Back to the Future](https://medium.com/@chatikyan/remote-compose-back-to-the-future-454b8e824fad)。SDUI要复活了？看起来比WebView和自定义SDUI要靠谱些，但由于还是alpha版本，还需观望。

## 2026年3月19日

[What I learned keeping an Android app alive 24/7 on Samsung, Xiaomi, and Honor](https://www.reddit.com/r/androiddev/comments/1rxhmu3/what_i_learned_keeping_an_android_app_alive_247/)很厉害也很辛苦。在dontkillmyapp有点跟不上时代的情况下，国内机器的情况只会更严峻吧。

## 2026年3月18日

AS Panda没有了关闭K2的开关，而只能使用[The Deprecation of K1 Mode](https://blog.jetbrains.com/idea/2025/12/the-deprecation-of-k1-mode/)中的配置使用K1。关闭后，IDE则会有其他问题。看来只能接受Deprecated enum无提示、Live Template不及时的K2了。等我有时间了再去开个工单，希望不会被忽视。也算是典型的Android开发体验了。

## 2026年3月17日

[Update Your Android Projects to AGP 9.0](https://www.youtube.com/watch?v=m0Cq6J-V_RY)与官方文档结合着看效果更好。

## 2026年3月16日

`com.github.ben-manes.versions`与AGP 9和Gradle 9一起运行时，不太和谐。该找一个替代了。

## 2026年3月15日

baselineprofile的正式版插件还不支持AGP 9，所以要么用测试版，要么注释掉。我选择稳妥的后者。

## 2026年3月14日

根据[Metro的文档](https://zacsweers.github.io/metro/latest/multiplatform/?h=ios#supported-targets-for-artifactsfeatures)与个人使用体验，距离在生产环境中使用Metro还需要数月的沉淀。

## 2026年3月13日

`Modifier.layout`还是可以拿来改内容的位置和大小的。

## 2026年3月12日

[Motion Blur for a Spinning Wheel in Jetpack Compose](https://proandroiddev.com/motion-blur-for-a-spinning-wheel-in-jetpack-compose-368c1647224d)的数学让人头大。有时间的话，客户端的实现性能最棒，但没时间的话，除了让AI来帮忙算算数学，还可以麻烦设计师直接输出Lottie或libpag格式的文件。

## 2026年3月11日

KMP项目升级AGP 9时，先迁移Android module再升级可以省去不少麻烦。

## 2026年3月10日

虽然`createDeviceProtectedStorageContext()`带有`Protected`字样，但使用其创建的`SharedPreferences`并不安全。通常用于给解锁屏幕前显示内容的应用，比如闹钟、音乐、电话等。

## 2026年3月9日

全Compose的应用可以把Navigation的配置都放在顶层，而各个屏幕只提供事件。这样换Navigation框架就会很容易。

## 2026年3月8日

AS的Gemini会更频繁地触发代理密码的对话框。彻底解决的办法是在设置中设置**手动**代理，输入任意密码并记忆。

## 2026年3月7日

Media3的`FrameExtractor`比Glide还要慢一点。只有在开了硬件加速才会快很多，但根据其文档，硬件加速会在一些设备上出错。看来，Media3的inspector还有很长的路。

## 2026年3月6日

Media3的`MetadataRetriever.retrieveDurationUs`比`MediaMetadataRetriever`慢不少。虽然尚不知其兼容有多好，但这个速度还不能用。

## 2026年3月5日

突然想起Relay，虽然早已知其破产，但没想到都一年了。想起了当初宣传时，其炫耀将原本的设计然后开发的流程变为了设计开发设计开发……相互交织的模式，也不知其为什么会觉得后者更好。

## 2026年3月4日

`src/main/java`支持Java和Kotlin，但`src/main/kotlin`只支持Kotlin，因此后者中的Java代码会编译失败，而且编译错误不知所云。

## 2026年3月3日

Kotlin的`coerceIn`方法会在参数范围不对时抛异常，导致原本是处理极端情况的代码在极端情况下崩溃。这里要么用Compose的`fastCoerceIn`，要么传参时二次`coerce`。

## 2026年3月2日

AI就像曾经技术进步一样，本以为可以让人力从繁重的劳动中解放出来，但结果是让人力可以在同样时间内完成更多事情。整个过程中，痛苦是守恒的。

## 2026年3月1日

Compose的State的`by`方法虽然用起来方便，但其模糊了State和其数值的界限，让很多State的Side Effects用法很微妙。我开始考虑不用`by`了。
