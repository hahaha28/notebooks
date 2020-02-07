# Content provider basics

> 笔记整理自[官方文档](https://developer.android.com/guide/topics/providers/content-provider-basics)
>
> 2020/1/29

内容提供器主要有以下两个用途：

1. 访问其他 App 的数据
2. 向其他 App 分享自己的数据

这篇文章将基于已存在的内容提供器，教你如何使用 provider client object 去访问内容提供器。

本文将介绍一下内容：

* 内容提供器如何工作
* 从内容提供器获取数据的 API
* 增删改查内容提供器中数据的 API
* 帮助你使用内容提供器的 API



## 概述

---

内容提供器将数据以表的形式提供给其他应用，类似于关系型数据库的表。

内容提供器使得数据层的访问更加容易，有如下功能：

* 给其他应用提供一个访问自己应用的数据入口
* 发送数据给 UI 组件（widget）
* 通过搜索框架（`SearchRecentSuggestionsProvider`）返回搜索建议结果
* 向服务器同步应用数据，通过使用 `AbstractThreadedSyncAdapter`
* 在 UI 中加载数据，使用 `CursorLoader`

![内容提供器和其他组件之间的关系图](https://github.com/hahaha28/photos/raw/master/notebooks/Android/content-provider-tech-stack.png)

### 1.访问提供器

访问内容提供器需要使用 `ContentResolver` 对象作为与提供器通信的客户端。

> 这个对象可以在应用的 `Context` 中获取到

当调用 `ContentResolver` 对象中的方法时，对应的 `ContentProvider` 会调用同名的方法，`ContentResolver` 中提供了基本的 CRUD 方法。

在 UI 中访问 `ContentProvider` 的一个通常模式是，使用 `CursorLoader` 在后台执行异步查询。在 `Activity` 或 `Fragment` 中使用 `CursorLoader` 执行查询，会顺序使用 `ContentResolver` 访问 `ContentProvider` 。这使得主线程不会被查询阻塞。

![模式示意图](https://github.com/hahaha28/photos/raw/master/notebooks/Android/content-provider-interaction.png)

### 2.示例：访问用户字典

用户字典是安卓一个内置的内容提供器，它的数据可能如下表所示：

| word        | app id | frequency | local | _ID  |
| ----------- | ------ | --------- | ----- | ---- |
| mapreduce   | user1  | 100       | en_US | 1    |
| precompiler | user14 | 200       | fr_FR | 2    |
| applet      | user2  | 225       | fr_CA | 3    |
| const       | user1  | 255       | pt_BR | 4    |
| int         | user5  | 100       | en_UK | 5    |

非常类似于关系型数据库的表，其中 `_ID` 这一列是主键，由提供器自动维护。

查询时，使用 `ContentResolver` 的 `query()` 方法：

```kotlin
// Queries the user dictionary and returns results
cursor = contentResolver.query(
        UserDictionary.Words.CONTENT_URI,   // The content URI of the words table
        projection,                        // The columns to return for each row
        selectionClause,                   // Selection criteria
        selectionArgs.toTypedArray(),      // Selection criteria
        sortOrder                          // The sort order for the returned rows
)
```

下表展示了`qurty()` 方法与 SQL 的等价关系：

| query() 参数  | SELECT 语句            | Notes                          |
| ------------- | ---------------------- | ------------------------------ |
| Uri           | From *table_name*      | Uri 相当于表名                 |
| projection    | *col,col,col,*...      | projection是要查询的列名的数组 |
| selection     | WHERE *col = value*    | selection指明了查询条件        |
| selectionArgs | 无对应                 | 就是selection语句中的 ？       |
| sortOrder     | ORDER BY *col,clo,*... | 查询结构的排序方式             |

### 3.内容 URI

内容 URI 能够唯一标识一条数据

```kotlin
content://user_dictionary/words/4
```

这条 URI 中：

* `content:` 是 ***scheme*** ，代表了这是一个内容 URI

* `user_dictionary` 是 ***authority*** ，代表了某一个提供器

* `words` 是 ***path***，代表了提供器中的哪一个表

* `4` 是 ***_ID***值，它代表了表中 `_ID` 为4的那条数据

要给一个Uri 加上 _ID 值，官方建议：

~~~kotlin
val singleUri: Uri = ContentUris.withAppendedId(UserDictionary.Words.CONTENT_URI, 4)
~~~

要用 `String` 构建 Uri ，可以这么做：

```kotlin
val uri: Uri = Uri.parse("content://user_dictionary/words")
```

> `Uri` 和 `Uri.Builder` 类提供了许多方法用来从 `String` 构建 `Uri` 对象。`ContentUris` 类提供方法用来给 Uri 追加 id 。（来自官方的 note）



## 从提供器获取数据

---

这一小节介绍如何从内容提供器中获取数据，将使用用户字典提供器作为例子。

> 为了方便介绍，一下代码运行在 UI 线程中。在实际使用中，请不要使用 UI 线程执行这些操作。

> CursorLoader 从 Android P（28）开始被弃用了，现在官方推荐使用 ViewModels 和 LiveData。

要从提供器获取数据，遵循以下步骤：

1. 申请提供器的读取权限
2. 构建查询所要的代码

### 1.申请提供器的读取权限

权限需要定义在 `manifest` 中，不需要在运行时申请，当应用安装时，用户会隐私授予权限。

权限到底是什么，需要看指定提供器的文档。

在此例中，用户字典提供器的读取权限是

```xml
android.permission.READ_USER_DICTIONARY
```

### 2.构造请求

query() 方法：

```java
public final Cursor query( Uri uri,
                         String[] projection,
                         String selection,
                         String[] selectionArgs,
                         String sortOrder
                         )
```

参数解释：

| 参数          | 类型     | 含义                                                         |
| ------------- | -------- | ------------------------------------------------------------ |
| uri           | Uri      | 这项必填，Uri 的解释可以看 概述的第三小节                    |
| selection     | String   | 选择条件，相当于 SQL 中的 Where（填入 `null` 代表无条件）    |
| selectionArgs | String[] | 如果选择条件中的参数用 `?` 代替，则参数写在这里（填入 `null` 代表无参数） |
| sortOrder     | String   | 排序条件（填入 `null` 代表无）                               |
| 返回参数      | Cursor   | `Cursor` 对象，会指向第一条数据之前，可能返回 null           |

### 3.从 query 结果获取数据

`query()` 返回的 `Cursor` 对象有三种情况：

```kotlin
when( mCursor?.count ){
    null -> {
        // 出现了错误
    }
    0 -> {
        // 当前条件下无查询结果
    }
    else -> {
        // 处理查询结果
    }
}
```

`Cursor` 对象可通过如下方法遍历：

```kotlin
mCursor?.apply{
    // 获取想要查询的列名的 index
    val index:Int = getColumnIndex(UserDictionary.Words.WORD)
    // 移动 Cursor 指针，在移动之前，Cursor 指向 -1 行，所以一定要先移动
    while( moveToNext() ){
        val word = getString(index)	//根据索引值获取数据
    }
}
```

> 除了 `getString()` ，`Cursor` 还有一系列 `getXxx()` 方法，也有 `getType()` 方法可以获取类型



## 插入数据

---

使用 `ContentResolver` 的 `insert()` 方法

```java
public final Uri insert(Uri uri,ContentValues values)
```

* `ContentValues` 类

    有点类似于 `Map` ，这个类用来存放要插入的数据

    使用 `put(String,Xxx)` 方法，第一个值填入列名，第二个值填入数据

示例：

```kotlin
// 定义要插入的数据
val newValues = ContentValues().apply {
    /*
     * Sets the values of each column and inserts the word. The arguments to the "put"
     * method are "column name" and "value"
     */
    put(UserDictionary.Words.APP_ID, "example.user")
    put(UserDictionary.Words.LOCALE, "en_US")
    put(UserDictionary.Words.WORD, "insert")
    put(UserDictionary.Words.FREQUENCY, "100")

}

newUri = contentResolver.insert(
        UserDictionary.Words.CONTENT_URI,   // the user dictionary content URI
        newValues                          // the values to insert
)
```

* 插入数据，不需要把所有属性都给出来
* 属性的类型不一定要相同
* 可以调用 `ContentValues.putNull()` 放入 `null` 值
* `_ID` 值无法指定，因为这是提供器自己维护的
* 返回结果是插入数据的 `Uri` ，失败会返回 `null`

> 要获取一个 `Uri` 对象里的 `_ID` ，可以使用 `ContentUris.parseId()`



## 更新数据

---

使用 `ContentResolver` 的 `update()` 方法

```java
public final int update( Uri uri,
                       ContentValues values,
                       String where,
                       String[] selectionArgs)
```

`ContentValues` 参数中，只要添入要更新的数据，不需要完整一行，可以将一个属性设为 `null` 将这列的对应值清空。

示例：

```kotlin
// Defines an object to contain the updated values
val updateValues = ContentValues().apply {
    /*
     * Sets the updated value and updates the selected words.
     */
    putNull(UserDictionary.Words.LOCALE)
}

// Defines selection criteria for the rows you want to update
val selectionClause: String = UserDictionary.Words.LOCALE + "LIKE ?"
val selectionArgs: Array<String> = arrayOf("en_%")

// Defines a variable to contain the number of updated rows
var rowsUpdated: Int = 0

...

rowsUpdated = contentResolver.update(
        UserDictionary.Words.CONTENT_URI,   // the user dictionary content URI
        updateValues,                      // the columns to update
        selectionClause,                   // the column to select on
        selectionArgs                      // the value to compare to
)
```

> 这段代码将 locale 中含有 "en" 的值都改为 `null`

## 删除数据

---

使用 `ContentResolver` 的 `delete()` 方法

```java
public final int delete( Uri uri,
                       String where,
                       String[] selectionArgs)
```

示例：

```kotlin
// Defines selection criteria for the rows you want to delete
val selectionClause = "${UserDictionary.Words.LOCALE} LIKE ?"
val selectionArgs: Array<String> = arrayOf("user")

// Defines a variable to contain the number of rows deleted
var rowsDeleted: Int = 0

...

// Deletes the words that match the selection criteria
rowsDeleted = contentResolver.delete(
        UserDictionary.Words.CONTENT_URI,   // the user dictionary content URI
        selectionClause,                   // the column to select on
        selectionArgs                      // the value to compare to
)
```





