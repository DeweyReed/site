---
layout: post
title: 开发历程
date: 2019/7/7
categories: What
---

整理了一下个人的开发历程。

<!--more-->

## 链接

- [Stackoverflow](https://stackoverflow.com/users/5507158/dewey-reed) 现在有 1600+ Rep啦。
- [Github](https://github.com/DeweyReed) 不会推广，靠搜索有了几十个星星。
- [Blog](https://aprildown.xyz/) 有开发内容，也有日常牢骚。

## 应用

独立开发、上架了几款应用，累计了几万下载。

### 计时机器

[酷安页面 现在4.8分](https://www.coolapk.com/apk/177033) / [Google Play 现在4.7分](https://play.google.com/store/apps/details?id=io.github.deweyreed.timer.google)

持续维护近两年的应用。

主用Kotlin，辅助Java。架构是MVVM和Clean Architecture的混合体，用Architecture Components + AndroidX实现，分成了18个模块Module。开发过程应用了TDD，为每个核心功能写了单元测试，为UI写了Instrumented Tests。非常注意Code Style，代码全都通过了Detekt和Ktlint。

异步使用了Kotlin Coroutines。数据保存使用了Room。导航使用了有多层NavGraph的Navigation。依赖注入使用了Dagger2-Android。数据序列化使用了对Kotlin友好的Moshi。后台唤醒使用了android-job和WorkManager，文件操作使用了Okio。

Github里的十几个Repo，也都是在开发过程中，写的Custom View和能分离出来的依赖。后端在国内和国外分别用了Firebase和LeanCloud。

开发过程中最大的感想就是，好的架构和抽象真的能让开发和扩展变得很方便。

### 其他应用

- 循环计时器: [酷安页面](https://www.coolapk.com/apk/118705) / [Google Play](https://play.google.com/store/apps/details?id=com.finalweek10.android.cyclealarm): 架构为MVC，使用了Content Provider进行数据管理和其权限，阅读、研究并运行了一些AOSP中的代码。

- 权限图书馆: [酷安页面](https://www.coolapk.com/apk/162565)：架构MVP为，使用了RxJava2进行事件发送、接受和处理。

- 剪贴板守护: [酷安页面](https://www.coolapk.com/apk/180063) / [Google Play](https://play.google.com/store/apps/details?id=io.github.deweyreed.clipboardcleaner)：清理剪贴板的小工具。

- 曾写过一个用OCR识别游戏中文字，使用Retrofit联网查询信息的辅助应用。但因为刚做出来，游戏就内置了对应的功能，所以没上架。

## 其他

## 其他信息

- [为Android Studio 3.5提出了严重Bug受到过AS的感谢(名字为DeweyReed)](https://developer.android.com/studio/releases#3-5-community-contributors)

## 其他技能

- 凑活的英语：看英文文档、看Google I/O视频、听一听英文Podcast、跟老外聊聊天没什么大问题。
- 设计原型，改图：计时机器的界面就是用Adobe XD做的；能用各种设计软件简单地改图。
- 应用上架和运营：画简单的图标、设计应用截图、构思应用介绍、听取用户反馈、求用户给好评。

## 联系方式

- Email: [pangshups@outlook.com](mailto:pangshups@outlook.com)
- 下方也有魔法师才能看到的评论框
