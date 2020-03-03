# Service overview

> 笔记整理自[官方文档](https://developer.android.com/guide/components/services)
>
> 2020/2/15

`Service` 是一个没有 UI 界面的应用组件，它可以由其他的应用组件启动并一直在后台运行。

服务有三种类型：

1. **Foreground**

    前台服务会显示一个通知，当用户不与 App 交互时，前台服务依然能够运行。

2. **Background**

    后台服务不可见。

    > 从 Android 8.0（API 26）开始，如果 App 不在前台，那么系统会[限制后台服务的运行](https://developer.android.com/about/versions/oreo/background.html) 。在大多数情况下，App 应该使用  [scheduled job](https://developer.android.com/topic/performance/scheduling.html)  来代替。

3. **Bound**

    组件能够和服务绑定，并且和服务进行通信（也可以跨进程）。当所有的组件都不与这个服务绑定时，这个服务会销毁。

> 服务并不是运行在一个单独的进程里，虽然没有UI，但还是默认运行在主线程



## 选择服务还是线程

如果当用户不使用 App 时，仍然要执行任务，才要选择使用服务。

> 服务默认运行在主线程，要自己切换



## 基础

要创建一个服务，要继承 `Service` 类，并重写一些回调方法。

* ```java
    public int onStartCommand (Intent intent,int flags,int startId)
    ```

    当 `startService()` 被调用时，系统会调用这个方法。

    从此，服务会在后台无限期运行。如果你实现了这个方法，当任务完成时，你应该调用 `stopSelf()` 或者 `stopService()` 将服务停止。

    > 如果只想绑定服务，不需要实现这个方法



* ```java
    public abstract IBinder onBind (Intent intent)
    ```

    当 `bindService()` 被调用时，系统会调用这个方法。

    返回一个 `IBinder` 用来和客户端交互，如果不允许绑定，则返回 `null` 。



* ```java
    public void onCreate ()
    ```

    当服务创建时，系统会调用此方法（只会调用一次）。

    这个方法在 `onStartCommand()` 和 `onBind()` 之前运行。



* ```java
    public void onDestroy ()
    ```

    当服务销毁时，系统会调用此方法。



### 注意事项

* 如果服务是由 `startService()` 启动，那么 `onStartCommand()` 会被调用。这个服务会无限期运行，除非调用 `stopSelf()` 或 `stopService()` 。
* 如果服务是由 `bindService()` 绑定，那么 `onStartCommand()` 不会被调用。当没有组件绑定这个服务时，这个服务就会被销毁。



### 后台保活

* 前台服务，几乎不会被杀掉
* 与用户正在交互的活动绑定的服务，极少可能被杀掉
* 长时间运行的服务，被杀掉的概率会提高



### 在清单文件声明服务

```xml
<manifest ... >
  ...
  <application ... >
      <service android:name=".ExampleService" />
      ...
  </application>
</manifest>
```

`android:name` 是唯一必要的属性，更多的属性可以看 [清单文件文档](https://developer.android.com/guide/topics/manifest/service-element.html) 。

`android:exported = false` 可以防止其他应用启动服务。



## 创建一个被启动的服务

一个被启动的服务是指，其他组件调用 `startService()` 启动的服务。这个服务的 `onStartCommand()` 会被调用。

当服务被启动后，即使启动它的组件被销毁，服务也会运行。所以，你需要注意调用 `stopSelf()` 或者 `stopService()` 去停止服务。

在 `startService()` 里传入的 `Intent` 能够带有数据，服务将会在 `onStartCommand()` 里得到这个 `Intent` 。

**要创建一个被启动的服务，你有两个类可以选择继承：**

1. **Service**

    这是所有服务的基类。当继承此类时，要记得自己创建一个线程，因为这个类默认运行在主线程

2. **IntentService**

    这是一个 `Service` 的子类。它会运行在工作线程，并处理所有启动请求。但是它只有一个线程，多个同时的请求会进入队列中。具体看下节。



### 继承 IntentService

这个类有下面这些特征：

* 它会创建一个工作线程，处理所有发给 `onStartCommand()` 的 `Intent` 。
* 发给 `onStartCommand()` 的 `Intent` 会进入队列，并交给 `onHandleIntent()` 处理。所以它是单线程的，但不在主线程。
* 当所有启动请求被处理后，会自动停止，不需要调用 `stopSelf()` 。
* 提供默认的 `onBind()` 实现并返回 `null` 。
* 提供默认的 `onStartCommand()` 实现，将 `Intent` 送进队列，并交给 `onHandleIntent()` 处理。

```kotlin
/**
 * A constructor is required, and must call the super [android.app.IntentService.IntentService]
 * constructor with a name for the worker thread.
 */
class HelloIntentService : IntentService("HelloIntentService") {

    /**
     * The IntentService calls this method from the default worker thread with
     * the intent that started the service. When this method returns, IntentService
     * stops the service, as appropriate.
     */
    override fun onHandleIntent(intent: Intent?) {
        // Normally we would do some work here, like download a file.
        // For our sample, we just sleep for 5 seconds.
        try {
            Thread.sleep(5000)
        } catch (e: InterruptedException) {
            // Restore interrupt status.
            Thread.currentThread().interrupt()
        }

    }
}
```

要继承此类只要做两个：

1. 调用父类构造器，传入名字
2. 实现 `onHandleIntent()` 

如果要重写其他的回调函数，一定要调用父类的函数。

如果要重写 `onBind()` 函数，不需要调用父类。

如果要重写 `onStartCommand()` ，一定要像下面这样：

```kotlin
override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
    Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show()
    return super.onStartCommand(intent, flags, startId)
}
```



### 继承Service

使用 `IntentService` 非常的简单，但如果你想要在服务里执行多个线程，就只能自己继承 `Service` 。

#### onStartCommand的返回值

`onStartCommand()` 函数要返回一个整数，这个整数描述了，当服务被杀掉后，系统如何继续这个服务。这个返回值有以下几种情况：

* **START_NOT_STICKY**

    在`onStartCommand()` 函数后，如果服务被系统杀掉，不会重启服务，除非有 pedding intent 要处理。

    这是最安全的服务，可以避免不必要的重启服务。适用于服务中的工作可以重启。

* **START_STICKY**

    在 `onStartCommand()` 函数后，如果服务被杀掉，系统会重启服务（表示怀疑）并重新调用 `onStartCommand()` 函数，并传给 `onStartCommand()` 一个值为 `null` 的 `Intent` 。

* **START_REDELIVER_INTENT**

    在 `onStartCommand()` 函数后，如果服务被杀掉，系统会重启服务（表示怀疑）并重新调用 `onStartCommand()` 函数，并传给 `onStartCommand()` 最后一个 `Intent` 。



## 创建一个绑定的服务

绑定的服务是指，其他组件通过调用 `bindService()` 创建的服务，这种服务一般情况下不允许使用 `startService()` 。

一个服务可以与多个组件绑定。当没有组件与服务绑定时，系统会摧毁这个服务。

与 `startService()` 创建的服务不同，你不需要自己去停止这个服务。

**解绑**服务，使用 `unBind()` 。

服务与其他组件通过 `IBinder` 来沟通。

> 绑定服务的具体，在另一篇笔记介绍



## 前台服务

前台服务会显示一个不可被关闭的通知，前台服务的优先级很高，一般不会被系统杀掉。

> 从 Android 9.0（API 28）开始，使用前台服务需要申请 `FOREGROUND_SERVICE` 权限，这是一个普通权限，不需要动态申请

要让服务运行在前台，调用：

```java
public final void startForeground (int id,Notification notification)
```

id 用来唯一标识通知

> 这个 id 不能是0，通知的优先级至少为 `PRIORITY_LOW`

**撤下前台服务**：

使用 `stopForeground()` 

这个方法只会让服务不在前台运行，并没有停止服务。

如果直接停止服务，前台的通知也会跟着消失。



## 服务的生命周期

服务的生命周期有两种情况，一种是通过 `startService()` 启动的服务，一种是通过 `bindService()` 启动的服务。但这两种并没有完全的分开，通过 `startService()` 启动的服务也可以被绑定，在这种情况下，`stopService()` 和 `stopSelf()` 并不会停止服务，而是要等到服务没有任何绑定。

![img](https://github.com/hahaha28/photos/raw/master/notebooks/Android/service_lifecycle.png)

```kotlin
class ExampleService : Service() {
    private var startMode: Int = 0             // indicates how to behave if the service is killed
    private var binder: IBinder? = null        // interface for clients that bind
    private var allowRebind: Boolean = false   // indicates whether onRebind should be used

    override fun onCreate() {
        // The service is being created
    }

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        // The service is starting, due to a call to startService()
        return mStartMode
    }

    override fun onBind(intent: Intent): IBinder? {
        // A client is binding to the service with bindService()
        return mBinder
    }

    override fun onUnbind(intent: Intent): Boolean {
        // All clients have unbound with unbindService()
        return mAllowRebind
    }

    override fun onRebind(intent: Intent) {
        // A client is binding to the service with bindService(),
        // after onUnbind() has already been called
    }

    override fun onDestroy() {
        // The service is no longer used and is being destroyed
    }
}
```

> 与 Activity 不同，服务的回调函数不需要调用父类的实现