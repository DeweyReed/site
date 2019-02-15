---
layout: post
title: 在Android Studio中引入AAR文件
date: 2018/8/13
categories: Android
---

## 2019年2月10日 更新

发现一个更好的方案：不管是自己的项目还是Fork来的项目，直接到Jitpack里找到对应的commit，拉下来直接用。这样就省去了很多体力劳动。而且这个过程并不需要配置Gradle，源码都自带。【完】

任务很简单，最小程度影响当前项目的前提下，导入一个AAR第三方库。

要解决的问题在于

1. 按导入`.jar`的方法导入`.aar`会失败
1. 使用Android Studio自带的`Improt Module`中的导入AAR会创建一个新的Module，而我们只想声明一个依赖

<!--more-->

## 这么做的优点

1. Module该有好处自然有的了
1. 不需要复杂漫长地发布到JCenter等地方，才能使用依赖
1. 修改后直接复制粘贴AAR就可使用

## 缺点也是明显的

1. 不要求发布，就不适用多人协作的大项目，电脑GG后源代码也会丢失
1. 不带注释，需要手动选择代码目录，有时候选择还会失效
1. 修改后复制粘贴AAR才能使用
1. AAR的版本被保存在了另一个项目中
1. AAR的依赖需要手动添加到项目的依赖中
1. AAR定义的Annotation，比如IntDef，需要手动在Proguard `-dontwarn`
1. 似乎并不能跨module使用

~~缺点比优点多可还行~~

个人习惯：

- 把名字改为library名+git commit编号
- 另建一个文件夹专门放各种AAR
- 把修改的项目丢到Github或GitBucket
- 在版本控制中删掉所有AAR

## 流程

### 1. 一个有正常的Library Module的Android项目

新建一个项目，带上需要的Library，或最简单的就是直接clone一个现有的依赖。

### 2. 生成AAR文件

使用`./gradlew assemble`或者Android Studio中, 点击Gradle面板/你的library/Tasks/build/assemble

我在使用中，单纯地运行应用并不一定能生成AAR。

### 3. 定位AAR文件

在项目/library/build/outputs/aar/中找到生成的AAR文件，给`library-release.aar`（谁能解释一下它和`library-debug.aar`除了名字不一样有什么区别？字节数都一样）改个名字，拉出来。

最好在文件名后加一个tag或版本号，确保以后更新时Sync Gradle可以成功更新代码库。

### 4. 把AAR复制到所需项目

复制到项目/app/src/main/libs/下，可能需要新建一个`libs`文件夹（文件名可以随便起）。

### 5. 在Gradle中添加目录

首先修改项目根目录主`build.gradle`，在`allprojects/repositories`(不写classpath的那个区域里)下添加

```Groovy
flatDir {
     dirs 'src/main/libs'
}
```

注意把`libs`替换为你那奇思妙想的新名字。

### 在Dependencies中引用AAR

添加`implementation(name:'library-name', ext:'aar')`

注意`name`中不带后缀。

Ta-Da。完成！

参考:

[How to Include an External .aar File Using Gradle?](https://medium.com/@notestomyself/how-to-include-external-aar-file-using-gradle-6604b378e808)