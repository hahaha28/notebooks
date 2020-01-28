# Lambda表达式

## 语法规则

Lambda表达式的语法为

~~~kotlin
{(形参列表) ->
 	//零条到多条可执行语句
}
~~~

Lambda表达式的语法和局部函数很相似，但有如下区别

* Lambda表达式总是被大括号括着
* 定义Lambda表达式不需要 `fun` 关键字，无须指定函数名
* 形参列表（如果有的话）在  `->`  之前声明，参数类型可以省略
* 函数体（Lambda表达式的执行体）放在 ` ->`  之后
* 函数的最后一个表达式自动作为Lambda表达式的返回值，无须使用 `return` 关键字



## 调用Lambda表达式

Lambda表达式的本质是功能更灵活的代码块，因此可以将Lambda表达式赋值给变量或直接调用

~~~kotlin
//定义一个Lambda表达式并赋值给变量
var square = {n:Int ->
	n*n
}
println(square(3))	//9
//定义一个Lambda表达式并直接调用
var result = {a:Int,b:Int ->
     a+b
}(1,2)
println(result)		//3
~~~



## 省略形参类型

如果Kotlin可以根据Lambda表达式上下文推断出形参类型，那么Lambda表达式就可以省略形参类型

~~~kotlin
//定义一个Lambda表达式并赋值给变量
var square:(Int)->Int = {n->
	n*n                        
}
~~~

由于square变量的类型已经指定，所以Kotlin可以推断出Lambda表达式的形参类型，所以可以省略



## 省略形参名

如果只有一个形参，Lambda表达式可以省略形参名，那么 `->` 也不需要，并且使用 `it` 代表形参

~~~kotlin
//定义一个Lambda表达式并赋值给变量
var square:(Int)->Int{it*it}
println(square(3))
~~~



## 调用Lambda表达式的约定

如果函数的最后一个参数是函数类型，而且你打算传入一个Lambda表达式作为相应的参数，那么就允许在圆括号之外指定Lambda表达式

~~~kotlin
var array = Array<Int>(5){it+1}
~~~

如果Lambda表达式是函数调用的唯一参数，则调用方法时的圆括号可以省略

~~~kotlin
var list = listOf("java","Kotlin","Go")
var rt = list.dropWhile{it.length>3}
~~~



## 个数可变的参数和Lambda表达式

如果函数的最后一个形参为函数参数，倒数第二个形参为可变参数

那么可以将Lambda表达式写在圆括号而不需要使用命名参数