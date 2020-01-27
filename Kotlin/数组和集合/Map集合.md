# Map 集合

> 与 Java 不同的是，Kotlin 的 **Map** 集合也被分为可变的和不可变的



## 创建 Map 集合

实际上 Kotlin 并没有实现任何 **Map** 集合类，因此不推荐通过构造器创建 **Map** 集合

使用工具函数创建 **Map** 集合：

* **mapOf()**

    返回**不可变**的 **Map** 集合

    该函数可接受 0 个或多个 **key-value** 作为集合的元素



* **mutableMapOf()**

    返回**可变**的 **MutableMap** 集合

    该函数可接受 0 个或多个 **key-value** 作为集合的元素



* **hashMapOf()**

    返回可变的 **HashMap** 集合

    该函数可接受 0 个或多个 **key-value** 作为集合的元素



* **linkedMapOf()**

    返回可变的 **LinkedHashMap** 集合

    该函数可接受 0 个或多个 **key-value** 作为集合的元素



* **sortedMapOf()**

    返回可变的 **TreeMap** 集合

    该函数可接受 0 个或多个 **key-value** 作为集合的元素



~~~kotlin
var map = mapOf("Java" to 1, "Kotlin" to 2, "Go" to 3)
println(map)
~~~

**Kotlin 需要使用 to 指定 key-value 对**



## 遍历 Map

> Kotlin 的 Map 提供了 operator 修饰的 get（）方法，因此可以用 **[]**

4 种方法，但不是全部：

~~~kotlin
var map = mapOf("Java" to 1, "Kotlin" to 2,"Go" to 3)
~~~

* 遍历 **Map** 的 **key-value** 对

~~~kotlin
for( en in map.entries ){
    println("${en.key} -> ${en.value}")
}
~~~
* 遍历 **Map** 的 **key** ，再通过 **key** 获取 **value**
~~~kotlin
for( key in map.keys ){
    println("${key} -> ${map[key]}")
}
~~~
* 直接用 **for-in** 循环遍历 **Map**
~~~kotlin
for( (key,value) in map ){
    println("${key} -> ${value}")
}
~~~

* 用 **Lambda** 表达式遍历 **Map**

~~~kotlin
map.forEach({
    println(" ${it.key} -> ${it.value} ")
})
~~~



## 添加

> 除了 mapOf() ，其他函数返回的都是可变的 Map 集合

* **put(key: K, value: V)**

    放入 **key-value** 对，如果 **key** 重复，则会覆盖

    

* **putAll( from: Map<out K,V>)**

    批量放入多个 **key-value** 对

    

* **使用 [] 运算符来放入 key-value 对**

    

## 删除

* **remove(key: K)**
* **clear()**

