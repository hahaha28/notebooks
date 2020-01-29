# Overview

> 笔记整理自[官方文档](https://developer.android.com/training/data-storage/shared)
>
> 2020/1/29

Shared storage 用来保存要与其他应用共享的数据

当 App 卸载时，这些数据仍然能够保存

有下列两种数据：

* **Media content**

    系统提供了一个公共文件夹用来保存这种文件。

    请使用 `MediaStore` API

* **Documents and other files**

    使用平台提供的 Storage Access Framework 来存取