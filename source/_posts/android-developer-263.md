---
layout: post
title: 一个人的Android开发可以走多远-26年3月版
date: 2026/3/1
categories: Android
---

该不会继续走啊走，

<!--more-->

## 2026年3月4日

`src/main/java`支持Java和Kotlin，但`src/main/kotlin`只支持Kotlin，因此后者中的Java代码会编译失败，而且编译错误不知所云。

## 2026年3月3日

Kotlin的`coerceIn`方法会在参数范围不对时抛异常，导致原本是处理极端情况的代码在极端情况下崩溃。这里要么用Compose的`fastCoerceIn`，要么传参时二次`coerce`。

## 2026年3月2日

AI就像曾经技术进步一样，本以为可以让人力从繁重的劳动中解放出来，但结果是让人力可以在同样时间内完成更多事情。整个过程中，痛苦是守恒的。

## 2026年3月1日

Compose的State的`by`方法虽然用起来方便，但其模糊了State和其数值的界限，让很多State的Side Effects用法很微妙。我开始考虑不用`by`了。
