---
layout: post
title: Android OpenGL Texture 坐标原点问题
date: 2023/8/23
categories: Android
---

在学习[OpenGL ES 2.0 for Android](https://www.amazon.com/OpenGL-Android-Quick-Start-Pragmatic-Programmers/dp/1937785343)时，其提到了OpenGL使用了传统的数学坐标系来定位位置，而在Texture的坐标时则含糊不清。而出来混，终于得还了。

TL;DR: `GLUtils.texImage2D`会把传入的`Bitmap`上下翻转，让可以开发者可以使用左上角为原点的坐标。但实际上，OpenGL总是以左下角为原点的。

<!--more-->

```Text
1----2
-    -
-    -
3----4
```

当上图为屏幕时，那么画满全屏Vertex的位置将为

```Text
1: (-1,  1)
2: ( 1,  1)
3: (-1, -1)
4: ( 1, -1)
```

但当上图为Texture时，坐标该是如何呢？根据书中所言，原点为左上角，也就是

```Text
1: ( 0,  0)
2: ( 1,  0)
3: ( 0,  1)
4: ( 1,  1)
```

和Android的绘制方向是一致的。在实际运行中，结果也是正确的。所以一直以来，我也以为Texture的坐标是从左上角开始的。

在学习OpenGL后，我开始尝试[android-gpuimage](https://github.com/cats-oss/android-gpuimage)，但发现在使用`GPUImageFilterGroup`时，会随机出现上下颠倒的问题。为了找到该问题的原因，我是上下求索。翻遍了Issue区、认真阅读代码、问了很多遍ChatGPT、在搜索引擎中搜了很多关键字、根据滤镜数量找规律……都没能找到问题根源。

在Issue区中找到一个Pull Request：[Re-fix: Grouped filters with filterCount%2==0 are flipped](https://github.com/cats-oss/android-gpuimage/pull/231/)，虽然在之后起到了大作用，但此时的我不仅看不明白其中的逻辑，也没能解决问题。

最后，我终于想起了走投无路之计策：用中文搜索问题！因为中文互联网几乎已等于只能吃老本的内容农场，所以我很少考虑这个方法。虽然在过去的确帮到我几次，但由于在其中找资料非常困难，这个方法几乎被遗忘了。

但是这次！我发现了[Android OpenGL 纹理坐标原点位置](https://www.jianshu.com/p/6c4c7426eef3)！虽然实话实话说这篇文章还是有点难懂，但我找到了最关键的一句：

> 在Android平台中，Bitmap绑定的2D纹理，是上下颠倒的， 可以按照在左上角处理

经过几天或在清醒时或在睡梦中的思考后，我终于想明白了这个问题。

`android-gpuimage`使用的是左上角坐标，这依赖于`GLUtils.texImage2D`的默认翻转。OpenGL总是使用左下角为原点，所以在默认翻转后，在FragmentShader中，Texture已经是上下颠倒的，这时再使用从左下角翻转到左上角的坐标绘制，又翻转了一次。一共了翻转了两次，等于没翻转，因此单个滤镜不会出错。

但是这个逻辑依赖于Texture是翻转的这一假设。

但`GPUImageFilterGroup`使用FrameBuffer实现，上一层的结果会作为下一层的Texture绘制到屏幕上，但是上一层的结果并不是翻转的，这与上述假设相矛盾，于是就在复杂处理中出现上下颠倒的奇怪问题。

相比于不停地使用坐标来解决上下颠倒问题，不如取消`GLUtils.texImage2D`的默认翻转（把Bitmap传给它前翻转一下），总是使用左下角为原点的坐标来得实在。于是在使用之前的Pull Request[Re-fix: Grouped filters with filterCount%2==0 are flipped](https://github.com/cats-oss/android-gpuimage/pull/231/)及其评论区的修改后，翻转解决了。

但是`GPUImageLookupFilter`出错了。不过这个好解决，因为这个滤镜的原理是根据图片中某个像素点的颜色，一通计算后得到一个新的坐标，然后到LUT图中找颜色。这其中的那通计算是以左上角为原点的，也就是假设了LUT图是颠倒的。那么我们不取消LUT图Texture的`GLUtils.texImage2D`的默认翻转就可以了。

## 关于中文搜索的沉思

在这次的问题中，我没能用英文搜索找到任何`GLUtils.texImage2D`默认翻转的资料。反而是再一次被中文内容拯救了，这说明之后还是应该给中文搜索多一些机会。

但中文搜索结果的处境依然不容乐观。对我来说，其更像是上午十点的古玩市场：剩下都大多是垃圾，而真正的宝贝需要高度的鉴别力。

就拿那篇文章来说，在开发文章聚集地转型为地摊文学编辑部的简书上，只有可怜的四个赞。从文章底部划过数十篇编乎一般的小说后，有几条评论。第一条是一位读者以很不客气的语气指责文章有错。第二条是作者耐心地重复解释了文章的内容。第三条则是另一位读者同样很不客气地在没有给出任何原因的情况下批评文章造成了误解。

虽说这篇文章的确有些难懂，但其中的道理是无误的。

我也由此想到了我之所以几乎从不在中文社区解答问题，很大一部分也是因为不仅得不到正反馈，很多情况下还会得到负反馈吧。
