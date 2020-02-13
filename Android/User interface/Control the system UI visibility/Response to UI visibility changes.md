# Response to UI visibility changes

> 笔记整理自[官方文档](https://developer.android.com/training/system-ui/visibility)
>
> 2020/2/10

## 注册监听器

要监听一个 `View` 的 UI 可见性变化，只要给他注册一个 `View.OnSystemUiVisibilityChangeListener`

```kotlin
window.decorView.setOnSystemUiVisibilityChangeListener { visibility ->
    // Note that system bars will only be "visible" if none of the
    // LOW_PROFILE, HIDE_NAVIGATION, or FULLSCREEN flags are set.
    if (visibility and View.SYSTEM_UI_FLAG_FULLSCREEN == 0) {
        // TODO: The system bars are visible. Make any desired
        // adjustments to your UI, such as showing the action bar or
        // other navigational controls.
    } else {
        // TODO: The system bars are NOT visible. Make any desired
        // adjustments to your UI, such as hiding the action bar or
        // other navigational controls.
    }
}
```

> 需要注意的是 `immersive sticky` 模式下的UI可见性变化是不会被监听到的