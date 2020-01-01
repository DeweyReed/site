---
layout: post
title: 一些关于Dagger2及其未来的思考
date: 2020/1/1
categories: Android
---

一切都始于[An Opinionated Guide to Dependency Injection on Android (Android Dev Summit '19)](https://www.youtube.com/watch?v=o-ins1nvbDg)。好消息是Google终于提供了官方的DI教程，而且质量颇高。坏消息是dagger-android要亡了。

<!--more-->

首先是[官方教程](https://developer.android.com/training/dependency-injection)，是我见过最通俗易懂、深入浅出的教程了，没啥问题。但它配套的[Codelab](https://codelabs.developers.google.com/codelabs/android-dagger/)手动管理了一个Scope和Component，以此来在多个Activity中注入相同的对象。这种做法一开始理解起来有点复杂，但用Dagger解决此类问题的方法也都与此大同小异吧。

我个人没遇到过此类问题。因为像是用户状态这类东西，一直都是放到一个Singleton里，也不需要担心是不是多个Activity。其他地方能用Navigation就用，配合ViewModel自带的Lifecycle管理，通常不需要专门用Scope来管理对象。

这个问题其实还有一个解决方法，就是用自定义ViewModel：[Vita](https://github.com/FarshadTahmasbi/Vita)。它的代码可能要改一改才能适合自己用，但原理就是根据Activity的Lifecycle Events来生成ViewModel。

但这暴露了一个问题，~~我用Dagger经验不丰富，~~ Dagger依然学起来难，用起来繁琐，这也跟视频后半部分提到的一样。为此Dagger团队正在发明新的API来简化Android上依赖注入，他们画的那些饼看起来也挺香的。**但是**，一想到现有的dagger代码和近期新项目不得不用dagger写，而这些代码在未来都要重构，我就头大。~~其实完全可以不动它们~~

[这里给dagger-android判了死刑](https://youtu.be/o-ins1nvbDg?t=537): `We are stopping its development. We are not adding any more features to do that.`。不过继续用也没啥问题，现在的又没有deprected。但dagger-android的局限也的确比较大，而它现在也只做到了能让大伙儿少写些类似于Subcomponent的胶水代码。

前几天在V2EX上看大伙儿讨论为啥程序员容易被淘汰，其中一个原因就是自己会的技术很快就过时了。想想也是，要是新的dagger-android一出，也没人关心你曾用dagger用得多么厉害吧。这让我想起了我曾经不知道Dagger需要Build Project来生成代码，结果死活找不到DaggerAppComponent，而现在的官方文档里把Make Project专门用框框标出来，新手一眼就明白了。

死鱼安乐啊死鱼安乐。
