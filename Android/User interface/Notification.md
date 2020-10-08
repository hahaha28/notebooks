# Notification

## 通知渠道

代码如下：

```kotlin
// 获取 NotificationManager 对象
val manager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
// 如果版本为 Android 8.0 及以上，则创建通知渠道
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    val channel = NotificationChannel(channelId, channelName, importance)
    manager.createNotificationChannel(channel)
}
```

创建一个通知渠道，需要渠道ID、名称、重要等级这三个参数。

* 渠道ID

  需要确保全局唯一性

* 名称

  名称是给用户看的

* 重要等级

  `NotificationManager`类的几个常量

  `IMPORTANCE_HIGH`、`IMPORTANCE_DEFAULT`、`IMPORTANCE_LOW`等

> 通知渠道创建一次之后便一直存在，同一个ID的通知渠道重复创建不会发生什么，可以改名。
>
> 用户可以在系统中设置重要程度。



## 通知的基本用法

### 发送通知

为了兼容性，使用`NotificationCompat`类

```kotlin
val notification = NotificationCompat.Builder(this,channelId)
	.setContentTitle("标题")
	.setContentText("内容")
	.setSmallIcon(R.drawable.small_icon)
	.setLargeIcon(BitmapFactory.decodeResource(resources,R.drawable.large_icon))
	.build()
// 获取 NotificationManager 对象
val manager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
// 发送通知，第一个参数为通知的ID，必须全局唯一
manager.notify(noticeId,notification)
```



### 通知的点击事件

使用`PendingIntent`，该类的构造器为私有，获取对象有三个方法

* PendingIntent.getActivity()
* PendingIntent.getBroadcast()
* PendingIntent.getService()

这三个方法的参数都相同，第一个参数为`context`，第二个参数传入0，第三个参数传入`Intent`，第四个参数传入0

> 为什么第二个和第四个参数传入0，我也不知道，好像挺复杂的，暂时没时间研究文档了

**示例：**

```kotlin
val intent = Intent(this,XxxActivity::class.java)
val pi = PendingIntent.getActivity(context,0,intent,0)
val notification = NotificationCompat.Builder(context,channelId)
	...
	.setContentIntent(pi)
	.build()
...
```

> 这里需要注意的是 Activity 的启动方式，例如在Activity已存在的情况下点击通知跳转Activity，那么默认方式会再新建一个。这篇[官方文档](https://developer.android.com/training/notify-user/navigation)是关于这方面的。



### 取消通知

有两种方式

1. **setAutoCancel(true)**

   这种方式最为简单，在`build`的连缀中加入即可

2. **manager.cancel(id)**

   用`NotificationManager`来取消通知，需要传入通知的id。

   这个id是发送时填入的id，而不是通知渠道的。

