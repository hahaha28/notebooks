# Menu

> 笔记整理自[官方文档](https://developer.android.com/guide/topics/ui/menus#groups)
>
> 2020-3-28



## 定义菜单项

在`res/menu/`目录下新建`xml`文件

### 标签

#### \<menu>

这是菜单文件的根标签



#### \<item>

每一个`<item>`代表一个菜单项

`<item>`里面可以用`<menu>`来创建2级菜单

> 2级菜单看起来相当蠢，还是别用了

**属性**

| 属性名           | 描述                         |
| ---------------- | ---------------------------- |
| android:id       | 指定一个id，要用“@+id/xxx”   |
| android:icon     | 图标                         |
| android:title    | 名字                         |
| app:showAsAction | 可以是 always、never、ifRoom |

> 更多属性参考这个 [文档](https://developer.android.com/guide/topics/resources/menu-resource)



#### \<group>

`<group>`是一个不可见的`<item>`容器，可以用来操作一组`<item>`



## 选项菜单

选项菜单（options menu）出现在AppBar上

### 创建

2个步骤：

**1.重写onCreateOptionsMenu()**

```kotlin
override fun onCreateOptionsMenu(menu: Menu): Boolean {
    val inflater: MenuInflater = menuInflater
    inflater.inflate(R.menu.game_menu, menu)
    return true
}
```

**2.重写onOptionsItemSelected()**

```kotlin
override fun onOptionsItemSelected(item: MenuItem): Boolean {
    // Handle item selection
    return when (item.itemId) {
        R.id.new_game -> {
            newGame()
            true
        }
        R.id.help -> {
            showHelp()
            true
        }
        else -> super.onOptionsItemSelected(item)
    }
}
```

> 可以使用 android:click 给菜单项设置点击事件

> 如果多个 Activity 有相同的选项菜单，考虑写一个父类



### 动态变更菜单项

两个步骤：

* 1. 把变更的代码写在 `onPrepareOptionsMenu()`里
* 2. 调用`invalidateOptionsMenu()`，这个方法会让系统重新调用`onPrepareOptionsMenu()`



## 上下文操作模式

效果如下图，代码参考 [这个笔记](../Sample/ActionMode.md)

<img src="https://github.com/hahaha28/photos/raw/master/notebooks/Android/action_mode.gif" alt="img" style="zoom: 25%;" />

> 上下文操作模式有两种，一种是给单个 View 用的，一种是给 ListView 用的，但是给 ListView 用的那种只适用于 AbsListView 的子类，而 RecyclerView 是继承自 ViewGroup 的，所以不能用。但是用第一种方法也实现了上图所示的效果，笔记也不记录第二种方法。

简单来说只需两步：

**1.实现ActionMode.Callback接口**

这个接口定义了当上下文操作模式被触发、销毁时，会发生什么

```kotlin
private val actionModeCallback = object : ActionMode.Callback {
    // Called when the action mode is created; startActionMode() was called
    override fun onCreateActionMode(mode: ActionMode, menu: Menu): Boolean {
        // Inflate a menu resource providing context menu items
        val inflater: MenuInflater = mode.menuInflater
        inflater.inflate(R.menu.context_menu, menu)
        return true
    }

    // Called each time the action mode is shown. Always called after onCreateActionMode, but
    // may be called multiple times if the mode is invalidated.
    override fun onPrepareActionMode(mode: ActionMode, menu: Menu): Boolean {
        return false // Return false if nothing is done
    }

    // Called when the user selects a contextual menu item
    override fun onActionItemClicked(mode: ActionMode, item: MenuItem): Boolean {
        return when (item.itemId) {
            R.id.menu_share -> {
                shareCurrentItem()
                mode.finish() // Action picked, so close the CAB
                true
            }
            else -> false
        }
    }

    // Called when the user exits the action mode
    override fun onDestroyActionMode(mode: ActionMode) {
        // 在销毁时一定要把这个设为null
        actionMode = null
    }
}
```

**2.调用startSupportActionMode()**

调用这个方法会开启上下文操作模式，这个方法返回一个`ActionMode`对象

```kotlin
someView.setOnClickListener{
	if(actionMode == null){
		actionMode = startSupportActionMode(actionModeCallback)
	}
}
```

> 官方文档中用的是 startActionMode() ，但是如果是用 androidX ，应该使用上面那个



**虽说使用上下文操作模式只有上面两步，但还是有很多坑的。所以我写了个[例子](../Sample/ActionMode.md)，如果要用到建议还是去参考一下。**

**下面说几个注意事项：**



### styles.xml文件

默认情况下，上下文操作模式的那个栏不会覆盖`Toolbar`，而且它和`Toolbar`本来就是相互独立的，所以当它出现时，`Toolbar`会往下移。

默认情况下，上下文操作模式的那个栏里的字体和背景颜色还有返回按钮，都不怎么好看，但是都可以设置。

```xml
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
        
        <!-- 这个属性设置ActionMode会覆盖Toolbar -->
        <item name="windowActionModeOverlay">true</item>
        <item name="actionModeStyle">@style/ActionModeStyle</item>
        <item name="actionModeCloseDrawable">@drawable/ic_return</item>
    </style>

    <!-- 设置背景和字体颜色 -->
    <style name="ActionModeStyle" parent="@style/Widget.AppCompat.ActionMode">
        <item name="background">@color/colorPrimary</item>
        <item name="titleTextStyle">@style/ActionModeTitleTextStyle</item>
    </style>

    <style name="ActionModeTitleTextStyle" parent="@style/TextAppearance.AppCompat.Widget.ActionMode.Title">
        <item name="android:textColor">@android:color/white</item>
    </style>
    
</resources>

```



### 标题

上下文操作模式的那个栏是和`Toolbar`独立的，它也可以设置标题

`ActionMode.setTitle()`

`ActionMode.setSubTitle()`



## 浮动上下文菜单和弹出菜单

[浮动上下文菜单官方文档](https://developer.android.com/guide/topics/ui/menus#FloatingContextMenu)

[弹出菜单官方文档](https://developer.android.com/guide/topics/ui/menus#PopupMenu)

在两者的效果很像，都是在某个`View`上弹出一个小菜单，而且我感觉这两个都不好用，所以不做笔记，这两个用法都很简单。

* 浮动上下文菜单只能由长按触发，而且UI受到系统限制。
* 弹出菜单有代码触发，但是UI受到系统限制，并不好看。



## 菜单组

感觉没什么用，可以用来操作一组菜单项的可见性等。

[官方文档](https://developer.android.com/guide/topics/ui/menus#groups)



## 复选框菜单项

感觉在现在的版本完全没法用，而且还丑（起码在我的小米cc9上用不了，点击无效）

[官方文档](https://developer.android.com/guide/topics/ui/menus#checkable)

