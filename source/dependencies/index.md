---
layout: post
title: Is the dependency safe to upgrade?
date: 2022/2/16
i18n_dir: :en
---

## Gradle

- 7.5.1: ✔
- 7.4.2: ✔
- 7.3.3: ✔
- 7.1.1: ✔

## Kotlin

- org.jetbrains.kotlin:kotlin

  - 1.7.20: ✔

    - `T & Any`
    - `min()` and `max()` throw exceptions if empty
    - `DeepRecursiveFunction`
    - [sam-with-receiver plugin](https://kotlinlang.org/docs/sam-with-receiver-plugin.html)

  - 1.6.21: ✔
  - 1.6.10: ✔

    - `buildList`, `buildSet`, and `buildMap`

  - 1.5.31: ✔

- [org.jetbrains.kotlinx:kotlinx-coroutines](https://github.com/Kotlin/kotlinx.coroutines/releases)

  - 1.6.4: ✔
  - 1.5.2: ✔

## Google

- com.android.tools.build:gradle(& Android Studio)

  - 7.3.1: ✔

    Since 7.1.3:

    - [Gradle Managed Devices](https://developer.android.com/studio/test/gradle-managed-devices)
    - [Jetifier warning and check in Build Analyzer](https://developer.android.com/studio/releases/gradle-plugin#jetifier-build-analyzer)
    - [Package attribute in manifest file is deprecated](https://developer.android.com/studio/releases/gradle-plugin#package-deprecated)

  - 7.2.2: ❌ Basically broken
  - 7.1.3: ✔
  - 7.0.4: ✔

- [androidx.appcompat:appcompat](https://developer.android.com/jetpack/androidx/releases/appcompat)

  - 1.5.1: ✔(Android 13(33))
  - 1.4.2: ✔
  - 1.4.1: ✔(Android 12(31))
  - 1.3.1: ✔

- [androidx.fragment:fragment](https://developer.android.com/jetpack/androidx/releases/fragment)

  - 1.5.5: ✔
  - 1.4.1: ✔(Android 12(31))

    - FragmentContainerView.getFragment

  - 1.3.6: ✔

- [androidx.lifecycle:lifecycle](https://developer.android.com/jetpack/androidx/releases/lifecycle)

  - 2.5.1: ✔
  - 2.4.1: ✔(Android 12(31))

    - ~~lifecycle-common-java8~~

  - 2.3.1: ✔

- [androidx.core:core-ktx](https://developer.android.com/jetpack/androidx/releases/core)

  - 1.9.0: ✔(Android 13(33))
  - 1.8.0: ✔
  - 1.7.0: ✔(Android 12(31))
  - 1.6.0: ✔

- [androidx.collection:collection-ktx](https://developer.android.com/jetpack/androidx/releases/collection)

  - 1.2.0: ✔

- [com.android.tools:desugar_jdk_libs](https://github.com/google/desugar_jdk_libs)

  - 2.0.0: 🚧(AGP 7.4)
  - 1.2.2: ✔(AGP 7.3, Android 13(33))
  - 1.1.5: ✔

    - `getDisplayName` doesn't work.

- [androidx.constraintlayout:constraintlayout](https://developer.android.com/jetpack/androidx/releases/constraintlayout)

  - 2.1.4: ✔

    - > Gone<->Visible components are now animated as expected

- [androidx.recyclerview:recyclerview](https://developer.android.com/jetpack/androidx/releases/recyclerview)

  - 1.2.1: ✔

- [androidx.navigation:navigation](https://developer.android.com/jetpack/androidx/releases/navigation)

  - 2.5.3: ✔
  - 2.4.2: ✔
  - 2.3.5: ✔

- [androidx.room:room](https://developer.android.com/jetpack/androidx/releases/room)

  - 2.4.3: ✔(Kotlin 1.7)
  - 2.4.1: ✔(Android 12(31))

    - Auto Migrations👍

  - 2.3.0: ✔

- [androidx.preference:preference](https://developer.android.com/jetpack/androidx/releases/preference)

  - 1.2.0: ✔
  - 1.1.1: ✔

- [androidx.work:work](https://developer.android.com/jetpack/androidx/releases/work)

  - 2.7.1: ✔(Android 12(31))
  - 2.6.0: ✔

- [androidx.startup:startup](https://developer.android.com/jetpack/androidx/releases/startup)

  - 1.1.1: ✔

- [androidx.security:security-crypto](https://developer.android.com/jetpack/androidx/releases/security)

  - 1.1.0-alpha03: ✔

- androidx.swiperefreshlayout:swiperefreshlayout

  - 1.1.0: ✔

- [androidx.exifinterface:exifinterface](https://developer.android.com/jetpack/androidx/releases/exifinterface)

  - 1.3.5: ✔

- [androidx.window:window](https://developer.android.com/jetpack/androidx/releases/window)

  - 1.0.0: ✔

- [com.google.android.material  :material](https://github.com/material-components/material-components-android/releases)

  - ❓ Versions afterward are too complicated to use

  - 1.4.0: ✔

- com.google.android.flexbox:flexbox

  - 3.0.0: ✔

- [com.google.dagger:dagger](https://github.com/google/dagger/releases)

  - 2.44.2: ✔
  - 2.42: ✔
  - 2.41: ✔
  - 2.40.5: ✔

- androidx.hilt:hilt-navigation-fragment

  - 1.0.0: ✔

- androidx.hilt:hilt-work

  - 1.0.0: ✔

- androidx.hilt:hilt-compiler

  - 1.0.0: ✔

- [com.android.billingclient:billing](https://developer.android.com/google/play/billing/release-notes)

  - 4.1.0: ✔
  - 3.0.2: ✔

- [com.google.android.gms:play-services-ads](https://developers.google.com/admob/android/rel-notes)

  - 21.4.0: ✔
  - 20.6.0: ✔

- [com.google.android.gms:play-services-auth](https://developers.google.com/android/guides/releases)

  - 20.2.0: ✔

## Third-party

- com.squareup.okhttp3:okhttp

  - 4.10.0: ✔
  - 4.9.3: ✔

- com.squareup.retrofit2:retrofit

  - 2.9.0: ✔

- [com.squareup.moshi:moshi](https://github.com/square/moshi/blob/master/CHANGELOG.md)

  - 1.14.0: ✔(Kotlin 1.7, Android 13(33))

    - No more `android.jetifier.ignorelist=moshi-1.13.0`

  - 1.13.0: ✔

    - Kotlin 1.6.0
    - AGP 7.0.x(and 7.1.x?): [`android.jetifier.ignorelist=moshi-1.13.0` in `gradle.properties`](https://github.com/square/moshi/issues/1463)

  - 1.12.0: ✔

- [com.squareup.okio:okio](https://github.com/square/okio/blob/master/CHANGELOG.md)

  - 3.2.0: ✔
  - 2.10.0: ✔

- com.squareup.leakcanary:leakcanary-android-startup

  - 2.10(Android 13(33)): ✔
  - 2.9.1: ✔

- com.jakewharton.timber:timber

  - 5.0.1: ✔

- [com.github.bumptech.glide](https://github.com/bumptech/glide/releases)

  - 4.14.2: ✔(Android 13(33))
  - 4.13.2: ✔
  - 4.12.0: ✔

- [com.airbnb.android:lottie](https://github.com/airbnb/lottie-android/releases)

  - 5.2.0: ✔
  - 5.0.3: ✔
  - 4.2.2: ✔

- com.github.chuckerteam.chucker:library

  - 3.5.2: ✔

- com.evernote:android-job

  - 1.4.2: ✔(Doesn't work on Android 12(31))

- pub.devrel:easypermissions

  - 3.0.0: ✔

- [com.mikepenz:fastadapter](https://github.com/mikepenz/FastAdapter/releases)

  - 5.7.0: ✔(Kotlin 1.7, Android 13(33))
  - 5.6.0: ✔(Android 12(31))
  - 5.5.1: ✔

- [org.greenrobot:eventbus](https://github.com/greenrobot/EventBus/releases)
  
  - 3.3.1: ✔
  - 3.2.0: ✔

- [net.lingala.zip4j:zip4j](https://github.com/srikanth-lingala/zip4j/releases)

  - 2.11.5: ✔
  - 2.10.0: ✔(UTF-8 Support)
  - 2.9.1: ✔

- [com.github.kizitonwose:CalendarView](https://github.com/kizitonwose/CalendarView/releases)

  - 1.0.4: ✔(java.time)
  - 0.3.5: ✔(ThreeTenABP)

- com.github.ben-manes:gradle-versions-plugin

  - 0.46.0: ✔

- [com.dropbox.dependency-guard:dependency-guard](https://github.com/dropbox/dependency-guard)

  - 0.3.2: ✔

- [com.facebook.android:facebook](https://github.com/facebook/facebook-android-sdk)

  - 13.2.0: !

    > The client token is now mandatory. SDK initialization will have a check on it and throw out an exception if the client token is not available.

  - 13.1.0: ✔

- [pl.droidsonroids.gif:android-gif-drawable](https://github.com/koral--/android-gif-drawable)

  - 1.2.25: ✔

## Chinese

- [com.tencent.bugly:crashreport](https://bugly.qq.com/docs/release-notes/release-android-bugly/)

  - 4.1.9: ✔
  - 4.0.4: ✔

    - Requires setting `deviceID`, `deviceModel`, `setIsDevelopmentDevice` and `closeNativeReport` manually

  - 3.4.4: ✔

- com.umeng.umsdk:common

  - 9.5.2: ✔
  - 9.4.7: ✔
  - 9.4.5, 9.4.6: ❌ broken
  - 9.4.4: ✔

- com.umeng.umsdk:asms

  - 1.6.3: ✔
  - 1.4.1: ✔

- com.umeng.umsdk:share

  - 7.2.0: ✔
  - 7.1.7: ✔

- [com.tencent.mm.opensdk:wechat-sdk-android-without-mta](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/Android.html)

  - 6.8.0: ✔

- [com.qiniu:qiniu-android-sdk](https://github.com/qiniu/android-sdk/releases)

  - 8.5.0: ✔
  - 8.4.4: ✔
  - 8.0.3: ✔(APK bloats afterwards)

- [com.meituan.android.walle:library](https://github.com/Meituan-Dianping/walle)

  - 1.1.7: ❌ no release notes
  - 1.1.6: ✔

- [com.alipay.sdk:alipaysdk-android](https://opendocs.alipay.com/common/02km9l)

  - 15.8.11: ✔

- [Q](https://wiki.connect.qq.com/sdk%E4%B8%8B%E8%BD%BD)[Q](https://wikinew.open.qq.com/#/iwiki/864512506)、穿山甲需要手动添加
