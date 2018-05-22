---
layout: post
title: Material Design 笔记
date: 2018/5/22
categories: Android
tags: 可能维护
---

**不保证全对、可能更新**

## Environment

### Surfaces

各个元素的厚度都是相同的，1dp，白色，自带一个阴影。但可以通过不同elevation和shadows表示高度。

- Elevation和Shadows: Elevation指的是元素的高度；后者指的是阴影大小。原则是，高度越大，阴影越大。

元素的分辨率应为无限 => 使用矢量图

在Surfaces上显示的内容只要不超出边界，厚度为0，随意使用。

同一时间这能操作一个Surface。多个Surfaces重叠时，使用不同的elevation分别。

Surface继承于一张纸: 

- 不是气体或液体，不能穿透。可以规则变形（通过折纸）、可以旋转或移动（动画要协调、用于容纳内容或其他目的）
- 可以改变透明度、长宽可变、可以分割或合并
- 不可以卷角
- 可以凭空出现或消失（魔法）
- 点击时，向上升起(elevation++, shadows++)迎接点击。

### Elevation && Depth

所有元素都有elevation，值越大，内容越重要。常见的有：Dialog 24dp, Drawer 16dp, App Bar 4dp, FAB 6dp

不同情况和布局，元素的elevation可变。比如点击Button 2dp => 8dp，Fab 6dp => 12dp。

为了解决elevation变化时，不同元素的重叠问题，被重叠的元素可以暂时消失（实现起来复杂了，这类行为尽可能减少）。

Elevation也被用作不同Surfaces的分割，也可以使用颜色或不透明度。

[各元素默认elevation](https://material.io/design/environment/elevation.html#default-elevations)

### Shadows

分为点光源（Key Light）和平行光源（Ambient Light），结合起来使用。

## Typography

- Typeface和Font：前者可以看成一种风格，后者是一种具体字体。
- Sans Serif和Serif：是两种typeface。Serif译作衬线，比如`I`，有衬线就是上下有横杠。`sans`是无的意思，非衬线体，`I`就一竖杠。
- Monospace：typeface，宽度都一样。表示数字时很有用。
- Handwriting：typeface，手写体，用于Headlines H1 - H6

分为了12个Type Scale。Headline H1 - H6, Subtitle 1 - 2, Body 1 - 2, Button, Caption和Overline

- Headlines：标题。除六个外还可以使用自定义的字体。Serif或sans serif也可以，它们适合小标题。
- Subtitles：副标题。Serif或sans serif也可以。但自定义字体可能会让问题看不清。
- Body：内容体。Serif或sans serif也可以。不用自定义字体。
- Caption && Overline(文字上方一横线)：极小字体，用于解释图片或标注。Serif或sans serif也可以。不用自定义字体。
- Button：通常都是sans serif + 全大写。也可以serif。和非互动文字混淆时，用户可能意识不到可以点击。不用自定义字体。

每行字数通常为短为20 - 40，中为40 - 60，最多120。

开发过程中，可以参考资料[2]中的字体，图省事得用`android:textAppearance="@style/TextAppearance.AppCompat.**"`

{% asset_img textAppearances.jpg Common textAppearance values %}

不知为何不是一一对应的，凑活着用。

资料：

1. [Material Desgin](https://material.io/)
1. [Valid values for android:fontFamily and what they map to?](https://stackoverflow.com/a/19692168/5507158)
