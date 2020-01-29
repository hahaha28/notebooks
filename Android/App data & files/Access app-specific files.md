# Access app-specific files

> 笔记整理自[官方文档](https://developer.android.com/training/data-storage/app-specific#kotlin)
>
> 2020/1/29

系统提供了下列两个位置去存储 app-specific 文件：

* **Internal storage directories**

    其他 app 无法访问这个位置的文件，从 Android 10（API 29）开始，这些位置的文件将加密。

    这个位置用来存储应用的私密文件。

    这个位置的存储空间是有限的，不应该用来保存大文件。



* **External storage directories**

    这个位置的文件能够被其他 app 访问，但是当 app 卸载时文件也会被删除。

**当 App 卸载时，所有 app-specific 存储都会被删除**



## Access from internal storage

----

这个位置有两个文件夹，一个用来存储持久化文件，一个用来存储缓存。

* 在这个地方读取不需要申请权限
* 这个位置的存储空间是有限的，官方认为应该先查询可用空间

### Access persistent files

> 这个位置一般在 data/data/包名/files/

 获取这个位置的文件夹

```kotlin
context.filesDir	//返回File对象
```

获取这个位置的所有文件名

```kotlin
context.fileList()	//返回一个 String[] ，包含所有文件名
```

> 并不包括缓存目录的文件，只是获取该目录下的所有文件名

#### 1.用 Java API 读取文件（夹）

```kotlin
val file = File( context.filesDir, fileName )
```

然后怎么做你应该懂了

#### 2.用 Android API 读取文件（夹）

```kotlin
// 获取输出流
context.openFileOutput( filename , Context.MODE_PRIVATE )
// 获取输入流
context.openFilieInput( filename )
// 创建或获取 文件夹
context.getDir( dirName ,Context.MODE_PRIVATE )  //父文件夹是 context.filesDir
```

从 Android 7.0（API 24）开始，只能填 `Context.MODE_PRIVET` ，否则报异常

> 这个API创建文件夹总是在 context.filesDir 下

#### 3.随安装附带的文件

如果想要文件随应用安装时就附带，可以把文件保存在项目的下面这个路径：

```kotlin
/res/raw
```

读取文件用下面这个方法：

```kotlin
// id 的前缀为 R.raw
public InputStream openRawResource(int id)
```



### Cache files

> 这个位置一般在 data/data/报名/cache/

用来保存一些临时的且私密的文件

> 缓存文件夹被设计用来存储少量的临时私密数据，如果系统存储空间不足，可能会自动删除缓存文件
>
> 要知道当前有多少可用空间，可以调用 `getCacheQuotaBytes()` 方法

获取缓存文件夹：

```kotlin
context.cacheDir	//返回一个 File 对象
```

#### 1.用 Java API 读取文件（夹）

```kotlin
val cacheFile = File( context.cacheDir, fileName )
```

接下去你应该懂了

#### 2.用 Android API 删除文件

```kotlin
context.deleteFile(fileName)
```

> 这个 API 只能删除内部存储中的文件



## Access from external storage

----

这个位置有两个文件夹，一个用来存储持久化文件，一个用来存储缓存。

* 从 Android 4.4（API 19）开始，不需要申请权限
* 从 Android 10（API 29）开始，这里的文件也不能被其他应用访问，Android 10一下的则可以
* 当 app 卸载时，这里的文件也会删除



### Access persistent files

> 目录在手机测试是 /storage/emulated/0/Android/data/包名/files

调用这个方法：

```kotlin
context.getExternalDir()	// 返回 File 对象
```

> 可以传入 null 值，获取到的是根目录



### Cache files

> 目录在手机测试是 /storage/emulated/0/Android/data/包名/cache

缓存路径这么获取：

```kotlin
context.externalCacheDir	// 返回一个 File 对象
```



### Media content

不共享的媒体文件，官方推荐不要乱存

```kotlin
context.getExternalDir(type)
```

参数类型根据媒体文件的类型，例如 `DIRECTORY_PICTURES`

具体类型可以在[这里](https://developer.android.com/reference/android/os/Environment#fields)查看



### Verify that storage is avaliable

为了确定 external storage 没有被偷，可以调用下面这个方法：

```kotlin
Environment.getExternalStorageStage()
```

返回 `MEDIA_MOUNTED` ，则可以读写

返回 `MEDIA_MOUNTED_READ_ONLY` ，则只能读



### Select a physical storage location

> 这玩意我也没试过

手机的 sd 卡是 external storage，而手机也会把内置存储划分一部分作为 external storage。所以，你可以选择要把数据存在哪一个 external storage里。

```kotlin
val externalStorageVolumes: Array<out File> =
        ContextCompat.getExternalFilesDirs(applicationContext, null)
val primaryExternalStorage = externalStorageVolumes[0]
```

> 文档说，按上面这么写就完事了



## Query free space

---

> 当扩展知识，这玩意估计也用不着

> 我不想写笔记了，有兴趣可以去看文档

> 就是讲手机存储空间不够的时候怎么办