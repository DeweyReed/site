---
layout: post
title: 一个人的Android开发可以走多远-26年2月版
date: 2026/2/1
categories: Android
---

我觉得还能走啊走，

<!--more-->

## 2026年2月15日

对于一些文档稀缺的API，比如很多CameraX的功能，与其找代码，还是直接问AI更快。

## 2026年2月14日

在浏览开发视频时，发现十年前Google IO的视频，于是简单看了看。发现曾经的内容质量与现在比有过之无不及，突然感觉懂得越多，懂得越少，这条路看来是无穷无尽的。

## 2026年2月13日

[jetpack-camera-app](https://github.com/google/jetpack-camera-app)是一个很复杂的CameraX项目，不过它包含了一些文档稀缺的冷门功能的用法。

## 2026年2月12日

写状态机时一定不能嫌麻烦而省去枚举或IntDef的使用，否则就像闭眼走夜路，迟早要出事儿。

## 2026年2月11日

Compose中为了避免多余的Composition，会用lambda传参，但如果在一层层的传参过程中有一层没用lambda，就会在每次Composition都创建新的lambda。虽然大多数时间不差这点儿性能，但这个code smell的确膈应人，也很难避免。要不写Lint？

## 2026年2月10日

[Building the Coroutine Framework from Scratch - Omkar Tenkale | droidcon india 2025](https://www.youtube.com/watch?v=fH6ZB_mPTBw)适合拿来涨涨见识。前几天的快速ANR也出自这里。

## 2026年2月9日

在Google IO、Dev Summit和Android Maker等一票会议青黄不接时，Droidcon成为了我主要的灵感汲取来源。不过其的Presentation也是良莠不齐，尤其是一些明显是厂商打广告的内容，还是要谨慎看待。

## 2026年2月8日

没想到在主线程`runBlocking(Dispatchers.Main) {}`就可以快速ANR。原因是主线程要等`runBlocking`运行完，而同时`runBlocking`要等主线程运行完，于是死锁了。

## 2026年2月7日

我知道AGP 9会引入很多问题，但没想到会有这么多：<https://agp-status.frybits.com/agp-9.0.0/>。很多插件的修复还是alpha版本，这意味着要等更久了。 

## 2026年2月6日

面对发布时间不长的心代码，发现AI幻觉严重。让人不由得担心AI训练把当前的老本吃完后，如何面对新的开发需求。

## 2026年2月5日

[Vertical text in Jetpack Compose (without rotation width problems)](https://dev.to/vladleesi/vertical-text-in-jetpack-compose-without-rotation-width-problems-4n2m)比我一开始想到的用Canvas一把梭对A11y更友好吧。

## 2026年2月4日

上台做Presentation时要把IDE调成浅色主题，否则观众看深色主题很吃力。

## 2026年2月3日

Compose Hot Reload很适合快速迭代自定义Composable，最后再把完成的内容复制到Android项目中。

## 2026年2月2日

AI Agent造成的源代码泄漏就是房间里的大象。不懂的开发者盲从，而了解的开发者要么默不作声，要么发言不被注意。至于提供Agent的大公司，更是集体沉默。

## 2026年2月1日

想要唤起Google Play支付，要么应用是Google Play签名的，也就是从其商店里下载，要么机器的账号在应用的测试名单中。非测试人员使用应用自己签名的应用会唤起失败。
