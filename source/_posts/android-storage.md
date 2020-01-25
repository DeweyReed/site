---
layout: post
title: Android储存方案研究及分析
date: 2020/1/25
categories: Android
---

自从Android Q引入了Scoped Storage，储存问题就像在Kotlin Coroutines之前的Android多线程，八仙过海，各显神通，而且每种方案的都有其长处和短处，Android也有了~~一万~~多种储存方案。那么在Google决定开发一个Android StorageX来解决这个问题之前，我自己先来捋一捋，正好最近[关于储存的官方文档](https://developer.android.com/guide/topics/data)也更新了。

本文只考虑棒棒糖之后的设备。因为`SharedPreference`和`Sqlite(Room)`没啥好说的，本文主要研究`MediaStore`、`File`和`SAF`。

<!--more-->

## `File`

指的是用于内部储存的`getFilesDir()`和`getCacheDir()`和外部的`getExternalFilesDir()`和`getExternalCacheDir()`，不要权限，卸载后会消失。

除了直接用`File`的API外，还可以用`Context`的`context.openFileOutput(filename, Context.MODE_PRIVATE)`及其其他方法来达到同样的效果（虽然我觉得后者挺难用而且有点坑）。

## `MediaStore`

> READ_EXTERNAL_STORAGE or WRITE_EXTERNAL_STORAGE when accessing other apps' files on Android 10 (API level 29) or higher
>
> Permissions are required for all files on Android 9 (API level 28) or lower

官方文档中说，用这个API(`MediaStore` + `ContentResolver`)来读写三种内容：图片、音频和视频。Q之前必须要权限，Q及之后读别人的文件才要权限。

这句话意味着Q及之后，用这个方法**写入**和**读取自己的写入的文件(Uri)**是**不需要权限**的，测试结果也的确是这样的。

这里有个问题，Q及之后是可以通过`RELATIVE_PATH`把图片保存进相册文件夹中的，但在之前是不能的，这时只能用已被废弃的硬编码 + `File`来储存了。

此外有一个`MediaStore.Downloads`，只针对Q及之后，但似乎没啥用？

此外有一个`MediaStore.Files`，Q之后显示应用创建的图片、音频和视频。

总结一下，Q之前需要读写外部需要权限，Q及之后如果只是读写自己的内容，完全可以不要权限啦。

## SAF

上述未包含的其他文件，不需要权限，读写单个文件很方便，[文档里很清楚](https://developer.android.com/training/data-storage/shared/documents-files)。

## 常见用法

这里列举一些我常用到的情况。

### 导出/导入一个备份文件

SAF。导出时还能让用户命名和选择位置。不要权限。

### 分享一个文件

`FileProvider` + `FLAG_GRANT_READ_URI_PERMISSION`导出为Uri，然后用`ShareCompat`分享。文件放内部储存居然也可以。

但是呢，如果项目中用了一些撒币的第三方分享库，而这些垃圾玩意儿只支持分享`File`和路径，这就有点麻烦了。解决方案有：

1. 把文件放到应用的外部私有文件夹中，然后把`File`或路径交给那些没有实现Scoped Storage的应用。但这意味着未来某个Android版本如果强制要求Scoped Storage，又得改代码。
1. 如果文件是图片的话，可以考虑分享一张小尺寸的Bitmap。
1. 偷梁换柱。依然使用`FileProvider`生成的Uri搭配`ShareCompat`分享，但设置`Intent`的package为对应应用的包名，如果解析Activity后没有对应的可启动Activity，就清除package后再启动分享选择器。但这么做难以处理某个应用的多种分享方式，比如微信分享和朋友圈分享，要么自己去找对应的Activity名字并承担可能失效的后果，要么只能用对应应用的难用且坑多分享库了。

### 分享一个`Bitmap`

把`Bitmap`保存到文件里，再[分享一个文件](#分享一个文件)。

### 选择音乐、图片

SAF可以选单个文件，不要权限。选多个文件或者要自定义UI的话，还是乖乖`MediaStore` + 读取权限吧。
