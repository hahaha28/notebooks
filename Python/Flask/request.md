# request

> [官方文档](https://flask.palletsprojects.com/en/1.1.x/api/#incoming-request-data)

```python
from flask import request
```

## 获取请求头

`request.headers`是所有请求头数据，字典格式

## 获取 Query Param

`request.args`是一个字典，包含了所有`Query Param`

若请求`url`为`http://localhost:8888/test?a=1&b=2`

```python
request.args['a']
request.args['b']
```

这么写，如果`a`或`b`不存在，就会返回错误400

可以判断键值是否存在于`request.args`字典中

```python
if 'a' not in request.args:
    pass # 不存在
```

## 获取 body 数据

`request.data`将请求的 body 数据解析为字符串

## 获取 json 数据

前提是：`Content-Type`为`application/json`，否则返回`None`

`request.json`将请求的body数据解析为json格式

## 获取表单数据

`request.form`是一个字典，包含了所有表单数据

假设提交的表单数据为 `account = test`

```python
request.form['account']
```

这么写，如果`account`不存在，会返回400错误



