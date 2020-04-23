# Overview

![img](http://img.inaction.fun/static/notebooks/Android/Android动画.svg)

`Android`中一共有两种类型的动画，**视图动画（View Animation）**和**属性动画（Property Animation）**



### 视图动画与属性动画的不同

* **实现原理不同**

  视图动画并没有改变`View`的属性，如果采用视图动画将`View`移动到某个位置，这个位置并不能触发点击事件，之前的位置反而可以。

  属性动画是通过改变`View`的属性来产生动画效果。



* **引入时间不同**

  视图动画在 `API 1` 引入

  属性动画在 `API 11` 引入



* **包名不同**

  视图动画在包`android.view.animation`下

  属性动画在包`android.animation`下



* **类的命名不同**

  视图动画的类取名都为`XxxAnimation`

  属性动画的类取名都为`XxxAnimator`

  