---
layout: post
title: 一个人的Android开发可以走多远
date: 2025/12/11
categories: Android
---

一个人一直走啊走，

<!--more-->

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
