# vararg参数



## 在函数参数中传入不定长数据

~~~kotlin
fun sum(vararg nums:Int):Int{
    val sum = nums.sum()
    return sum
}
~~~

类似于 Java 的int...

传入的参数被当成 **Array\<T>** 数组，但由于Int有优化，所以此处为 **IntArray** 类型

**如果可变形参后还有形参，这之后的形参需要用命名参数传值**



## 将数组传入 vararg 参数

需要使用 **spread** 操作符（*前缀数组）

~~~kotlin
var nums:IntArray = intArrayOf(1,2,3)
var sum = sum(*nums)
~~~

