---
layout: post
title: Android应用白名单指南
date: 2019/1/11
categories: Android
---

更新日期：2020年8月22日

## 背景

第三方厂商生产的Android设备（包括但不限于一加、华为、三星、小米、魅族、华硕、Wiko、联想、OPPO、VIVO、诺基亚、索尼等，排名根据严重程度分先后），都存在不同程度的强杀后台的问题。具体表现为应用在熄屏或切换到其他应用时，经过短则几十秒，长则十几分钟后，应用被系统强制关闭。

需要用户自行进入系统设置中，设置一些选项才可让应用正常运行。这一过程在本文中称为“白名单”。

<!--more-->

> 问：为什么微信、QQ、网易云音乐等应用进入后台后可正常运行？
>
> 答：生产厂商给予了这些流行的大众应用默认白名单的特别优待。

以下资料搜集自网络，请自行参考使用。

## 指南

1. Github项目[dont-kill-my-app](https://dontkillmyapp.com/)，记录了一些流行设备的白名单指南（英文）

1. [少数派 - 手机里有无法正常运行的 Android 应用？用这四步手动排查问题](https://sspai.com/post/55264)（中文）

1. [轻应用](http://wpengapp.com/lightstart)的[白名单指南](http://wpengapp.com/romsetting)（有图片、中文）

1. [某个应用的Bitbucket项目中的一个Issue，提供了很多手机手机白名单过程的图片指南](https://bitbucket.org/copluk/acr/issues/607)（英文）

1. [Slack的帮助文档，提供了很多设备的**通知**白名单的指南](https://get.slack.help/hc/en-us/articles/360001562747-Known-issues-with-Android-notifications)（英文）

## 计时机器的额外指南

1. 在应用打开【侧边栏/抽屉/汉堡包】->【帮助与反馈】->【白名单指南】，其中有计时机器需要白名单的解释，下方还**可能**会有一些按钮，点击后可以打开设备的一些设置页面。没有的话，就需要参照[上述指南](#指南)。

1. 华为设备的一张单独的链接：[https://aprildown.xyz/2017/12/10/timer-huawei-solution/](https://aprildown.xyz/2017/12/10/timer-huawei-solution/)
