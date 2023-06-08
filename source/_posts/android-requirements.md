---
layout: post
title: Android开发的一些知识
date: 2018/12/31
categories: Android
---

一些Android开发的知识。

<!--more-->

1. At a high level, what does the Android architecture look like?

    <https://github.com/DeweyReed/site/blob/master/source/_posts/android-requirements-architecture.md>

1. At a high level, what happens when you hit the build button on Andorid Studio?

    <https://github.com/DeweyReed/site/blob/master/source/_posts/android-requirements-start-button.md>

1. What happens when you install an application on the device?

    <https://stackoverflow.com/questions/7773360/what-exactly-happens-when-i-install-an-android-application>

1. How Dalvik and ART VMs work?

    <https://github.com/DeweyReed/site/blob/master/source/_posts/android-requirements-dalvik-art.md>

1. How does Inter-Process-Communication work on Android？

    来自Linux的跨进程通信，Android有自己的实现。用于不同Android Components之间如何通信。有多种机制：Intents、Bundles、Binders、Service、BroadcastReceivers。这些实现为通信提高了安全性。

    一些情况可以需要ALDL来实现跨进程通信：<https://developer.android.com/guide/components/aidl>

1. How are apps sandboxed and why does it matter?

    <https://source.android.com/security/app-sandbox>

    借用了Linux的用户权限系统来给每个应用提供各自的权限，达到了应用分离、沙箱的效果。默认情况下，普通应用没有跟其他应用和系统应用互动的权限。

1. Processes and application lifecycle

    <https://developer.android.com/guide/components/activities/process-lifecycle>

    应用在需要运行时，创建进程。再不再运行且系统需要内存时回收。系统根据一个应用正在运行中的组件来给应用分等级，并在需要内存时，从低等级开始回收。

    BroadcastReceiver在返回之后，系统就会认为它没用了。需要回收时就会清理掉它和它产生的线程。
