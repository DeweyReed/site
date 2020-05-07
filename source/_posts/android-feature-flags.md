---
layout: post
title: Android上一个简单的Feature Flag实现
date: 2020/4/21
categories: Android
---

起因是看了Jeroen Mols大佬的[Feature Flags](https://jeroenmols.com/blog/2019/08/13/featureflags/)系列，深受启发。在看了代码后，萌生了写一个适合自己的Feature Flag框架。现在整出来了，但发现使用场景有点局限😂，所以把代码丢在这里以备不时之需。

<!--more-->

我个人的需求没原文中那么复杂，只需要一个可以在代码里手动调整的开关，一个可以在运行时进行修改的面板，同时要让R8把未使用的所有代码剔除。

首先是可以在代码里手动调整的开关，用`buildConfigField`：

```Grovvy
android {
    buildTypes {
        debug {
            buildConfigField "boolean", "FEATURE_FLAG", "Boolean.parseBoolean(\"true\")"
        }
        release {
            buildConfigField "boolean", "FEATURE_FLAG", "false"
        }
    }
}
```

- `"Boolean.parseBoolean(\"true\")"`确保IDE不会警告，就像`BuildConfig.DEBUG`一样。

整个框架，抄了就走：

```Kotlin
enum class Feature(
    val key: String,
    val title: String,
    val description: String,
    val default: Boolean = true
) {
    Log(
        key = "feature_log",
        title = "Log",
        description = "All log to console"
    ),
    Network(
        key = "feature_network",
        title = "Network",
        description = "Inspect network requests"
    )
}

object FeatureFlagManager {

    private lateinit var sharedPreferences: SharedPreferences

    fun init(context: Context) {
        if (!BuildConfig.FEATURE_FLAG) return
        sharedPreferences = context.safeSharedPreference
    }

    /**
     * If we pass a value directly, this method won't get removed by R8.
     * Passing a lambda does the job.
     * I learn this at https://youtu.be/MYQWtNG2so8?t=362
     */
    fun isFeatureEnabled(f: () -> Feature): Boolean {
        if (!BuildConfig.FEATURE_FLAG) return false
        return f.invoke().enabled
    }

    fun manageFeatures(context: Context) {
        if (!BuildConfig.FEATURE_FLAG) return
        val features = Feature.values()
        MaterialAlertDialogBuilder(context)
            .setCancelable(false)
            .setTitle("Feature Flag Manager")
            .setAdapter(
                object : BaseAdapter() {
                    override fun getCount(): Int = features.size
                    override fun getItem(position: Int): Any = features[position]
                    override fun getItemId(position: Int): Long =
                        features[position].ordinal.toLong()

                    override fun getView(
                        position: Int,
                        convertView: View?,
                        parent: ViewGroup
                    ): View {
                        val view = convertView ?: LayoutInflater.from(context)
                            .inflate(R.layout.list_item_feature_flag, parent, false)
                        val layout = view.findViewById<ListItemWithLayout>(R.id.listItemFeatureFlag)
                        val feature = features[position]
                        layout.listItem.run {
                            setPrimaryText(feature.title)
                            setSecondaryText(feature.description)
                        }
                        layout.getLayoutView<CompoundButton>().run {
                            setOnCheckedChangeListener(null)
                            isChecked = feature.enabled
                            setOnCheckedChangeListener { buttonView, isChecked ->
                                feature.enabled = isChecked
                                buttonView.indefiniteSnackbar(
                                    message = "Restart the app",
                                    actionText = "Now",
                                    action = {
                                        ProcessPhoenix.triggerRebirth(context)
                                    }
                                )
                            }
                        }
                        return view
                    }
                },
                null
            )
            .setPositiveButton(android.R.string.ok, null)
            .setNegativeButton(android.R.string.cancel, null)
            .show()
    }

    private var Feature.enabled
        get() = sharedPreferences.getBoolean(key, default)
        set(value) {
            sharedPreferences.storeBoolean(key, value)
        }
}
```

使用：

```Kotlin
FeatureFlagManager.init(this)
if (FeatureFlagManager.isFeatureEnabled { Feature.Log }) {
    Timber.plant(Timber.DebugTree())
}
```

运作原理：

- `isFeatureEnabled`

  ```Kotlin
  /**
      * If we pass a value directly, this method won't get removed by R8.
      * Passing a lambda does the job.
      * I learn this at https://youtu.be/MYQWtNG2so8?t=362
      */
  fun isFeatureEnabled(f: () -> Feature): Boolean {
      if (!BuildConfig.FEATURE_FLAG) return false
      return f.invoke().enabled
  }
  ```

  顾名思义，传Lambda而不是直接传Feature确保R8可以在这段代码没被使用时移除掉它，不然Feature会留在最终的APK里。

  担心性能问题？

  - 我没做Benchkmark，所以不考虑优化。
  - 我寻思以大部分应用的水平，这个Lambda根本不在瓶颈上。
  - 请信任智能的R8，人家比咱上心，也比咱厉害，也许人家已经搞定了。
  - Romain Guy大佬怎么做，我就怎么做！

- `setAdapter`

  之所以使用`AlertDialog`的`setAdapter`而不是自定义`View`，是因为这样可以让列表滚动时在顶端和底端有一个分割线，我挺喜欢这个设计，但缺点就是要处理`ListView`😢

- `ListView`的每个条目的命名`R.layout.list_item_feature_flag`

  我之前用的是`R.layout.item_feature_flag`，但发现R8在把整个`Feature`移除后死活不把这个文件处理掉，查看`resources.txt`并研究后才发现，所有以`item`开头的资源，都不会被处理（当然还有其它各种开头，[具体在这里](https://android.googlesource.com/platform/tools/base/+/master/build-system/gradle-core/src/main/groovy/com/android/build/gradle/tasks/ResourceUsageAnalyzer.java#827)）。修改为`list`开头后就好了，虽然文件还在，但里面的内容已经没了。

  另一种方法是根据[官方文档底部的方法](https://developer.android.com/studio/build/shrink-code.html#troubleshoot-resource-shrink)来确保这些文件被移除。我懒，所以没试这种方法。

- `getView`绑定

   `ListItemWithLayout`就是一个`CompoundButton`配一个`TextView`，不稀奇。包括其他的一些方法，都是简单包装了一下，顾名思义。

- `ProcessPhoenix.triggerRebirth(context)`

   我这里用[ProcessPhoenix](https://github.com/JakeWharton/ProcessPhoenix)来强制重启，但它有点Bug，需要你在启动的`Activity`加一个category：`<category android:name="android.intent.category.DEFAULT" />`。

   我也找了一下`chromium`是怎么重启的，发现人家使用JNI重启进程，我寻思有没有谁来打包成一个库呀？
