---
layout: post
title: Android多进程开发笔记
date: 2024/6/23
categories: Android
---

RT

<!--more-->

- [把需要长期后台运行的服务放到单独的进程中来降低被杀几率](https://x.com/oasisfeng/status/1633822931523612676)
- 通过[`android:process=":process"`](https://developer.android.com/guide/topics/manifest/service-element#proc)指定运行于新的进程
- 自定义的`Application`（`android:name=".App"`）会被每个进程运行一次

  应在自定义的`Application`中提前考虑多进程使用：

    1. 在`Application`判断进程名，按需初始化组件
        - [Is there a way to get current process name in Android](https://stackoverflow.com/a/55842542/5507158)
    1. 将组件放到`androidx.startup`中并手动初始化

- `SharedPreference`无法跨进程使用，因此[Use DataStore in multi-process code](https://developer.android.com/topic/libraries/architecture/datastore#multiprocess)
