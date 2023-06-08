---
layout: post
title: 一些可能有用的Android代码 - Android Makers 2022版
date: 2022/6/25
categories: Android
---

[Android Makers 2022](https://www.youtube.com/playlist?list=PLn7H9CUCuXAuLnmPD-wsljzYxHt3UD5zn)

<!--more-->

## buildSrc Deprecated

对编译速度有影响，所以过气了。换用Version Catalogs。

## Single-variant Libraries

```Kotlin
androidComponents {
    beforeVariants(selector().withBuildType("debug")) { builder ->
        builder.enable = false
    }
}
```

## Disable Android Features

```Groovy
# Build features that are disabled by default in all projects
android.default.buildFeatures.aidl=false
android.default.buildFeatures.buildconfig=false
android.default.buildFeatures.renderscript=false
android.default.buildFeatures.resvalues=false
android.default.buildFeatures.shaders=false
```

## TaskUpToDateValidator.kt

[TaskUpToDateValidator.kt](https://github.com/androidx/androidx/blob/androidx-main/buildSrc/private/src/main/kotlin/androidx/build/uptodatedness/TaskUpToDateValidator.kt)

## modules-graph-assert

<https://github.com/jraska/modules-graph-assert>

## 文件保存

{% asset_img save_files.png [save_files.png](./android-code-snippets-android-makers22/save_files.png) %}
