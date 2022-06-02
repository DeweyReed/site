---
layout: post
title: 更新，还是不更新，这是个问题
date: 2022/5/25
categories: Android
---

项目依赖想升不敢升，升了怕有坑，到底怎么整？

<!--more-->

## 术语

依赖 == 第三方库 == Library ≈≈ 某个Github项目

## 如果东西没坏，就别修理它

升级依赖最糟糕的体验就是，本来没问题，升级升出了问题。

所以如果没有“想升级”之外更充分的理由，尽量别升级依赖。要升级**等最少几个月再升级**。

## 依赖的源头

[KISS, Keep it simple, stupid](https://en.wikipedia.org/wiki/KISS_principle)

从源头上来讲，要**尽量减少使用的依赖**。

一些对应用架构影响很大的依赖，比如一些MVI框架、复杂的UI嵌套实现等，一定要**慎重考虑依赖的必要性**，不然未来积重难返。

还有一些专用的、不怎么更新的依赖，要么一个版本用到老，要么拷贝到项目中。

## 依赖的选择

即使是一些作者积极维护、社区反馈热烈、代码质量很高的依赖，也会偶尔出现有坑的版本。更别提那些无人问津、一两个版本就杳无音信的依赖了。在选择前一定要评估**评估依赖的维护状态**。

针对一些闭源依赖，有新版本时一定要等几个月再尝试。

## 依赖的管理

个人用[ben-manes/gradle-versions-plugin](https://github.com/ben-manes/gradle-versions-plugin)来检测需要升级的依赖，和[dropbox/dependency-guard](https://github.com/dropbox/dependency-guard)来保证升级后的依赖版本（排查出依赖的依赖的升级）。

## 依赖的更新

有新版本时一定要等一两个月再尝试，无论大小。

### 小更新

大部分依赖更新只需修改版本号，这样在发现问题回退版本时很容易。

### 大更新

对于有着Breaking Changes或需要大幅修改项目库的依赖更新，要特别小心。**在升级前一定要规划好来避免意外**，比如专门开一个Git Branch、进行专门的测试等，以免升级时发现太复杂而放弃，或升级后发现旧版本更适合自己。

虽然一些依赖的大版本更新有着兼容，但也要小心。比如Paging3虽然兼容旧版API，但行为却有些许变化。

### 新项目

因为会进行详尽的测试，这里当然要使用**最新稳定版**的依赖。

### 老项目

为了项目稳定，尽量不升级依赖，或者只升级那些确定很稳定的依赖。

## 其他的更新

印象最深刻的要属某次升级了某个开源应用，结果在我的设备上有Bug。作者修了一个月，但这期间等不及只好删掉应用装旧版本，数据全没啦！

教训就是，如果某次更新是无法回退的，且更新失败的代价是难以接受的，那么**如果它没坏，就别修理它**！