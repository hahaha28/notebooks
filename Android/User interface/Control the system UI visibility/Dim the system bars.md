# Dim the system bars

> 笔记整理自[官方文档](https://developer.android.com/training/system-ui/dim)
>
> 2020/2/9

## 注意

我感觉这玩意相当难用。

在魅族Note9（Android 9.0) 上测试，状态栏上的其他App的通知图标会消失，状态栏会变暗，变模糊，但仍然会显示时间和电量。导航栏原本是白底的，调用方法后会变成黑底的，也会模糊和变暗一点，但还是看得见。总之，效果挺差的。

在虚拟机（Android 10.0）上测试，状态栏上的图标仅剩电量，状态栏会变暗一点，但不会不可见。而导航栏我看不到任何变化。总之，效果很差。



## 概述

这篇文章介绍如何使系统栏变暗（模糊？），仅能在 Android 4.0（API 14）及以上才能使用。

> Dim 不知道怎么翻译比较好，我感觉只是变暗

> 系统栏，包括顶部的状态栏和底部的导航栏

注意，是使系统栏变暗，而**不是让系统栏消失**。界面的内容并不会改变大小，因为系统栏只是不可见，但仍然占据着那个位置，当用户触摸时，系统栏会再次复原。



## Dim 状态栏和导航栏

用下面这段代码就行

```kotlin
// This example uses decor view, but you can use any visible view.
activity?.window?.decorView?.apply {
    systemUiVisibility = View.SYSTEM_UI_FLAG_LOW_PROFILE
}
```

当用户触摸状态栏或系统栏或切到其他App再回来时，这个效果就会消失。



## 代码清除 flag

上一节的那个 flag 除了手动清除，也能用代码清除

```kotlin
activity?.window?.decorView?.apply {
    // Calling setSystemUiVisibility() with a value of 0 clears
    // all flags.
    systemUiVisibility = 0
}
```



