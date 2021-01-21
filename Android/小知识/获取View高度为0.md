不管是在`onCreate()`还是`onStart()`、`onResume`，获取布局里的任何 View 的高度都为0

[View's getWidth() and getHeight() returns 0](https://stackoverflow.com/questions/3591784/views-getwidth-and-getheight-returns-0)

```kotlin
textView.viewTreeObserver.addOnGlobalLayoutListener {
	textView.height
}
```

