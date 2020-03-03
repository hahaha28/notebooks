# Activity lifecycle

> 整理自以下两篇官方文档
>
> [Understand the Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)
>
> [Handle Activity State Changes](https://developer.android.com/guide/components/activities/state-changes)

**Activity 的所有生命周期函数在重写时都必须调用父类**

![生命周期图](https://github.com/hahaha28/photos/raw/master/notebooks/Android/activity_lifecycle.png)

## 生命周期变化

### 1.Activity 被部分遮盖

当 Activity 部分不可见时会执行 `onPause()` ，但 `onStop()` 不会执行。

仔细看上面的图，这时如果返回 Activity ，会执行 `onResume()` 



### 2.Activity 被完全遮盖

此时 `onStop()` 会被执行，在这之前，`onPause（）` 当然也会。当返回这个 Activity 时，会回到 `onStart()`。

**情况：**

* 启动另一个 Activity
* 弹出一个全屏的对话框
* 按 Home 键
* 按 最近任务键



### 3.配置发生变化

**情况：**

* 改变语言
* 改变屏幕方向
* 分屏

这时，Activity 会被摧毁并重新启动



### 4.onRestart()什么时候会执行

如果 `onStop()` 之后，返回的是之前的Activity且是同一个对象，那么 `onRestart()` 会执行。

如果返回之前的 Activity ,但不是同一个对象，那么 `onRestart()` 不会执行。

> 这么看有点玄学，不如别用了



## 临时保存UI状态

### 为什么要保存UI状态？

考虑这种情况，如果用户在 `EditText` 输入了一段文字，然后用户进行分屏操作，这时 Activity 会被销毁并重新启动，所以用户在 `EditText` 中的文字也会丢失。

### 该怎么做？

使用 `onSaveInstanceState(Bundle)` + `onRestoreInstanceState(Bundle)`



### onSaveInstanceState(Bundle)

* **什么时候会执行这个回调？**

  当Activity跳转时，到后台时，等等。

  但是，Activity 被用户关闭时不会回调。

  重要的是，Activity **分屏**时，**改变方向**时，**语言改变**时，**被系统杀掉时**，这个方法会被回调。

  重写这个方法时一定要**调用父类**，因为系统默认使用这个方法来保存一些数据。



### onRestoreInstanceState(Bundle)

* 这个方法什么时候被回调？
  * 在 `onStart()` 之后，`onResume()` 之前
  * 并且 `Bundle` 不为 `null` 

所以在这个方法里处理数据，不用担心 `Bundle` 为 `null` ，而 `onCreate(Bundle)` 里的 `Bundle` 是有可能为 `null` 的。