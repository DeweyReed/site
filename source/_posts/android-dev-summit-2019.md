---
layout: post
title: Android Dev Summit 2019 笔记
date: 2019/11/13
categories: [Android, Notes]
---

RT

<!--more-->

### USB

- MAC: `system-Profiler SPUSBDataType`
- Linux: `lsusb -vvv`
- Windows: `USBView.exe`

### Gradle

`settings.gradle`: `project(":core").projectDir = new File(rootDir, "libraries/core")`

```Groovy
def androidx = [:]
androidx.appcompat = "androidx.apcompat:appcompat:1.1.0"

ext.libs = [:]
exit.libs.androidx = androidx

implementation libs.androidx.appcompat
```

### ADB

```Bash
adb emu fold
adb emu wm size
adb emu wm size 1148x2480
adb emu wm size reset
```

### Drawing Behind System Bar

1. Change system bar colors

    values-v29/themes.xml:

    ```XML
    <item name="android:navigationBarColor">@android:color/transparent</item>
    ```

    values/themes.xml: `#B3FFFFFF`(70% white)

    values-night/themes.xml: `#B3000000`(70% black)

1. Request to be laid out fullscreen

    ```Kotlin
    view.systemUiVisibility =
        // We wish to be laid out as if the navigation bar was hidden
        View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
        // We wish to be laid out fullscreen, behind the status bar
        View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
        // We wish to be laid out at the most extreme scenario of any other flags
        View.SYSTEM_UI_FLAG_LAYOUT_STABLE
    ```

1. Avoid overlays with system UI

    Always use the ViewCompat method:

    ```Kotlin
    ViewCompat.setOnApplyWindowInsetsListener(view) { v, insets ->
        // Do something with the insets
        v.updatePadding(
            bottom = insets.systemWindowInsets.bottom
        )
        v.updateLayoutParams<MarginLayoutParams>(
            bottomMargin = insets.systemWindowInsets.bottom
        )
        // return the insets
        insets
    }
    ```

    ```Kotlin
    WindowInsetsCompat.getSystemWindowInsets(): Insets
    WindowInsetsCompat.getSystemGestureInsets(): Insets
    WindowInsetsCompat.getMandatorySystemGestureInsets(): Insets
    ```

    onLayout/onDraw: `View.setSystemGestureExclusionRects(List<Rect> rects)`

### Scopes

Fragment, Fragment ViewModel, Activity, Activity ViewModel, Application & its ViewModel

### One way to handle back

```Kotlin
val dispatcher by lazy { requireActivity().onBackPressedDispatcher }
lateinit var callback: OnBackPressedCallback

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    callback = dispatcher.addCallback(this) {
        showConfirmDialog()
    }
}

private fun onConfirm() {
    callback.enabled = false
    dispatcher.onBackPressed()
}
```

### Intent Handling

1. A lot of intent handlers couble be disabled.
1. Check if an intent handler exists, don't assume it will just work.

```Kotlin
if (intent.resolveActivity(packageManager) == null) {
    // Error!
} else {
    startActivity(intent)
}
```

### Scoped Storage

1. Read only video/image/audio files

    MediaStore + READ_EXTERNAL_STORAGE

    ```Kotlin
    data class Video(val uri: Uri, val name: String, val duration: Int, val size: Int)
    val videoList = mutableListOf<Video>()

    val projection = arrayOf(
        MediaStore.Video.Media._ID,
        MediaStore.Video.Media.DISPLAY_NAME,
        MediaStore.Video.Media.DURATION,
        MediaStore.Video.Media.SIZE
    )

    val selection = "Video.Media.DURATION) >= ?"
    val selectionArgs = arrayOf(
        TimeUnit.MILLISECONDS.convert(5, TimeUnit.MINUTES).toString()
    )
    val query = contentResolver.query(
        MediaStore.Video.Media.EXTERNAL_CONTENT_URI,
        projection,
        selection,
        selectionArgs,
        "${MediaStore.Video.Media.DISPLAY_NAME} ASC"
    )

    query?.use { cursor ->
        val idColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media._ID)
        val nameColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DISPLAY_NAME)
        val durationColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DDRATION)
        val sizeColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.SIZE)

        while(cursor.moveToNext()) {
            // we 11 use the column indexes that we found above
            val id = cursor.getLong(idColumn)
            val name = cursor.getString(nameColumn)
            val duration = cursor.getInt(durationColumn)
            val size = cursor.getInt(sizeColumn)

            val contentUri = ContentUris.withAppendedId(
                MediaStore Video Media EXTERNAL_CONTENT_URI. id
            )

            videolist += Video(contentUri, name, duration, size)
        }
    ```

1. Read non-media files

    SAF(No Permission): ACTION_OPEN_DOCUMENT, ACTION_OPEN_DOCUMENT_TREE + takePersistableUriPermission

    ```Kotlin
    fun intentPickDocument() {
        val intent = Intent(Intent.ACTION_OPEN_DOCUMENT).apply {
            addCategory(Intent.CATEGORY_OPENABLE)
            type = "application/pdf"
        }
        startActivityForResult(intent, PICK_PDF_FILE)
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, resultData: Intent?) {
        super.onActivityResult(requestCode, resultCode, resultData)

        if (requestCode == PICK_PDF_FILE && resultCode == Activity.RESULT_OK) {
            resultData?.data?.let { uri ->
                renderPdf(uri)

                contentResolver.query(uri, null, null, null, null, null)?.use {
                    val name = it.getString(cursor.getColumnIndexOrThrow(OpenableColumns.DISPLAY_NAME))
                    val size = it.getString(cursor.getColumnIndexOrThrow(OpenableColumns.SIZE)) // May throw
                }
            }
        }
    }
    ```

1. Add & Edit image/video/audio files

    MediaStore + WRITE_EXTERNAL_STORAGE(READ?) + ACCESS_MEDIA_LOCATION to access EXIF data

    ```Kotlin
    val selectedImageUri = Uri.parser("content://media/external/images/media/44")

    fun editImage() {
        try {
            contentResolver.openFileDescriptor(selectedtmageUri, "w")?.use {
                setGrayscaleFilter(it)
            }
        } catch (securityException: SecurityException) {
            if (Build.VERSIN.SDK_INT >= Build.VERSION_CDDES.Q) {
                val recoverableSecurityException = securityException as? RecoverableSecurityException ?: throw securityException
                val intentSender = recoverableSecurityException.userAction.actionIntent.intentSender

                intenSender?.let {
                    startIntentSenderForResult(intentSender, EDIT_IMAGE_REQUEST, null, 0, 0, 0, null)
                }
            } else {
                throw securityException
            }
        }
    }
    ```

1. File management or Backup features need to approved in Play Console.

### ANR

1. Background Services => MediaBrowserServer(API 26)
1. WorkManager
1. Reduced use of SharedPreferences
1. Reorganized the app graph to perform less object instantiation in application's onCreate()

### Theme

`AppTheme` => `Theme.MaterialComponents.Light` => `Theme.AppCompat.Light` => `Theme.Material.Light`

```Kotlin
@ColorInt
fun Context.getThemeColor(
    @AttrRes themeAttrld: Int
): Int {
    return obtainStyledAttributes(
        intArrayOf(themeAttrld)
    ).use {
        it.getColor(0, Color.MAGENTA)
    }
}
```

```Kotlin
val themedContext = ContextThemeWrapper(context, R.style.ThemeOverlay_Owl_Blue)
val inflater = LayoutInflater.from(themedContext)
val view = inflater.inflate(...)
```

- Use literal names, relavant to the value(brand_blue > color_primary)
- Name

    Theme.AppTheme.Blue

    Widget.AppTheme.Toolbar.Green

    StyleType.GroupName.SubGroupName.VariantName

- ~~`<View style="@style/Theme.AppTheme.Foo" />`~~
- Files

  - themes.xml: Themes and ThemeOverlays
  - types.xml: TextApperances, Text size dimens
  - styles.xml: Widget styles
  - dimens.xml, colors.xml, strings.xml

- `?textApperanceHeandline1` > `@style/TextApperance.MaterialComponts.Headline1`

### Permissions

- Mimimum permissions
- Request in context
- Libraries
- Mimimize localtion and background location
