# Object 类方法

## equals()

* 对于基本类型，`==`判断两个值是否相等，没有`equals()`
* 对于引用类型，`==`判断两个变量是否引用同一个对象，`equals`判断引用的对象是否等价

```java
public class Example {
    
    private int x;

    @Override
    public boolean equals(Object o) {
        if (this == o) 
            return true;
        if (o == null || getClass() != o.getClass()) 
            return false;
        Example example = (Example) o;
        return x == example.x;
    }
}
```

