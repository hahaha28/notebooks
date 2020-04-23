# LayoutInflater

* 参考文章

  [Android LayoutInflater原理分析，带你一步步深入了解View(一)](https://blog.csdn.net/guolin_blog/article/details/12921889)

  [三个案例带你看懂LayoutInflater中inflate方法两个参数和三个参数的区别](https://blog.csdn.net/u012702547/article/details/52628453#commentBox)



## 概述

`LayoutInfalter`主要用来加载布局，`inflate()`的作用就是把一个用`xml`定义的布局文件加载出来。

注意与`findViewById()`的区别，`inflater()`是加载一个布局文件，`findViewById()`是在一个已加载的视图结构中查找一个`View`。



## 获取实例

有两种方法：

```kotlin
val layoutInflater = LayoutInfalter.from(context)
```

```kotlin
val layoutInflater = context.
	getSystemService(Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater
```

第一种方法其实是第二种方法的简写



## 加载布局

使用`LayoutInflater`的`infalte()`方法

```java
public View inflate(int resource, ViewGroup root, boolean attachToRoot)
```

第一个参数是要加载的布局文件的`id`

下面讲讲第二个和第三个参数

| root     | attachToRoot | 说明                                                         |
| -------- | ------------ | ------------------------------------------------------------ |
| null     | -            | 如果root为null,则代表这个加载的布局没有根布局，attachToRoot写什么都无所谓 |
| 不为null | true         | 表示将 root 设为加载的布局的根布局，则加载的布局的根View的宽高属性将会生效，这时，布局已经加入 root 中，不能再调用 addView 添加 |
| 不为null | false        | 加载的布局的根View的宽高属性将会生效，但是 root 不是其根布局，如果要显示这个加载的布局，需要再调用 addView 添加 |

> 当 root 不为 null ，attachToRoot 为 false 时，加载的布局和root并没有什么关系，它仅仅是借助 root 让自己的宽高属性生效，但是它的宽高也和 root 的宽高没有什么关系。

> 如果一个加载的布局的宽高属性不生效，则其默认是wrap_content，这有时会出现问题，例如，一个约束布局中包含一个按钮，按钮的宽度为match_parent，而如果约束布局的宽高属性不生效，那么什么都不会显示出来。但如果按钮的宽度设置为wrap_content，那么会显示出来。