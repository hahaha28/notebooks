# ActionMode

[源码](https://github.com/hahaha28/sample-actionMode)

效果如下图

<img src="https://github.com/hahaha28/photos/raw/master/notebooks/Android/action_mode.gif" alt="img" style="zoom: 25%;" />

用到了菜单中的 *上下文模式* 



## MainActivity.kt

```kotlin
package com.example.sample.actionmode

import android.graphics.Color
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.view.Menu
import android.view.MenuItem
import android.view.View
import android.widget.Toast
import androidx.appcompat.view.ActionMode
import androidx.appcompat.widget.Toolbar
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView

class MainActivity : AppCompatActivity() {

    private val actionModeCallback = object: ActionMode.Callback{
        override fun onActionItemClicked(mode: ActionMode?, item: MenuItem?): Boolean {
            return when(item?.itemId){
                R.id.delete -> {
                    adapter.deleteAllSelected()
                    Toast.makeText(this@MainActivity,"delete",Toast.LENGTH_SHORT).show()
                    actionMode?.finish()
                    true
                }
                else -> false
            }
        }

        override fun onCreateActionMode(mode: ActionMode?, menu: Menu?): Boolean {
            mode?.menuInflater?.inflate(R.menu.action_mode,menu)
            return true
        }

        override fun onPrepareActionMode(mode: ActionMode?, menu: Menu?): Boolean {
            return true
        }

        override fun onDestroyActionMode(mode: ActionMode?) {
            actionMode = null
            adapter.clearSelected()
        }

    }

    private var actionMode:ActionMode? = null   // ActionMode
    private lateinit var adapter:MyAdapter      // adapter
    private lateinit var dataList:MutableList<String>    // 数据集

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 设置Toolbar
        val toolbar: Toolbar = findViewById(R.id.toolbar)
        setSupportActionBar(toolbar)
        toolbar.setTitleTextColor(Color.WHITE)

        // 设置RecyclerView
        val recyclerView = findViewById<RecyclerView>(R.id.recycler_view)
        val layoutManager = LinearLayoutManager(this)
        recyclerView.layoutManager = layoutManager
        dataList = MutableList(20){
            "第${it}项"
        }
        adapter = MyAdapter(dataList)
        recyclerView.adapter = adapter

        // 设置点击事件
        adapter.onClickListener = {position:Int,view:View ->

            // 如果处于 ActionMode
            if(actionMode != null){
                adapter.setSelected(position)
                actionMode?.title = "已选择${adapter.getSelected()}项"
            }else {
                // 否则是正常的点击
                Toast.makeText(this, "click$position", Toast.LENGTH_SHORT).show()
            }
        }

        // 设置长按点击事件
        adapter.onLongClickListener = { position: Int, view: View ->
            if(actionMode == null){
                actionMode = this.startSupportActionMode(actionModeCallback)
            }
            adapter.setSelected(position)
            actionMode?.title = "已选择${adapter.getSelected()}项"
        }
    }
}
```



## MyAdapter.kt

```kotlin
package com.example.sample.actionmode

import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class MyAdapter(val dataList:MutableList<String>): RecyclerView.Adapter<MyAdapter.ViewHolder>() {

    /**
     * 用来记录哪些项被点击了
     */
    private val selectedItem:MutableList<Int> = mutableListOf()

    /**
     * 长按点击事件
     */
    var onLongClickListener : (position:Int,view:View)->Unit = { p: Int, view: View -> }

    /**
     * 点击事件
     */
    var onClickListener : (position:Int,view:View)->Unit = {p:Int,view:View->}

    class ViewHolder(val view:View):RecyclerView.ViewHolder(view){
        val textView:TextView = view.findViewById(R.id.textView)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_view,parent,false)
        val holder = ViewHolder(view)
        holder.view.setOnClickListener {
            onClickListener(holder.adapterPosition,it)
        }
        holder.view.setOnLongClickListener {
            onLongClickListener(holder.adapterPosition,it)
            true
        }
        return holder
    }

    override fun getItemCount(): Int = dataList.size

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.textView.text = dataList[position]
        holder.view.isSelected = position in selectedItem
    }

    /**
     * （取消）选中某项
     */
    fun setSelected(position:Int){
        if(position in selectedItem){
            selectedItem.remove(position)
        }else{
            selectedItem.add(position)
        }
        // 为什么这里不用notifyItemChanged()? ,因为亲测用这个
        // 方法的话速度慢一截，即点击之后有一个短暂延迟才会有改变
        // 出乎意料的是notifyDataSetChanged()反而没有延迟
//        notifyItemChanged(position)
        notifyDataSetChanged()
    }

    /**
     * 清除所有选中状态
     */
    fun clearSelected(){
        selectedItem.clear()
        notifyDataSetChanged()
    }

    /**
     * 获取选中的数量
     */
    fun getSelected():Int = selectedItem.size

    /**
     * 删除所有选中项
     */
    fun deleteAllSelected(){
        selectedItem.sort()
        for( i in selectedItem.size-1 downTo 0){
            dataList.removeAt(selectedItem[i])
        }
    }
}
```





## res/menu/action_mode.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/delete"
        android:icon="@drawable/ic_delecte"
        android:title="delete"
        app:showAsAction="always"/>
</menu>
```



## values/styles.xml

```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>

        <item name="windowActionModeOverlay">true</item>
        <item name="actionModeStyle">@style/ActionModeStyle</item>
        <item name="actionModeCloseDrawable">@drawable/ic_return</item>
    </style>

    <style name="ActionModeStyle" parent="@style/Widget.AppCompat.ActionMode">
        <item name="background">@color/colorPrimary</item>
        <item name="titleTextStyle">@style/ActionModeTitleTextStyle</item>
    </style>

    <style name="ActionModeTitleTextStyle" parent="@style/TextAppearance.AppCompat.Widget.ActionMode.Title">
        <item name="android:textColor">@android:color/white</item>
    </style>
    
</resources>

```

