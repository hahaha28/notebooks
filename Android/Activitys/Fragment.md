# Fragment

> 笔记整理自[官方文档](https://developer.android.com/guide/components/fragments)
>
> 2020-3-8

![img](http://img.inaction.fun/static/10963.png)



## 创建Fragment

需要直接或间接继承`Fragment`类，然后遵循以下步骤：

### 设置UI

需要在`onCreateView()`回调里返回一个`View`作为布局

**示例：**

```kotlin
class MyFragment: Fragment(){
    override fun onCreateView(
    		inflater: LayoutInflater,
    		container: ViewGroup?,
          savedInstanceState: Bundle?
    ):View {
        return inflater.inflate(R.layout.fragment_ui,container,false)
    }
}
```

### 把Fragment加入Activity

**有两种方法：**

#### 1.在Activity的布局文件声明

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment android:name="com.example.news.ArticleListFragment"
            android:id="@+id/list"
            android:layout_weight="1"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
    <fragment android:name="com.example.news.ArticleReaderFragment"
            android:id="@+id/viewer"
            android:layout_weight="2"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
</LinearLayout>
```

**注意：**

* `android:name`属性声明了使用哪个类去实例化

> 每个 fragment 必须有一个唯一的身份标识，这样系统才能在activity重启时恢复fragment。
>
> 可以声明 android:id ，也可以用 android:tag



#### 2.用代码添加

1. 必须指定存放 `fragment` 的 `ViewGroup`
2. 必须使用`FragmentTransaction`的API

```kotlin
// 获取 fragmentTransaction
val fragmentManager = supportFragmentManager
val fragmentTransaction = fragmentManager.beginTransaction
// 添加
val fragment = MyFragment()
fragmentTransaction.add(R.id.fragment_container ,fragment)
fragmentTransaction.commit()
```

> 更多关于 fragmentTransaction 的用法，请看本页 [Fragment的事务](#Fragment的事务)



## 管理Fragment

使用`FragmentManager`管理`Fragment`

```kotlin
getSupportFragmentManager()
```

**获取Activity中的Fragment**

使用`findFragmentById()`或`findFragmentByTag()`

**回滚Fragment的事务**

`popBackStack()`

关于事务请看[Fragment的事务](#Fragment的事务)

**给返回栈注册监听器**

`addOnBackStackChangedListener()`



## Fragment的事务

`Fragment`可以在`Activity`中添加、移除、替换，还能设置保存点回滚事务。



### 事务是什么

事务是一组操作的集合，操作包括，添加、移除、替换`Fragment`等操作。而且事务能够被回滚。

事务要借助`FragmentTransaction`的API

**获取FragmentTransaction：**

```kotlin
val fragmentManager = supportFragmentManager
val fragmentTransaction = fragmentManager.beginTransaction()
```

要执行事务，必须调用`commit()`提交。

```kotlin
fragmentTransaction.commit()
```



### 回滚事务

```kotlin
val newFragment = ExampleFragment()
val transaction = supportFragmentManager.beginTransaction()
transaction.replace(R.id.fragment_container, newFragment)
transaction.addToBackStack(null)
transaction.commit()
```

在上面的代码中，`replace`这个操作被添加到一个Activity维护的返回栈中，当`onBackPressed()`被调用，即用户触摸返回键时，这个事务将被回滚。

**一个事务是一起回滚的**

一个事务中可以有很多操作，但只要`addToBackStack()`后，回滚时便是一起回滚。

如果有多个事务添加到了返回栈中，那么它们会依次回滚。

> 可以用 FragmentManager的popBackStack() 指定要回滚到哪一次事务。

如果在替换`fragment`时没有`addTobackStack()`，那么这个`fragment`会被直接摧毁，否则它的状态会被保留。

> 在执行 Fragment 事务时，可以在 commit() 前调用 setTransition() 让转换拥有一个动画。



## 与Activity的交互

在`Fragment`中，可以调用

```kotlin
getActivity()
```

获得一个 `FragmentActivity` 对象。

然后可以轻易的获取到`Activity`中的`View`，如下：

```kotlin
val listView:View? = activity?.findViewById(R.id.list)
```

同理，`Activity`也可以获取到`Fragment`



## 设置AppBar的菜单

遵循以下步骤：

1. **在`onCreate`中`setHasOptionsMenu(true)`**

   如果没有这一步，`fragment`将不会有`onCreateOptionsMenu()`的回调。

2. **实现`onCreateOptionsMenu()`**

3. **在`onOptionsItemSelected()`处理点击事件**

上下文菜单也可以设置。

> Fragment 的 Activity 会先于 Fragment 接收到菜单点击事件的回调，如果 Activity 没有处理，才会传递到 Fragment。
