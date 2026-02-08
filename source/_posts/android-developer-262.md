---
layout: post
title: 一个人的Android开发可以走多远-26年2月版
date: 2026/2/1
categories: Android
---

我觉得还能走啊走，

<!--more-->

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
