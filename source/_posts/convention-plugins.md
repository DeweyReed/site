---
layout: post
title: 知Convention Plugins然且知Convention Plugins所以然
date: 2025/9/21
categories: Android
---

最近终于狠下心来，认真研究了Gradle Convention Plugins的Android项目实现。本文一行行地分析了其实现、原理与理由。如果只是上手，直接去扒[Now in Android的实现](https://github.com/android/nowinandroid/tree/main/build-logic)更为快捷。

<!--more-->

## 选择的理由

为了在多模块项目中共享Gradle配置，最直接的方式当然是———— ~~不用多模块！~~ 在根`build.gradle`中写`subprojects.afterEvaluate`。但时至今日，这种方法没有扩展度且显得很业余。

几年前就有很多人转向了[`buildSrc`](https://docs.gradle.org/current/userguide/sharing_build_logic_between_subprojects.html)作为项目构建最前期的运行内容，但是这个方案性能差：类似问题在**重要内容养大象**[Herding Elephants](https://developer.squareup.com/blog/herding-elephants/#from-buildsrc-to-build-logic)亦有记载，而且限制多：实际上手后会发现各种依赖不兼容问题。虽然[最近的Gradle的教程](https://www.youtube.com/playlist?list=PLLQbIfXVLZqG3wNV9vKDr8Mqv8TvogykN)提到该方法适用于中小项目，但既生瑜何生亮。

后来[Herding Elephants](https://developer.squareup.com/blog/herding-elephants/)横空出世，似乎有承前启后之作用。它提到了使用`build-logic`。一句题外话，我虽然一直有订阅Square的博客，并在这篇文章发布时，在第一时间也看过其内容，但由于其博客内容大多抽象，所以一直未能领略其精髓。

随着Kotlin DSL和Version Catalog推出，Convention Plugins成了管理配置的最佳实践。这也是开头推荐直接抄[Now in Android的实现](https://github.com/android/nowinandroid/tree/main/build-logic)的原因。

联想到当年Uncle Bob提出Clean Architecture，Android开发的一些最佳实践或默认方案总有一些前人栽树之意味，而这棵树也是充满了前人之个性。

## 前期的准备

Gradle太灵活了，[一个问题有八种解决方法](https://docs.gradle.org/current/userguide/implementing_gradle_plugins.html)。考虑到[Pre-compiled script plugin性能差](https://github.com/android/nowinandroid/issues/39)且Kotlin真好用的情况，我们直接使用最好的Kotlin + Version Catalog + Binary plugin。这意味着在实现前，要先把Gradle迁移到Kotlin DSL。

好在`build.gradle.kts`的重写可以一文件一个文件地进行，所以先把文件语法优化，再修改后缀，然后解决报错，最后Sync。如此这般不断重复。

## 实现的细节

我们以[Now in Android的实现](https://github.com/android/nowinandroid/tree/main/build-logic)为基础来一点点讲解。

我们先从根目录的[`settings.gradle.kts`](https://github.com/android/nowinandroid/blob/main/settings.gradle.kts)开始：

```Kotlin
pluginManagement {
    includeBuild("build-logic")
    repositories {
```

这里要在`pluginManagement`下`includeBuild`，说明我们要写Plugin。这与另一个可顶层调用的方法`includeBuild`是不同的。其他内容原封不动。

接下里的内容都在`build-logic`目录中，打包地很好。

从[build-logic/settings.gradle.kts`](https://github.com/android/nowinandroid/blob/main/build-logic/settings.gradle.kts)开始：

```Kotlin
pluginManagement {
    ...
}
```

这一部分对于大多数项目是多余的。之所以这里有一块，是因为nia额外用到了一个Plugin。之后会讲。

接下来的内容：

```Kotlin
dependencyResolutionManagement {
    repositories {
        google {
            content {
                includeGroupByRegex("com\\.android.*")
                includeGroupByRegex("com\\.google.*")
                includeGroupByRegex("androidx.*")
            }
        }
        mavenCentral()
    }
```

这里的`dependencyResolutionManagement.repositories`中内容应修改为和根目录的[`settings.gradle.kts`](https://github.com/android/nowinandroid/blob/main/settings.gradle.kts)中的`pluginManagement.repositories`一致，因为我们要引用Plugin作为依赖，或者说，Convention Plugins中，我们要使用Plugin的代码而非其实现。对于常见的三个Plugin来源：`google`、`gradlePluginPortal`和`mavenCentral`，有的插件会出现在其中某一个或某两个中，所以一致的配置确保我们可以找到同样的内容。

接下来：

```Kotlin
    versionCatalogs {
        create("libs") {
            from(files("../gradle/libs.versions.toml"))
        }
    }
}

rootProject.name = "build-logic"
include(":convention")
```

该内容是在[Gradle的Version Catalog的文档中](https://docs.gradle.org/current/userguide/version_catalogs.html#sec:buildsrc-version-catalog)提及的。从`buildSrc`到`build-logic`的实现，最简单的情况只需要改一下文件夹名称，因此这里Version Catalog的声明可以被共享也不足为奇。

在[`build-logic/gradle.properties`](https://github.com/android/nowinandroid/blob/main/build-logic/gradle.properties)中：

```Kotlin
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.configureondemand=true
org.gradle.configuration-cache=true
org.gradle.configuration-cache.parallel=true
```

`org.gradle.configureondemand=true`：这么多年了还在测试，而且我还能记得它当年跟Instant Run闹了不小的冲突。我推荐还是删了这行吧。

`org.gradle.configuration-cache.parallel=true`：死活没找到文档。处于安全起见，建议先删掉。

其他内容照抄。

接下来是[`build-logic/convention/build.gradle.kts`](https://github.com/android/nowinandroid/blob/main/build-logic/convention/build.gradle.kts)：

```Kotlin
plugins {
    `kotlin-dsl`
    alias(libs.plugins.android.lint)
}
```

第二个Plugin就是那个导致要在[`build-logic/settings.gradle.kts`](https://github.com/android/nowinandroid/blob/main/build-logic/settings.gradle.kts)声明`pluginManagement`的Plugin。考虑到大多数项目不会用，这里就删掉好了。如果要使用其他Plugin，那在`settings.gradle.kts`就要写好对应的`pluginManagement.repositories`，写法和普通Plugin一样。

接下来：

```Kotlin
group = "com.google.samples.apps.nowinandroid.buildlogic"
```

虽说声明`group`是个好习惯，但我至今也没搞明白不这么做会有什么后果。

继续：

```Kotlin
java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}

kotlin {
    compilerOptions {
        jvmTarget = JvmTarget.JVM_17
    }
}
```

既然有了Version Catalog，那我就不允许版本号出现在这里，这里修改为使用Version Catalog的风格：

首先在Version Catalog中增加一个版本号：

```toml
jvmTarget = "17"
```

然后：

```Kotlin
java {
    val javaVersion = JavaVersion.toVersion(libs.versions.jvmTarget.get())
    sourceCompatibility = javaVersion
    targetCompatibility = javaVersion
}

kotlin {
    compilerOptions {
        jvmTarget = org.jetbrains.kotlin.gradle.dsl.JvmTarget
            .fromTarget(libs.versions.jvmTarget.get())
    }
}
```

接下来：

```Kotlin
dependencies {
    compileOnly(libs.android.gradleApiPlugin)
    compileOnly(libs.android.tools.common)
    compileOnly(libs.compose.gradlePlugin)
```

这里写明我们在Convention Plugins中会用到的Plugin。之所以是`compileOnly`，我猜是因为Plugin我们这里只需要代码，具体的运行是在项目各Module中的。

这里专门把Plugin的依赖再在Version Catalog中写一遍就不漂亮了。在[Gradle的Version Catalog的文档中](https://docs.gradle.org/current/userguide/version_catalogs.html#sec:buildsrc-version-catalog)，我们可以找到不用再写一遍的方法。这里稍加修改成为：

```Kotlin
dependencies {
    compileOnly(plugin(libs.plugins.kotlin.android))
    compileOnly(plugin(libs.plugins.android.application))
    compileOnly(plugin(libs.plugins.android.library))
}

private fun plugin(plugin: Provider<PluginDependency>): Provider<String> {
    return plugin.map { "${it.pluginId}:${it.pluginId}.gradle.plugin:${it.version}" }
}
```

这样就不用专门为了Convention Plugins而修改Version Catalog的结构了。

然后就是照猫画虎了。从Plugin的实现到注册，都可以根据自己偏好删删改改了。

```Kotlin
gradlePlugin {
    plugins {
        register("androidApplicationCompose") {
            id = libs.plugins.nowinandroid.android.application.compose.get().pluginId
            implementationClass = "AndroidApplicationComposeConventionPlugin"
        }
    }
}
```

之所以用`register`而不用`create`是因为其方法文档中提到前者懒加载，后者总是加载。

## 未解的疑问

- 为什么Plugin是在根目录完成的呢？[Implementing Binary Plugins](https://docs.gradle.org/current/userguide/implementing_gradle_plugins_binary.html)明明是在对应包名下完成的。不过似乎没什么影响？
- 为什么要叫做`build-logic`呢？该不会因为养大象的文章中就是这么称呼的？
- 一个功能的实现散落在Gradle文档网站的数个页面中，阅读体验很差。
- 虽说`gradlePluginPortal`可以方便Plugin管理，但现在感觉情况更混乱了。
- 就像当年发布第三方Library时，大伙儿都会到处抄袭一些没人能懂但能跑的起来的Maven发布代码，如今的Convention Plugins看起来也是如此，尤其在AI都不知道最佳实践，只能乱生成代码的阶段。
