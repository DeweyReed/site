---
layout: post
title: Android动态主题
date: 2018/9/14
categories: Android
---

2018年12月1日 更新Cyanea

不是给用户几个主题选择的多主题，而是可以让用户选择任意颜色，这让难度上了一档次，尤其是想用一种不那么侵入的方法实现的话。

从主题的实现来分，主要分依赖Android和自己动手。但这里按实现难度排序。

<!--more-->

2018年12月1日 更新Cyanea

发现了[Cyanea](https://github.com/jaredrummler/Cyanea)，也是可以动态颜色，黑科技，效果极佳，除了不仅需要代码还需要很多styles。

初步看了一下代码，它不像Aesthetic一样拦截View创建过程，而是拦截Resources的getColor和getColorStateLlist返回自定义颜色，并使用自定义的LayoutInflater，在View创建之后，使用反射给View的ColorStateList修改颜色。这样，一些直接着色很困难的View（说的就是TimePicker）也可以一并被着色。

好强啊。

【完】2018年12月1日 更新Cyanea

## 幼儿园难度 - 着色次数少的动态主题

像`网易云音乐`这种，除了标题栏有主题色外，用到主题色的地方基本没有。只需要判断一下颜色亮暗，修改一下标题栏图标色和背景色就可以啦。

优点：简单到做这种应用的开发者都用不着找资料

缺点：使用体验很一般

## 简单难度 - 预设的多主题

这个也很容易，只需要把预设的主题放到`styles.xml`中，然后在`onCreate`的`super`前调用`setTheme`，让Android框架自己处理各种主题颜色即可。[`Colorful`](https://github.com/garretyoder/Colorful)就是这么做的，它的`styles.xml`中存放了很多很多预设颜色和它们的组合。

优点：

- 简简单单
- 所有用到的地方都能着色（跟下一方案相比尤其重要）

缺点：除了不能让用户随便选，没有缺点

## 正常难度 - 手动着色的动态主题

像`酷安`这种，对每个用到主题色的地方进行着色。

这里可以对需要用的主题色的`View`新建一个子类，创建时着色。[`Aesthetic`](https://github.com/afollestad/aesthetic)就是这么做的，而且它机智地使用了`LayoutInflaterCompat.setFactory2`来偷梁换柱，把每个`View`都换成可着色的子类。

优点：用户体验++
缺点

- 实现复杂，现有的Library(`Aesthetic`)都有点问题。
- 有的`View`不能被正常着色，比如`CoordinatorLayout`跟它的子子孙孙们，有不少坑，有的还得反射才行。

## 老手难度 - 动态主题 + 主题包

`Telegram`啦。它是自己动手，丰衣足食。

这种方案不仅可以自定义颜色，还可以使用别人的主题包。

有个现成的框架`Android-skin-support`，也是用`setFactory`实现的，不过没有用过，使用体验不知如何。

优点：用户体验#

缺点：

- 主题包主要是给社交软件用的，而且不是必备功能，因此使用面较窄
- 实现起来很繁琐

## 总之

要么应用很简单，要么只提供预设主题，否则想要实现动态主题，此时此刻并不轻松。没有现成的壮硕的Library能用，意味着自己的造轮子。比如`Aesthetic`现在还有一大堆Bug。

## 夜间模式

我倾向于将夜间模式和主题分开，两者互不影响。

夜间模式也有两种实现方式：

1. 同样使用`setTheme`，把什么`windowBackground`都手动设置。
1. 推荐的是技能`DayNight`style，并使用`AppCompatDelegate.setDefaultNightMode`调用`res-night`下的资源，基本上属于support library白给的夜间主题。
