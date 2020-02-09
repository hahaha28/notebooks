# Hide the navigation bar

> 笔记整理自[官方文档](https://developer.android.com/training/system-ui/navigation)
>
> 2020/2/9

## 注意事项

* 隐藏导航栏后，只要触碰屏幕的任何位置，flag 就会被清除，如果同时也隐藏状态栏，那么也同样。
* 基于上一条，体验挺差的。
* 当 UI flags 被清除后，需要重新设置，所以要监听 UI 变化。
* 如果在 `onCreate()` 里调用方法隐藏状态栏，那么如果 home 键再回来，就会失效，所以建议在 `onResume()` 或 `onWindowFocusChanged()`。
* 官方建议在隐藏导航栏时也要隐藏状态栏也要隐藏 `ActionBar`。



## 隐藏导航栏

使用 `View.SYSTEM_UI_FLAG_HIDE_NAVIGATION` flag

下面的代码同时隐藏了状态栏和导航栏

```kotlin
window.decorView.apply {
    // Hide both the navigation bar and the status bar.
    // SYSTEM_UI_FLAG_FULLSCREEN is only available on Android 4.1 and higher, but as
    // a general rule, you should design your app to hide the status bar whenever you
    // hide the navigation bar.
    systemUiVisibility = View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or View.SYSTEM_UI_FLAG_FULLSCREEN
}
```



## 让内容显示在导航栏后面

将内容显示在导航栏之后，即导航栏如果没隐藏就会遮住一部分内容。

这么做是为了让隐藏/显示导航栏时，内容UI 不会重新调整大小。

设置两个 flag

```kotlin
View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION	// 这个flag能让内容显示在导航栏之后
view.SYSTEM_UI_FLAG_STABLE	//这个flag据说能让app的布局保持稳定
```

