---
layout: post
title: Android依赖笔记
date: 2022/6/2
categories: Android
---

整理自[The definitive guide to Android library development by Jeroen Mols, Plaid EN](https://www.youtube.com/watch?v=lq3PL8bHIn4)

<!--more-->

- 相比JAR（代码压缩包？），AAR还有Resources和Manifest，但二者都没有签名且不包括依赖。它们各自所需的依赖定义于pom文件中。

- 如果所需的依赖是本地文件，则需要方法来绕过限制：

    1. 把依赖发布到Maven
    1. FAR AAR插件
    1. 把依赖内容复制到Library中

- 限制多个依赖间的内部类

    1. `package com.example.internal.database`
    1. 混淆为`package com.example.internal.a`
    1. 把依赖内容复制到Library中

- Transitive Dependency及在pom文件中定义的依赖

    Grdle会默认选择所有版本最高的依赖。调整方法有

    1. 忽略依赖的Transitive Dependency

        ```Groovy
        implementation ('com.example:library:1.0.0') {
            exclude group: 'com.squareup.okhttp3', module: 'okhttp'
        }
        ```

    1. 全局指定依赖版本

        ```Groovy
        configurations.all {
            resolutionStrategy {
                force 'com.squareup.okhttp3:okhttp:3.12.0'
            }
        }
        ```

    这些方法可能会造成编译或运行时失败，尽量避免它们。

- 开发Library时，使用稳定版本（几个月到一两年）的依赖，最好不要更新。
- 在`res/values/public.xml`声明后，只是Android Studio不再自动补全了。

    要用：

    ```Groovy
    android {
        resourcePrefix 'mylib_'
    }
    ```

    来限定名称空间。不过只是让Android Studio警告一下。

- 设置为必须声明Class Visibility

    ```Groovy
    android {
        kotlinOptions {
            freeCompilerArgs += '-Xexplicit-api=strict'
        }
    }
    ```
