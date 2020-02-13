# Intent

> 笔记整理自[官方文档](https://developer.android.com/guide/components/intents-filters)
>
> 2020/2/10

`Intent` 是一个消息传递对象，主要功能有三个：

1. 启动 `Activity`
2. 启动服务
3. 传递广播



## Intent的类型

### 1.显式Intent

按名称指定要启动的组件。

通常，在自己的应用中使用显示Intent来启动组件。

**服务必须用显示Intent启动**，且不要为服务声明Intnet过滤器。使用隐式Intent启动服务存在安全隐患，因为无法知道哪些服务将响应Intent，从Android5.0（API 21）开始，如果使用隐式Intnet调用bindService（），系统会引发异常。

### 2.隐式Intent

不会指定特定的组件，而是声明要执行的常规操作。

创建隐式Intent并将其传递给startActivity（）后，Android系统会搜索所有应用中与Intent匹配的过滤器，找到匹配项后，系统会启动该组件并传递Intent对象。



## 构建Intent

`Intent` 中包含的主要信息如下：

### 组件名称

要启动的组件名称。如果是显示Intent则必须指定，隐式Intent则不需要。启动Service时，应始终指定组件名称。

Intnet的这一字段是一个ComponentName对象，可以使用`setComponent()`、`setClass()`、`setClassName()` 或 `Intent` 构造函数设置组件名称。

### 操作

指定要执行的通用操作。可以指定自己的操作供 `Intnent` 在自己的应用内使用（请使用包名作为前缀确保唯一性），通常使用的操作是由 `Intent` 类或其他框架类定义的操作常量。可以查阅 `Intnet` 类参考文档。

使用 `setAction()` 或 `Intent` 构造函数为 `Intent` 指定操作。

### 数据

引用待操作数据和/或该数据 `MIME` 类型的 `URI` 。提供的数据类型通常由 `Intent` 的操作决定。除了指定 `URI` 以外，指定数据类型往往也很重要，数据的 `MIME` 类型有助于 Android 系统找到接受 `Intent` 的最佳组件。但有时，`MIME` 类型可以从 `URI` 中推断得出。

调用 `setData()` 设置数据 `URI` ，调用 `setType()` 设置 `MIME` 类型，调用 `setDataAndType` 同时设置两者。

**不能同时调用 `setData()` 和 `setType()` ，因为它们会互相抵消，可以使用 `setDataAndType()`。**

### 类别（Category）

一个包含应处理Intent组件类型的附加信息的字符串。可以将任意数量的类别描述放入一个Intent中，但大多数Intent均不需要类别。

调用addCategory（）指定类别。

---

以上的四个属性（组件名称、操作、数据和类别）表示 `Intent` 的既定特征，Android系统通过这些属性决定启动哪个组件。

`Intent` 还可以携带一些不影响其如何解析为应用组件的信息，如下：

---

### Extra

调用 `putExtra()` 方法（可以流式写法）添加 `extra` 数据，每个方法均接受两个参数：键名和值。也可以创建一个包含所有 `extra` 数据的 `Bundle` 对象，然后调用 `putExtras()` 方法放入 `Bundle` 对象。

### Flag

标志可以指定 Android 系统如何启动 `Activity`（例如，`Activity` 应属于哪个任务），以及启动之后如何处理（例如，它是否属于最近的 `Activity` 列表）。

详细信息，参阅 `setFlags()` 方法文档。



## 隐式Intent注意事项

用户可能没有任何应用能匹配发送出去的隐式 `Intent`，如果出现这种情况，调用将会失败并且应用会崩溃。要验证是否有 `Activity` 能接收 `Intent` ，请对 `Intent` 对象调用 `resolveActivity()` ，如果结果为非空，则可以安全调用 `startActivity()` 。

```kotlin
// Create the text message with a string
val sendIntent = Intent().apply {
    action = Intent.ACTION_SEND
    putExtra(Intent.EXTRA_TEXT, textMessage)
    type = "text/plain"
}

// Verify that the intent will resolve to an activity
if (sendIntent.resolveActivity(packageManager) != null) {
    startActivity(sendIntent)
}
```



## 强制使用应用选择器

如果有多个应用响应隐式Intent，用户可以选择要使用的应用并设置默认选项，则下次不会再出现应用选择器。但可以采用显示方式显示选择器对话框使得每次都会出现。

要显示选择器，请使用createChooser（）创建Intent，并将其传递给startActivity（）。

```kotlin
val sendIntent = Intent(Intent.ACTION_SEND)
...

// Always use string resources for UI text.
// This says something like "Share this photo with"
val title: String = resources.getString(R.string.chooser_title)
// Create intent to show the chooser dialog
val chooser: Intent = Intent.createChooser(sendIntent, title)

// Verify the original intent will resolve to at least one activity
if (sendIntent.resolveActivity(packageManager) != null) {
    startActivity(chooser)
}
```

