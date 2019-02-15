---
layout: post
title: Android开发：Dalvik和ART
date: 2019/2/15
categories: Android
---

[Android开发的一些知识 - How Dalvik and ART VMs work?](https://aprildown.xyz/2018/12/31/android-requirements/)

<!--more-->

## Dalvik

运行在4.4之前设备上的虚拟机。JIT编译，每应用一个，运行着Dalvik字节码（.dex）的文件。在运行时把.dex字节码转化为硬件特有的指令。

要塞到小设备（体积、内存）里所以体量很小、优化很少。内存分配和回收很慢。堆碎片化（有什么专业的对应翻译吗）严重。适合不怎么分配内存的应用，因此早期Android开发会教导大伙儿尽量少分配对象。

Dalvik虽然为低内存设备优化过，但依然使用了JIT。

## ART

在4.4需手动开启、5.0之后默认开始。依然运行着.dex字节码，相比Dalvik，ART各方面都更好。随着新版本的推出，ART性能也在越来越好。整个Android平台也在和它互相适配。

主要的优势有：

### 1. AOT

传统的VM是解释型的，一行一行转换巨慢。

后来出现了JIT，放到虚拟机里跑字节码，但它延迟大、占用内存大，但优点是可以观察代码的具体运行，并进行直接的优化。

AOT只在安装应用时把代码编译为机器对应的字节码，这样运行时更快，内存占用更小。因为第一次运行编译时时间多，所以可以在这一次里进行更细致的优化。这和移动设备要求延迟低、内存小的相性很般配。

- 早期的iOS比Android跑的快，有一部分原因就是其设备架构统一，所以就可以跑编译后的代码，当然快啦。
- JIT也在运行啦。在一些代码瓶颈处（Hotspot）会把代码留下来以后跑JIT。

### 2. 优化了的GC

#### Dalvik的行为

分配：从头找一个足够的空挡放进去。

回收：

1. 【暂停所有线程】来找根索引。
1. 所有线程恢复运行，Dalvik顺藤摸瓜，从根索引找到所有可以到达的对象，并标记它们。
1. 再次【暂停所有线程】来找所有可以到达的对象，因为程序可能在第二步又分配的对象。
1. 回收掉没有被标记的对象

如果要分配一个大对象，内存又不够用，但内存里又有马上要被回收但还没被回收的对象，这时就会触发`GC_FOR_ALLOC`，专门为了这次分配回收一次。

如果要分配一个大对象，内存又不够用，但内存里也没有可回收的对象，这时就会增大堆或者out of memory。

Dalvik不会压缩/移动内存来腾空间，导致堆碎片化严重。

#### ART的行为

全方面地更快啦。

分配：`RosAlloc`取代了原来的`dlmalloc`（是C和C++中使用的算法）。特点有

1. 可以针对某一线程进行内存分配
1. 将小对象内存分配打包
1. 将大对象内存分配按页对齐
1. 更好的上锁和解锁

分配大对象（**现在**只是针对至少12KB的primitive数组或String）时，不是找空的坑位，而是直接放到空旷的某个地方。

ART学会了通过压缩/移动内存来腾空间，堆碎片化解决了。ART会在应用进入后台后，不影响用户体验时，再进行内存整理，**或者**应用在前台，而急需内存时。

回收：

四个阶段中的第一个阶段不再暂停其他线程。第三个阶段也更快了。

Minor GC：跟踪所有上一次GC后分配的对象，回收时先处理它们，因为它们寿命更短，处理更快。

### 牛轧糖的ART

把分配器用汇编代码重写了一遍。

### 奥利奥的ART

新的回收器，可以在前台进行持续地内存优化/压缩。结果就是整个系统的内存占用和堆碎片化都有了大幅度优化。移动内存只需要移动指针。

内存分配更快了导致synchronized的对象池的性能有一些情况下比直接分配更差。

AOT跟JIT合作，情况更复杂了。安装时并不会完全编译代码，以此提升安装速度。第一次运行时依赖JIT，然后找到运行瓶颈，再在后台进行AOT和不断地优化。这一过程不断重复，结果就是应用越用越快。

此外还有大量大量的优化。

## 参考

1. https://source.android.com/devices/tech/dalvik
1. https://www.quora.com/From-what-Ive-read-online-it-seems-like-Google-is-planning-to-default-to-ART-in-a-future-release-Why-is-ART-better-than-Dalvik
1. [Trash Talk (Android Dev Summit '18)](https://www.youtube.com/watch?v=Zc4JP8kNGmQ)
1. [Deep Dive into the ART Runtime (Android Dev Summit '18)](https://www.youtube.com/watch?v=vU7Rhcl9x5o)
