---
layout: post
title: Android故障艺术
date: 2019/6/5
categories: Android, Notes
---

[Glitch art (故障艺术)](https://en.wikipedia.org/wiki/Glitch_art)，第一见的时候被吓到了，但之后越看越上瘾。

这篇笔记记录怎么在Andorid上用代码实现这玩意儿。

<!--more-->

## 不停抖动View

最简单的方案，通过叠加很多View，然后让它们各自向各个方向一直移动，就可以创建出类似于某音Logo的效果。

现成的：[Android-Glitch-Text-Effect](https://github.com/irshuLx/Android-Glitch-Text-Effect)。

## 随机修改JPEG

这个也简单。[aLibGlitch](https://github.com/BoD/aLibGlitch)就是这么做的。因为JPEG的压缩算法，只需要简单地替换一些bit，就可以让图片崩坏地很厉害。

## 对画布进行各种变换

在 https://github.com/chemickypes/Glitchy/blob/master/glitch/src/main/java/me/bemind/glitch/Glitcher.kt 考古到了几个变换Canvas的方法。没什么特殊的地方，就是发挥奇思妙想对Canvas进行各种修改。

<details>
  <summary></summary>
  <p>这个项目的代码是真的看得我头大。</p>
</details>

## 对像素点进行各种变化

在 https://github.com/g-whiz/pxSort 考古到了一些操作像素点的方法。应该是有数学公式之类在背后支撑的，但无奈它产出的效果不合我意，就懒得找了。注意各个效果的参数是在assets里的数据库里的。

## 参考

- [Android-Glitch-Text-Effect](https://github.com/irshuLx/Android-Glitch-Text-Effect)
- [aLibGlitch](https://github.com/BoD/aLibGlitch)
- [Glitchy](https://github.com/chemickypes/Glitchy)
- [pxSort](https://github.com/g-whiz/pxSort)

<details>
  <summary></summary>
  <p>Glitch Art似乎已经不像前几年那么火了，这些Repo也很久没维护了，用了各种奇技淫巧才让它们跑起来。</p>
</details>
