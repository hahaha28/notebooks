# File

> java.io.File

* `/`表示Unix中的根目录(Android是这个），windows用`\`
* 将操作系统中的，文件，目录（文件夹），路径封装成File对象
* File是一个与系统无关的类



* 静态变量 File.separator

  文件目录分隔符，不同操作系统输出不一样



## 构造方法

![image-20201125160556972](http://img.inaction.fun/static/92971.png)

## 常用方法

* **创建文件**

  ```java
  boolean createNewFile()
  ```

  构造File对象，调用这个方法，创建文件

  不能用这个方法创建文件夹

* **创建文件夹**

  ```java
  boolean mkdir() // 只能创建一级文件夹
  boolean mkdirs()  // 可以创建多级文件夹（也可以是一级）
  ```

  构造File对象，调用mkdirs()方法，创建文件夹

* **删除文件（夹）**

  ```java
  boolean delete()
  ```

  构造File对象，调用此方法，删除文件或文件夹

  **不能删除多级文件夹**

## 文件属性相关方法

* **文件大小**

  ```java
  long	length()
  ```

* **文件是否存在**

  ```java
  boolean	exists()
  ```

* **判断是文件还是文件夹**

  ```java
  boolean isDirectory()
  boolean isFile()
  ```

* **获取文件（夹）父路径**

  ```java
  String getParent()
  File getParentFile()
  ```

* **获取名字**

  ```java
  String getName()
  ```

* **获取文件夹下的所有文件(夹）名**

  ```java
  String[] list()
  ```

* **获取文件夹下的所有文件（夹）File对象**

  ```java
  File[] listFiles()
  ```

* **获取File对象的绝对路径**

  ```java
  String getAbsolutePath()
  ```

## 删除文件夹

