# qEnable fullscreen mode

> 笔记整理自[官方文档](https://developer.android.com/training/system-ui/immersive)
>
> 2020/2/9

## 三种全屏模式

Android 提供三种全屏模式：

1. Lean Back
2. Immersive
3. Immersive Sticky

这三种模式都能使系统栏消失，区别在于用户怎么使系统栏回来。

### 1.Lean Back

* 点击屏幕任何位置都会出现系统栏

* 这种模式适用于用户几乎不点击屏幕（看视频）

要使用这种模式，需要设置以下 flag

```kotlin
View.SYSTEM_UI_FLAG_FULLSCREEN or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
```

当系统栏出现后，需要重新设置 flag ，所以要监听变化。

### 2.Immersive

* 滑动上下边缘会出现系统栏
* 这种模式适用于用户需要频繁点击屏幕（游戏，相册）

要使用这种模式，设置下面这些 flag

```kotlin
View.SYSTEM_UI_FLAG_IMMERSIVE
or
View.SYSTEM_UI_FLAG_FULLSCREEN
or
View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
```

### 3.Sticky immersive

* 滑动边缘会短暂出现透明的系统栏
* 这种模式适用于用户不想退出

要使用这种模式，设置下面这些 flag

```kotlin
View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
or
View.SYSTEM_UI_FLAG_FULLSCREEN
or
View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
```

**注意：**

由于这种模式，滑动边缘时系统栏也只会短暂的出现，然后回再消失，所以和前两种模式不同，这种模式没办法监听到 UI 变化。



## 使用全屏模式

无论使用哪种全屏模式，都要调用 `setSystemUiVisibility()` 并传入 flag

```kotlin
activity.window.decorView.apply{
    // 设置flag
    systemUiVisibility = View.SYSTEM_UI_FLAG_FULLSCREEN 
    					or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
}
```

要**退出全屏模式**只要把 flag 设为 0 即可。

> 最好加入其他 flag ，将内容显示在系统栏之后，这样当系统栏出现/隐藏时，UI 才不会反复调整大小
>
> 可以参考笔记中的 [Hide the status bar](Hide the status bar.md)和[Hide the navigation bar](Hide the navigation bar.md)，或者参考下面的示例代码

示例代码：

```kotlin
override fun onWindowFocusChanged(hasFocus: Boolean) {
    super.onWindowFocusChanged(hasFocus)
    if (hasFocus) hideSystemUI()
}

private fun hideSystemUI() {
    // Enables regular immersive mode.
    // For "lean back" mode, remove SYSTEM_UI_FLAG_IMMERSIVE.
    // Or for "sticky immersive," replace it with SYSTEM_UI_FLAG_IMMERSIVE_STICKY
    window.decorView.systemUiVisibility = (View.SYSTEM_UI_FLAG_IMMERSIVE
            // Set the content to appear under the system bars so that the
            // content doesn't resize when the system bars hide and show.
            or View.SYSTEM_UI_FLAG_LAYOUT_STABLE
            or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
            or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
            // Hide the nav bar and status bar
            or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
            or View.SYSTEM_UI_FLAG_FULLSCREEN)
}

// Shows the system bars by removing all the flags
// except for the ones that make the content appear under the system bars.
private fun showSystemUI() {
    window.decorView.systemUiVisibility = (View.SYSTEM_UI_FLAG_LAYOUT_STABLE
            or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
            or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN)
}
```

