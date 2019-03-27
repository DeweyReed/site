---
layout: post
title: Android文字转语音(TTS)引擎
date: 2019/3/11
categories: Android
---

位于【设置】-【无障碍】-【文字转语音(TTS)输出】。在这里可以配置引擎。一些应用会使用这个引擎进行文字阅读。但因为国内设备的特殊情况，想要让这个玩意儿阅读中文，还是需要一些设置的。

<!--more-->

## 背景

这个设置是系统自带。这个功能不仅仅用于盲人使用设备时，将屏幕上的文字阅读出来，更广泛的用途是，语音播报内容，比如导航、天气预报等。这个功能的登场次数不算多，但我见过不少。

几乎所有Android设备会自带一个`Pico TTS`。

> Pico TTS: AOSP自带的TTS，支持的语言只有英德西法意。

它不支持中文，加上国内多数厂商对这块儿并不怎么重视，导致了TTS这个功能在国内基本是荒废的。但Android留出接口，可以安装一些第三方支持中文的引擎来让Android阅读中文。

**有的国产系统后台管理严格，导致TTS无法启动运行，这种情况下TTS就已经凉了**

### 特殊情况

国内一些导航应用导航时使用的方法，并不是使用这个引擎，而是另起炉灶，用的自己的方法或购买第三方的SDK。

## 引擎

### Google文字转语音

在Google自家的设备上取代了`Pico TTS`，支持地球上绝大多数语言，基本是首选。在酷安和Google Play或各大应用市场都可以找到。

它默认会联网获取语音，但可能出现因网络状况而读不出来的情况，所以需要提前下载好语音包。但即使下好了语音包，有时也会读不出来 ㄟ( ▔, ▔ )ㄏ

## 小爱 TTS

似乎只有在小米应用商店有下载：http://app.mi.com/details?id=com.xiaomi.mibrain.speech

没有试过，但应用信息

> 软件大小:20.86 M 版本号：1.1.1 更新时间：2018-08-02

怕是不靠谱。

## 讯飞 TTS

据说是国内最好的，但我找不到下载它TTS引擎的链接，而且他家的官网和产品也是很混乱。市面上流传的也只是一个3.0的安装包，出于谨慎考虑，还是继续观望吧。

## 还有呢？

信息太少了，一个能打的都找不到了。