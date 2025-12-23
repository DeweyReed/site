---
layout: post
title: 一个人的Android开发可以走多远
date: 2025/12/11
categories: Android
---

一个人一直走啊走，

<!--more-->

## 2025年12月23日

在设置里打开一下手动Sync：`Project Sync mode -> Manual Sync with remidners`。否则只是打开一个项目找找代码，都要等待下载各种依赖。

## 2025年12月22日

我现在还没用上K2。一开始我就发现其不会提醒变量的Deprecation，比如`Bitmap.CompressFormat.WEBP`，时至今日也没修复。由于一直懒得开一个issuetracker工单，所以一直在等啊等。类似Deprecation不提醒的问题，之前AS就发生过好几次，我也会因此直接跳过这些AS版本。而K2之前连Live Template的自动补全都坏了，所以还是等等吧，即使现在K2默认打开。

估计Google和JB都发现，测试功能不放到生产环境，就没太多人愿意测试吧。

## 2025年12月21日

Droidcon居然又开始发印度的视频了，不过看起来强度不大。很多类似的Presentation都是某个公司专用的框架或实现。虽然分享精神可贵，但这些内容的大部分都不适合其他公司，而且很多情况下，看似能解决其复杂问题的复杂方案，都过拟合了，也让新人上手变得很困难。

## 2025年12月20日

对于把ComposeView转换成Bitmap，现有的专用API需要内容显示在屏幕上，但如果内容不在屏幕上呢？我发现可以给`activity.window.decorView`塞一个不可见ComposeView来实现，这样可以避免手搓ComposeView时遇到的很多崩溃。

## 2025年12月19日

总算看完了今年Droidcon的视频，接下来要整理一些内容了。也不知还有多少人会像我一样，但毕竟那些视频播放量也就几百。其中也有不少视频是换个城市再放送，加上跳过多余的背景介绍和快进无用内容，其实也不算很多。

## 2025年12月18日

在之前的博客中也部分提到过：AS和Gradle会在新的大版本发布时，不再给上一个大版本打补丁，这意味着上一个大版本总算是“稳定”了。AndroidX的某个大版本通常会在30多天更新补丁版本。那么如果40天的时候还没有补丁版本，意味着这个大版本也算是“稳定”了。

## 2025年12月17日

AI对Stackoverflow的冲击很大吧。面对一些常规问题，AI的回答会完善许多。面对一些罕见问题，虽然偶尔还会到Stackoverflow找一找，但效果也不如跟AI聊天好。我在AI时代来临前积攒的Reputation已经像是当年勇了。

## 2025年12月16日

在生成安装包时，尤其对于有XML或ViewBinding的项目，有时会出现类似symbol unresolved的报错。如果确定不是项目问题，很多开发者都只能Invalidate cache and restart，费时费力。不过我很久之前发现了一个方法，在各个系统上都很好使。苦于无处分享，只能放在这里：

1. Clean project
2. 关掉AS
3. 删掉`系统用户目录/.gradle/caches`文件夹下，`build-cache-1`，`jars-9`和`journal-1`三个文件夹
4. 打开项目，Sync然后Build

这个方法很快，不需要重新下载依赖。我已经很久没有Invalidate cache and restart了。

## 2025年12月15日

在AS的设置`Build, Execution, Deployment -> Build Tools -> Gradle`中，可以配置JDK。[官方推荐配置为GRADLE_LOCAL_JAVA_HOME](https://developer.android.com/build/jdks#jdk-config-in-studio)。但之前都是无视变量名称，根据AS大趋势，选一个版本，但这几天才发现GRADLE_LOCAL_JAVA_HOME下拉菜单可以直接选AS自带的JDK，这个很好很方便。

话说话来，为什么默认设置不够用呢？因为要缝补一些年久失修的项目。如果不想经历整修整个项目才能运行的痛苦，用其AGP版本对应的AS是最直接地可以无痛运行项目的方法，但这也意味着机器上会有多个AS版本和JDK版本，进而要给不同项目选不同的JDK。

## 2025年12月14日

昨天怠惰，今天补上：

[Surviving and fixing bugs in 3rd-party libraries - Yury Vlad & Andrei Belous | droidcon London 2025](https://www.youtube.com/watch?v=1fnTUAAam-A)还可以。学到了Gradle dependency locking，但由于输出格式与Dependency guard不同，所以二者不能平替，却能互补。

另外，通过Composite build来引用其他项目的代码，虽然看起来不错，但在亲自上手之前，我还不知道会有多少坑。

今天的份：

这样每天都进步一些，这正确的方向吗？会不会这只是自己一厢情愿地重复自己擅长的事情，而忽略了更重要的事情呢？

## 2025年12月12日

最近在努力看Droidcon的Presentation视频。别看数量很多，但大部分的内容都可以快进浏览，然后由于乏善可陈，故抛弃。时不时会发现一些意外的惊喜，学到一些新的技巧。

我也会时不时想起七八年前躺在床上昏昏欲睡地用蹩脚的英文看Realm发的技术视频的那段日子。

## 2025年12月11日

我发现LLM很适合完成一些简短但繁琐的填坑式工作，比如实现一个独自的算法或交互、搜索一些自己都说不清的内容。没有LLM的帮助，自己站在坑边，也能看清坑里有多深，坑对面有多远，但有了LLM的帮助，就可以瞬间完成。

一旦复杂度提升，LLM就有点顾此失彼了，特别是在甄别最佳实践时。这对开发者的要求还不低哩。
