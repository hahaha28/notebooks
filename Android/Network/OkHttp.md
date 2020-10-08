# OkHttp

> [github](https://github.com/square/okhttp)
>
> [官网](https://square.github.io/okhttp/)
>
> [API文档](https://square.github.io/okhttp/3.x/okhttp/)

![](http://img.inaction.fun/static/14384.svg)

## 一. 创建客户端

```kotlin
val client = OkHttpClient()
```

就一行代码，其实有一个`OkHttpClient.Build`类，那这里为什么没在构造的时候用？看源码

```java
public OkHttpClient() {
  this(new Builder());
}
```

原来这是一个快捷操作

## 二. 构造请求

`OkHttp`中的请求由`Request`类表示，而`Request`类对象又由`Request.Builder`类来创建

> [Request.Builder API 文档](https://square.github.io/okhttp/3.x/okhttp/okhttp3/Request.Builder.html)

Http 的请求由请求行、请求头、请求数据三部分构成，下面依序说明如何在`OkHttp`中构建。

### 1.请求行（方法，URL）

```kotlin
val request = Request.Builder()
				.url(url)         // url
				.get()          // 请求方法 GET
			//	.post(requestBody)   // 请求方法 POST
             	.build()          // 构建
```

### 2.请求头

> 这个用的比较少，具体可以看 [Request.Builder API 文档](https://square.github.io/okhttp/3.x/okhttp/okhttp3/Request.Builder.html)

```java
Request.Builder header(String name, String value)
```

`Request.Builder`的方法，用来在`header`中添加一对数据

### 3.请求体

在`OkHttp`中，请求体由`RequestBody`类表示

![RequestBody](http://img.inaction.fun/static/12211.svg)

`RequestBody`是一个抽象类，其有两个实现类

* `FormBody`

  代表表单数据的body

* `MultipartBody`

  用来上传文件的body

这两个实现类都用构造器来创建实例

**构建FormBody示例**

```kotlin
val formBody = FormBody.Builder()
		.add("name","hhh")
		.add("password","123456")
		.build()
```

**构建MultipartBody示例**

```kotlin
val multipartBody = MultipartBody.Builder()
		.type(MultipartBody.FORM)	// 设置content-type 为 multipart/form-data
		.addFormDataPart("name","hhh") // 添加一对数据
		.addFormDataPart(          // 添加文件
        	"file-key",
           "file-name",
           byteArray.toRequestBody(
               "image/png".toMediaType(),
           		0,byteArray.size
           )
        )
		.build()
```

## 三. 发送请求

### 1. 同步

```kotlin
val responseBody = client.newCall(request).execute()
```

### 2. 异步

```kotlin
client.newCall(request).enqueue(object:Callback{
    override fun onFailure(call:Call, e:IOException){
        
    }
    override fun onResponse(call:Call, response: Response){
        
    }
})
```

