---
layout: post
title: Dagger2 入门路线图 (2018年5月)
date: 2018/5/16
categories: Android
tags: 可能维护
---

现在是**2018年5月**，Dagger2的版本是**2.16**，Kotlin版本是**1.2**，IDE是**Android Studio**。

过时的话就要话就要慎重地看。

Dagger2学起来是真心难受，现有的很多资料都已过世，误人子弟，而且和Kotlin结合在一起，会有很多很多坑和弯路。

> Copy-pasting code makes it hard to refactor later on. As more and more developers copy-paste that block, fewer will know what it actually does.

本路线图指明的是：

从：对Dagger2只懂一点，不熟悉，只会到处复制粘贴。

到：熟悉Dagger2的使用方法，明白什么时候用interface，什么时候用abstract class，什么时候用@JvmStatic等等，能够看懂生成的代码，能分辨出Dagger2实现的好坏。

<!--more-->

- 因为Dagger2 + Kotlin有不少坑，建议先用Java操练，再用Kotlin。

1. [官方文档 User's Guide](https://google.github.io/dagger/users-guide)

    Dagger2面向的是Java项目，所以先不涉及Android代码也可以运行。

    官方文档现在比较友好了，结合[其中的代码](https://github.com/google/dagger/tree/master/examples/simple/src/main/java/coffee)，并阅读生成的代码，理解Dagger2的原理。试一试什么@Singleton啊，@Named呀。

    - 使用Build => Make Project生成代码。
    - 这一步研究生成的代码可以说很重要了。Dagger2的实现并不复杂，但信心++。

1. [官方文档 Multibindings](https://google.github.io/dagger/multibindings)

    官方文档的顺序有问题，所以先跳过Android的部分。

    Multibindings内容比较简单，主要为以后做准备。

1. [官方文档 Subcomponents](https://google.github.io/dagger/subcomponents)

    重头戏来了，Subcomponenets将会是使用dagger-android的核心，必须要理解的内容。

    - 至于后面的Producers，Testing，需要时再看也可以。

1. [官方文档 Android](https://google.github.io/dagger/android)

    这其中最莫名其妙的内容应该是`@ContributesAndroidInjector`的引入。简单来说它仅仅是简化了Subcomponent的建立过程。看不懂时先忽略，明白了Subcomponent的使用再回来看它。

    想要具体理解dagger-android的使用，还需要搭配

    1. [[HOW-TO] Android Dagger (2.11–2.14) Butterknife (8.7-8.8) MVP (Part 1)](https://proandroiddev.com/how-to-android-dagger-2-10-2-11-butterknife-mvp-part-1-eb0f6b970fd)

        非常详细的指南。可以算是Best Practices了。

        - 这个月刚更新的，还热乎，乘早了。
        - 虽然支持到Dagger2.14，但Dagger2.15、2.16也只是修理修理Bug
        - 它支持support和无support的版本，还包括对应的kotlin版本。

1. Dagger + Kotlin

    多坑地带。宝贝有

    [Kotlin+Dagger best practices/documentation](https://github.com/google/dagger/issues/900)

    [@Named(...) qualifier not working with Firebase or Kotlin](https://github.com/google/dagger/issues/848)

    比较有用的有：静态@Provides的两种方法、@field:Named的使用等等。

1. 其他

    [Dagger 2 Generated Code.](https://medium.com/mindorks/dagger-2-generated-code-9def1bebc44b)粗略地介绍了Dagger2生成的代码。如果在第一步研究够了的话，这里不是什么问题。

至此，如果项目中出现了以下情况，都可以认为其不是Best Practices：

- Android项目中没有使用dagger-android(AndroidInjector等)
- 在Activity中引用Application来获得DaggerAppComponent再注入，而不是使用AndroidInjection
- 没有使用Scope而是手动管理。
- 到处都是@Singleton而没有用@Reusable或其他
- 其他的还没想到或见到