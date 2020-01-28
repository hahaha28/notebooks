# final 、const和 open 修饰符

> final 关键字表示它修饰的类、属性和方法不可变

Kotlin 会为非抽象类自动添加 `final` 修饰符，也会为非抽象方法、非抽象属性等无需重写的成员自动添加 `final` 修饰符。如果开发者希望取消 Kotlin 自动添加 `final` 修饰符，可使用 `open` 修饰符。

* Kotlin 与 Java 的一个重大区别是：**Kotlin 的 `final` 不能修饰局部变量**



## const 宏替换常量

在 Java 中，可以使用 `final` 修饰 `宏变量`

> `宏变量` 在编译阶段就会被替换掉

Kotlin 不允许用 `final` 修饰局部变量，而是使用 `const` 修饰 `宏变量`

`宏变量` 的常量除使用 `const` 修饰之外，还需满足如下条件：

1. 位于顶层或者是对象表达式的成员
2. 初始值为基本类型或者字符串字面值
3. 没有自定义的 `getter` 方法

```kotlin
// 定义支持 “宏替换” 的常量
const val VALUE = 28
fun main(){
    println(VALUE)
}
```

此外，如果被赋值的表达式只是基本的算术表达式或进行字符串连接运算，没有访问普通变量、常量、调用方法，那么 Kotlin 编译器同样会把这种 `const` 常量当成 `宏变量` 处理。

```kotlin
//下面定义了3个“宏变量”
const val a = 5 + 2
const val b : Double = 1.2/3
const val str : String = "hhh" + "ttt"
//下面book常量的值因为使用了另一个变量，所以不是“宏变量”
val d = 99.0
const val book:String = "hhh" + d
```



## final

* `final` 修饰的属性不能被重写
* `final` 修饰的方法不能被重写
* `final` 修饰的类不能被继承

Kotlin 会为非抽象类自动添加 `final` 修饰符，也会为非抽象方法、非抽象属性等无需重写的成员自动添加 `final` 修饰符。