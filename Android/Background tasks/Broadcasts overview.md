# Broadcasts overview

> 笔记整理自 [官方文档](https://developer.android.com/guide/components/broadcasts)
>
> 2020.3.3

[查看总结的脑图](https://github.com/hahaha28/photos/raw/master/notebooks/Android/broadcast-mind.png)



## 概念

* 广播是一种观察者模式
* 当某些事件发生时，系统会发出广播
* 也可以发送自定义广播
* 广播消息封装在 `Intent` 里
  * `action` 代表广播的事件
  * 可以携带数据



## 版本变化

* **Android 9（API 28）**

  从 Android 9 开始，`NETWORK_STATE_CHANGED_ACTION` 广播里不再包含用户的位置和个人信息。



* **Android 8（API26)**

  从 Android 8.0 开始，系统对静态广播接收器施加限制。

  绝大部分的静态广播接收器（在 `manifest` 中注册的）都无法使用。

  > 少数可以用的看 [这个文档](https://developer.android.com/guide/components/broadcast-exceptions)



* **Android 7（API 24）**
  * 系统不再发送两个广播：
    * `ACTION_NEW_PICTURE`
    * `ACTION_NEW_VIDEO`
  * 接收 `CONNECTIVITY_ACTION` 无法使用静态广播接收器



## 接收广播

广播接收器有两种：

1. 在上下文注册的（动态广播）
2. 在清单文件注册的（静态广播）



### 上下文注册的接收器

以监听网络变化为例：

```kotlin
// 首先自定义一个接收器
val br :BroadcastReceiver = object : BroadcastReceiver(){
    override fun onReceive(context:Context?,intent:Intent){
        //网络变化
    }
}
// 定义过滤器，确定你要接收的广播
val filter = IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION)
// 注册
registerReceiver(br,filter)

// 在适当的地方要取消注册
unregisterReceiver(br)
```

> 上面只是监听了网络的变化，要具体知道网络的连接情况，请看 [这篇官方文档](https://developer.android.com/training/monitoring-device-state/connectivity-status-type)

**注意事项：**

* 上下文注册的接收器只要当上下文有效时其才会有效，所以用 `Activity` 的 `Context` 和 `Application` 的 `Context` 是不一样的。
* 接收**本地广播**请使用 `LocalBroadcastManager.registerReceiver(BroadcastReceiver,IntentFilter)`



### 清单文件声明的接收器

当App安装时，这种接收器就会被注册。

**使用方式：**

```kotlin
// 首先写一个实现类
class MyBroadcastReceiver : BroadcastReceiver(){
    override fun onReceive(context:Context, intent:Intent){
        
    }
}
```

```xml
<!-- 在manifest文件中声明 -->
<receiver android:name = ".MyBroadcastReceiver" android:exported ="true">
	<intent-filter>
    	<action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
    </intent-filter>
</receiver>
```



### Effects on process state

> 这部分内容有点偏，有兴趣自己去看官方文档



## 发送广播

程序可以发送自定义的广播：

* 广播消息被封装在 `Intent` 中
* `Intent` 的 `action` 唯一标识这个广播，所以应该用包名做前缀
* 可以用 `putExtra()` 放入数据
* 可以用 `setPackage(String)` 限制接收的 App

### 1.无序广播

广播以随机顺序发送给所有接收器。

```java
sendBroadcast(Intent intent)
```



### 2.有序广播

广播依次传递给所有接收器，每个接收器都可以拦截这个广播。

广播的传递顺序由 `android:priority` 属性决定，优先级相同则随机。

```java
sendOrderBroadcast( Intent intent, String receiverPermission)
```

> 第二个参数可以传入null，具体见[权限限制](#权限限制)



### 3.本地广播

本地广播只在 App 内部传播，更为高效。

```java
LocalBroadcastManager.sendBroadcast(Intent intent)
```



## 权限限制

> 这部分内容有点偏，不做详细笔记，有兴趣去看[官方文档](https://developer.android.com/guide/components/broadcasts#restrict-broadcasts-permissions)

可以限制广播的接收和发送的 App 必须具有某些权限。

* 发送广播时传入权限，限制只拥有相应权限的 App 才能接收到广播
* 注册接收器时传入权限，限制只接收具有相应权限的 App 发送的广播