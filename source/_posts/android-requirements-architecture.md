---
layout: post
title: Android开发：Android框架
date: 2019/2/3
categories: Android
---

[Android开发的一些知识 - At a high level, what does the Android architecture look like?](https://aprildown.xyz/2018/12/31/android-requirements/)

<!--more-->

参考[Platform Architecture](https://developer.android.com/guide/platform/)

![](https://developer.android.com/guide/platform/images/android-stack_2x.png)

我们久经考验的Linux内核可以提供可靠安全的运行环境：权限、进程分离、IPC、应用沙箱。包括了各种硬件的驱动。

Hardware Abstraction Layer (HAL)，硬件抽象层。抽象具体硬件实现的。

HAL上有Android Runtime。运行程序的。每个应用都有自己的进程和ART实例。负责AOT和JIT、有专门设计的GC等。具体参见系列另一篇Dalvik和ART。

还有一些常有的Java Libraries。

Native C/C++ Libraries：大部分系统服务（ART和HAL）都使用了native libraries written in C and C++。通过Java JNI放出一些接口。

Java API Framework：写应用时接触到Java API。像是View、Resources、Notification、Activity、Content Providers...

System Apps：系统应用。当然还有各种第三方应用。

[之前的笔记：Android Internal 笔记](https://aprildown.xyz/2018/05/12/android-internal/)
