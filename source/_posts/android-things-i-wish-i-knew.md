---
layout: post
title: Android开发后悔的九件事
date: 2022/12/20
categories: Android
---

RT

<!--more-->

## 本应适度使用Material Components

虽说部分组件，诸如`MaterialButton`、`MaterialCardView`和`CoordinatorLayout`，自定义程度很高，用起来也很顺手。但另外一些，诸如`TabLayout`、`BottomAppBar`和`TopAppBar`等，需要诸多奇技淫巧才能符合UI。到头来，其实不如用`TextView`和`ImageView`从头攒一个，来得容易实现和维护。

更别提从Material 3开始，想要把背景颜色设置为一个特定值都变得困难起来。

## 本应谨慎使用Post

在`onCreate`或`onCreateView`中，所有View还未被定位和绘制。这时获取其宽度或高度会为0。一个常见的方法是将获取尺寸的代码放到一个`post {}`中（我最早从AdMob的早期文档中看到类似的用法）。大多数情况下，问题会得到解决。

但这会打开一个潘多拉魔盒。这个方案意味着每次修改`View`的属性后，想要得到修改后的状态，就得Post一次。逻辑复杂之后，代码库很快就会充斥着Post嵌套Post多次的糟糕实现。

根据[Understanding when and how Android Views have dimensions set](https://cheesecakelabs.com/blog/understanding-android-views-dimensions-set/)，`View.doOnLayout()`是比`Post`更好的方案。但这依然解决不了嵌套太多的问题。而最终解决方案则和下一条有关。

## 本应在View层少放业务逻辑

想法和MVI或Jetpack Compose类似，View收到的永远是UI的状态数据，而业务逻辑全都在ViewModel中。虽然只是把一个文件分为两个文件，但UI实现的复杂度会得到惊人的下降。配合`SavedStateHandle`，还可以存活过Configuration changes。

常见的Configuration changes不仅是旋转屏幕，还包括深色模式切换、时区变化、一些破手机切换个应用就会被杀等情况。这里还是要早做打算。

这样也可以缓解不少上一条Post嵌套太多的问题。

## 本应适度使用Single Activity

没人会告诉你，在面对一些核心操作时，Single Activity就力不从心了。

比如在Fragment中放相机预览，每次切换页面后返回时，相机都得重新启动一遍，使用体验很糟糕。

在一些应用核心操作时，最好单独使用一个Activity。这并不意味不需要控制好UI状态，只是不让这些核心状态太容易地被丢失。

## 本应细心规划Navigation

如果要在不同屏幕间做约定，最好在顶层设计一些通用抽象的功能。不然未来就看不懂代码了。

## 本应记录更多日志

面对一些棘手的Bug，在尝试各种方案的同时，要确保多留一些日志。

## 本应谨慎使用null

null代表的是不存在，而不是默认值。使用null就意味需要在之后不断地判断是否为null。

比如用户的主题设置怎么会不存在，最多也就是默认主题。对吧！

能不用null就不用。使用enum、sealed interface或者继承都会优雅的多。

## 本应注释废弃的功能

把废弃功能入口关掉就好，谁不知道未来会不会有用。

## 本应适度使用Singleton

这里不是用多了，而是用少了。

都怪Android一开始鼓吹不用Singleton，导致我尽可能用依赖注入的`@Resuable`来控制内存。但是这会让一些简单的操作都复杂起来。

既然一个对象从应用开始之初就会被分配并一直使用，就乖乖用Singleton。性能问题几乎永远不会在于几个Singleton。
