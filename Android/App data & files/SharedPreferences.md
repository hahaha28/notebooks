# SharedPreferences

> 笔记整理自官方文档 [Save key-value data](https://developer.android.com/training/data-storage/shared-preferences)
>
> 2020/9/28

## 获取对象

有两种方法

### 1. Context 类的 getSharedPreferences() 方法

```java
public abstract SharedPreferences getSharedPreferences(String name,
                                                      int mode)
```

* name

  指定文件名（不写后缀），如果文件不存在，则会创建一个

* mode

  操作模式，只能是`MODE_PRIVATE`，其他模式都废弃了

### 2. Activity 类的 getPreferences() 方法

```java
public SharedPreferences getPreferences(int mode)
```

* mode

  操作模式，只能是`MODE_PRIVATE`，其他模式都废弃了

使用这个方法会将当前的**类名作为文件名**

## 写

3个步骤：

1. 用`sharedPreferences.edit()`方法获取`SharedPreferences.Editor`对象
2. 使用类似`putInt()`和`putString()`方法放入数据
3. 调用 `apply()`或`commit()`保存数据

> apply() 是异步的
>
> commit() 是同步的

```java
SharedPreferences.Editor editor = getSharedPreferences("data"
                                 ,MODE_PRIVATE).edit();
editor.putString("name","hahaha");
editor.putInt("age",28);
editor.apply();
```

## 读

两个步骤：

1. 获取`SharedPreferences`对象
2. 调用类似`getInt()`或`getString()`方法获取数据

```java
SharedPreferences pref = getSharedPreferences("data",MODE_PRIVATE);
String name = pref.getString("name");
int age = pref.getInt("age");
```

