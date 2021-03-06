# List 集合

> 与 Java 类似，**List** 集合最大特征就是集合元素都有对应的顺序索引。**List** 集合允许使用重复元素，可以通过索引来访问指定位置的集合元素。**List** 集合默认按元素的添加顺序设置元素的索引。

## 创建 List 集合

Kotlin 并未真正实现 **List** 集合，因此不推荐通过构造器来创建 **List** 集合。

使用工具函数创建 **List** 集合：

* **listOf()**

    返回**不可变**的 **List** 集合

    该函数可接受 0 个或多个参数作为集合的元素

    

* **listOfNotNull()**

    返回**不可变**的 **List** 集合

    与上一个函数的唯一区别是，会自动去掉传入参数中的**null**值

    

* **mutableListOf()**

    返回**可变**的 **MutableList** 集合

    该函数可接受 0 个或多个参数作为集合的元素

    

* **arrayListOf()**

    返回可变的 **ArrayList** 集合

    该函数可接受 0 个或多个参数作为集合的元素