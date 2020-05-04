# App Bar

> 笔记整理自官方文档 Add the app bar 下的5篇文章

**为什么要用Toolbar?**

因为原来的`ActionBar`在各个安卓版本上表现不一致，而`ToolBar`只要用`support`库就都一样



## 添加Toolbar

**遵循以下步骤：**

1. **使用NoActionBar主题**

   ```xml
   <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
   	...
   </style>
   ```

2. **添加一个Toolbar**

   ```xml
   <androidx.appcompat.widget.Toolbar
      android:id="@+id/my_toolbar"
      android:layout_width="match_parent"
      android:layout_height="?attr/actionBarSize"
      android:background="?attr/colorPrimary"
      android:elevation="4dp"
      android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
      app:popupTheme="@style/ThemeOverlay.AppCompat.Light"/>
   ```

3. **在onCreate()里设置**

   ```kotlin
   setSupportActionBar(findViewById(R.id.my_toolbar))
   ```



## 标题居中

默认的标题在左侧，那个不可更改，要让标题居中

**遵循以下步骤：**

1. **在Toolbar里放一个TextView**

   ```xml
   <androidx.appcompat.widget.Toolbar
      android:id="@+id/my_toolbar"
      android:layout_width="match_parent"
      android:layout_height="?attr/actionBarSize"
      android:background="?attr/colorPrimary"
      android:elevation="4dp"
      android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
      app:popupTheme="@style/ThemeOverlay.AppCompat.Light">
      <TextView
         android:id="@+id/title"
         android:layout_gravity="center"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="22sp"/>
   </androidx.appcompat.widget.Toolbar>
   ```

2. **隐藏原来的标题**

   ```kotlin
   supportActionBar?.setDisplayShowTitleEnabled(false)
   ```



## Action Button

`Action Button`是`Toolbar`上的按钮，其可以直接显示在`Toolbar`上，也可以折叠进菜单中。

**请遵循以下步骤：**

1. **在res/menu/下新建xml文件**

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <menu xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto">
       <item android:id="@+id/test"
           android:title="test"
           android:icon="@drawable/ic_search"
           app:showAsAction = "always"/>
   </menu>
   ```

   `app:showAsAction`有以下取值：

   * always
   * ifRoom
   * never

2. **重写onCreateOptionsMenu()**

   ```kotlin
   override fun onCreateOptionsMenu(menu: Menu?): Boolean {
       menuInflater.inflate(R.menu.xxx, menu)
       return true
   }
   ```

3. **重写onOptionsItemSelected()**

   ```kotlin
   override fun onOptionsItemSelected(item: MenuItem) = when (item.itemId) {
       R.id.xxx -> {
           // 点击事件
           true
       }
       else -> {
           // If we got here, the user's action was not recognized.
           // Invoke the superclass to handle it.
           super.onOptionsItemSelected(item)
       }
   }
   ```

   



## Navigation icon

`Toolbar`可以设置一个`navigation icon`，在`Toolbar`的最左侧

```kotlin
toolbar.setNavigationIcon(R.drawable.ic_return)
toolbar.setNavigationOnClickListener{
    // 点击事件
}
```

> 注意：以上方法是 Toolbar 的，不是 ActionBar 的



## Up Action

这是一个`ActionBar`左侧的导航按钮

```kotlin
// 首先要启用
supportActionBar?.setDisplayHomeAsUpEnabled(true)
// 可以设置图标，也可以用系统默认的
supportActionBar?.setHomeAsUpIndicator(R.drawable.xxx)
// 可以在 onOptionsItemSelected() 中写点击事件
// id 是 android.R.id.home
```

**这个导航按钮和Navigation icon有什么不同？**

我看不出有什么不同，但是如果二者都设置的话，这一个会被覆盖。

`Navigation Icon`的点击事件看起来容易设置一些。

> 可以在 manifest 文件中声明要返回的 Activity 是哪一个，但我觉得这么做很蠢，有兴趣自己去看文档



## Action View

<img src="http://img.inaction.fun/static/59716.jpg" alt="img" style="zoom:50%;" />

<img src="http://img.inaction.fun/static/27982.jpg" alt="img" style="zoom:50%;" />

### 什么是Action View?

看上面的两张图片，一般情况下，它像是一个`Action Button`，点击之后，它会展开布局，用户需要点击返回键才能返回。



### 如何添加？

**遵循以下步骤：**

1. **在res/menu/下新建xml文件**

   ```xml
   <item android:id="@+id/action_search"
        android:title="@string/action_search"
        android:icon="@drawable/ic_search"
        app:showAsAction="ifRoom|collapseActionView"
        app:actionViewClass="android.support.v7.widget.SearchView" />
   ```

   > collapseActionView 是必须的，ifRoom 可以换成其他的

   此处有两种写法：

   * 使用`app:actionViewClass`

     这意味着你必须有一个自定义`View`实现了`CollapsibleActionView`接口。

     注意，这个类是不仅要实现接口，还要是个自定义`View`，否则无法展开。

   * 使用`app:actionLayout="@layout/xxx"`

     这只要填入一个布局就可以了，当按钮被点击时，这个布局会出现在`Toolbar`中。

   

2. **重写onCreateOptionsMenu()**

   ```kotlin
   override fun onCreateOptionsMenu(menu: Menu?): Boolean {
       menuInflater.inflate(R.menu.options, menu)
       // ...
   
       // Define the listener
       val expandListener = object : MenuItem.OnActionExpandListener {
           override fun onMenuItemActionCollapse(item: MenuItem): Boolean {
               // Do something when action item collapses
               return true // Return true to collapse action view
           }
   
           override fun onMenuItemActionExpand(item: MenuItem): Boolean {
               // Do something when expanded
               return true // Return true to expand action view
           }
       }
       // Get the MenuItem for the action item
       val actionMenuItem = menu?.findItem(R.id.myActionItem)
       // Assign the listener to that action item
       actionMenuItem?.setOnActionExpandListener(expandListener)
       // Any other things you have to do when creating the options menu...
       return true
   }
   ```

   此处，不仅仅是把东西加到`Toolbar`在，还设置了监听。

   

### 两种方式该如何选择？

1. 自定义`View`会麻烦一点，记得要实现`CollapsibleActionView`接口，这个接口规定了这个`View`展开和折叠时的回调。这种方式虽然麻烦，但是可以直接在类里面写展开的监听。
2. 直接给一个布局的方式比较简单，但是实际中肯定还是要在`Activity`里写展开的监听的。



### 这玩意好用吗？

恐怕不太好用，有以下缺点：

1. 展开时最左侧会出现`Android`默认的返回按钮，即使你设置了按钮的样式，在这种情况下还是会出现系统默认的。
2. 布局的宽度和高度都受到`Toolbar`的限制，而且宽度只能`wrap_content`，用`match_parent`无效，这是最致命的。



## Action Provider

**什么是Action Provider?**

我觉得和`Action View`挺像的，也没有细致的研究，感觉用处不大。

有兴趣自己去看文档。