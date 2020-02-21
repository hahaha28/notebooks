# DBCP 连接池

> [Apache commons 官网](https://commons.apache.org/downloads/index.html)
>
> Apache commons 的包在 github 也有

dbcp 是 apache 实现的连接池。

## 导包

要导入 4 个包，我吐了

```note
comnons-dbcp2		
commons-pool2
commons-logging		// 这个是旧版不用的，要注意
mysql-connector-java	//驱动是必须的
```



## 连接

```kotlin
val dataSource = BasicDataSource().apply{
    // 驱动的类名
    driverClassName = "com.mysql.cj.jdbc.Driver"
    // url，其中 study 为数据库名，?后面的那个是设置时区为UTC（可以其他的）
	url = "jdbc:mysql://localhost:3306/study?serverTimezone=UTC"
    // 设置用户名密码
    username = "root"
    password = "123456"
}
// 此处应该捕获 SQLException 异常（懒得写了）
val con = dataSource.connection
```

