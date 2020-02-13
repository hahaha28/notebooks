# Intent filter

> 笔记整理自[官方文档](https://developer.android.com/guide/components/intents-filters)
>
> 2020/2/10

要公布应用可以接收哪些隐式Intent，需要在清单文件中使用  `<intent-filter>` 元素为应用组件声明一个或多个 `Intent` 过滤器。仅当隐式 `Intent` 可以通过 `Intent` 过滤器之一传递时，系统才会将该 `Intent` 传递给应用组件。

显示 `Intent` 始终会传递给其目标，无论组件声明的 `Intent` 过滤器如何均是如此。



## \<intent-filter>元素

* **\<action>**

    在name属性中，声明接受的Intent操作。该值必须是操作的文本字符串值，而不是类常量。

* **\<data>**

    使用一个或多个指定数据URI各个方面（scheme、host、port、path等）和MIME类型的属性，声明接受的数据类型。

* **\<category>**

    在name属性中，声明接受的Intent类别。该值必须是操作的文本字符串值，而不是类常量。

    为了接受隐式Intent，必须将 `CATEGORY_DEFAULT` 类别包括在Intent过滤器中。

```xml
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
```

* `<intent-filter>` 中可以包含多个 `<action>` 、`<data>` 或 `<category>` 。但组件需要能过处理这些元素的所有组合。
* 一个组件中可以包含多个 `<intent-filter>` ，隐式 `Intent` 只要满足其中一个就会启动该组件。



## 限制对组件的访问

将组件的 `exported` 属性设置为 `false`，则只有自己的应用可以启动该组件。这样可以防止其他应用通过显示或隐式 `Intent` 启动该组件。



## Intent匹配

当系统收到隐式 `Intent` 时，它根据三个方面将该 `Intent` 与 `Intent` 过滤器比较：

1. Action
2. Data (both URI and data type)
3. Category

### Action

要指定接受的intent操作，`<intent-filter>` 既可以不声明任何 `<action>` 元素，也可以声明多个 `<action>` 元素。

如果过滤器未列出任何操作，则 `Intent` 没有任何匹配项，因此所有 `Intent` 均无法通过测试。但是，如果 `Intent` 未指定操作，则会通过测试（只要过滤器至少包含一个操作）。

### Category

要指定接受的intent类别，`<intent-filter>` 既可以不声明任何 `<category>` 元素，也可以声明多个此类元素。

`Intent` 若要通过类别测试，则 `Intent` 中的每个类别均必须与过滤器中的类别匹配。反之则未必然，`<intent-filter>` 中声明的类别可以超出 `Intent` 中指定的类别，且 `Intent` 仍会通过测试。因此不含类别的 `Intent` 始终会通过类别测试。

Android 会自动将 `CATEGORY_DEFAULT` 类别应用与传递给 `startActivity()` 和 `startActivityForResult()` 的所有隐式 `Intent` 。

### Data

要指定接受的Intent数据，`<intent-filter>` 既可以不声明任何 `<data>` 元素，也可以声明多个此类元素。

```xml
<intent-filter>
    <data android:mimeType="video/mpeg" android:scheme="http" ... />
    <data android:mimeType="audio/mpeg" android:scheme="http" ... />
    ...
</intent-filter>
```

每个 `<data>` 元素均可指定一个URI结构和数据类型（MIME类型）。URI的每个部分均包含单独的 `scheme`、`host`、`port`、`path`属性:

```xml
<scheme>://<host>:<port>/<path>
```

例如

```kotlin
content://com.example.project:200/folder/subfolder/etc
```

在此URI中，`scheme` 是 `content`，主机是 `com.example.project`，端口是 `200`，路径是 `folder/subfolder/etc`。

在 `<data>` 元素中，上述每个属性均为可选，但存在线性依赖关系：

* 如果未指定 `scheme` ，则会忽略 `host`
* 如果未指定 `host` ，则会忽略 `port` 
* 如果未指定 `scheme` 和 `host` ，则会忽略 `path`

> URI 中可以包含 星号 通配符

将 `Intent` 中的URI与 `<intent-filter>` 中的URI进行比较时，它仅与 `<intent-filter>` 中包含的部分 `URI` 进行比较。例如过滤器仅指定 `scheme` ，则具有该 `scheme` 的所有 `URI` 均能通过匹配。

数据测试会将 `Intent` 中的 `URI` 和 `MIME` 类型与过滤器中的进行比较。规则如下：

- 仅当 `<intent-filter>` 未指定任何URI或MIME类型时，不包含URI和MIME类型的Intent才会通过测试。
- 对于包含URI但不含MIME类型（既未显示声明，也无法通过URI推断得出）的Intent，仅当其URI与过滤器的URI格式匹配、且过滤器同样未指定MIME类型时，才会通过测试。
- 仅当过滤器列出相同的MIME类型且未指定URI格式时，包含MIME类型、但不含URI的Intent才会通过测试。
- 仅当MIME类型与过滤器中列出的类型匹配时，同时包含URI类型和MIME类型（通过显示声明，或可以通过URI推断得出）的Intent才会通过测试的MIME类型部分。如果Intent的URI与过滤器中的URI匹配，或者如果Intent具有 `content:` 或 `file:` URI且过滤器未指定URI，则Intent会通过测试的URI部分。换言之，如果过滤器只是列出MIME类型，则假定组件支持 `content:` 和 `file:` 数据。

最后一条规则说明，想要接受指定数据类型的数据，只要指定 MIME 类型就行。

```xml
<intent-filter>
    <data android:mimeType="image/*" />
    ...
</intent-filter>
```

> 这是最为常用的一种情况

```xml
<intent-filter>
    <data android:scheme="http" android:mimeType="video/*" />
    ...
</intent-filter>
```

> 这种情况也常见



## 查询Intent匹配的应用

[PackageManager](https://developer.android.com/reference/android/content/pm/PackageManager.html) 有一系列的 `query...()` 方法能够查询相应特定 `Intent` 的组件，`resolve...()` 方法能够确定相应的最佳组件。

例如，`queryIntentActivities()` 能查询相应特定 `Intent` 的 `Activity`。`queryIntentServices()` 能查询相应特定 `Intent` 的服务。