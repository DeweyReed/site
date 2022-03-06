---
layout: post
title: Gradle缓存笔记
date: 2022/3/6
categories: Android
---

来自于：

- [Understanding Different Gradle Caches for Android Projects, part 1.](https://jasonatwood.io/archives/1966)
- [Understanding Different Gradle Caches for Android Projects, part 2](https://jasonatwood.io/archives/1995)
- [Understanding Different Gradle Caches for Android Projects, part 3](https://jasonatwood.io/archives/2127)

<!--more-->

[Build]-[Clean Project]和`gradlew clean`都是仅删除根目录和Module目录下的`build`文件夹。`build`文件夹是Gradle自带的、默认的缓存策略。

Build Cache（现在默认关闭，通过`--build-cache`或`org.gradle.caching=true`打开），使用了默认单项目缓存一样的机制，会把部分缓存保存到`~/.gradle/caches/build-cache-*`，机器上所有项目可共享。这也就是为什么Dagger偶尔失效时，需要删除系统Gradle缓存的原因。

- 文中提到`R.java`，但记得在某个AGP版本中，文件已经变成`jar`，轻易找不到了。
- `kapt`的`useBuildCache`[在某个版本中默认开启了](https://docs.gradle.org/current/userguide/caching_android_projects.html#annotation_processors_and_kotlin)。[关闭文档](https://kotlinlang.org/docs/kapt.html#gradle-build-cache-support)。

Gradle Deamon默认开启。

依赖储存在`~/.gradle/caches/*`中。其中`transform-*`保存了本地修改后的依赖，非下载内容。

Invalidate Caches / Restart只清理IDE缓存，不清理上述缓存。

`android.enableBuildCache`在AGP 7中被弃用。

`org.gradle.unsafe.configuration-cache=true`实验中的Configuration Cache，期待正式版。缓存储存在`[PROJECT_ROOT]/.gradle/configuration-cache`中。

还有一些[各种属性的默认值](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties)。
