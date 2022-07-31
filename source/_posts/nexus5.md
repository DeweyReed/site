---
layout: post
title: Nexus 5 购入 刷机 升级 笔记
date: 2022/7/8
categories: Android
---

在论坛中看到可以入手一台Nexus 5来测试应用，于是开始了折腾之旅。

<!--more-->

从淘宝或咸鱼花一百左右收一台Nexus 5。注意它是用老版的Android数据线。

到手是Android 4.4，在升级到Android 5.0.1后，一切正常。于是开始熟练地`adb shell pm uninstall --user 0 -k ...`卸载起系统应用来。但是在最后卸载Google搜索后，把机器给弄死机了。无奈只好强制恢复出厂设置。

根据[Nexus 刷机步骤](https://zhuanlan.zhihu.com/p/19635679)，`关机状态下长按音量下+电源就可以进入`。要先按音量下。

顺利双清后，发现系统连不上Wifi了。一通失败的操作后，最后决定根据上文链接手动换系统。

先去 <https://developers.google.com/android/images> 下载压缩包，然后ADB一通操作。据说直接运行flash-all脚本就可以，但是我总会遇到系统无限重启的问题。

最后根据[Nexus 5刷机无限重启的解决方案](https://www.jianshu.com/p/c14f003fa844)，只好手动输入命令：

```Bash
fastboot erase cache
fastboot erase userdata
fastboot erase boot
fastboot erase cache
fastboot erase recovery
fastboot erase system

fastboot flash bootloader bootloader-hammerhead-hhz20h.img
fastboot reboot-bootloader

fastboot flash radio radio-hammerhead-m8974a-2.0.50.2.30.img
fastboot reboot-bootloader

fastboot flash recovery recovery.img
fastboot flash boot boot.img
fastboot flash system system.img
fastboot flash cache cache.img
fastboot flash userdata userdata.img
```

另外发现为了解决无法连接Wifi的问题，必须刷到Andorid 4.4，再到手机中手动升级到Andorid 5。
