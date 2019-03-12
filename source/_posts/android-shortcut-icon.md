---
layout: post
title: Android创建快捷方式图标的简单快速方法
date: 2019/3/11
categories: Android
---

从Google Material Icons或其他地方拉一个**24dp**的vector，1分钟内创建其对应的快捷方式图标。

<!--more-->

1. 修改`viewportWidth`和`viewportHeight`为`48.0`。

1. 把现有的所有`<path>...</path>`放到一个`<group>`中，并添加`translate`：

    ```XML
    <vector ...>
        <group
            android:translateX="12"
            android:translateY="12">
            <!-- 原来的path，记得改颜色-->
            <path ...>
        </group>
    </vector>
    ```

1. 在`<group>`前添加这一段（就是画了个圆）:

    ```XML
        <path
            android:fillColor="#F5F5F5"
            android:pathData="M24,24m-22,0a22,22 0,1 1,44 0a22,22 0,1 1,-44 0" />
    ```

1. 完成！我记得这样的结果是符合MaterialDesign的。