# EventBus

> [github](https://github.com/greenrobot/EventBus)
>
> [官方文档](https://greenrobot.org/eventbus/documentation/)

## 一. 简单使用

分为三步

### 1.定义事件

```kotlin
class MsgEvent(val msg:String)
```

### 2.准备订阅者

```kotlin
override fun onStart(){
    super.onStart()
    EventBus.getDefault().register(this) // 注册
}
override fun onStop(){
    EventBus.getDefault().unregister(this) // 取消注册
    super.onStop()
}
```

```kotlin
// 定义接受事件后的回调
@Subscribe(threadMode = ThreadMode.MAIN)
fun onMsgEvent(event: MsgEvent){
    event.msg
}
```

### 3.发送事件

```kotlin
EventBus.getDefault().post(MsgEvent("Hello EventBus"))
```

## 二. ThreadMode

> [关于ThreadMode的官方文档](https://greenrobot.org/eventbus/documentation/delivery-threads-threadmode/)

| ThreadMode   | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| POSTING      | 这是默认的模式，事件处理所在的线程会和发送事件者的线程相同。这种模式消耗最小，因为完全不需要切换线程。**不能执行耗时操作**，因为可能处于UI线程。 |
| MAIN         | 事件处理位于主线程。**不能执行耗时操作**。                   |
| MAIN_ORDERED | 事件处理位于主线程。发送的事件会进入**队列**，所以post()会立即返回，多个事件之间有先后顺序。**不能执行耗时操作**。 |
| BACKGROUND   | 事件处理位于非主线程。如果post()位于非主线程，那么事件处理的线程与其相同；如果post()位于UI线程，那么事件处理会位于一个非UI线程，但是这个模式的所有事件处理位于同一个非UI线程，它们按序执行。**不能执行耗时操作**，否则可能会阻塞。 |
| ASYNC        | 事件处理位于非主线程。这个线程一定不同于UI线程与post的线程，它会是一个单独的线程。这个模式**适用于处理耗时操作**。 |

## 三. Sticky Events

粘性事件，在发送后最后一次事件的数据会被保存在**内存**中，之后每次相应的观察者注册时都会再次触发该事件。

> 如果发送两个相同类型的粘性事件，这两个事件都会被接收到，但是只有最后一个会被保留在内存中。

**使用：**

```kotlin
EventBus.getDefault().postSticky(new MsgEvent("Sticky Event"))
```

```kotlin
@Subscribe(sticky=true , threadMode = ThreadMode.Main)
fun onMsgEvent(event : MsgEvent){
    // ...
}
```

**手动获得或移除粘性事件：**

默认情况下，粘性事件一直被保存在内存中，除非内存释放。但也可以手动移除

```kotlin
// 手动获取粘性事件
val stickyEvent = EventBus.getDefault().getStickyEvent(MsgEvent::class.java)
// 手动移除
EventBus.getDefault().removeStickyEvent(stickyEvent)
// 另一种移除方式
val event = EventBus.getDefault().removeStickyEvent(MsgEvent::class.java)
```

## 四. 优先级与取消事件

接收者优先级越高越先收到消息（默认优先级为0）

**优先级不会影响不同`ThreadMode`的事件的分发顺序**

```kotlin
@Subscribe(priority=1)
fun onEvent(event:MsgEvent){
    // ...
}
```

* 取消事件，仅能在接收者线程与发布者线程相同的情况下使用

```kotlin
@Subscribe(priority=1)
fun onEvent(event:MsgEvent){
    EventBus.getDefault().cancelEventDelivery(event)
}
```

