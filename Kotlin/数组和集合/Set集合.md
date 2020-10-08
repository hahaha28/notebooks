# Set 集合

> **Set** 集合的元素不允许重复，除此之外，**Set** 集合与 **Collection** 集合提供的方法大致相同。

## 创建 Set 集合

实际上，**Kotlin** 并没有实现任何 **Set** 集合类，因此不推荐使用构造器创建 **Set** 集合

使用如下函数来创建 **Set** 集合：

* **setOf()**

    返回**不可变**的 **Set** 集合

    该函数可接受 0 个或多个参数，这些参数将作为集合的元素

    

* **mutableSetOf()**

    返回可变的 **MutableSet** 集合

    该函数可接受 0 个或多个参数，这些参数将作为集合的元素

    

* **hashSetOf()**

    返回可变的 **HashSet** 集合（无序集合）

    该函数可接受 0 个或多个参数，这些参数将作为集合的元素



* **linkedSetOf()**

    返回可变的 **LinkedHashSet** 集合（有序集合）

    该函数可接受 0 个或多个参数，这些参数将作为集合的元素



* **sortedSetOf()**

    返回可变的 **TreeSet** 集合（会按照大小对元素进行排序）

    该函数可接受 0 个或多个参数，这些参数将作为集合的元素

## 遍历 Set

* **for-in** 遍历 **Set**

~~~Kotlin
var set = setOf(1,2,3)
for( num in set ){
    println(num)
}
~~~

* **forEach()** 方法遍历

    **Set** 集合继承了 **Iteratable** ，因此可以使用该接口中定义的 **forEach()** 

    该方法接受一个 **Lambda** 表达式作为参数

~~~kotlin
var set = setOf(1,2,3)
set.forEach({
    println(it)
})
~~~

* 使用索引遍历

    因为 **setOf()** 方法返回的 **Set** 集合是有序的，所以可以用索引遍历

~~~kotlin
var set = setOf(1,2,3)
for( i in set.indices ){
    println(set.elementAt(i))
}
~~~

## 添加元素

> setOf()返回的是不可变集合，不能添加元素

* **add(element: E)**
* **addAll(elements: Collection\<E>)**

~~~kotlin
var set = mutableSetOf<Int>()
set.add(1)
println(set)	// [1]
set.addAll(setOf(2,3,4))
println(set)	// [1,2,3,4]
~~~

## 删除元素

> setOf()返回的是不可变集合，不能删除元素

* **remove(element: E)**

    删除指定元素，成功返回 true

* **removeAll(elements: Collection\<E>)**

    批量删除集合中的多个元素

* **retainAll(elements: Collection\<E>)**

    只保留 **Set** 集合中与 **elements** 集合共有的元素

* **clear()**

    清空集合