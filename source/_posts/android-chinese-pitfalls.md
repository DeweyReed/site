---
layout: post
title: 在国内做Android开发踩过的坑
date: 2019/8/25
categories: Android
---

曾经怀疑过很多次是不是自己的代码有问题，到头来全是各厂商的魔改惹的祸。

<!--more-->

## [android-in-chia/Compatility](https://github.com/android-in-china/Compatibility/issues)

前人踩过的坑，以此祭天。

## LeanCloud混淆规则

官网的当然是keep所有喽，但我发现使用

```Proguard
-dontwarn com.avos.**
-dontnote com.avos.**
-keepnames class com.avos.** { *;}
```

似乎并不影响。

## fastjson混淆规则

这里指的是Java版并不是fastjson-android版。

官网的当然是。。没有啦，所以民间流传的当然是keep所有喽，但我发现使用

```Proguard
-dontwarn com.alibaba.fastjson.**
-dontnote com.alibaba.fastjson.**
```

似乎并不影响。

## 抖音SDK没有混淆规则

它们的“开放”平台没有混淆规则，但不加混淆会调用失败，只能keep所有了。感兴趣的可以试一试`-keepnames`。

## QQ钱包的Android SDK接入

[官网的文档](https://qpay.qq.com/buss/wiki/99/1233)有错。

参数`serialNumber`为`null`或为空会导致失败，而服务端又没有提供，但填个任意字符串（我用了当前时间）就能调用了。

另外，在它们的SDK里发现了这个：

```Java
} else if (TextUtils.isEmpty("native")) {
    return false;
} else if ("native".compareTo("native") != 0) {
    return false;
```

看不懂有啥用，希望哪位大佬点拨一下。

另外的另外，它们的SDK也应该没提供混淆，只能惯例keep所有。

## 友盟Share SDK

QQ配置文档有错。用了AAR的依赖后，不需要修改Manifest，而要在app module中添加

```Groovy
android {
    defaultConfig {
        ...
        manifestPlaceholders = [qqappid: 'appid']
    }
```

另外，不需要分享的回调结果时，就不需要在`onActivityResult`调用它们的API的。

## `BitmapFactory`返回旧图像

在一些特定的设备（只在华为设备上遇到过，其他未知）上，对一些特定尺寸的图片（640 * 640），使用`BitmapFactory.decodeFile/Stream`等类似方法解码`Bitmap`时，返回的不是此时此刻文件里的图片，而是上一次保存的图片。

最后换了`Glide`加载图片解决的。简单看了下源码，`Glide`似乎是用`ByteBuffer`加载本地图片的。

此问题的发生原因和解决方法暂时未知。

## The End

> 你觉得我喜欢去研究这些东西嘛？
