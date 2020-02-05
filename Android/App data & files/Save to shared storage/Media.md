# Access media files from shared storage

> 笔记整理自[官方文档](https://developer.android.com/training/data-storage/shared)
>
> 2020/2/5

Android 系统会自动扫描外部存储中的媒体文件，并归类到以下集合中：

* **`MediaStore.Images`**

* **`MediaStore.Videos`**

* **`MediaStore.Audio`**

* **`MediaStore.Downloads`**

    这个集合只有在 Android 10 及以上才能使用

还有一个集合是 `MediaStore.Files` ，它的内容取决于是否使用沙盒模式：

1. 在沙盒模式下

    这个集合仅包括自己 App 创建的 照片、视频、音频文件。

2. 非沙盒模式下

    这个集合包括所有媒体文件。



## 申请权限

---

### 1.存储权限

如何申请存储权限取决于是否使用沙盒模式（Android 10）

#### 启用沙盒模式（scoped storage）

如果启用了沙盒模式，读写自己app 的媒体文件（包括`MediaStore.Downloads`）不需要申请任何权限。

读写其他 app 的媒体文件需要申请 `READ_EXTERNAL_STORAGE` 权限，并且只能读写以下集合：

* `MediaStore.Images`
* `MediaStore.Video`
* `MediaStore.Audio`

> 在沙盒模式下，如果要访问其他 APP 的 `MediaStore.Downloads` ，请使用 Storage Access Framework

#### 没启用沙盒模式

没启用沙盒模式，设备低于 Android 10 或者运行在存储兼容模式（storage compatibility feature)下。

此时无论访问谁的媒体文件，都要申请相关存储权限。

### 2.媒体位置权限

`Access_Media_Location`，新增于Android 10，这是一个需要运行时申请的权限

> 例如获取照片中的位置信息，就需要这个权限



## 查询媒体集合

---

下面是一个例子：

```kotlin
// Need the READ_EXTERNAL_STORAGE permission if accessing video files that your
// app didn't create.

// Container for information about each video.
data class Video(val uri: Uri,
    val name: String,
    val duration: Int,
    val size: Int
)
val videoList = mutableListOf<Video>()

val projection = arrayOf(
    MediaStore.Video.Media._ID,
    MediaStore.Video.Media.DISPLAY_NAME,
    MediaStore.Video.Media.DURATION,
    MediaStore.Video.Media.SIZE
)

// Show only videos that are at least 5 minutes in duration.
val selection = "${MediaStore.Video.Media.DURATION} >= ?"
val selectionArgs = arrayOf(
    TimeUnit.MILLISECONDS.convert(5, TimeUnit.MINUTES).toString()
)

// Display videos in alphabetical order based on their display name.
val sortOrder = "${MediaStore.Video.Media.DISPLAY_NAME} ASC"

val query = ContentResolver.query(
    MediaStore.Video.Media.EXTERNAL_CONTENT_URI,
    projection,
    selection,
    selectionArgs,
    sortOrder
)
query?.use { cursor ->
    // Cache column indices.
    val idColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media._ID)
    val nameColumn =
            cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DISPLAY_NAME)
    val durationColumn =
            cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DURATION)
    val sizeColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.SIZE)

    while (cursor.moveToNext()) {
        // Get values of columns for a given video.
        val id = cursor.getLong(idColumn)
        val name = cursor.getString(nameColumn)
        val duration = cursor.getInt(durationColumn)
        val size = cursor.getInt(sizeColumn)

        val contentUri: Uri = ContentUris.withAppendedId(
            MediaStore.Video.Media.EXTERNAL_CONTENT_URI,
            id
        )

        // Stores column values and the contentUri in a local object
        // that represents the media file.
        videoList += Video(contentUri, name, duration, size)
    }
}
```

注意事项：

* 不要在主线程调用 `query()`
* 缓存列名的索引值，不要重复调用 `getColumnIndexOrThrow()`
* 像上面的代码那样获取 `contentUri`
* Devices that run Android 10 and higher require [column names that are defined](https://developer.android.com/reference/android/provider/MediaStore.MediaColumns) in the `MediaStore` API. If a dependent library within your app expects a column name that's undefined in the API, such as `"MimeType"`, use [`CursorWrapper`](https://developer.android.com/reference/android/database/CursorWrapper) to dynamically translate the column name in your app's process.



## 加载文件缩略图

---

加载文件缩略图（file thumbnails），使用 `loadThumbnail()` 并传入想要的缩略图大小

```kotlin
// Load thumbnail of a specific media item.
val thumbnail: Bitmap =
        applicationContext.contentResolver.loadThumbnail(
        content-uri, Size(640, 480), null)
```



## 打开媒体文件

---

两种方式：

### 1.使用 File descriptor

```kotlin
// Open a specific media item using ParcelFileDescriptor.
val resolver = applicationContext.contentResolver

// "rw" for read-and-write;
// "rwt" for truncating or overwriting existing file contents.
val readOnlyMode = "r"
resolver.openFileDescriptor(content-uri, readOnlyMode).use { pfd ->
    // Perform operations on "pfd".
}
```

### 2.使用文件流

```kotlin
// Open a specific media item using InputStream.
val resolver = applicationContext.contentResolver
resolver.openInputStream(content-uri).use { stream ->
    // Perform operations on "stream".
}
```



## 添加媒体文件

---

参考如下代码：

```kotlin
// Add a specific media item.
val resolver = applicationContext.contentResolver

// Find all audio files on the primary external storage device.
// On API <= 28, use VOLUME_EXTERNAL instead.
val audioCollection = MediaStore.Audio.Media.getContentUri(
        MediaStore.VOLUME_EXTERNAL_PRIMARY)

// Publish a new song.
val newSongDetails = ContentValues().apply {
    put(MediaStore.Audio.Media.DISPLAY_NAME, "My Song.mp3")
}

// Keeps a handle to the new song's URI in case we need to modify it
// later.
val myFavoriteSongUri = resolver
        .insert(audioCollection, newSongDetails)
```

### 1.设置文件状态

如果存储一个较大的媒体文件，可以告诉别人这个文件正在存储当中。在Android 10及以上，可以设置 `IS_PADING` 为1，此时只有自己的 App 能够看到该媒体文件，直到将 `IS_PADING` 设置为 0。

示例代码：

```kotlin
// Add a media item that other apps shouldn't see until the item is
// fully written to the media store.
val resolver = applicationContext.contentResolver

// Find all audio files on the primary external storage device.
// On API <= 28, use VOLUME_EXTERNAL instead.
val audioCollection = MediaStore.Audio.Media
        .getContentUri(MediaStore.VOLUME_EXTERNAL_PRIMARY)

val songDetails = ContentValues().apply {
    put(MediaStore.Audio.Media.DISPLAY_NAME, "My Workout Playlist.mp3")
    put(MediaStore.Audio.Media.IS_PENDING, 1)
}

val songContentUri = resolver.insert(audioCollection, songDetails)

resolver.openFileDescriptor(songContentUri, "w", null).use { pfd ->
    // Write data into the pending audio file.
}

// Now that we're finished, release the "pending" status, and allow other apps
// to play the audio track.
songDetails.clear()
songDetails.put(MediaStore.Audio.Media.IS_PENDING, 0)
resolver.update(songContentUri, songDetails, null, null)
```

### 2.指定存储位置

一般情况下，媒体文件会被存储在默认的位置上，例如照片默认存储在 `Environment.DIRECTORY_PICTURES` 文件夹下。

如果想要指定一个文件夹存储，可以设置文件的 `MediaColumns.RELATIVE_PATH`

> 这挺有必要的



## 更新媒体文件

---

参考如下代码：

```kotlin
// Updates an existing media item.
val mediaId = // MediaStore.Audio.Media._ID of item to update.
val resolver = applicationContext.contentResolver

// When performing a single item update, prefer using the ID
val selection = "${MediaStore.Audio.Media._ID} = ?"

// By using selection + args we protect against improper escaping of // values.
val selectionArgs = arrayOf(mediaId.toString())

// Update an existing song.
val updatedSongDetails = ContentValues().apply {
    put(MediaStore.Audio.Media.DISPLAY_NAME, "My Favorite Song.mp3")
}

// Use the individual song's URI to represent the collection that's
// updated.
val numSongsUpdated = resolver.update(
        myFavoriteSongUri,
        updatedSongDetails,
        selection,
        selectionArgs)
```

以上代码同样适用于不用沙盒模式的情况

> You can move files on disk during a call to [`update()`](https://developer.android.com/reference/android/content/ContentResolver.html#update(android.net.Uri,%20android.content.ContentValues,%20java.lang.String,%20java.lang.String[])) by changing `MediaColumns.RELATIVE_PATH` or [`MediaColumns.DISPLAY_NAME`](https://developer.android.com/reference/android/provider/MediaStore.MediaColumns.html#DISPLAY_NAME).

### 更新其他App的媒体文件

如果启用沙盒模式，通常情况下不能更新其他App的媒体文件。

如果用户同意仍然有可能做到，当抛出 `RecoverableSecurityException` 时，你可以申请用户同意（我也看不懂），代码如下：

```kotlin
// Apply a grayscale filter to the image at the given content URI.
try {
    contentResolver.openFileDescriptor(image-content-uri, "w")?.use {
        setGrayscaleFilter(it)
    }
} catch (securityException: SecurityException) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
        val recoverableSecurityException = securityException as?
            RecoverableSecurityException ?:
            throw RuntimeException(securityException.message, securityException)

        val intentSender =
            recoverableSecurityException.userAction.actionIntent.intentSender
        intentSender?.let {
            startIntentSenderForResult(intentSender, image-request-code,
                    null, 0, 0, 0, null)
        }
    } else {
        throw RuntimeException(securityException.message, securityException)
    }
}
```



## 删除媒体文件

---

参考以下代码：

```kotlin
// Remove a specific media item.
val resolver = applicationContext.contentResolver

// URI of the image to remove.
val imageUri = "..."

// WHERE clause.
val selection = "..."
val selectionArgs = "..."

// Perform the actual removal.
val numImagesRemoved = resolver.delete(
        imageUri,
        selection,
        selectionArgs)
```

如果不是沙盒模式，那么可以用以上代码删除其他App的媒体文件。

如果现在沙盒模式中删除其他App的媒体文件，参考 `更新媒体文件` 笔记中的代码。



## 读写媒体文件的注意事项

---

### 1.存储卷

Android 10 及以上能够获取外部存储卷（external storage volume）的名字。

首选的存储卷总是叫 `MediaStore.VOLUME_EXTERNAL_PRIMARY`，能够调用 `MediaStore.getExternalVolumeNames()` 获取存储卷的名字

```kotlin
val volumeNames: Set<String> = MediaStore.getExternalVolumeNames(context)
val firstVolumeName = volumeNames.iterator().next()
```

### 2.读取照片的位置信息

在Android10 及以上，要读取照片位置信息

1. 需要申请 `ACCESS_MEDIA_LOCATION` 

2. 参考如下代码：

    ```kotlin
    val photoUri: Uri = Uri.withAppendedPath(
            MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
            cursor.getString(idColumnIndex)
    )
    
    // Get location data using the Exifinterface library.
    // Exception occurs if ACCESS_MEDIA_LOCATION permission isn't granted.
    photoUri = MediaStore.setRequireOriginal(photoUri)
    contentResolver.openInputStream(photoUri)?.use { stream ->
        ExifInterface(stream).run {
            // If lat/long is null, fall back to the coordinates (0, 0).
            val latLong = latLong ?: doubleArrayOf(0.0, 0.0)
        }
    }
    ```

### 3.分享媒体文件

分享媒体文件请使用 `content://` URI，并使用内容提供器

### 4.使用真实路径访问文件

如果启用了沙盒模式，那么使用真实路径访问文件仅限于 app-specific directories of external storage，（内部存储也可以用）。即使你有 `READ_EXTERNAL_STORAGE` 权限，当你用真实路径去访问其他 App 的文件时，会产生 `FileNotFoundException`。例如，如果你想访问 `/sdcard/DCIM/IMG1024.JPG`，你应该使用 `MediaStore` 的 API。

### 5.不要传递文件真实路径

如果要告诉另一端代码某文件的位置，不要传递该文件的真实路径，官方推荐使用 file descriptor

```kotlin
val contentUri: Uri = ContentUris.withAppendedId(
        MediaStore.Audio.Media.EXTERNAL_CONTENT_URI,
        cursor.getLong(BaseColumns._ID))
val fileOpenMode = "r"
val parcelFd = resolver.openFileDescriptor(contentUri, fileOpenMode)
val fd = parcelFd?.detachFd()
// Pass the integer value "fd" into your native code. Remember to call
// close(2) on the file descriptor when you're done using it.
```

> To learn more about accessing files in native code, see the [Files for Miles](https://www.youtube.com/watch?v=oIn0MZQJpp0&t=15m20s) talk from Android Dev Summit '18, starting at 15:20.

### 6.媒体文件的App属性

启动沙盒模式后，每一个媒体文件都有一个App属性，用来标记这个媒体文件属于哪一个App，并且该App可以无需任何权限就能访问该文件。

如果App被重装，那么读取之前App的媒体文件仍然需要权限。