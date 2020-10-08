# Blueprint

> [官方文档](https://flask.palletsprojects.com/en/1.1.x/tutorial/views/)
>
> [Blueprint对象文档](https://flask.palletsprojects.com/en/1.1.x/api/#blueprint-objects)
>
> [示例代码](https://github.com/hahaha28/flask-blueprint-sample)

`pip install Blueprint`

蓝图就是将视图函数分文件写，然后再将这些分开写的函数注册到主运行文件中

## 简单使用

![image-20200710170238247](http://img.inaction.fun/static/16306.png)

上图是一个简单的文件结构，`test1.py`文件就是用蓝图来写的视图函数

简单来说，使用蓝图分为两步：

1. 构建蓝图对象
2. 在`app.py`中注册蓝图

**1.构建蓝图对象（test1.py中的代码）：**

```python
from flask import Blueprint

test1 = Blueprint('test1',__name__)

@test1.route('/view1-test1')
def view1_test1():
    return 'view1-test1'
```

**2.注册蓝图（app.py中的代码）：**

```python
from flask import Flask
from view1.test1 import test1

app = Flask(__name__)
app.register_blueprint(test1)	# 注册蓝图对象

@app.route('/')
def hello_world():
    return 'Hello World!'


if __name__ == '__main__':
    app.run()
```

