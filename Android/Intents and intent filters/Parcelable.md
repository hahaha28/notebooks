# Parcelable

> [参考文章](https://www.jianshu.com/p/97503d7faaf3)

使用`Serializable`接口在`Intent`中传递数据效率很低，因为它进行了大量的`I/O`操作。所以`Android`提供了`Parcelable`接口，虽然实现起来比较麻烦，但是效率更高。

* 如果用`Intent`传递对象数据，就用`Parcelable`
* 如果是文件或网络中的序列化，就用`Serializable`

## 基本使用

```kotlin
class Person: Parcelable{
    var name = ""
    var age = 0
    
    override fun writeToParcel(parcel: Parcel, flags: Int){
        parcel.writeString(name)
        parcel.writeInt(age)
    }
    override fun describeContents(): Int{
        return 0  // 这个一般返回0就行了
    }
    companion object CREATOR : Parcelable.Creator<Person> {
        override fun createFromParcel(parcel: Parcel): Person{
            val person = Person()
            person.name = parcel.readString()?:""
            person.age = parcel.readInt()
            return person
        }
        override fun newArray(size: Int): Array<Person?> {
            return arrayOfNulls(size)
        }
    }
}
```

* **记住读取的顺序要和写入的顺序相同**
* 一般情况下照搬上面的代码就可以了

## Kotlin的简化使用方法

**使用条件：**要传递的所有数据都封装在对象的主构造函数中

```kotlin
@Parcelize
class Person(var name: String, var age: Int): Parcelable
```

## descrebeContents() 为什么返回0？

可以看看[官方文档](https://developer.android.com/reference/android/os/Parcelable#describeContents())

这个函数只能返回两个值，一个是0，另一个是`CONSTANTS_FILE_DESCRIPTOR`

一般返回0就行了

## newArray() 是干什么的？

可以参考 [stack overflow 的这个提问](https://stackoverflow.com/questions/22037801/parcelable-what-is-newarray-for)

是反序列化`Parcelable`对象数组时用的，按照上面示例代码返回就行了。