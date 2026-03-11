---
layout: post
title: 一个人的Android开发可以走多远-26年3月版
date: 2026/3/1
categories: Android
---

该不会继续走啊走，

<!--more-->

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
