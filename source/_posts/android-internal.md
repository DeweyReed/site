---
layout: post
title: Android Internal 笔记
date: 2018/5/12
categories: Android
tags: 不再维护
---

之前不知在哪里看到Android开发者都应该了解一下Android Internal，就截了图记了下来。现在有时间了，就找了一些Android Internal的内容，记了些笔记。相应的资料也列在了各个章节末尾。

**不是很全，不保证全对，不保证维护**

## 基于Linux内核的Android

Android之所以享誉盛名，是因为在Linux内核基础上已改的面目全非。。就是为了适应手机那小内存、差CPU、续航是问题等的特点。Android不是一个Linux的发行版。

<!--more-->

- 优化后的OOM(Out of Memory) Killer

    给每个进程加了`oom_adj`的优先值，把不同进程分到不同的级别中(Foreground Processes, Visible Processes, Service Process, Background Process)，内存不够时先杀值大（优先级低）的进程。

- WakeLocks

    保活CPU(+ 屏幕)的工具。本来是让应用更好地工作，结果成了流氓应用的工具。现在这玩意儿很多时候也不好使了。

- `init`和`zygote`

    用来启动各种`deamon`，比如`adbd`, `logd`, `zygote`。

    `zygote`会经常在日志底部中出现。默认的`fork()`速度慢，所以它是一个快捷进程+`deamon`，用于快速启动应用。

- Binder IPC

    处于安全考虑，每个应用被对待成Linux不同不用户，拥有不同的`uid`。

    IPC(Inter-Process Communication)：跨进程通信。

    例如`Intent`通信，可以在不同组件中异步交换数据；又如`ContentProvider`，可以在不同应用中交换数据。它们都默认在`MainThread`的`Looper`中运行。

- Managers

    系统服务，管理系统资源

  - `WindowManager`：负责窗口、动画等。
  - `PackageManager`：查询`Manifest.xml`，解析包名。
  - `ActivityManager`：负责`lifecycle`、各种组件、电量、`configuration changes`。（根据个人经验，冻结应用也涉及它）
    创建`Stack`，里面放`Task`，里面放`Activity`的索引。每个应用在单独的线程中。

参考：

1. [Android Internals For Developers : Part I](https://android.jlelse.eu/android-internals-for-developers-part-i-982a4409f4b5)
1. [Android Internals for Developers: Part II](https://android.jlelse.eu/android-internals-for-developers-part-ii-c6ca94243efa)
1. [Android Internals](https://academy.realm.io/posts/360-andev-2017-effie-barak-android-internals/)

## Handler

这是Android自己的消息传递机制。不同线程通过和Handler交流、传递信息。日常开发中主要用于异步。使用不当会造成严重的内存泄漏。

每一个Handler都一个Looper和Message Queue。在创建Handler是有两个构造函数，无参的是新建一个在当前线程的Handler，有参的指定一个Looper。而Looper可以操作Message Queue，因此是必须的。不同Handler可以共享一个Looper，它们的Message Queue跟随Looper，也共享一个。

- Message

    数据的包装类，被发送给Message Queue。Message从消息池（最大有50个）中取出再使用，所以就会有

    `mHandler.obtainMessage(MSG_SHOW_IMAGE, mBitmap).sendToTarget();`

    每一个Message都有一个`target`，类型是Handler。上面一行默认把会把`target`设置为自己。

- Message Queue

    链表，存放需要实现的Message。根据`SystemClock.uptimeMillis()`和时间戳来判断该执行什么。

    Android也用几个自用的Handler，也是使用了Main Looper:

  - Choreographer：处理垂直同步和帧刷新。好像有个Library就是用这个显示当前屏幕的帧数。
  - ViewRoot: 处理输入、窗口事件、Configuration Changes等
  - InputMethodManager: 处理键盘输入。LeakCanary经常提示我它泄露了。。
  - 等等等等

- Looper

    `Thread`默认运行完就结束，想要让线程一直留着，等着以后接着用，就需要Looper来“保活”。
    它也负责从Message Queue中取消息，指派给Handler。一个线程一个Looper一个Message Queue若干Handler。

    `Looper.prepare()`: 确保当前线程和Looper已配对。

    `Looper.loop()`: 开始处理Message Queue中的Message。实现里有个`for (;;) {`的无限循环。稍微研究了一下，没有消息时，非常粗暴地使用CPU一直等待。

    为了方便，异步时不手动Looper + 线程，而用更方便的`HandlerThread`，继承自`Thread`，处理了`Looper`的工作。

    ```Java
    private final Handler handler;
    private final HandlerThread handlerThread;
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate();
        handlerThread = new HandlerThread("HandlerDemo");
        handlerThread.start();
        handler = new CustomHandler(handlerThread.getLooper());
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        handlerThread.quit();
    }
    ```

参考:

1. [Android Handler Internals](https://medium.com/@jagsaund/android-handler-internals-b5d49eba6977)
1. [Understanding Activity.runOnUiThread()](https://medium.com/@yossisegev/understanding-activity-runonuithread-e102d388fe93)

## Storage储存

### Internal Storage 内部储存

是在应用私有的一个储存空间，存放了`SharedPreferences`、数据库等文件。其他应用和用户在没有root和找到漏洞前无法访问。在实现上，就是分配各每个应用的一个文件夹。

位置在`/data/data/package-name/`(不一定总是这个路径，经常变)。

`files`文件夹：通过`Context.getFilesDir()`来获取路径，所以创建文件时的姿势应为`val f = File(getFilesDir(), "foo.txt")`

Android 3.0及以后，内部储存的空间大大增加。几乎所有不需要让用户和其他应用使用的数据，都应该放在内部储存中。

要想共享内部储存中的数据，使用`FileProvider`和`ContentProvider`，给每个`Uri`都赋予权限，安全且统一。

### External Storage 外部储存

位于`/sdcard/`(不一定总是这个路径，经常变)下的内容，文件浏览中看到的东西。也包括SD卡中的内容。但现在很多手机都不支持SD卡了，自带的管够。

读写需要权限。
正常情况下用`Environment.getExternalStorageDirectory()`访问。但多数情况下应使用`Context.getExternalFilesDir()`来获取`/sdcard/Android/data/package-name/files/`下应用各自的位置。

参考

1. [The Storage Situation: Internal Storage](https://commonsware.com/blog/2017/11/13/storage-situation-internal-storage.html)
1. [The Storage Situation: External Storage](https://commonsware.com/blog/2017/11/14/storage-situation-external-storage.html)

## Proguard

流程：

1. Read config(proguard-rules.pro)
1. Finding Seeds => seeds.txt => Entry points
1. Shrinking => usage.txt => Removed codes
1. Obfuscation => mapping.txt
1. Dump => dump.txt
1. Write Classes => .class

aapt会根据Manifest给应用项目自动生成各种各样的keep rules。

参考：

1. [How Proguard Works](https://www.youtube.com/watch?v=F9ymcWoDEtc)
1. [Effective ProGuard keep rules for smaller applications (Google I/O '18)](https://www.youtube.com/watch?v=x9T5EYE-QWQ)

## Memory

分割为Pages，每页4KB。类型有Used Pages, Cached Pages, Free Pages.

减小内存使用的方法：

1. 使用Memory Profile, 查看java heap, app heap
2. 减小APK大小

参考：

1. [Understanding Android memory usage (Google I/O '18)](https://www.youtube.com/watch?v=w7K0jio8afM)