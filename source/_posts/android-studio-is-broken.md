---
layout: post
title: 天天坏，天天修的Android Studio
date: 2020/9/20
categories: Android
---

记录了个人在确定不是代码问题后，如何修理坏掉的Android Studio的一些经验。

<!--more-->

“坏掉”指的是所有不是自己代码问题的问题。

## Dagger

[曾经的某些AS版本和Dagger的某些版本不兼容](https://stackoverflow.com/a/52499659/5507158)，而Dagger出了问题，多数人会先怀疑自己（而且多数情况也的确是自己的问题）。现在有了Hilt，问题会好很多。所以针对Dagger的问题，先学好Dagger，再去Issues里逛一逛。

确认代码没问题后，可以尝试将项目或全局的`gradle.properties`中的各种配置注释掉重新Build一次。各种为了加速Gradle的配置有时会帮倒忙。

最近发现当`DaggerAppComponent`中有类型不匹配、缺少Injector的问题时，手动修改生成的`DaggerAppComponent`，然后重新Build，有时能解决办法，当然这是在确信自己的Dagger代码无误，且各种Clean、清缓存都不起作用的前提下。

2021新发现的方法！给类改名字。

## `git clean -fdx`

删除所有没被Git管理的文件夹和文件。Android Studio中的Clean Project有时不够用。

## 修改`settings.gradle`中的`rootProject.name`

如果没有`rootProject.name`，新建一个Android项目，参考里面的示例代码。

把`rootProject.name = "ProjectName"`中的`ProjectName`修改为任意名字，保险起见重启一下AS，再修改回原名字，再重启一下AS。这个方法可以解决一些Gradle文件不在IDE中的Android标签下显示的问题。

## 删除Gradle缓存

最近遇到一个问题，Kotlin Extension Functions不被识别，上述方案都试过了没用。最后只好打起了Gradle的主意。

除了`.gradle/gradle.properties`，把`.gradle`和`.m2`都删掉。因为小文件很多，删除时间很长，所以要一次性永久删除，如果先丢到回收站，等待时间要翻倍。

## 删除Android Studio缓存

最近升级到4.2后，遇到问题仅删除Gradle缓存已经不够了。导出AS设置，然后把所有AS缓存都删光，然后重启。相当于重装了。

## `deep-clean`

使用Github的[deep-clean](https://github.com/rock3r/deep-clean)。

因为破坏力太大，所以个人还没使用过，不过可以参考其中删除了哪些文件和文件夹。

通常是不需要删除IDE配置文件的，而且个人还从没走到那个地步。希望未来也不需要吧。
