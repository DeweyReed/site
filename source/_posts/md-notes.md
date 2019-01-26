---
layout: post
title: Material Design 笔记
date: 2018/5/27
categories: Android
---

**不保证全对、可能更新，这玩意儿主要是给自己看的**

2018年5月27日更新：更新了一些无用的笔记
2018年5月23日更新：增加了Material Desgin 2字体的可能的可选值

[Material Desgin](https://material.io/)

<!--more-->

## Design

### Environment

#### Surfaces

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

#### Elevation && Depth

所有元素都有elevation，值越大，内容越重要。常见的有：Dialog 24dp, Drawer 16dp, App Bar 4dp, FAB 6dp

不同情况和布局，元素的elevation可变。比如点击Button 2dp => 8dp，Fab 6dp => 12dp。

为了解决elevation变化时，不同元素的重叠问题，被重叠的元素可以暂时消失（实现起来复杂了，这类行为尽可能减少）。

Elevation也被用作不同Surfaces的分割，也可以使用颜色或不透明度。

[各元素默认elevation](https://material.io/design/environment/elevation.html#default-elevations)

#### Shadows

分为点光源（Key Light）和平行光源（Ambient Light），结合起来使用。

### Layout

元素按8dp排列，小元素按4dp。sp默认大小与dp一样，设置中的字体大小会给sp一个倍数。

Columns, Gutters, Margins：Margins就是左右边边界（手机16dp，平板24dp），Columns是一个个竖块儿（宽度可变)，Gutters是Columns之间的空隙（宽度可变）。

Columns和Gutters应为一个宽，一个窄。

常用的Breakpoints就只剩600dp了吧。

可触摸元素至少是48dp，边界8dp。不可触摸元素至少是24dp，边界8dp。

行高通常为48dp。

[响应式](https://material.io/design/layout/component-behavior.html#responsive-patterns)个人实现思路：

- Reveal Drawer：知道MaterialDrawer似乎可以直接拿来用，Support Library中的还真不知道
- Reveal Options：Custom View吧
- Reveal Card：TextView中的叫ell什么的属性
- Transform Drawer to Tab：两套UI和代码？
- Transform List to Grid：换一下LayoutManager
- Transform Overflow Menu：ifRoom的属性
- Devide Drawer：同Reveal Drawer
- Devide Tab to Cards：两套UI和代码？
- Reflow List：FlexboxLayout
- Reflow Tab to List：两套UI和代码？
- Reflow Picker：两套UI和代码？
- Expand Cards：设置最大宽度和Increment
- Expand Dialog：有区别吗？
- Position Sheet to Overflow Menu：两套UI和代码？
- Position FAB：两套代码？

### Navigation

[实现？](https://material.io/design/navigation/navigation-transitions.html)

### Color

Primary Color：出现次数最多的颜色。没有Accent时取代Accent。

[各种文字颜色和透明度的推荐值](https://material.io/design/color/text-legibility.html)

### Typography

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

开发过程中，可以参考[Valid values for android:fontFamily and what they map to?](https://stackoverflow.com/a/19692168/5507158)中的字体，图省事得用`android:textAppearance="@style/TextAppearance.AppCompat.**"`

更新：在新版本的Material Desgin 2中，似乎可以通过类似`?attr/textAppearanceHeadline6`或`TextAppearance.MaterialComponents.Subtitle2`获取相应的字体。

### Iconography

图标48dp。编辑时放大四倍，按192dp编辑，4dp边缘。方形边长152dp，圆形直径176dp。

### Shape

### Interaction

### Communication

## Development

### Development Tuturials(Kotlin Codelabs)

新玩意：

- `MaterialButton`取代默认`Button`。使用`style="@style/Widget.MaterialComponents.Button.TextButton"`可以移除背景。
- 很多元素用`MaterialComponents`取代`AppCompat`
- `CardView`被囊括在design library中，更名为`MaterialCardView`
- `?attr/textAppearanceHeadline6`：新的(?)`textAppearance`，符合Design中的Type Scale
