# Guide to background processing

> 笔记整理自[官方文档](https://developer.android.com/guide/background)
>
> 2020/2/15

## 后台进程的限制

当一个 App 对用户不可见时，系统会限制它的后台线程。

* **Android 6.0（API 23）**

    引入  [Doze mode and app standby](https://developer.android.com/training/monitoring-device-state/doze-standby)  Doze 模式限制 App 当屏幕关闭且手机静止不动时。App standby 将不使用的应用调整状态，限制网络等。

* **Android 7.0（API 24）**

    限制隐式广播，并引入 [Doze-on-the-Go](https://developer.android.com/about/versions/nougat/android-7.0#doze_on_the_go) 。

* **Android 8.0（API 26）**

    进一步 [limited background behavior](https://developer.android.com/about/versions/oreo/background) ，例如在后台获取位置。

* **Android 9.0（API 28）**

    引入  [App Standby Buckets](https://developer.android.com/topic/performance/appstandby) ，App 申请资源会基于 App 使用动态优化。



## 选择正确的解决方案

![img](https://developer.android.com/images/guide/background/bg-job-choose.svg)



## WorkManager

对于可以推迟的工作，甚至是应用或设备重启也会运行，可以选择 `WorkManager` 。`WorkManager` 会当条件良好（比如网络和电量都好）的时候才会运行任务。



## Foreground services

必须立刻执行，且要完整执行，请使用 foreground service 。

前台服务对用户可见，且用户不能关闭那个通知。



## AlarmManager

如果要工作在精确的时间运行，使用 `AlarmManager` 。

`AlarmManager` 会在精确的时间启动你的 App。



## DownloadManager

如果要执行长时间的 Http 请求，考虑使用 `DownloadManager` 。

`DownloadManager` 能监听 Http 状态，并且在发生错误或网络重连时能重新执行下载。