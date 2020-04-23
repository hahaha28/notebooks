# View Binding

> 笔记整理自[官方文档](https://developer.android.com/topic/libraries/view-binding)
>
> 2020.3.5



## 配置

**build.gradle**

在模块的 `build.gradle` 下添加启用代码。

```gradle
android{
	...
	viewBinding{
		enabled = true
	}
}
```

> 要在 Android Studio 3.6以上才能用，gradle 插件的版本也要更新

**不生成绑定**

如果想要某个布局文件不生成映射对象，在根布局中设置：

```xml
<LinearLayout
	...
    tools:viewBindingIgnore = "true">
</LinearLayout>
```



## 使用

1. 每个布局文件会有一个映射类，类名是布局文件名转换的驼峰命名法+Binding

   ```note
   activity_main 会映射成 ActivityMainBinding
   ```

2. 布局文件中每一个有 id 的控件都会被映射成类的属性，没有 id 的不会映射

3. 可以使用 `getRoot()` 方法得到根布局

4. 布局需要 `inflate` 不要忘了

### 在 Activity 中使用

```kotlin
private lateinit var binding: ActivityMainBinding
override fun onCreate(savedInstanceState:Bundle?){
    super.onCreate(savedInstanceState)
    // 填充布局，设置根布局
    binding = ActivityManiBinding.inflate(layoutInflater)
    setContentView(binding.root)
}
```

### 在 Fragment 中使用

**注意：**

Fragment 的生命周期比他们的布局还长，所以在 `onDestroyView` 要释放对象

```kotlin
private var _binding: ResultProfileBinding? = null
// This property is only valid between onCreateView and
// onDestroyView.
private val binding get() = _binding!!

override fun onCreateView(
    inflater: LayoutInflater,
    container: ViewGroup?,
    savedInstanceState: Bundle?
): View? {
    _binding = ResultProfileBinding.inflate(inflater, container, false)
    val view = binding.root
    return view
}

override fun onDestroyView() {
    _binding = null
}
```

