# Hide the status bar

> 笔记整理自[官方文档](https://developer.android.com/training/system-ui/status)
>
> 2020/2/9

## 注意事项

* 隐藏状态栏后，再次滑下状态栏，状态栏就会再次出现。
* 当 UI flags 被清除后，需要重新设置，所以要监听 UI 变化。
* 如果在 `onCreate()` 里调用方法隐藏状态栏，那么如果 home 键再回来，就会失效，所以建议在 `onResume()` 或 `onWindowFocusChanged()`。
* 官方建议在隐藏状态栏时也要隐藏 `ActionBar`。



## 隐藏状态栏（Android 4.0及以下）

Android 4.0（API 14）都2020年了，谁会用啊。

有兴趣自己看[文档](https://developer.android.com/training/system-ui/status#40)



## 隐藏状态栏（Android 4.1及以上）

在 Android 4.1（API 16）及以上，隐藏状态栏使用 `setSystemUiVisibility()` 方法。

```kotlin
window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_FULLSCREEN
```

官方建议，隐藏状态栏时一定也要隐藏 `ActionBar` 

```kotlin
// 文档的 actionBar?.hide() 肯定是没效果的
// 至于下面这么写有没有效果，可能有吧
supportActionBar?.hide()
```



## 让内容显示在状态栏之后

将内容显示在状态栏之后，即状态栏如果没隐藏就会遮住一部分内容。

这么做是为了让隐藏/显示状态栏时，内容UI 不会重新调整大小。

设置两个 flag

```kotlin
View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN	// 这个flag能让内容显示在状态栏之后
View.SYSTEM_UI_FLAG_LAYOUT_STABLE	//这个flag据说能让app的布局保持稳定
```

**注意事项：**

将内容显示在状态栏之后，那么你要确保当状态栏出现之后，想要显示的 UI 不会被挡住。可以给那个 View 添加下面这个属性

```xml
android:fitsSystemWindows = true
```

这样这个View的UI 就会和屏幕顶部保持一段距离。

如果你想要自定义这个距离，那就看[文档](https://developer.android.com/training/system-ui/status#behind)去吧。





