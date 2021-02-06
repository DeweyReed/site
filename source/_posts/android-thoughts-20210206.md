---
layout: post
title: Android开发牢骚
date: 2021/2/6
categories: Android
---

好几个月没写东西了，主要是因为开发界无新鲜事，没啥可写。今天勉为其难地发发牢骚。

<!--more-->

## `StateFlow`和`SharedFlow`

Kotlin Coroutines的新玩意儿，看样子要在Kotlin项目中替代LiveData，最近的MAD Skills的问答视频中也表达了类似的观点。

但我在UI层使用Coroutines的体验实在难言理想，比如`Fragment.viewLifecyclerOwner`在`Fragment.onViewCreated`丢NPE呀，`lifecycleScope.launchWhen`不正常运行呀、自定义`CoroutineScope`里的代码不执行呀等，还有[文档中那劝退的两段说明](https://developer.android.com/kotlin/flow/stateflow-and-sharedflow)。和UI层通信还是乖乖用`LiveData`吧。

`callbackFlow`和`TestCoroutineDispatcher`作为实验API已经快两年了吧，JetBrains是不是把它们都忘了呀。

最近浏览了Flutter的异步实现，很是羡慕。突然觉得手里的Coroutines不香了。

## `Hilt`

Dagger是个好东西，虽然学起来有几丢丢难，但依赖注入让新增功能和测试变得很容易。

Hilt更是好东西，学起来无脑，用起来也简单。

唯一想吐槽的就是这玩意儿虽说还是Alpha，但官方已经到处推荐人使用了。官方说法是，Hilt很稳定啦，我们Google内部的应用也都在用，跑在很多很多设备上都没问题啊，只不过想在正式版前再加点东西、改点东西。可惜我被坑怕了，非正式版的不敢用，正式版了也要等几个修复版才敢用，就这个样子还遇到不少坑哩。

语义化版本是不是只算加分项？

## `Android Studio`

IDE开发都是这个样子吗，搬了西瓜丢芝麻，这里修好那里漏？

AS 4.x版本可以说体验极差。Google内部是不是人手三台MBP，每台机器跑10个Android模拟器不带卡。

每天都需要多次重启和Invalidate Cache and Restart。ViewBinding不显示了、资源ID不识别了、Kapt莫名报错了、Dagger生成代码不正常了……

## 结语

也许开发一直都是这个样子吧，还记得AS 2.3的Instant Run坏掉时，到处找解决方案，还参与Stackoverflow讨论的情景。时至今日，嘛也不剩了。
