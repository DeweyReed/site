---
layout: post
title: Android开发的一些知识：在Android Studio点击Run之后
date: 2019/2/3
categories: Android
---

[Android开发的一些知识 - At a high level, what happens when you hit the build button on Andorid Studio?](https://aprildown.xyz/2018/12/31/android-requirements/)

<!--more-->

[课程: Gradle for Android and Java](https://cn.udacity.com/course/gradle-for-android-and-java--ud867)

Gradle已经提供了很多Automation相关的工作，Google就选它作为Android的build system。

Android Studio把所有任务都交给了Gradle来完成，自己只是一个wrapper。但Gradle并不了解Android，所以Google提供了android-gradle-plugin，简称AGP。

新建一个Empty Activity的Kotlin项目。

点击Run，发现运行了

`Executing tasks: [:app:assembleDebug]`

这里摘一些有用的Tasks:

```
> Transform ... // 首先是Jetifier和AAR transform
...
> Task :app:compileDebugAidl // 处理AIDL，用于IPC https://developer.android.com/guide/components/aidl
> Task :app:compileDebugRenderscript // 处理RenderScript https://developer.android.com/guide/components/aidl
> Task :app:checkDebugManifest // 检查Manifest
> Task :app:generateDebugBuildConfig // 生成BuildConfig
...
> Task :app:generateDebugResValues // 首先处理并生成资源Resources
> Task :app:generateDebugResources
> Task :app:createDebugCompatibleScreenManifests
> Task :app:mergeDebugResources
> Task :app:processDebugManifest // 处理Manifest
> Task :app:processDebugResources
> Task :app:compileDebugKotlin // 编译Kotlin
> Task :app:prepareLintJar
> Task :app:generateDebugSources
> Task :app:javaPreCompileDebug // 预编译Java
> Task :app:compileDebugJavaWithJavac // 编译Java
> Task :app:instantRunMainApkResourcesDebug
...
> Task :app:mergeDebugShaders // 继续处理资源，Shaders和Assets
> Task :app:compileDebugShaders
> Task :app:generateDebugAssets
> Task :app:mergeDebugAssets
> Task :app:validateSigningDebug // 签名
> Task :app:signingConfigWriterDebug
> Task :app:processInstantRunDebugResourcesApk
...
> Task :app:compileDebugNdk // NDK
> Task :app:mergeDebugJniLibFolders // JNI
> Task :app:transformNativeLibsWithMergeJniLibsForDebug
> Task :app:processDebugJavaRes // 处理Java Resources
> Task :app:transformResourcesWithMergeJavaResForDebug
> Task :app:transformNativeLibsAndResourcesWithJavaResourcesVerifierForDebug
> Task :app:transformClassesWithInstantRunForDebug
> Task :app:transformClassesAndClassesEnhancedWithInstantReloadDexForDebug
> Task :app:incrementalDebugTasks
> Task :app:preColdswapDebug
> Task :app:fastDeployDebugExtractor
> Task :app:generateDebugInstantRunAppInfo
> Task :app:transformClassesWithDexBuilderForDebug
> Task :app:transformDexArchiveWithExternalLibsDexMergerForDebug
> Task :app:transformDexArchiveWithDexMergerForDebug
> Task :app:transformDexWithInstantRunDependenciesApkForDebug
> Task :app:instantRunSplitApkResourcesDebug
> Task :app:transformDexWithInstantRunSlicesApkForDebug
> Task :app:packageDebug // 打包
> Task :app:buildInfoGeneratorDebug
> Task :app:compileDebugSources
> Task :app:assembleDebug
```

接下来会运行`installDebug`。就是简单地把apk push到设备上安装。

最后运行adb打开app的launcher Activity。
