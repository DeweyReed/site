---
layout: post
title: Android简繁体翻译到底该放哪儿？
date: 2022/12/25
categories: Android
---

Android开发有着和[ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)与[BCP 47](https://www.rfc-editor.org/info/bcp47)相似但不同的语言管理方案。

<!--more-->

## TL;DR

如果minSdk大于等于24(Android 7, Nougat)，根据[官方文档](https://developer.android.com/guide/topics/resources/providing-resources#AlternativeResources)，简体中文放`values-b+zh+Hans`，繁体中文放`values-b+zh+Hant`。

但没人会这么做吧！

如果minSdk小于等于23(Android 6, Marshmallow)，经过个人测试，简体中文放`values-rCN`，繁体中文放`values-rTW`。如果要针对香港特殊翻译，再用`values-rHK`，澳门、新加坡同理。

## 官方文档有坑

[讲解如何增加翻译的官方文档](https://developer.android.com/training/basics/supporting-devices/languages)和前段时间的Android Dev Summit的一个视频中中，都推荐BCP 47格式（比如`values-b+zh+Hans`），但**都不提示这是Android 7及之后才支持的格式**。

在之前的版本使用该格式时，应用并不会崩溃。但在缺少对应的翻译时，系统会发现新格式包含`zh`，进而先显示新格式的简体翻译，没有时显示新格式的繁体翻译。

## 英文翻译

以情况简单的英文为例，Android系统并没有单独的“英语”的语言选择，而有的是诸如“英语（美国）”、“英语（英国）”和“英语（印度）”等选择。这种情况下`Locale.getDefault().toLanguageTag()`将会是`en-US`、`en-GB`和`en-IN`等。

这里可以把英文翻译统一放到`values-en`，但通常英语是直接放到`values`中作为默认语言。

## 中文翻译

但简繁体中文并不能统一放到一个文件夹中。Android系统团队显然意识到了类似问题，但Android 7才支持为时已晚~~（要是AGP向前兼容就好了）~~。

以情况复杂的繁体中文为例，虽然翻译放到了台湾（`values-rTW`），但是：

在Android 5和6中，只有“简体中文”和“繁体中文”两个语言选项。前者对应`values-rCN`，后者对应`values-rTW`。这里并没有之后系统中，可以选择地区的选项。

从Android 7开始，在选择语言的同时，也必须选择语言的地区。这时就出现了港澳台三地的繁体中文版本。

在个人测试中，以香港繁体中文为例，系统选择`values-b+zh+Hant`和`values-rTW`或`values-rHK`的优先级在不同手机上是不同的。但肯定是繁体中文。

系统也足够智能，如果没有`values-rHK`，它会选择`values-rTW`，即使这里的地区是台湾而不是用户设置的香港。

系统会选择和用户设置地区不同的文件夹，这里是最反直觉的地方。这是因为找不到地区对应的翻译时，系统会选择同语言的其他地区翻译，来确保至少是语言是正确的。

## 奖励1

`build.gradle`中的指定语言的`resConfigs`是要和对应文件夹一致的。比如`resConfigs 'en', 'zh-rCN', 'zh-rTW'`

## 奖励2

在[Per-app language preferences](https://developer.android.com/guide/topics/resources/app-languages)，Android依然沿用了[反直觉的缩写](https://developer.android.com/guide/topics/resources/app-languages#sample-config)。这里似乎和`Locale.toLanguageTag()`是一致的。

## 参考

- <https://developer.android.com/guide/topics/resources/localization>
- <https://developer.android.com/guide/topics/resources/providing-resources#AlternativeResources>
- <https://developer.android.com/training/basics/supporting-devices/languages>
