---
layout: post
title: 个人偏爱的Android应用架构
date: 2021/4/18
categories: Android
---

根据几年来的开发经验和阅读过的代码，总结出了个人觉得还算稳定可持续的Android应用架构。它以MVVM为基础，融合了Clean Architecture，参考了[iosched](https://github.com/google/iosched)，尽可能地照顾到测试和扩展。

不定期更新新的想法。

<!--more-->

应用细分了许多Gradle Module，且除顶层app外，每个Module都是Android Library（`com.android.library`）。

应用的调用逻辑为Activity -> Fragment -> ViewModel -> UseCase -> Repository。我们按照Moule依赖顺序，从UseCase开始。

**这里重度使用依赖注入，并尽可能使用了`@Reusable`。**

## `domain`

最底层的基础Module，之后的所有Module都会隐式地依赖它。定义了：

- Entity：用到的所有Java Bean或者Kotlin `data class`
- Repository：全部为`interface`。提供各种底层操作，每个操作尽可能只完成简单的一件事，而将具体的整合留给下一位。
- UseCase：一个抽象类为[CoroutineUseCase](https://github.com/google/iosched/blob/main/shared/src/main/java/com/google/samples/apps/iosched/shared/domain/CoroutineUseCase.kt)。继承时，依赖注入`CoroutineDispatcher`和将使用到的Repository。定义了所有用到的操作（[一个简单的例子](https://github.com/google/iosched/blob/main/shared/src/main/java/com/google/samples/apps/iosched/shared/domain/users/FeedbackUseCase.kt)）。

  - 为了照顾单元测试，避免使用Android专用代码（例如使用Kotlin Flow替代LiveData）。
  - 关于在应用的哪一层捕获异常，是一个见仁见智的问题。我个人偏向在UseCase中捕获异常。有异常就封装到一个`Result<Xxx>`（和Kotlin中的`Result`类似，为了避免烦人的警告而手动定义）中，没异常就直接返回。

- 常量、日志、异常、Feature Flag等基础配置
- 统计、崩溃收集的接口

## `data`

```Groovy
api project(":domain")
```

实现了`domDaain`中的Repository，并用Dagger的`@Binds`绑定到对应的Repository。定义了并实现了数据库、网络、本地储存、WorkManager等来完成Repository的任务。

- 使用Room时，数据在本Module定义为XxData（包含了数据的字段定义），而Repository中要返回XxEntity，所以在返回前要手动或定义专门的Mapper类进行转换。

  这么做可以将具体实现和应用逻辑分割。UI层直接处理简单的`data class`，而无需担心JSON、数据库等配置。

- 得益于Hilt，绑定Repository的类可以定义为私有类。
- 不在本Module中存放资源。非要不可的话，要么依赖注入，要么在`res/values/ids.xml`定义一个同名资源来引用。
- 因为基本全是内部实现，所以几乎都是`internal`。配合Hilt应该可以全部为`internal`。

## `presentation`

**在有了Hilt之后，本Module可以被删除，而将ViewModel移动到各个功能Module中。**

```Groovy
api project(":domain")
```

定义了所有用到的ViewModel，ViewModel中被注入了各个需要的UseCase。

- 业务逻辑应该在UseCase中，所以ViewModel中尽可能保持简单。
- 虽然Google官方建议在Kotlin-only的项目中用Flow替代LiveData，但我习惯在ViweModel中将Flow调用`asLiveData()`转换后给UI层使用，因为在UI层使用Flow坑太多。

## `app-base`

以`app-`开头的Module将会按应用的不同功能分类，而本Module是它们的基础Module。

```Groovy
api project(":domain")
```

定义了：

- 各种UI层（Android层）的基础UI类、通用View、通用工具类。
- 除了各个功能Module的特有资源，其他资源都放在这里。例如应用图标、通用图标、颜色、主题、翻译等。

## `app-xxx`

```Groovy
api project(":app-base")
// api project(":prentation") // Hilt之前
```

将应用按功能分类，包含了功能的界面、Activity、Framgnet等。用了Hilt之后，也可以把ViewModel放进来。

## `app`

```Groovy
implementation project(":domain")
implementation project(":data")
// implementation project(":prentation") // Hilt之前
implementation project(":app-base")
implementation project(":app-xxx")

compileOnly ... // Dagger可能会抱怨找不到data中使用的一些类，所以加进来就可以。
```

应用入口，也是仅有依赖了`data` Module的一处。这样将应用实现细节通过依赖注入抽象出来，可以达到代码不互相污染、提升编译速度、方便测试、提高逆向难度、提高可扩展性等的效果。

- Navigation Component配置如下：

  - 在`app-base`的`res/values/ids.xml`定义了一群id，代表各个目的地或操作。
  - 在`app-xxx`中实现各个Fragment，并按需使用其他id。
  - 在`app`的Navigation graph中，将id和Fragment或操作对应起来。

  这么做的缺点是：
  
  - 无法保证编译时安全，少配置了东西只有在运行才会崩溃，但我还没找到更好的办法。
  - 不能使用生成的参数类。解决办法是乖乖`bundleOf`。

  2021年5月11日更新：

  刚刚看了[Navigation: Navigating between modules - MAD Skills](https://www.youtube.com/watch?v=IvFSJVTGDb4)，视频中是通过在子Module中定义Nested Graph，父Module中引用实现的，这样可以让子Module隐藏Nested Graph中的实现。这个方法比上述的要好。

  但如果出现了子Module之间跳转的情况，那可能还是需要之前的方法。

## 各种使用场景

- 单元测试主要是UseCase，可以新建一个测试Module，也可以在`domain`中就地解决。
- Instrumented Test可以新建一个测试Module，也可以在`app`中就地解决。
- 国产SDK

  因为它们代码混乱、污染名称空间，最好把它们丢到一个专门的Module（例如`app-trash`、`app-third`）中养蛊。如果它们只提供JAR或AAR，就把文件引用为一个新Module(New -> New Module -> Import .JAR/.AAR Package)，然后再在我们的养蛊Module中，引用它们。之后在`app-base`中定义`interface`，`app-trash`中实现，`app`中依赖绑定，`app-xxx`注入使用。

  上述方法看起来很麻烦，但因为国产SDK太过恶心、给人造成的麻烦太多，怎么加抽象都不为过。另外，有了Lifecycle、新的Activity Result API等，上述方法也容易了些。

  个人经验：永远不要直接引用它们，哪怕多加几个抽象Module。

- MVI

  因为现有的MVI框架还不够成气候，所以现在还是MVVM。但MVI最重要的是思想，Jetpack Compose也是有类似的思想。在将ViewModel精简、将逻辑放到UseCase后，实现起来还是容易的。
