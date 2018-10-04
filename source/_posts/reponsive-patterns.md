---
layout: post
title: Material Desgin响应式布局Android实现思路
date: 2018/6/3
categories: Android
---

虽然Google已经放弃了它的Android平板产品，但开发者不会（哭）。

在Mateiral Design中，有一部分是[关于适配手机小屏幕和平板大屏幕的内容](https://material.io/design/layout/component-behavior.html#responsive-patterns)。这里是他们的一些实现思路。

<!--more-->

## Reveal 1: Drawer

![Reveal 1: Drawer](https://material.io/design/assets/1eEEDI_xEyFFa6y-_yyRxyP-TpgsPb2Pr/layout-responsive-patterns-reveal-navdrawer.png)

简单方案：使用[MaterialDrawer](https://github.com/mikepenz/MaterialDrawer)，内置了MiniDrawer支持。在代码中根据屏幕大小判断并设置。

[MaterialDrawer MiniDrawerActivity](https://github.com/mikepenz/MaterialDrawer/blob/develop/app/src/main/java/com/mikepenz/materialdrawer/app/MiniDrawerActivity.java)和[它的Layout XML](https://github.com/mikepenz/MaterialDrawer/blob/develop/app/src/main/res/layout/activity_mini_drawer.xml)

复杂方案：[手动实现](https://nganthony.github.io/2016/01/24/implementing-android-mini-navigation-drawer.html)

其他方案：等待Google官方支持

## Reveal 2: Options

![Reveal 2: Options](https://material.io/design/assets/1QUva50z9eylQ2Me2EAptRdB3Yfu03WuP/layout-responsive-patterns-reveal-wordprocessor.png)

简单方案：使用HorizontalScrollView、FlexboxLayout、Overflow Menu的话，修改好一些属性后无需担心。

复杂方案：使用CustomView，手动排列、显示、隐藏

## Reveal 3: Dialog

![Reveal 3: Dialog](https://material.io/design/assets/1X0_fk6SxxVfzcq3FXawUxT5HmiKta3b3/layout-responsive-patterns-reveal-card.png)

手动判断，为小屏幕隐藏那些过多的内容。

## Transform 1: Drawer to Tab

![Transform 1: Drawer to Tab](https://material.io/design/assets/1vz5WkJG9K_dyBrfJHekJ8c5Wsol-yN9O/layout-responsive-patterns-transform-navdrawer.png)

为了适配Tab，就得用ViewPager。

所以使用ViewPager + Fragments，在手机屏幕下，[禁止ViewPager滑动](https://stackoverflow.com/a/13437997/5507158)，移除或隐藏TabLayout，使用Drawer进行切换Fragments；平板屏幕下，允许ViewPager滑动，增加或显示TabLayout，移除Drawer，让用户滑动进行Navgiate。

## Transform 2: List to Grid

![Transform 2: List to Grid](https://material.io/design/assets/1ikwM7lbX4PAK34WZOL4IMBm9SIhaN2Di/layout-responsive-patterns-transform-list.png)

使用不同的LayoutManager和ViewHolder(使用泛型Adapter?)

## Transform 3: Overflow Menu

![Transform 3: Overflow Menu](https://material.io/design/assets/1a7U47c6ZgWVfDZKRpZ7FzLKgMmZdfOvy/layout-responsive-patterns-transform-toolbar.png)

设置showAsAction为ifRoom或者在代码中根据屏幕设置。

## Devide 1: Drawer

![Devide 1: Drawer](https://material.io/design/assets/1oIscQOmfgUeVn7YHdm4j103W8K3SONg_/layout-responsive-patterns-transform-divide-1.png)

同[Reveal 1: Drawer](#reveal-1:-drawer)

## Devide 2: Tab to Cards

![Devide 2: Tab to Cards](https://material.io/design/assets/1NWWybr23o4mQ3eka3MIZfNomzNoPGaT4/layout-responsive-patterns-transform-divide-2.png)

手机屏幕用ViewPager，平板屏幕用两个Fragment。在Activity中判断。

## Reflow 1: List to Grid

![Reflow 1: List to Grid](https://material.io/design/assets/15dkIemLoJvvIR4Y--C_aylSLrub4e-RW/layout-responsive-patterns-transform-reflow-1.png)

同[Transform 2: List to Grid](#transform-2:-list-to-grid)

## Reflow 2: Tabs to Lists

![Reflow 2: Tabs to Lists](https://material.io/design/assets/1VXqd6WD0BvreENnNk0g_dqtyUv8rAMsT/layout-responsive-patterns-transform-reflow-2.png)

更换每个Fragment的LayoutManager。手机屏幕使用ViewPager，平板屏幕使用Static Fragments。

## Reflow 3: Time Picker

![Reflow 3: Time Picker](https://material.io/design/assets/1Aj5t9FM3cIrRgmCaTnPWrKLlPUjd34bv/layout-responsive-patterns-transform-reflow-3.png)

使用不同的Layout XML

## Expand 1: Max Size

![Expand 1: Max Size](https://material.io/design/assets/1L6lMye5M8N6h3NHkmRD9b7mqWNd-aqIY/layout-responsive-patterns-transform-expand-1.png)

使用不同的style设置width和height。

顶部空白：1. 顶部添加一个空白View，手机屏幕上0dp，平板屏幕上有高度。2. 直接使用不同的paddingTop。

## Expand 2: Dialog

![Expand 2: Dialog](https://material.io/design/assets/1apntZGLUuUlSkhpnTJe2ikMeTIFP1cKJ/layout-responsive-patterns-transform-expand-2.png)

我不认为这需要修改任何代码。

## Position 1: Menu

![Position 1: Menu](https://material.io/design/assets/1pmCqvMVPajmoDsmxmL-JBz4zqy88dSEa/layout-responsive-patterns-transform-position-1.png)

在代码中使用BottomDialog并显示或隐藏OverflowMenu(修改onCreateOptionsMenu返回值)。

## Position 2: FAB

![Position 2: FAB](https://material.io/design/assets/1QALKDtXeGMSXcRu3uOA7eOUDoa2GMnlm/layout-responsive-patterns-transform-position-2.png)

不同的style，设置FAB的anchor。也可以通过代码设置。