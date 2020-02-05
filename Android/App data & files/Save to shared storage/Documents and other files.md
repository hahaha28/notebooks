# Access documents and other files from shared storage

> 笔记整理自[官方文档](https://developer.android.com/training/data-storage/shared/documents-files)
>
> 2020/2/5

在 Android 4.4（API 19) 及以上，可以使用 ***Storage Access Framework*** 来访问外部存储中的文件，而且不需要申请任何权限。



## SAF 的用处

---

1. 创建文件

    使用 `ACTION_CREATE_DOCUMENT` intent action 让用户将文件保存在指定位置

2. 打开文件

    使用 `ACTION_OPEN_DOCUMENT` intent action 让用户选择指定文件打开

3. 获取某个文件夹的权限

    使用 `ACTION_OPEN_DOCUMENT_TREE` intent action（Android 5.0，API 21 及以上），让用户指定一个文件夹，然后App能够获取该文件夹下的访问权限（包括子文件夹）。



## 创建文件

---

使用 `ACTION_CREATE_DOCUMENT` intent action 去调用系统 file picker，然后用户选择一个位置保存文件。

> ```note
> 这种做法不能覆盖已有的文件，如果新建的文件与目录下其他文件重名，那么系统会在文件名后加一个数字。例如confirmation.pdf如果重名了，系统会修改为confirmation(1).pdf
> ```

Intent 的配置参数：

* 指定文件名
* 指定 MIME 类型
* （可选）传入一个 URI 指定 file picker 第一次打开的位置

```kotlin
// Request code for creating a PDF document.
const val CREATE_FILE = 1

private fun createFile(pickerInitialUri: Uri) {
    val intent = Intent(Intent.ACTION_CREATE_DOCUMENT).apply {
        addCategory(Intent.CATEGORY_OPENABLE)
        type = "application/pdf"
        putExtra(Intent.EXTRA_TITLE, "invoice.pdf")

        // Optionally, specify a URI for the directory that should be opened in
        // the system file picker before your app creates the document.
        putExtra(DocumentsContract.EXTRA_INITIAL_URI, pickerInitialUri)
    }
    startActivityForResult(intent, CREATE_FILE)
}
```



## 打开文件

---

使用 `ACTION_OPEN_DOCUMENT` intent action 去调用 system picker，然后用户选择要打开的文件。

intent 可以配置如下：

* 指定要打开的文件的MIME类型，全类型为 \*/*
* （可选）传入一个 URI 指定 file picker 第一次打开的位置

```kotlin
// Request code for selecting a PDF document.
const val PICK_PDF_FILE = 2

fun openFile(pickerInitialUri: uri) {
    val intent = Intent(Intent.ACTION_OPEN_DOCUMENT).apply {
        addCategory(Intent.CATEGORY_OPENABLE)
        type = "application/pdf"

        // Optionally, specify a URI for the file that should appear in the
        // system file picker when it loads.
        putExtra(DocumentsContract.EXTRA_INITIAL_URI, pickerInitialUri)
    }

    startActivityForResult(intent, PICK_PDF_FILE)
}
```



## 获取指定文件夹的权限

---

> 在 Android 5.0（API 21）及以上才能使用

使用 `ACTION_OPEN_DOCUMENT_TREE` intent action 去调用 system picker，然后用户选择一个位置，App 便会获得这个位置所有文件的访问权限（包括子文件夹）。

同样能使用 URI 指定 file picker 第一次打开的位置。

```kotlin
fun openDirectory(pickerInitialUri: Uri) {
    // Choose a directory using the system's file picker.
    val intent = Intent(Intent.ACTION_OPEN_DOCUMENT_TREE).apply {
        // Provide read access to files and sub-directories in the user-selected
        // directory.
        flags = Intent.FLAG_GRANT_READ_URI_PERMISSION

        // Optionally, specify a URI for the directory that should be opened in
        // the system file picker when it loads.
        putExtra(DocumentsContract.EXTRA_INITIAL_URI, pickerInitialUri)
    }

    startActivityForResult(intent, your-request-code)
}
```



## 在调用完 file picker 之后

---

在用户使用系统的 file picker 之后，App 可以在 `onActivityResult()` 得到一个 URI

```kotlin
override fun onActivityResult(
        requestCode: Int, resultCode: Int, resultData: Intent?) {
    if (requestCode == your-request-code
            && resultCode == Activity.RESULT_OK) {
        // The result data contains a URI for the document or directory that
        // the user selected.
        resultData?.data?.also { uri ->
            // Perform operations on the document using its URI.
        }
    }
}
```

在得到 URI 后，你可以做以下这些操作

### 1.确定文件提供器支持的操作

不同的内容提供器允许的操作也不同，检查 [`Document.COLUMN_FLAGS`](https://developer.android.com/reference/android/provider/DocumentsContract.Document#COLUMN_FLAGS) 中的值，确定能提供那些操作。

> ??? 没看懂

### 2.保留权限

当 App 从系统 file picker 获得 URI 时，App 也获取了该 URI 的访问权限，该权限会一直保留直到用户的手机重启。

有一种办法能够永久保留指定 URI 的权限

```kotlin
val contentResolver = applicationContext.contentResolver

val takeFlags: Int = Intent.FLAG_GRANT_READ_URI_PERMISSION or
        Intent.FLAG_GRANT_WRITE_URI_PERMISSION
// Check for the freshest data.
contentResolver.takePersistableUriPermission(uri, takeFlags)
```

> 如果该文件被转移或者删除，那么权限也会消失

### 3.检索文件属性

> 使用内容提供器，常规操作

```kotlin
val contentResolver = applicationContext.contentResolver

fun dumpImageMetaData(uri: Uri) {

    // The query, because it only applies to a single document, returns only
    // one row. There's no need to filter, sort, or select fields,
    // because we want all fields for one document.
    val cursor: Cursor? = contentResolver.query(
            uri, null, null, null, null, null)

    cursor?.use {
        // moveToFirst() returns false if the cursor has 0 rows. Very handy for
        // "if there's anything to look at, look at it" conditionals.
        if (it.moveToFirst()) {

            // Note it's called "Display Name". This is
            // provider-specific, and might not necessarily be the file name.
            val displayName: String =
                    it.getString(it.getColumnIndex(OpenableColumns.DISPLAY_NAME))
            Log.i(TAG, "Display Name: $displayName")

            val sizeIndex: Int = it.getColumnIndex(OpenableColumns.SIZE)
            // If the size is unknown, the value stored is null. But because an
            // int can't be null, the behavior is implementation-specific,
            // and unpredictable. So as
            // a rule, check if it's null before assigning to an int. This will
            // happen often: The storage API allows for remote files, whose
            // size might not be locally known.
            val size: String = if (!it.isNull(sizeIndex)) {
                // Technically the column stores an int, but cursor.getString()
                // will do the conversion automatically.
                it.getString(sizeIndex)
            } else {
                "Unknown"
            }
            Log.i(TAG, "Size: $size")
        }
    }
}
```

### 4. 打开文件

这里展示两种方式

#### 1）Bitmap

```kotlin
val contentResolver = applicationContext.contentResolver

@Throws(IOException::class)
private fun getBitmapFromUri(uri: Uri): Bitmap {
    val parcelFileDescriptor: ParcelFileDescriptor =
            contentResolver.openFileDescriptor(uri, "r")
    val fileDescriptor: FileDescriptor = parcelFileDescriptor.fileDescriptor
    val image: Bitmap = BitmapFactory.decodeFileDescriptor(fileDescriptor)
    parcelFileDescriptor.close()
    return image
}
```

> 不要在主线程执行这些操作

#### 2) InputStream

```kotlin
val contentResolver = applicationContext.contentResolver

@Throws(IOException::class)
private fun readTextFromUri(uri: Uri): String {
    val stringBuilder = StringBuilder()
    contentResolver.openInputStream(uri)?.use { inputStream ->
        BufferedReader(InputStreamReader(inputStream)).use { reader ->
            var line: String? = reader.readLine()
            while (line != null) {
                stringBuilder.append(line)
                line = reader.readLine()
            }
        }
    }
    return stringBuilder.toString()
}
```

### 5.编辑文件

```kotlin
val contentResolver = applicationContext.contentResolver

private fun alterDocument(uri: Uri) {
    try {
        contentResolver.openFileDescriptor(uri, "w")?.use {
            FileOutputStream(it.fileDescriptor).use {
                it.write(
                    ("Overwritten at ${System.currentTimeMillis()}\n")
                        .toByteArray()
                )
            }
        }
    } catch (e: FileNotFoundException) {
        e.printStackTrace()
    } catch (e: IOException) {
        e.printStackTrace()
    }
}
```

> `DocumentFile` 类的 `canWrite()` 方法并不能表明你的App能写这个文件，要确定App是否能写这个文件，请查询`FLAG_SUPPORTS_WRITE`

### 6.删除文件

> 如果这个文件的 [`Document.COLUMN_FLAGS`](https://developer.android.com/reference/android/provider/DocumentsContract.Document.html#COLUMN_FLAGS) 包含 [`SUPPORTS_DELETE`](https://developer.android.com/reference/android/provider/DocumentsContract.Document.html#FLAG_SUPPORTS_DELETE)，那么你就有权限删除这个文件。

```kotlin
DocumentsContract.deleteDocument(applicationContext.contentResolver, uri)
```

### 7.打开虚拟文件

没看懂？好像是指云端的文件，那就不用管了。。。