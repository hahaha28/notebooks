# Collection接口

## 常用方法

| 返回类型        | 方法名                                 | 描述             |
| --------------- | -------------------------------------- | ---------------- |
| boolean         | add (E e)                              | 添加             |
| boolean         | addAll (Collection<? extends E> c)     | 添加             |
| boolean         | remove (Object o)                      | 移除             |
| boolean         | removeAll (Collection<?> c)            | 移除             |
| default boolean | removeIf (Predicate<? super E> filter) | 移除特定数据     |
| boolean         | retainAll (Collection<?> c)            | 保留指定数据     |
| void            | clear()                                | 移除所有         |
| boolean         | contains (Object o)                    | 是否包含某个数据 |
| boolean         | containsAll (Collection<?> c)          | 是否包含指定数据 |
| int             | size()                                 | 集合大小         |
| Iterator\<E>    | iterator()                             | 迭代器           |
| Object[]        | toArray()                              | 转换为数组       |
| \<T> T[]        | toArray (T[] a)                        | 转换为数组       |

## removeIf()

```java
LinkedList<Integer> list = new LinkedList<>();
list.addAll(Arrays.asList(1,2,3,4,5,6));
list.removeIf(num ->{
    if(num >= 3 && num <= 5){
        return false;
    }
    return true;
});
System.out.println(list);
// 只剩下 3,4,5
```

## toArray()

```java
LinkedList<String> list = new LinkedList<>();
list.addAll(Arrays.asList("1","2","3"));
String[] strArray = list.toArray(new String[]{});
System.out.println(Arrays.toString(strArray));
```

> Collection<Integer> 无法转换为 int[]

