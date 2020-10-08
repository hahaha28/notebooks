# Storage overview

## 几种存储类型的比较

|                                 | 类型                                                         | Access method                                                | 权限                                                         | Can other apps access? | Files removed on app uninstalled? |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------- | --------------------------------- |
| ***App-specific files***        | 仅供自己app使用的数据                                        | 内部存储：**getFilesDir()** or **getCacheDir()**             | None                                                         | No                     | Yes                               |
|                                 |                                                              | 外部存储： **getExternalFilesDir()** or **getExternalCacheDir()** | Android4.4（API 19）及以上不需要                             | Yes                    | Yes                               |
| ***Media***                     | 共享的媒体文件                                               | **MediaStore** API                                           | **READ_EXTERNAL_STORAGE** or **WRITE_EXTERNAL_STORAGE** when accessing other apps' files on Android 10 (API level 29) or higher.Permissions are required for all files on Android 9 (API level 28) or lower. | Yes                    | No                                |
| ***Documents and other files*** | Other types of shareable content, including downloaded files | Storage Access Framework                                     | None                                                         | Yes                    | No                                |
| ***App preferences***           | Key-value pairs                                              | [Jetpack Preferences](https://developer.android.com/guide/topics/ui/settings/use-saved-values) library | None                                                         | No                     | Yes                               |
| ***Database***                  | Structured data                                              | [Room](https://developer.android.com/training/data-storage/room) persistence library | None                                                         | No                     | Yes                               |

> 该表格来自Android官方文档，[去看看](https://developer.android.com/training/data-storage)

* 需要注意的是，内部存储空间是有限的，大文件不能存在里面