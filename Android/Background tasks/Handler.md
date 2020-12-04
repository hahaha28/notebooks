# Handler

> [参考文章1：Android由浅及深Handler消息机制](https://mp.weixin.qq.com/s/sJDzs4wnAkip3834fRCqPg)
>
> [参考文章2：Android中为什么主线程不会因为Looper.loop()里的死循环卡死？](https://www.zhihu.com/question/34652589)
>
> [参考文章3：Handler内存泄漏详解及其解决方案](https://blog.csdn.net/javazejian/article/details/50839443)

## Handler 有哪些作用

1. 切换代码执行的线程
2. 按顺序规则的处理消息，避免并发
3. 阻塞线程，避免让线程结束
4. 延迟处理消息

## 原理

![](http://img.inaction.fun/static/96109.webp)

Handler 机制依赖于四个类：`Handler`、`Looper`、`MessageQueue`、`Message`，其中`MessageQueue`是`Looper`内部的一个对象，`MessageQueue`和`Looper`每个线程最多只能有一个（使用`ThreadLocal`），而`Handler`可以有很多个。

`Looper`不断从`MessageQueue`中获取`Message`，当`Handler`调用`send`或`post`方法发送`Message`时，`Message`会进入`MessageQueue`，同时，`Message`中会持有`Handler`的引用。当这个`Message`被`Looper`拿出来时，会调用相应的`Handler`对象进行处理。

## Handler 的使用

* **创建**

```java
// 第一种方法，使用 callback 创建 handler
Handler handler = new Handler(Looper.myLooper(),new callback(){
    public Boolean handleMessage(Message msg){
        // ......
    }
});

// 第二种方法，继承 Handler 并重写handlerMessage 方法
static MyHandler extends Handler{
    public MyHandler(Looper looper){
        super(looper);
    }
    
    public void handlerMessage(Message msg){
        super.handlerMessage(msg);
        // ........
    }
}
```

>  注意第二种方法一定要使用静态内部类，如果使用内部类或者匿名内部类，都有可能会造成内存泄漏。因为非静态内部类会持有外部类的引用，而`Handler`发送的`Message`会持有`Handler`的引用。如果这个`Message`是个延迟的消息，Activity 退出了，而`Message`还没有被处理，这时 Activity 对象就无法被回收，导致内存泄漏。

* **发送消息**

  有两种系列的方法：postXxx 和 sendXxx

  ```java
  boolean post (Runnable r);
  boolean postDelay (Runnable r, long delayMills);
  boolean postAtTime (Runnable r, long uptimeMills);
  boolean postAtFrontOfQueue (Runnable r);
  ```

  ```java
  boolean sendMessage (Message msg);
  boolean sendMessageDelay (Message msg, long delayMills);
  boolean sendMessageAtTime (Message msg, long uptimeMills);
  boolean sendMessageFrontOfQueue (Message msg);
  ```

  `post` 系列方法，是将`Runnable`对象封装成`Message`，具体是将`Runnable`对象赋值给`Message`的`callback`变量

* **处理消息**

  `Looper`处理消息时，是调用`handler`的`dispatchMessage`方法来处理，源码如下：

  ```java
  public void dispatchMessage(@NonNull Message msg) {
      if (msg.callback != null) {
          handleCallback(msg);
      } else {
          if (mCallback != null) {
              if (mCallback.handleMessage(msg)) {
                  return;
              }
          }
          handleMessage(msg);
      }
  }
  
  private static void handleCallback(Message message) {
      message.callback.run();
  }
  ```

  由以上源码可知，如果`Message`没有`callback`，就会调用`Handler`中的`handleMessage`（也就是创建`Handler`时重写的那个方法），如果`Message`有`callback`，就会调用`callback.run()`，并且不会调用`handleMessage`方法。

  所以使用`handler`的`post`方法是不会经过`handleMessage`的。



## Message

`Message`是负责承载消息的类，主要关注他的内部属性：

```java
// 用户自定义，主要用于辨别Message的类型
public int what;
// 用于存储一些整型数据
public int arg1;
public int arg2;
// 可放入一个对象
public Object obj;
// Bundle数据
Bundle data;
// Message处理的时间。相对于1970.1.1而言的时间
// 对用户不可见
public long when;
// 处理这个Message的Handler
// 对用户不可见
Handler target;
// 当我们使用Handler的post方法时候就是把runnable对象封装成Message
// 对用户不可见
Runnable callback;
// MessageQueue是一个链表，next表示下一个
// 对用户不可见
Message next;
```

当我们获取Message的时候，官方建议是通过Message.obtain()方法来获取，当使用完之后使用recycle()方法来回收循环利用。而不是直接new一个新的对象。

一般来说，我们不需要调用recycle去回收，在 `Looper`中会自动把`Message`回收。



## Looper

`Looper`负责从消息队列中取出消息，然后交给`Handler`处理，如果队列中没有消息，则`MessageQueue`的`next`方法会阻塞线程。每个线程最多只能有一个`Looper`，使用`ThreadLocal`来保证。

> 主线程已经有 Looper 了，不需要自己处理

* **初始化当前线程的Looper**

  `Looper.prepare()`

  这是静态方法，用来初始化当前线程的Looper，一个线程不能初始化两次。

* **获取当前线程的Looper**

  `Looper.myLooper()`

  静态方法，其实就是调用 ThreadLocal 的 get 方法

* **启动Looper**

  `Looper.loop()`

  静态方法，当`Looper`初始化完成后，是不会自己启动的，需要调用这个方法。

  调用此方法后，`Looper`会不断循环获取消息。

* **退出Looper**

  `quit()`或`quitSafely()`

  不是静态方法，`quit()`是直接将`Looper`退出，`quitSafely()`是将消息队列中的不需要等待的消息处理完成之后再退出。