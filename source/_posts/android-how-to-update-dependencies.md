---
layout: post
title: 更新Android依赖的潜在风险
date: 2025/8/23
categories: Android
---

最近看了一篇文章[The Hidden Dangers of AGP Version Upgrades: A Developer’s Nightmare](https://proandroiddev.com/the-hidden-dangers-of-agp-version-upgrades-a-developers-nightmare-ba9e6132c34d)，其中提到了一个更新AGP导致的崩溃。碰巧我也解决过此问题，并对此类问题颇有心得。

<!--more-->

## 类似问题有很多

我在[Is the dependency safe to upgrade?](https://deweyreed.github.io/dependencies/)中记录了常用的Android开发依赖的各个版本的可用状态。比如Gradle 8.13会在有时Sync失败、AGP 8.10.1推荐使用的K2无法提示Deprecated的变量、datastore 1.1.2有极端崩溃等。

我是在升级到8.1.4(Giraffe)时，遇到了类似文中提到的`NoClassDefFoundError`崩溃。原因是R8遇到一些只有带参数的构造方法被使用的类时，会删掉它们的无参数的空构造方法。由于我遇到的崩溃是和反射有关，那么添加Proguard规则可以解决问题。而文中的堆栈信息似乎还包括Paging，其崩溃的根源更复杂了。对于Paging 3，我则有[更多怨言](https://deweyreed.github.io/2022/01/31/android-paging3/)。

## 解决方法在这里

因为我的日常工作涉及维护多个项目，那么更新依赖就要非常谨慎，否则就要在某个依赖出问题时，面临频繁更新所有项目的重复任务。

其实可以毫无追求地将一个依赖版本用到老，毕竟啥也不做，就不会犯错。但为了使用更好的技术，比如最近新增加的Jetpack Compose dropShadow，又不能不更新依赖。

可惜任何方法只能减小依赖出问题的影响。

## 时间选择要谨慎

我选择了在某个项目**有重要功能的一个版本**来更新，这样QA可以更认真全面地测试应用。

## 版本选择不省心

比如最近Android Studio要每个月都发新版本了，这意味着虽然问题可以更快地被修复，但犯错的成本也降低了。

根据个人观察：

- AndroidX的版本如果在发布**40天**后依然没有修复版本，那基本是最终版本了。
- Android Studio和Gradle在新的大版本发布后，**上一个大版本**就算是最终版本了。

最终版本依然可能有问题，但解决方法通常比较容易找得到，毕竟隔了那么久。

在更新开始时，首先要**认真阅读更新日志**。比如我在开发者论坛中，常常会遇到很多开发者抱怨一些早已在更新日志中警告过的问题。

## 版本里有脏东西

在更新过程中，要使用[dependency-guard](https://github.com/dropbox/dependency-guard)确保更新的依赖所依赖的版本也是没问题的。比如一些版本经常会在小版本中，偷偷地升级Target API或Kotlin版本。

## 更新之后不停息

在更新上线后，要持续监控至少一周时间。在确保没问题后，可以等待其他项目有中等程度修改，再更新这些依赖。

以上。
