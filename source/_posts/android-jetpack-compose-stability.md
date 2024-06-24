---
layout: post
title: Jetpack Compose Stability白话版
date: 2022/6/24
categories: Android
---

官方文档一边说不要过度优化，一边花了长长长的篇幅讲性能优化。

但优秀的开发者在点点滴滴中优化性能，而不是等到性能真出问题时再挠头。本文记录如何写Composable来得到最好的Stability。

## TL;DR

- 书写Composable或用于Composable参数的类时，优先考虑Primitive类型
- 把Android SDK、第三方依赖、其他Module的类放到[Stability configuration file](https://developer.android.com/develop/ui/compose/performance/stability/fix#configuration-file)中

<!--more-->

## 总是优先考虑Primitive类型

```Kotlin
enum class Tab { Home, Profile }

@Composable
private fun ScreenPrimitive(
    int: Int,
    float: Float,
    string: String,
    tab: Tab,
    lambda: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Text(
        text = "$int $float $string $tab",
        modifier = modifier.clickable(onClick = lambda),
    )
}
```

```Text
restartable skippable scheme("[androidx.compose.ui.UiComposable]") fun ScreenPrimitive(
  stable int: Int
  stable float: Float
  stable string: String
  stable tab: Tab
  stable lambda: Function0<Unit>
  stable modifier: Modifier? = @static Companion
)
```

除了Primitive类型，字符串、枚举和Lambda也是没问题的，因为**Jetpack Compose Compiler确定在创建它们后，内容无法变化**。这一句话也是Stability的核心，并将在之后得到更多解释。

如果能用一些Primitive类型书写Composable是最好的，因为不仅Stable，还提高了复用度，但有时我们需要一个类，比如参数太多、需要特定功能、要一定抽象等，那么情况就不一样了。

## 同一Module的类

```Kotlin
data class Screen(
    val title: String,
    val time: Long,
    val tab: Tab,
)

@Composable
private fun ScreenInternalClass(
    screen: Screen,
    modifier: Modifier = Modifier,
) {
    Text(
        text = screen.run { "$title $time $tab" },
        modifier = modifier,
    )
}
```

```Text
stable class Screen {
  stable val title: String
  stable val time: Long
  stable val tab: Tab
  <runtime stability> = Stable
}

restartable skippable scheme("[androidx.compose.ui.UiComposable]") fun ScreenInternalClass(
  stable screen: Screen
  stable modifier: Modifier? = @static Companion
)
```

如果用到的类和Composable在同一个Module，且这个类的内容符合上述的全是Primitive类型，它也是没问题的。

Jetpack Compose之所以能得出这个结论，是**因为`Screen`所在Module启用了Jetpack Compose Compiler**。因为当前Module要用Jetpack Compose，所以Compiler早已启用了，于是从结果上来看，同一Module的只带Primitive类型的类是没问题的。这一点在下一点更为重要。

## 不同Module的类

```Kotlin
// 在一个新建的Module中
data class ScreenFromLibrary(
    val title: String,
    val time: Long,
)

// 在一个另外的Module中
@Composable
private fun ScreenExternalClass(
    screen: ScreenFromLibrary,
    modifier: Modifier = Modifier,
) {
    Text(
        text = screen.run { "$title $time" },
        modifier = modifier,
    )
}
```

```Text
restartable scheme("[androidx.compose.ui.UiComposable]") fun ScreenExternalClass(
  unstable screen: ScreenFromLibrary
  stable modifier: Modifier? = @static Companion
)
```

这时有问题了。因为Jetpack Compose依赖于Jetpack Compose Compiler来推测一个类的Stability，而新建的Module没有启用Compiler，于是就`unstable`了。

解决方法有几个：

- 使用[Stability configuration file](https://developer.android.com/develop/ui/compose/performance/stability/fix#configuration-file)
- 在新建的Module中配置好Jetpack Compose及其依赖，让其检测各种类型。缺点是要污染新建的Module的依赖。
  - 虽然也有第三方库来只引入少量依赖，但1. 第三方依赖越少越好。2. 大依赖小依赖都是污染。

## 依赖中的类

当参数来自于Android SDK或某个依赖时，又出问题了。

```Kotlin
@Composable
private fun ScreenSharedPreferences(
    sharedPreferences: SharedPreferences,
    modifier: Modifier = Modifier,
) {
    Text(
        text = sharedPreferences.getInt("key", -1).toString(),
        modifier = modifier,
    )
}
```

```Text
restartable scheme("[androidx.compose.ui.UiComposable]") fun ScreenSharedPreferences(
  unstable sharedPreferences: SharedPreferences
  stable modifier: Modifier? = @static Companion
)
```

因为Jetpack Compose Compiler无法触及`SharedPreferences`所处的位置，Android SDK，所以其无法确定Stability。解决方法有三个：

### 配置文件

和上文一样的使用[Stability configuration file](https://developer.android.com/develop/ui/compose/performance/stability/fix#configuration-file)。

### 二次包装

```Kotlin
@Immutable
data class SharedPreferencesWrapper(
    val sharedPreferences: SharedPreferences,
)

@Composable
private fun ScreenSharedPreferencesWrapper(
    sharedPreferencesWrapper: SharedPreferencesWrapper,
    modifier: Modifier = Modifier,
) {
    Text(
        text = sharedPreferencesWrapper.sharedPreferences.getInt("key", -1).toString(),
        modifier = modifier,
    )
}
```

```Text
stable class SharedPreferencesWrapper {
  unstable val sharedPreferences: SharedPreferences
}

restartable skippable scheme("[androidx.compose.ui.UiComposable]") fun ScreenSharedPreferencesWrapper(
  stable sharedPreferencesWrapper: SharedPreferencesWrapper
  stable modifier: Modifier? = @static Companion
)
```

`@Immutable`告诉Compiler：凡是跟我混的，都别管，懂了没！

### 打开Strong Skipping Mode

Jetpack Compose面对Stable类型，通过`==`来决定是否触发Recomposition，面对Unstable类型，总是触发Recomposition。打开[Strong skipping mode](https://developer.android.com/develop/ui/compose/performance/stability/strongskipping)后，面对Unstable类型，使用`===`来决定是否触发Recomposition。因为`SharedPreferences`通常是Singleton，那么这个方法可以避免多余的Recomposition。

但是1. 这并不会让开发者理解性能优化。2. 该选项会在未来默认打开。

## List

```Kotlin
@Composable
private fun ScreenList(
    list: List<Int>,
    modifier: Modifier = Modifier,
) {
    Text(
        text = list.size.toString(),
        modifier = modifier,
    )
}
```

```Text
restartable scheme("[androidx.compose.ui.UiComposable]") fun ScreenList(
  unstable list: List<Int>
  stable modifier: Modifier? = @static Companion
)
```

您猜怎么着，Unstable！原因是`List`的本质上可能是个`MutableList`（`val list: List<Int> = mutableListOf()`），属于创建后内容可以变化的类型。

解决方法有三个：

### 又是配置文件

同上。

### 又是二次包装

同上。

### Immutable collections

Jetpack Compose Compiler给 [Immutable collections](https://developer.android.com/develop/ui/compose/performance/stability/fix#immutable-collections) 偷偷开了绿灯。

但要注意**列表中的类`ImmutableList<StableClass>`也要Stable**。

## @Stable

虽然是很重要的注释，会在类或接口中包含`mutableStateOf`时用到，但是我倾向于让Jetpack Compose只完成将数据转换为界面的任务。当不得不使用这个注释时，通常意味着UI代码职责不清晰，这才是更紧急的问题。

因此出于个人偏好，会避免使用该注释，同时这里也将不再讨论。

## 总结

见开头。
