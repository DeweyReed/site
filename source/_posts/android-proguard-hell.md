---
layout: post
title: Android Proguard Hell
date: 2018/12/16
categories: Android
---

源自于无法忍受LeanCloud的Keep所有类的Proguard规则，想要精简一下APK而摸了Proguard的底。

<!--more-->

Proguard基本上有个任务，精简（删除），混淆，优化。

## 规则

- -keep 不精简且不混淆这个类和它的成员，“别摸这个类”

    ```Proguard
    -keep class android.support.annotation.Keep
    -keep class androidx.annotation.Keep

    -keep @android.support.annotation.Keep class * {*;}
    -keep @androidx.annotation.Keep class * {*;}
    ```

- -keepclasseswithmemebers 效果跟上一条一致，但可以更具体地声明包含某种成员的类。

    ```Proguard
    -keepclasseswithmembers class * {
        @android.support.annotation.Keep <methods>;
    }

    -keepclasseswithmembers class * {
        @androidx.annotation.Keep <methods>;
    }

    -keepclasseswithmembers class * {
        @android.support.annotation.Keep <fields>;
    }

    -keepclasseswithmembers class * {
        @androidx.annotation.Keep <fields>;
    }

    -keepclasseswithmembers class * {
        @android.support.annotation.Keep <init>(...);
    }

    -keepclasseswithmembers class * {
        @androidx.annotation.Keep <init>(...);
    }
    ```

- -keepclassmembers -keep针对某个类，这个针对某个成员，效果一致（不精简且不混淆）。“别摸这个类的这个成员”

    ```Proguard
    -keepclassmembers enum * {
        public static **[] values();
        public static ** valueOf(java.lang.String);
    }

    -keepclassmembers class * implements android.os.Parcelable {
        public static final ** CREATOR;
    }

    -keepclassmembers class **.R$* {
        public static <fields>;
    }

    # Preserve annotated Javascript interface methods.
    -keepclassmembers class * {
        @android.webkit.JavascriptInterface <methods>;
    }
    ```

- -keepnames 只精简不混淆

- -keepclasseswithmembernames 效果跟上一条一致，但可以更具体地声明包含某种成员的类

- -keepclassmembernames 只针对这个类的这个成员，只精简不混淆。不影响这个类。

## 输出

seeds.txt：第一步(Read Input)的产物。内容是所有匹配proguard-rules的类和成员。是Proguard没有摸过的内容。

usage.txt：第二步(Shrink)的产物。在知道不能摸的内容后，这里是所有Proguard摸过的内容。

mapping.txt：第三步(Obfuscate)的产物，记录了Proguard怎么摸的。是一个混淆前后的对应表。

dump.txt：第四步(Dump)的产物，Progurad处理后的，未压缩或未优化的结果。体量巨大，但可以用于不反编译APK就可以看到APK的内容。

## 参考

1. [Shrink your code and resources](https://developer.android.com/studio/build/shrink-code)

1. [Troubleshooting ProGuard issues on Android](https://medium.com/google-developers/troubleshooting-proguard-issues-on-android-bce9de4f8a74)

1. [Reading ProGuard’s Outputs](https://jebware.com/blog/?p=484)

1. [Feeding ProGuard’s inputs: where are all of these rules coming from?](https://jebware.com/blog/?p=498)

1. [How Proguard Works](https://www.youtube.com/watch?v=F9ymcWoDEtc)