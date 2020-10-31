---
layout: post
title: Android故障艺术
date: 2019/6/5
categories: [Android, Notes]
---

**2020年10月16日更新：增加了更多资料**

[Glitch art (故障艺术)](https://en.wikipedia.org/wiki/Glitch_art)，第一见的时候被吓到了，但之后越看越上瘾。

这篇笔记记录怎么在Andorid上用代码实现这玩意儿。

<!--more-->

## 不停抖动View

最简单的方案，通过叠加很多View，然后让它们各自向各个方向一直移动，就可以创建出类似于某音Logo的效果。

现成的：[Android-Glitch-Text-Effect](https://github.com/irshuLx/Android-Glitch-Text-Effect)。

## 随机修改JPEG

这个也简单。[aLibGlitch](https://github.com/BoD/aLibGlitch)就是这么做的。因为JPEG的压缩算法，只需要简单地替换一些bit，就可以让图片崩坏地很厉害。

[SlideToGlitch](https://github.com/shubhamvernekar/SlideToGlitch)是另外一个有类似算法的项目。受到了[jpg-glitch](https://github.com/snorpey/jpg-glitch)这个web项目的启发。

这个可以作为应用内的一个转场动画，如果不担心被吓到的用户打差评的话。

## 对画布进行各种变换

在 https://github.com/chemickypes/Glitchy/blob/master/glitch/src/main/java/me/bemind/glitch/Glitcher.kt 考古到了几个变换Canvas的方法。没什么特殊的地方，就是发挥奇思妙想对Canvas进行各种修改。

<details>
  <summary></summary>
  <p>这个项目的代码是真的看得我头大。</p>
</details>

## 对像素点进行各种变化

在 https://github.com/g-whiz/pxSort 考古到了一些操作像素点的方法。应该是有数学公式之类在背后支撑的，但无奈它产出的效果不合我意，就懒得找了。注意各个效果的参数是在assets里的数据库里的。

## OpenGL

最近发现了[高品质后处理：十种故障艺术(Glitch Art)算法的总结与实现](https://zhuanlan.zhihu.com/p/148256756)，是针对Unity的各种故障效果，是通过OpenGL实现的。Android也支持OpenGL，懂OpenGL的可以手动把代码复制修改后使用。拿最简单的RGB Split Glitch来说，在搭配[android-gpuimage](https://github.com/cats-oss/android-gpuimage)后，只需要改几个名称就可以放到应用里了。
