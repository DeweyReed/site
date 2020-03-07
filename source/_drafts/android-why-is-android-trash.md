---
layout: post
title: “为啥Android是个垃圾”
date: 2020/1/30
categories: Android
---

去年在V2EX上收集了不少关于Android和iOS争论的帖子，今天整理并分析一下这个命题的原因、表现和结论等。

因为我是Android开发者，也对Matrial Design颇是喜爱，所以**必然不会贬低Android**。~~那贬低谁呢？我就不可以都夸嘛？~~

先说结论，**二者其实算是针尖对麦芒，相差无几。之所以存在体验性差异，很大程度上是Google Play在国内的缺失。**

<!--more-->

## 卡顿问题

以小见大，先从[今天去面试，面试官问为啥 android 用久了比 IOS 卡](https://www.v2ex.com/t/621694)入手。

## Android系统很差劲，用久了就会变卡

这个是呼声最高的之一吧，主要论点有：

### Android有Java虚拟机的性能牺牲，而iOS直接跑二进制代码

话没错，为了兼容不同的CPU，而使用了Java虚拟机，**当然有性能差距。但重要的是差距有多大**。

一方面Java虚拟机只是一层，底层跑的依然C++。

另一方面，放在当年Dalvik时代，能跑起来就算不错的情况下，差距自然很大。但随着Android棒棒糖在2014发布，新的运行时ART诞生，并且一直优化**至今，Java虚拟机的性能牺牲和直接跑二进制代码之间的差距已经不再是性能瓶颈**。如果激进一点，还可以说二者相差无几（这是个人根据使用体验的猜测，并未有具体数据的支撑）。具体内容在[Android开发的一些知识：Dalvik和ART](https://aprildown.xyz/2019/02/15/android-requirements-dalvik-art/)整理过。

因为棒棒糖之前的卡顿历史，相比iOS之下，很多人自然有了Android比较卡的固有印象并一直保持至今。

### Android应用是服务性质的，而iOS应用是传统软件，可以完全退出

这个真蛋疼。Android整了个“计划经济”：应用启动时正常启动，但什么时候结束不是由用户何时退出决定的，而是由系统资源不够时统一强制回收。这就导致了开发者在开发应用，需要考虑应用在**任何时候**都可能被系统回收的问题，而用户失去了**快捷地**完全关闭应用的方案，但这里有两个例外：

- 用户可以完全关闭一个应用，通过打开应用的系统设置界面，点击“强行停止”，但这一点都不快捷，因为系统并不想让你这么做。
- 国产系统为了弥补这一个问题，几乎都有把一个应用从最近使用的列表中划出时，完全关闭应用的特点。

在做出这个决定时，Android绝对低估了不少开发者对于流氓应用的热衷。于是越来越多的流氓应用和习惯成自然的普通应用，都开始在被系统强制回收前，利用自己无法被用户轻易关闭的这个特点，不再担心自己会被因为太臃肿而被用户关闭的问题，最终导致了整个系统体验都很糟糕。

那当初为何要怎么做呢？这里引用[Dianne Hackborn的一句话](https://web.archive.org/web/20180505121013/https://plus.google.com/+DianneHackborn/posts/FXCCYxepsDU)

> we needed to give the platform more control over how an app runs. In particular, we wanted to build a system where the user never needed to think about starting and stopping apps, but rather the system took care of this for them... so the system had to have some more information about what is going on inside of each app, and be able to launch apps in various well-defined ways whenever it is needed even if it currently isn't running.

简单总结就是，Android系统想要更好地服务用户，免除用户担心应用启动、关闭和运行的问题。我打个比方就是，Android想这个快餐店，客户可以来了，吃了然后走人，怎么做、怎么处理垃圾等问题都不需要担心。但明显，Android起初做的并不好，用户反而需要更担心应用的启动和关闭问题。

### 早期的Android系统的卡顿、唤醒和权限等问题比较严重，给人们留下了固有印象

## 国内Android缺乏有效强力的应用审核机制。iOS由苹果一统全球

国外Android有Google Play，唯独国内Android缺了市场管理者，由能力层次不齐、要求各不相同各家硬件制造商圈地管理。

### 流氓应用相互唤醒、性能糟糕、随意储存，拖慢了系统

国内用户常用的Android应用都很卡，给人以整个系统很卡的感觉。

### 国内Android分发渠道不重视权限管理

### 主要的SDK提供商贩卖用户信息

## 一分钱一分货。千元Android机器和其数倍价格iPhone相比，当然打不过

在五、六、七、八千Android机器，这个问题不再严重。

### 有的Android厂商为了节省成本，使用了低劣且更易老化的硬件设备，从而让用户更快地感觉很卡

### 千元机市场占比很大，所以人们常用千元机和iPhone比较

## 安全问题

[为什么说 IOS 系统比 Android 系统要安全？](https://www.v2ex.com/t/631240)

- Android开源且开放，被用于应用和厂商用于流氓行为。iOS的闭源也是一种安全措施。
- 很多厂商能力参差不齐，手机也不能及时打补丁。
- 权限控制的问题。“不给权限不给用”
- 因为Android占用比大，所以有更多人在找漏洞。

## 应用体积问题

[为什么现在 Android 应用体积那么大？](https://www.v2ex.com/t/640162))

- 很多应用针对不用CPU架构用了大量ABI。
- 一些本应系统系统提供的架构，因为国内Android对应功能的缺失，只能由应用自带。
- 跨平台技术不可避免地占用大量空间。
- 开发过程混乱，资源重复打包、`.DS_Store`文件。
- 国内Android不支持Android App Bundle。

## 结论

逛论坛看帖子也真就图一乐儿，不能当真。
