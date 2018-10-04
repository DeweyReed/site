---
layout: post
title: Google I/O 2018 笔记
date: 2018/5/12
categories: Android
---

把今年I/O的内容参加的差不多了，这里记录一些对我有用的东西。

<!--more-->

## Jetpack

感觉像是给Development换了个更酷的名字。四块中Architecture中才是最重要的各种Libraries。

最有亮点的应该是Navigator。在将Activity和Fragment的逻辑分离出来后，单Activity加多Fragment成为了官方推荐的做法，这样让动画也更方便了。

WorkManager算是造了一个大轮子，使用起来似乎比现有的更方便。把后台运算、服务、Job Scheduler、GCM、FCM等都可以替代了。

Paging，ListAdapter和Recycler Selection覆盖了更多的列表显示。

## ContraintLayout

RelativeLayout都不推荐，要被取代了。而且还引入了关键帧动画，可以说野心很大了。

## Architecture

当初看Android Permormance Pattern的内容还历历在目，现在就说枚举可以用了，内存使用也放开一些手脚，也有了推荐的框架。

## 其他

- Material Theming和Component给开发和设计带来了更好的指导。
- 长文字的显示可以用RecyclerView或者premeasure来处理
- 使用夜间主题来省电
- Slice是不是要取代Widget呀，还要观望一下。