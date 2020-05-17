# Serializable

> 参考文章：
>
> [Java 序列化 之 Serializable](https://www.jianshu.com/p/af2f0a4b03b5)
>
> [如果你的Serializable类包含一个不可序列化的成员，会发生什么？你是如何解决的?](https://blog.csdn.net/troubleshooter/article/details/99304762)

`Serializable`用来将对象序列化与反序列化，需要注意的是，序列化的和反序列化的不是同一个对象，它们只是数据相同而已。

**静态成员是不会被序列化的。**

## 基本使用

对于一般的类来说，让其可序列化非常简单，只要实现`Serializable`接口即可。

```kotlin
class A: Serializable{
    var name: String = ""
    var list: List<Int> = listOf()
    var array: Array<String> = arrayOf()
}
```

> 类里面有集合和数组都没关系

如果类里面有其他的类类型，则那个类也必须实现`Serializable`接口，或者为其添加`@Transient`注解。

**静态成员不会被序列化。**

> 在 Java 中，是使用 transient 关键字，在 Kotlin 中，是用注解

## @Transient 注解

当某个字段被`@Transient`注解标记后，该字段就会被序列化机制忽略。

```kotlin
class A( var name: String, @Transient var b: B): Serializable
class B(var value:String)
```

> 在上述代码中，A实现Serializable接口，而B没有，如果不将字段 b 标记为 transient ，则 A 也无法序列化

## 序列化类中不可序列化的成员

**分为两步：**

1. 用`@Transient`标记不可序列化的成员
2. 添加**私有的**`readObject()`和`writeObject()`方法

**示例：**

```kotlin
class A(var name:String, @Transient var b: B): Serializable{

    private fun readObject(s: ObjectInputStream){
        s.defaultReadObject()
        val value = s.readObject() as String
        b = B(value)
    }

    private fun writeObject(s: ObjectOutputStream){
        s.defaultWriteObject()
        s.writeObject(b.value)
    }
}
class B(val value:String)
```

* `readObject()`和`writeObject()`定义为私有方法，这样可以避免被继承、重写等。而`Java`通过反射可以调用私有方法，所以照常序列化。
* 对于类中不可序列化的成员，就将其拆分为可序列化的部分，反序列化时再自己组装。
* 不要忽略`defaultWriteObject()`和`defaultReadObject()`方法。

## serialVerisionUID

```java
private static final long serialVersionUID = 12345678L
```

这个属性用于版本控制。

如果类中没有显示声明这个属性，那么`Java`编译器会根据属性和方法自动生成。

> 方法中的内容改变，该值不会改变

如果一个类之前序列化到文件中，然后为该类添加了一个属性，这时反序列化就会失败，因为`serialVersionUID`的值不同。

可以自己指定这个字段，这样就能自己控制版本。

## 示例：序列化对象到内存中

序列化的具体执行时由`ObjectOutputStream`类的`writeObject()`和对应的读取进行的。

而节点流可以选择文件、网络或内存。

```kotlin
class A(var name:String, @Transient var b: B): Serializable{
    
    private fun readObject(s:ObjectInputStream){
        s.defaultReadObject()
        val value = s.readObject() as String
        b = B(value)
    }

    private fun writeObject(s:ObjectOutputStream){
        s.defaultWriteObject()
        s.writeObject(b.value)
    }
}
class B(val value:String)


fun main(){

    val a = A("test",B("28"))

    val baos = ByteArrayOutputStream()
    val oos = ObjectOutputStream(baos)

    oos.writeObject(a)
    val bytes = baos.toByteArray()

    val bais = ByteArrayInputStream(bytes)
    val ois = ObjectInputStream(bais)

    val a2 = ois.readObject() as A
    
}
```





