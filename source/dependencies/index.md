---
layout: post
title: Is the dependency safe to upgrade?
date: 2022/2/16
i18n_dir: :en
---

## Gradle

```Script
gradle wrapper --gradle-version=
```

> Note, you need to run this command twice to upgrade both Gradle and the Gradle Wrapper itself

<https://gradle.org/releases/>

- 9.0.0: ??(Java 17, Kotlin 2.2) The whole community isn't ready yet
- 8.14.3: ✔
- 8.13: ❌
- 8.11.1: ✔
- 8.10.2: ✔
- 8.8: ✔(Kotlin 1.9)
- 8.2.1: ✔
- 8.1.1: ✔

  - [Configuration cache stable](https://docs.gradle.org/8.1.1/userguide/configuration_cache.html)(requires Kotlin 1.8 and `sourceCompatibility`, etc.)

    ```Kotlin
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_17.toString()
    }
    ```

- 8.0.2: ✔
- 7.5.1: ✔

## Kotlin

- org.jetbrains.kotlin:kotlin

  - [2.1.21](https://kotlinlang.org/docs/whatsnew2120.html): ✔

    - `KSP` required for `moshi`, `room`, and `androidx.hilt`

  - [2.0.21](https://kotlinlang.org/docs/whatsnew2020.html): ✔
  - [2.0.0](https://kotlinlang.org/docs/whatsnew20.html): ⬆

    - [Compose Compiler Gradle plugin](https://developer.android.com/develop/ui/compose/compiler)
    - [Add the `.kotlin` directory to your project's `.gitignore` file.](https://kotlinlang.org/docs/whatsnew20.html#new-directory-for-kotlin-data-in-gradle-projects)
    - `enumValues<T>()` -> `enumEntries<T>()`
    - [Stable `AutoCloseable` interface](https://kotlinlang.org/docs/whatsnew20.html#stable-autocloseable-interface)

  - 1.9.25: ✔

    - `Enum.values()` -> `Enum.entries`
    - `data object EndOfFile : ReadResult`
    - `..<`

  - 1.8.22: ✔
  
    - kotlin-stdlib-jdk7 and kotlin-stdlib-jdk8 -> kotlin-stdlib
    - ~~`kotlinx-android-extension`~~

  - 1.7.20: ✔

    - `T & Any`
    - `min()` and `max()` throw exceptions if empty
    - `DeepRecursiveFunction`
    - [sam-with-receiver plugin](https://kotlinlang.org/docs/sam-with-receiver-plugin.html)

  - 1.6.21: ✔
  - 1.6.10: ✔

    - `buildList`, `buildSet`, and `buildMap`

  - 1.5.31: ✔

- [org.jetbrains.kotlinx:kotlinx-coroutines](https://github.com/Kotlin/kotlinx.coroutines/releases)

  - 1.10.2: ✔(Kotlin 2.1)
  - 1.9.0: ✔(Kotlin 2.0)
  - 1.8.1: ✔
  - 1.8.0: ✔(Kotlin 1.9)
  - 1.7.3: ✔
  - 1.7.1: ✔(Kotlin 1.8)

    - `TestCoroutineScheduler`, `runTest`, and `TestScope` API are promoted to stable

  - 1.6.4: ✔
  - 1.5.2: ✔

- [org.jetbrains.kotlinx:kotlinx-collections-immutable](https://github.com/Kotlin/kotlinx.collections.immutable/blob/master/CHANGELOG.md)

  - 0.3.8: ✔
  - 0.3.7: ✔
  - 0.3.5: ✔

- [com.google.devtools.ksp](https://github.com/google/ksp/releases)

  - 2.1.21-2.0.2: ✔

## Google

- com.android.tools.build:gradle(& Android Studio)
  
  `What's New` in Android Studio has more release notes.

  - 8.13.0(Narwhal 3 Feature Drop | 2025.1.3, Gradle 8.13): ??
    - [Android view setting to display build files under corresponding modules](https://developer.android.com/studio/releases/past-releases/as-narwhal-3-feature-drop-release-notes#android-view-build-files)
    - [Play Policy Insights in Android Studio](https://developer.android.com/studio/releases/past-releases/as-narwhal-3-feature-drop-release-notes#play-policy-insights)
    - [Test and develop with app backup and restore](https://developer.android.com/studio/releases/past-releases/as-narwhal-3-feature-drop-release-notes#test-backup-restore)
    - [New setting to disable Automatic Sync](https://developer.android.com/studio/releases/past-releases/as-narwhal-3-feature-drop-release-notes#auto-sync-setting)
  - 8.12.2(Narwhal 2025.1.2, Gradle 8.13): ❌(Mysterious 8.12.3)
    - [Studio Labs](https://developer.android.com/studio/releases/past-releases/as-narwhal-release-notes#studio-labs)
    - [Gemini in Android Studio's Agent Mode](https://developer.android.com/studio/releases/past-releases/as-narwhal-feature-drop-release-notes#agent-mode)
    - [16 KB page size support](https://developer.android.com/studio/releases/past-releases/as-narwhal-feature-drop-release-notes#16kb-support)
    - [Partner Device Labs available with Android Device Streaming](https://developer.android.com/studio/releases/past-releases/as-narwhal-feature-drop-release-notes#partner-device-labs)
    - [K2 mode by default](https://developer.android.com/studio/releases/past-releases/as-narwhal-feature-drop-release-notes#k2-mode-default): ❗ No warnings on deprecated fields
  - 8.10.1(Meerkat 2024.3.2, Gradle 8.11.1): ✔
    - [KMP Shared Module integration with Android applications](https://developer.android.com/studio/releases/past-releases/as-meerkat-release-notes#kmp-template)
    - K2: ❌
  - 8.8.2(Ladybug 2024.2.2, Gradle 8.10.2): ✔
    - [New Gemini in Android Studio features](https://developer.android.com/studio/releases/past-releases/as-ladybug-feature-drop-release-notes#gemini-in-as)
    - [localeFilters](https://developer.android.com/build/releases/gradle-plugin-api-updates#resconfig-deprecated)
  - 8.6.1(Koala 2024.1.2, Gradle 8.7): ✔ Lots of bugs, but at least usable.
  - 8.4.2(JellyFish 2023.3.1, Gradle 8.6): ❌ Broken Logcat and ADB Wifi
    - [Gemini](https://developer.android.com/studio/preview/gemini)
  - 8.3.2(Iguana 2023.2.1, Gradle 8.4, JDK 17, Maxium API Level 34): ✔

    - [❗ Unable to install APK with spaces in name](https://issuetracker.google.com/issues/317131109): `archivesBaseName`
    - [Baseline Profiles module wizard](https://developer.android.com/studio/releases/past-releases/as-iguana-release-notes#baseline-profiles-module-wizard)
    - [UI Check mode in Compose Preview](https://developer.android.com/studio/releases/past-releases/as-iguana-release-notes#compose-ui-check)

  - 8.2.2(Hedgehog 2023.1.1): ✔
    - [❗ How I fix NOTHING TO SHOW in my vector asset in android studio](https://stackoverflow.com/questions/64382564/how-i-fix-nothing-to-show-in-my-vector-asset-in-android-studio)
    - [Live Edit updated manual mode shortcut](https://developer.android.com/studio/releases/past-releases/as-hedgehog-release-notes#live-edit-manual-mode)(`Control`+`\`)
    - [SDK Upgrade Assistant updates](https://developer.android.com/studio/releases/past-releases/as-hedgehog-release-notes#sdk-upgrade-assistant)
  - 8.1.4(Giraffe): Skipped

    - ❗ Upgrade all dependencies to the latest first
    - 🤔 Proguard removes empty constructors?

      ```Text
      -keepclassmembers class * extends foo.bar.Class {
          <init>();
      }
      ```

    - [Kotlin DSL is the default for build configuration](https://developer.android.com/build/releases/past-releases/agp-8-1-0-release-notes#kotlin-dsl)

      [Migrate your build configuration from Groovy to Kotlin](https://developer.android.com/build/migrate-to-kotlin-dsl)

  - 8.0.2(Flamingo, 2022.2.1): ✔

    - [🟠 Retrofit After enable R8 full mode getting ParameterizedType error](https://github.com/square/retrofit/issues/3751) Gone in retrofit 2.11.0
    - [🟠 Proguard Rules (OkHttp 4.7.2)](https://github.com/square/okhttp/issues/6258) Gone in okhttp 4.12.0
    - AGP Upgrade Assistant 👍
    - [JDK 17 required to run AGP 8.0](https://developer.android.com/build/releases/past-releases/agp-8-0-0-release-notes#jdk-17-agp)

  - 7.4.2: ❌ Broken

    - New Logcat
  
  - 7.3.1: ✔

    Since 7.1.3:

    - [Gradle Managed Devices](https://developer.android.com/studio/test/gradle-managed-devices)
    - [Jetifier warning and check in Build Analyzer](https://developer.android.com/studio/releases/gradle-plugin#jetifier-build-analyzer)
    - [Package attribute in manifest file is deprecated](https://developer.android.com/studio/releases/gradle-plugin#package-deprecated)

  - 7.2.2: ❌ Basically broken
  - 7.1.3: ✔
  - 7.0.4: ✔

- [androidx.appcompat:appcompat](https://developer.android.com/jetpack/androidx/releases/appcompat)

  - 1.7.1: ✔
  - 1.7.0: ✔
  - 1.6.1: ✔

    - `AppCompatDelegate.setApplicationLocales(LocaleListCompat)`

  - 1.5.1: ✔(Android 13(33))
  - 1.4.2: ✔
  - 1.4.1: ✔(Android 12(31))
  - 1.3.1: ✔

- [androidx.activity:activity](https://developer.android.com/jetpack/androidx/releases/activity)

  - 1.11.0: ??(Android 16(36))
  - 1.10.1: ✔(Android 15(35))
    - `LocalActivity`
  - 1.9.0: ✔

    - `OnUserLeaveHintProvider`
    - APIs now warn when calling `onBackPressedDispatcher.onBackPressed()`
    - `activity-ktx` is now completely empty.

  - 1.8.1: -

    - `ComponentActivity.enableEdgeToEdge()` has been added to easily set up the edge-to-edge display in a backward-compatible manner.

  - 1.7.2: ✔(Kotlin 1.8, Compose 2023.06.01)

- [androidx.fragment:fragment](https://developer.android.com/jetpack/androidx/releases/fragment)

  - 1.8.9: ??
  - 1.8.8: ✔
  - 1.8.6: ✔
  - 1.8.5: ✔
  - 1.8.4: ✔
  - 1.8.2: ✔

    - `Composable`(`fragment-compose`): `AndroidFragment`

  - 1.7.1: ✔
  - 1.7.0: ✔
  - 1.6.2: ✔
  - 1.6.1: ✔
  - 1.5.6: ✔
  - 1.4.1: ✔(Android 12(31))

    - FragmentContainerView.getFragment

  - 1.3.6: ✔

- [androidx.lifecycle:lifecycle](https://developer.android.com/jetpack/androidx/releases/lifecycle)

  - 2.9.4: ??
  - 2.9.3: ??
  - 2.9.1: ✔

    - `SavedStateHandle.getMutableStateFlow`
    - KotlinX Serialization Support

  - 2.8.7: ✔
  - 2.8.6: ⬆(Compose 1.7)
  - 2.8.4: ✔
  - 2.8.2: ✔

    - `LocalLifecycleOwner` => `androidx.lifecycle:lifecycle-runtime-compose`
    - `onClick: () -> Unit = dropUnlessResumed/Started { navController.navigate(NEW_SCREEN) }`
    - `ViewModel.viewModelScope` is now an overridable constructor parameter, allowing you to inject your own dispatcher

  - 2.7.0: ✔(Android 14(34))

    - Read [Important changes since 2.6.0](https://developer.android.com/jetpack/androidx/releases/lifecycle#2.7.0)
    - `LiveData.map`, `switchMap`
    - `SavedStateHandle`, `Array<CustomParcelable>`
    - `LifecycleEventEffect`, `LifecycleStartEffect`, `LifecycleResumeEffect`

  - 2.6.2: ✔
  - 2.6.1: ✔(Kotlin 1.8)

    - `LiveData.isInitialized`
    - ~~`Lifecycle.launchWhenX`~~
    - If you want `Observer.onChanged()` to accept a nullable type, you must instantiate the `Observer` with a nullable type.

  - 2.5.1: ✔
  - 2.4.1: ✔(Android 12(31))

    - ~~lifecycle-common-java8~~

  - 2.3.1: ✔

- [androidx.core:core-ktx](https://developer.android.com/jetpack/androidx/releases/core)

  - 1.17.0: ??
  - 1.16.0:  ✔
    - JSpecify nullness annotations
    - KTX
      - `Bitmap.createBitmap`
      - `Bitmap.createScaledBitmap`
      - `Color.parseColor`
      - `BitmapDrawable(`
      - `.getPixel`
      - `.childCount`
      - `.edit()`
      - `ColorDrawable`
  - 1.15.0: ^(Android 15(35))
  - 1.13.1: ✔
  - 1.12.0: ✔(Android 14(34))

    - New `LocalePreferences` APIs to help developers to easily access locale data or user's locale preferences.
    - Added `LinkMovementMethodCompat` that fixes link touch handling
    - Added a `SoftwareKeyboardControllerCompat` to provide direct methods to show and hide the software keyboard given a View.

  - 1.10.1: ✔(Kotlin 1.8)

    - Adds compatibility methods for new APIs introduced in Android 13 for Parcels, Bundles, and Intents.

  - 1.9.0: ✔(Android 13(33))
  - 1.8.0: ✔
  - 1.7.0: ✔(Android 12(31))
  - 1.6.0: ✔

- [androidx.collection:collection-ktx](https://developer.android.com/jetpack/androidx/releases/collection)

  - 1.5.0: ✔
    - `SieveCache` is a new allocation-free replacement for `LruCache` that offers better hit ratio characteristics.
  - 1.4.5: ✔
  - 1.4.0: ✔

    - `ScatterMap`, `ScatterSet`, and `ObjectList`.

  - 1.3.0: ✔

    - You can safely drop any dependencies on `collection:collection-ktx` in favor of `collection:collection` as `collection-ktx` is now empty.
    - ❗ `ArrayMap.isEmpty` -> `ArrayMap.isEmpty()`

  - 1.2.0: ✔

- [com.android.tools:desugar_jdk_libs](https://github.com/google/desugar_jdk_libs)

  - 2.1.5: ✔
  - 2.1.3: ❌(AGP 8.0)
    - java.lang.IllegalArgumentException: this and base files have different roots
  - 2.0.4: ✔
  - 2.0.3: ✔(AGP 7.4)
  - 1.2.2: ✔(AGP 7.3, Android 13(33))
  - 1.1.5: ✔

    - `getDisplayName` doesn't work.

- [androidx.constraintlayout:constraintlayout](https://developer.android.com/jetpack/androidx/releases/constraintlayout)

  - 2.2.1: ✔
  - 2.2.0: ✔
  - 2.1.4: ✔

    - > Gone<->Visible components are now animated as expected

- androidx.constraintlayout:constraintlayout-compose

  - 1.1.1: ✔
  - 1.1.0: ✔(Compose 1.7)
    - ! `Dimension.ratio` only allows `W:H`
  - 1.0.1: ✔

- [androidx.recyclerview:recyclerview](https://developer.android.com/jetpack/androidx/releases/recyclerview)

  - 1.4.0: ✔(Android 15)
  - 1.3.2: ✔ Depending on a beta viewpager2
  - 1.3.0: ✔

    - `ConcatAdapter.getWrappedAdapterAndPosition`

  - 1.2.1: ✔

- [androidx.viewpager2:viewpager2](https://developer.android.com/jetpack/androidx/releases/viewpager2)

  - 1.1.0: ✔
  - 1.0.0: ✔

- [androidx.navigation:navigation](https://developer.android.com/jetpack/androidx/releases/navigation)

  - 2.9.4: ??
  - 2.9.3: ✔
  - 2.9.1: ✔
  - 2.8.6: ✔
  - 2.8.5: ✔
  - 2.8.4: ✔(Compose 1.7)

    - Navigation Kotlin DSL Type-Safety(used by Navigation Compose)
    - `navigation-fragment-compose`: `<composable android:id="@+id/home_screen"...`

  - 2.7.7: ✔(Android 14(34))
  - 2.5.3: ✔
  - 2.4.2: ✔
  - 2.3.5: ✔

- [androidx.room:room](https://developer.android.com/jetpack/androidx/releases/room)

  - 2.8.1: ??
  - 2.8.0: ??
  - 2.7.2: ✔(Kotlin 2.1)
  - 2.6.1: ✔(Android 14(34))
    - [The new artifact for the Room Gradle Plugin](https://developer.android.com/jetpack/androidx/releases/room#2.6.0-alpha02)
  - 2.5.2: ✔
  - 2.4.3: ✔(Kotlin 1.7)
  - 2.4.1: ✔(Android 12(31))

    - Auto Migrations👍

  - 2.3.0: ✔

- [androidx.preference:preference](https://developer.android.com/jetpack/androidx/releases/preference)

  - 1.2.1: ✔
  - 1.1.1: ✔

- [androidx.work:work](https://developer.android.com/jetpack/androidx/releases/work)

  - 2.10.3: ❌
  - 2.10.2: ❌
  - 2.10.1: ✔(Android 15(35))
  - 2.9.1: ✔
  - 2.9.0: ✔(Android 14(34))

    - `work-runtime-ktx` is now empty.
    - `override val workManagerConfiguration: androidx.work.Configuration`

  - 2.8.1: ✔(Kotlin 1.7.21)
  - 2.7.1: ✔(Android 12(31))
  - 2.6.0: ✔

- [androidx.startup:startup](https://developer.android.com/jetpack/androidx/releases/startup)

  - 1.2.0: ✔
  - 1.1.1: ✔

- [androidx.media3](https://developer.android.com/jetpack/androidx/releases/media3)

  - 1.8.0: ✔
    - `ExoPlayer.setScrubbingModeEnabled(boolean)`
    - `DefaultEncoderFactory.setEnableCodecDbLite`
    - Filling an initial gap (added via `addGap()`) with silent audio now requires explicitly setting `experimentalSetForceAudioTrack(true)` in `EditedMediaItemSequence.Builder`.
  - 1.6.1, 1.7.1: ✔
  - 1.4.1: ✔

- [androidx.security:security-crypto](https://developer.android.com/jetpack/androidx/releases/security)

  - 1.1.0-alpha06: ✔
  - 1.1.0-alpha03: ✔

- androidx.swiperefreshlayout:swiperefreshlayout

  - 1.1.0: ✔

- [androidx.exifinterface:exifinterface](https://developer.android.com/jetpack/androidx/releases/exifinterface)

  - 1.4.1: ✔
  - 1.3.7: ✔
  - 1.3.6: ✔

- [androidx.window:window](https://developer.android.com/jetpack/androidx/releases/window)

  - 1.4.0: ✔
  - 1.3.0: ✔

    - `WindowSizeClass`

  - 1.2.0: ✔
  - 1.1.0: ✔
  - 1.0.0: ✔

- [androidx.datastore](https://developer.android.com/jetpack/androidx/releases/datastore)
  - 1.1.7: ??
  - 1.1.2: ❌

- [Jetpack Compose](https://developer.android.com/jetpack/androidx/releases/compose)

  - [BOM to library version mapping](https://developer.android.com/develop/ui/compose/bom/bom-mapping)

  - 2025.10.00: ??(Compose 1.9.3)
  - 2025.09.01: ??(Compose 1.9.2, Lifecycle 2.9.4)
  - 2025.09.00: ??(Compose 1.9.1)
  - 2025.08.01: ✔(Compose 1.9.0)
    - ❗ `ViewCompat.setOnApplyWindowInsetsListener` doesn't work on `ComposeView`
    - `LocalIndication provides`: `IndicationNodeFactory`  
    - `Modifier.dropShadow()` and `Modifier.innerShadow()`
    - `Modifier.onVisibilityChanged` and `Modifier.onFirstVisible`
    - `rememberLazyListState(cacheWindow =  LazyLayoutCacheWindow())`
    - `OutputTransformation`
    - `Modifier.scrollable2D`
  - 2025.06.01, 2025.07.00: ✔(Compsoe 1.8.3)
  - 2025.06.00: ✔(Compose 1.8.2)
    - `Modifier.semantics { contentType = ContentType.Username }`
    - `autoSize = TextAutoSize.StepBased()`
    - `Modifier.onLayoutRectChanged`
    - `LocalOverscrollConfiguration` -> `LocalOverscrollFactory`
    - `LocalConfiguration.current.screenWidthDp` -> `LocalWindowInfo.current.containerSize`
    - ~~`ContextualFlowRow` and `ContextualFlowColumn`~~
  - 2025.04.01: ⬆️(Android 15, Compose 1.8)
  - 2025.04.00: ⬆️
  - 2025.02.00, 2025.03.01: ✔(Compose 1.7.8)
  - 2024.12.01: ✔(Compose 1.7.6)
  - 2024.11.00: ✔(Compose 1.7.5)

    - Apply `AppCompatDelegate.setDefaultNightMode`
    - `LocalRippleTheme` -> `LocalRippleConfiguration`
    - `windowInsets =` -> `contentWindowInsets =`
    - `CoordinatorLayout`

  - 2023.06.01: ✔(Compose UI 1.4 + Kotlin 1.8.22 + Compose Compiler Plugin 1.4.8 + accompanist 0.30.1)

- [com.google.android.material:material](https://github.com/material-components/material-components-android/releases)

  - 1.9.0: ??

    Set the background color:

    ```XML
      <style name="AppTheme" parent="Theme.Material3.DayNight.NoActionBar">
          <item name="elevationOverlayEnabled">false</item>
          <item name="android:colorBackground">#FFF</item>
          <item name="colorSurface">#FFF</item>
      </style>
    ```

  - 1.4.0: ✔

- com.google.android.flexbox:flexbox

  - 3.0.0: ✔

- [com.google.dagger:dagger](https://github.com/google/dagger/releases)

  - 2.57.1: ??
  - 2.56.2: ✔(Kotlin 2.1)
  - 2.55: ✔
  - 2.52: ✔
  - 2.51.1: ✔
  - 2.50: ✔(Hilt Kotlin 1.9)
  - 2.47: ✔
  - 2.46.1: ✔
  - 2.45: ✔
  - 2.44.2: ✔
  - 2.41: ✔
  - 2.40.5: ✔

- androidx.hilt:hilt-*

  - 1.3.0: ??
  - 1.2.0: ✔(Android 1.9)
  - 1.0.0: ✔

- [com.android.billingclient:billing](https://developer.android.com/google/play/billing/release-notes)

  - 7.1.1: ✔(Android 14(34))

    - `enablePendingPurchases()` => `enablePendingPurchases(PendingPurchasesParams.newBuilder().enableOneTimeProducts().build())`
    - ~~`BillingClient.queryPurchaseHistoryAsync()`~~

  - 6.2.1: ✔
  - 6.0.1: ✔
  - 4.1.0: ✔
  - 3.0.2: ✔

- [com.google.android.gms:play-services-ads](https://developers.google.com/admob/android/rel-notes)

  - 22.2.0: ✔
  - 21.4.0: ✔
  - 20.6.0: ✔

- [com.google.android.gms:play-services-auth](https://developers.google.com/android/guides/releases)

  - 21.1.1: 🚧
  - 21.0.0: ❗

    - Removed Credentials API. Replaced by Credential Manager.

  - 20.6.0: ✔
  - 20.2.0: ✔

## Third-party

- [com.squareup.okhttp3:okhttp](https://github.com/square/okhttp/blob/master/CHANGELOG.md)

  - 4.12.0: ✔(Kotlin 1.9)
  
    - No more proguard rules

  - 4.11.0: ✔
  - 4.10.0: ✔
  - 4.9.3: ✔

- [com.squareup.retrofit2:retrofit](https://github.com/square/retrofit/blob/trunk/CHANGELOG.md)

  - 3.0.0: ✔
  - 2.11.0: ✔

    - No more proguard rules

  - 2.9.0: ✔

- [com.squareup.moshi:moshi](https://github.com/square/moshi/blob/master/CHANGELOG.md)

  - 1.15.2: ✔
  - 1.15.1: ✔(Kotlin 1.9)
  - 1.15.0: ✔(Kotlin 1.8)
  - 1.14.0: ✔(Kotlin 1.7, Android 13(33))

    - No more `android.jetifier.ignorelist=moshi-1.13.0`

  - 1.13.0: ✔

    - Kotlin 1.6.0
    - AGP 7.0.x(and 7.1.x?): [`android.jetifier.ignorelist=moshi-1.13.0` in `gradle.properties`](https://github.com/square/moshi/issues/1463)

  - 1.12.0: ✔

- [com.squareup.okio:okio](https://github.com/square/okio/blob/master/CHANGELOG.md)

  - 3.15.0: ✔
  - 3.10.2: ⬆️(Kotlin 2.1)
  - 3.9.1: ✔
  - 3.9.0: ✔(Kotlin 1.9)
  - 3.3.0: ✔(Kotlin 1.8)
  - 3.2.0: ✔
  - 2.10.0: ✔

- com.squareup.leakcanary:leakcanary-android-startup

  - 2.14: ✔
  - 2.13: ✔
  - 2.12: ✔
  - 2.10(Android 13(33)): ✔
  - 2.9.1: ✔

- com.jakewharton.timber:timber

  - 5.0.1: ✔

- [com.github.bumptech.glide](https://github.com/bumptech/glide/releases)

  - 4.16.0: ✔
  - 4.15.1: ✔
  - 4.14.2: ✔(Android 13(33))
  - 4.13.2: ✔
  - 4.12.0: ✔
  
- [io.coil-kt](https://github.com/coil-kt/coil)

  - 3.2.0: ✔(Kotlin 2.1)
  - 2.7.0: ⬆️(Kotlin 2.0)
  - 2.5.0: ⬆️(Kotlin 1.9, Android 14(34))
  - 2.4.0: ✔

- [com.airbnb.android:lottie](https://github.com/airbnb/lottie-android/releases)

  - 6.5.2: 🚧(ImageAssetManager NPE Fixed)
  - 6.1.0+: ❌ ImageAssetManager NPE
  - 6.0.1: ✔

    - Allow animations in zip files to contain embedded base64 encoded images

  - 5.2.0: ✔
  - 5.0.3: ✔
  - 4.2.2: ✔

- com.github.chuckerteam.chucker:library

  - 4.1.0: ??
  - 4.0.0: ✔(Material 1.9.0)
  - 3.5.2: ✔

- com.evernote:android-job

  - 1.4.2: ✔(Doesn't work on Android 12(31))

- pub.devrel:easypermissions

  - 3.0.0: ✔

- [com.mikepenz:fastadapter](https://github.com/mikepenz/FastAdapter/releases)

  - 5.7.0: ✔(Kotlin 1.7, Android 13(33))
  - 5.6.0: ✔(Android 12(31))
  - 5.5.1: ✔

- [org.greenrobot:eventbus](https://github.com/greenrobot/EventBus/releases)
  
  - 3.3.1: ✔
  - 3.2.0: ✔

- [net.lingala.zip4j:zip4j](https://github.com/srikanth-lingala/zip4j/releases)

  - 2.11.5: ✔
  - 2.10.0: ✔(UTF-8 Support)
  - 2.9.1: ✔

- [com.github.kizitonwose:CalendarView](https://github.com/kizitonwose/CalendarView/releases)

  - 1.0.4: ✔(java.time)
  - 0.3.5: ✔(ThreeTenABP)

- [com.github.ben-manes:gradle-versions-plugin](https://github.com/ben-manes/gradle-versions-plugin)

  - 0.52.0: ✔
  - 0.51.0: ✔
  - 0.50.0: ✔

- [com.dropbox.dependency-guard:dependency-guard](https://github.com/dropbox/dependency-guard)

  - 0.5.0: ✔
  - 0.4.3: ✔
  - 0.3.2: ✔

- [com.facebook.android:facebook](https://github.com/facebook/facebook-android-sdk)

  - 13.2.0: !

    > The client token is now mandatory. SDK initialization will have a check on it and throw out an exception if the client token is not available.

  - 13.1.0: ✔

- [pl.droidsonroids.gif:android-gif-drawable](https://github.com/koral--/android-gif-drawable)

  - 1.2.29: ✔(Kotlin 2.0)
  - 1.2.28: ✔
  - 1.2.25: ✔

- [com.scottyab:rootbeer-lib](https://github.com/scottyab/rootbeer)

  - 0.1.1: 🚧(Android 15(35))
  - 0.1.0: ✔

## Chinese

- [com.tencent.bugly:crashreport](https://bugly.qq.com/docs/release-notes/release-android-bugly/)

  - 4.1.9.3: ✔
  - 4.1.9.2: ✔
  - 4.0.4: ✔

    - Requires setting `deviceID`, `deviceModel`, `setIsDevelopmentDevice` and `closeNativeReport` manually

  - 3.4.4: ✔

- [com.umeng.umsdk:common](https://devs.umeng.com/)

  - 9.8.8: ??
  - 9.8.5: ✔
  - 9.8.0: ✔
  - 9.7.5: ✔
  - 9.6.8: ✔
  - 9.5.2: ✔
  - 9.4.7: ✔
  - 9.4.5, 9.4.6: ❌ broken
  - 9.4.4: ✔

- com.umeng.umsdk:asms

  - 1.8.7.2: ✔
  - 1.8.6: ✔
  - 1.8.3: ✔
  - 1.6.3: ✔
  - 1.4.1: ✔

- com.umeng.umsdk:share

  - 7.3.7: ✔
  - 7.3.5: ✔
  - 7.3.4: ✔
  - 7.2.0: ✔
  - 7.1.7: ✔

- com.umeng.umsdk:apm

  - 2.0.6: ??
  - 2.0.4: ✔

- [com.tencent.mm.opensdk:wechat-sdk-android-without-mta](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/Android.html): Deprecated

  - 6.8.0: ✔

- [com.tencent.mm.opensdk:wechat-sdk-android](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/Android.html)

  - 6.8.34: ✔

- [com.qiniu:qiniu-android-sdk](https://github.com/qiniu/android-sdk/releases)

  - 8.9.1: ✔
  - 8.8.0: ✔
  - 8.7.0: ✔
    - ❗ `android.permission.READ_PHONE_STATE`
  - 8.5.2: ✔
  - 8.4.4: ✔
  - 8.0.3: ✔(APK bloats afterwards)

- [com.github.Meituan-Dianping.walle:library](https://github.com/Meituan-Dianping/walle)

  - 1.1.7: ✔
  - 1.1.6: ✔

- [com.alipay.sdk:alipaysdk-android](https://opendocs.alipay.com/common/02km9l)

  - 15.8.35: ✔
  - 15.8.33: ✔
  - 15.8.17: ✔
  - 15.8.16: ✔
  - 15.8.14: ✔
  - 15.8.11: ✔

- [QQ](https://wiki.connect.qq.com/sdk%E4%B8%8B%E8%BD%BD)、穿山甲需要手动添加
