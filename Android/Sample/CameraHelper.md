# CameraHelper

[github地址](https://github.com/hahaha28/CameraHelper)



## 主要思路

将操作封装在一个无界面的`Fragment`中

`Provider`也在库中定义



## 坑

当我在同一部手机的第二个App使用这个库时，AndroidStudio在安装App时报错：

```error
INSTALL FAILED CONFLICTING PROVIDER
```

由于代码编译没问题，我本来以为是AndroidStudio抽风，但重启了也不行，google之后才知道，因为这个App和另一个使用这个库的App里面的`Provider`的`authority`相同，`Provider`的`authority`必须唯一，所以一般用包名，但是这个库里面还是写死了，所以有两个App在一部手机上使用时就会冲突。

**解决办法：**

将原来的 `authority`：

```xml
android:authorities="fun.inaction.camera.helper.fileprovider"
```

改为：

```xml
android:authorities="${applicationId}.fun.inaction.camera.helper.fileprovider"
```

这里的`applicationId`由于每个App都会不同，所以避免了冲突

调用`Provider`的地方也要修改

```kotlin
val authority = "${context!!.packageName}.fun.inaction.camera.helper.fileprovider"
```



**参考的文章：**

[install failed conflicting](https://stackoverflow.com/questions/16267785/install-shows-error-in-console-install-failed-conflicting-provider)

[how to get android application id](https://stackoverflow.com/questions/1264397/how-to-get-android-application-id)



