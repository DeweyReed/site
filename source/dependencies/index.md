---
layout: post
title: Is the dependency safe to upgrade?
date: 2022/2/16
i18n_dir: :en
---

## Gradle

- 7.3.3: ✔
- 7.1.1: ✔

## Kotlin

- org.jetbrains.kotlin:kotlin

  - 1.6.10: ✔

    - `buildList`, `buildSet`, and `buildMap`

  - 1.5.31: ✔

- org.jetbrains.kotlinx:kotlinx-coroutines

  - 1.6.0: ✔
  - 1.5.2: ✔

## Google

- com.android.tools.build:gradle

  - 7.0.4: ✔

- androidx.appcompat:appcompat

  - [1.4.1](https://developer.android.com/jetpack/androidx/releases/appcompat#1.4.1): ✔(Android 12(31))
  - 1.3.1: ✔

- androidx.fragment:fragment

  - [1.4.1](https://developer.android.com/jetpack/androidx/releases/fragment#version_14_2): ✔(Android 12(31))

    - FragmentContainerView.getFragment

  - 1.3.6: ✔

- androidx.lifecycle:lifecycle

  - [2.4.1](https://developer.android.com/jetpack/androidx/releases/lifecycle#version_24_2): ✔(Android 12(31))

    - ~~lifecycle-common-java8~~

  - 2.3.1: ✔

- androidx.core:core-ktx

  - [1.7.0](https://developer.android.com/jetpack/androidx/releases/core#core_and_core-ktx_version_17_2): ✔(Android 12(31))
  - 1.6.0: ✔

- androidx.collection:collection-ktx

  - [1.2.0](https://developer.android.com/jetpack/androidx/releases/collection#version_120_3): ✔

- [com.android.tools:desugar_jdk_libs](https://github.com/google/desugar_jdk_libs)

  - 1.1.5: ✔

    - `getDisplayName` doesn't work.

- androidx.constraintlayout:constraintlayout

  - [2.1.3](https://developer.android.com/jetpack/androidx/releases/constraintlayout#version_21_2): ✔

- androidx.recyclerview:recyclerview

  - [1.2.1](https://developer.android.com/jetpack/androidx/releases/recyclerview#version_121_3): ✔

- androidx.navigation:navigation

  - [2.4.x](https://developer.android.com/jetpack/androidx/releases/navigation#version_241_3): 🚧
  - 2.3.5: ✔

- androidx.room:room

  - [2.4.2](https://developer.android.com/jetpack/androidx/releases/room#version_242_3): ✔
  - 2.4.1: ✔(Android 12(31))

    - Auto Migrations👍

  - 2.3.0: ✔

- androidx.preference:preference

  - [1.2.0](https://developer.android.com/jetpack/androidx/releases/preference#version_12_2): 🚧
  - 1.1.1: ✔

- androidx.work:work

  - [2.7.1](https://developer.android.com/jetpack/androidx/releases/work#version_27_2): ✔(Android 12(31))
  - 2.6.0: ✔

- androidx.startup:startup

  - [1.1.1](https://developer.android.com/jetpack/androidx/releases/startup#version_111_3): 🚧
  - 1.1.0: ✔

- androidx.security:security-crypto:

  - [1.1.0-alpha03](https://developer.android.com/jetpack/androidx/releases/security#version_110_2): ✔

- androidx.swiperefreshlayout:swiperefreshlayout

  - 1.1.0: ✔

- com.google.android.material:material

  - [1.5.0](https://github.com/material-components/material-components-android/releases/tag/1.5.0): 🚧
  - 1.4.0: ✔

- com.google.android.flexbox:flexbox

  - 3.0.0: ✔

- com.google.dagger:dagger

  - 2.40.5: ✔

- androidx.hilt:hilt-navigation-fragment

  - 1.0.0: ✔

- androidx.hilt:hilt-work

  - 1.0.0: ✔

- androidx.hilt:hilt-compiler

  - 1.0.0: ✔

- [com.android.billingclient:billing](https://developer.android.com/google/play/billing/release-notes)

  - 4.0.0: ✔
  - 3.0.2: ✔

- [com.google.android.gms:play-services-ads](https://developers.google.com/admob/android/rel-notes)

  - 20.3.0: ✔

## Third-party

- com.squareup.okhttp3:okhttp

  - 4.9.3: ✔

- com.squareup.retrofit2:retrofit

  - 2.9.0: ✔

- [com.squareup.moshi:moshi](https://github.com/square/moshi/blob/master/CHANGELOG.md)

  - [1.13.0](https://github.com/square/moshi/blob/master/CHANGELOG.md#version-1130): ✔

    - Kotlin 1.6.0
    - AGP 7.0.x(and 7.1.x?): [`android.jetifier.ignorelist=moshi-1.13.0` in `gradle.properties`](https://github.com/square/moshi/issues/1463)

  - 1.12.0: ✔

- [com.squareup.okio:okio](https://github.com/square/okio/blob/master/CHANGELOG.md)

  - [3.0.0](https://github.com/square/okio/blob/master/CHANGELOG.md#version-300): ✔
  - 2.10.0: ✔

- com.squareup.leakcanary:leakcanary-android-startup

  - 2.8.1: ✔

- com.jakewharton.timber:timber

  - 5.0.1: ✔

- [com.github.bumptech.glide](https://github.com/bumptech/glide/releases)

  - 4.13.x: 🚧
  - 4.12.0: ✔

- [com.airbnb.android:lottie](https://github.com/airbnb/lottie-android/releases)

  - 5.0.x: 🚧
  - 4.2.2: ✔

- com.github.chuckerteam.chucker:library

  - 3.5.2: ✔

- com.evernote:android-job

  - 1.4.2: ✔(Doesn't work on Android 12(31))

- pub.devrel:easypermissions

  - 3.0.0: ✔

- [com.mikepenz:fastadapter](https://github.com/mikepenz/FastAdapter/releases)

  - 5.6.0: ✔(Android 12(31))
  - 5.5.1: ✔

- [org.greenrobot:eventbus](https://github.com/greenrobot/EventBus/releases)
  
  - 3.3.1: ✔
  - 3.2.0: ✔

- net.lingala.zip4j:zip4j

  - 2.9.1: ✔

- [com.github.kizitonwose:CalendarView](https://github.com/kizitonwose/CalendarView/releases)

  - 1.0.4: ✔(java.time)
  - 0.3.5: ✔(ThreeTenABP)

- com.github.ben-manes:gradle-versions-plugin

  - 0.42.0: ✔

## Chinese

- [com.tencent.bugly:crashreport](https://bugly.qq.com/docs/release-notes/release-android-bugly/)

  - 4.0.0: ❌ no release notes
  - 3.4.4: ✔

- com.umeng.umsdk:common

  - 9.4.7: ✔
  - 9.4.5, 9.4.6: ❌ broken
  - 9.4.4: ✔

- com.umeng.umsdk:asms

  - 1.6.0: ✔
  - 1.4.1: ✔

- [com.tencent.mm.opensdk:wechat-sdk-android-without-mta](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/Android.html)

  - 6.8.0: ✔

- [com.qiniu:qiniu-android-sdk](https://github.com/qiniu/android-sdk/releases)

  - 8.4.1: ✔
  - 8.4.0: ✔
  - 8.0.3: ✔(APK bloats afterwards)

- [com.meituan.android.walle:library](https://github.com/Meituan-Dianping/walle)

  - 1.1.7: ❌ no release notes
  - 1.1.6: ✔

- [Q](https://wiki.connect.qq.com/sdk%E4%B8%8B%E8%BD%BD)[Q](https://wikinew.open.qq.com/#/iwiki/864512506)、穿山甲、支付宝需要手动添加