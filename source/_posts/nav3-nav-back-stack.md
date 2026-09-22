---
layout: post
title: Navigation3 NavBackStack 最佳写法 —— 在AI和官方Skills拉胯时
date: 2026/9/12
categories: Android
---

本以为使用顶尖大语言模型 + Android官方Skills，能够在AI时代轻松解决Nav3的迁移，但没想到又进了一次兔子洞。

<!--more-->

## TL;DR

无论是Android还是KMP，我推荐在创建NavBackStack时，一律采用**Closed polymorphism**并使用：

```Kotlin
@Serializable
internal sealed interface AppRoute : NavKey {
    @Serializable
    data object Home : AppRoute

    @Serializable
    data class Detail(val id: Long) : AppRoute
}

val backStack: NavBackStack<AppRoute> = rememberSerializable(
    serializer = serializer(),
) {
    NavBackStack(AppRoute.Home)
}
```

这样的方法最简单、最直观、不崩溃、活过进程重启、跨平台。

## 简单的方法

通常大家接触到的第一个方法是：

```Kotlin
rememberNavBackStack(Home)
// or rememberNavBackStack(Home("start"), Details(42))
```

其实现为：

```Kotlin
@Composable
public fun rememberNavBackStack(vararg elements: NavKey): NavBackStack<NavKey> {
    return rememberSerializable(
        serializer = NavBackStackSerializer(elementSerializer = NavKeySerializer())
    ) {
        NavBackStack(*elements)
    }
}
```

这里的`NavKeySerializer`在用反射，其注释也提到了`on **Android**, state is saved/restored using a **reflection-based serializer**; on **other platforms this will fail at runtime**`，但是后半句不对，因为其他平台，也就是使用KMP时，压根没有这个只写NavKey的方法，自然谈不上`fail at runtime`。

那在KMP中要怎么写呢？

## 混乱的现状

在[官方文档](https://developer.android.com/guide/navigation/navigation-3/save-state)、[官方Sample](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:navigation3/navigation3-runtime/samples/src/main/kotlin/androidx/navigation3/runtime/samples/NavBackStackSamples.kt)、[KMP文档](https://kotlinlang.org/docs/multiplatform/compose-navigation-3.html)、[上个月的Talk](https://youtu.be/6g2mjgljn7M?si=sYVLIsUCroX24uba&t=456)还有各种教程和AI生成的代码中，有五花八门的写法，包括`polymorphic`、`subclass`、`subclassesOfSealed`等等。都不会提醒什么情况是出于什么考虑该选择什么代码。

最典型有：

```Kotlin
private val config = SavedStateConfiguration {
    serializersModule = SerializersModule {
        polymorphic(NavKey::class) {
            subclass(RouteA::class, RouteA.serializer())
            subclass(RouteB::class, RouteB.serializer())
        }
    }
}
private val config = SavedStateConfiguration {
    serializersModule = SerializersModule {
        polymorphic(NavKey::class) {
            subclassesOfSealed<Route>()
        }
    }
}
val backStack = rememberNavBackStack(config, RouteA)
```

我让AI使用`android studio`命令，调用nav3的Skill，参考官方的Samples，再加上自定义的最佳实践Skill，到最后也没能产出一个最佳实践。于是只能自己上了。

## 长长的文档

最有用的还是[Kotlin Serialization的文档](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serialization-guide.md)。别看链接那么多，它把单篇文档中的小标题也写上了。

如果不亲自阅读，是无法对Kotlin Serialization有一个全面的掌握的。比如插件和`kotlinx-serialization-core`负责serializer，而`kotlinx-serialization-json`负责JSON，而nav3与JSON无关，所以只需要前者。我记得好像官方文档一开始教的是json的版本？

在第四个文档，我们可以知道Kotlin Serialization会自动支持Closed polymorphism，也就是sealed，而并不需要特殊处理。

而Nav3选择默认Open polymorphism的原因，除了想要把选择权更多地交给开发者，还要受[其推荐的多模块](https://developer.android.com/guide/navigation/navigation-3/modularize)影响：

![Feature module dependency diagram showing how implementation modules can depend on api modules.](https://developer.android.com/static/images/topic/libraries/architecture/nav3-module-graph.png)

而说实话，这种仅为了跳转页面，就去依赖其他功能模块的做法，有点污染模块关系的意味，就像AndroidX Paging这种让所有模块都增加依赖的做法，并不是一个可扩展的路径。个人更偏好将页面跳转交给调用者。

## 最终的结论

我们的要求如下：

- 一个跨平台的统一写法。否则针对特定平台就要特定假设，这通常意味着注释、文档、甚至专门的Skill。
- 避免反射。反射性能差、容易破坏R8/Proguard、不跨平台
- 使用Closed polymorphism来管理所有页面
- 能够解决进程重启的问题。否则直接用`mutableStateListOf`就好了。

这样我们可以得出最佳实践：

```Kotlin
val backStack: NavBackStack<AppRoute> = rememberSerializable(
    serializer = serializer(),
) {
    NavBackStack(AppRoute.Home)
}
```

**需要手动声明类型名，否则类型推断会错误**。

要注意的是，如果是Open polymorphism或者非要用NavKey，就得用SavedStateConfiguration了。现在看起来，这一点很简单直接，但整个理解过程并不轻松。

## Bonus

- back得用`removeLastOrNull`，还有手动确保列表不空
- 使用`Snapshot.withMutableSnapshot`确保中间状态列表不空
- 声明类型`mutableListOf<AppRoute>(AppRoute.Home, AppRoute.Settings)`才能在Kotlin/Native通过编译
