# response

> [官方文档](https://flask.palletsprojects.com/en/1.1.x/api/#response-objects)

```python
from flask import make_response
```

一般情况下，return 就是设置返回体，而返回头什么的由`flask`自动设置

如果想要自己设置，那就要用`make_response()`方法自己构造`response`对象

**常用属性方法：**

| 属性/方法             | 说明                                       |
| --------------------- | ------------------------------------------ |
| headers               | 返回头                                     |
| mimetype              | 类型，直接设置这个比设置Content-Type更方便 |
| status_code           | 状态码                                     |
| set_cookie(key,value) | 设置cookie                                 |

## 设置返回头

```python
@app.route('/')
def test():
    # 构造response对象，并设置返回体
    response = make_response('hello world')
    # 设置返回头
    response.headers['test'] = 'header test'
    return response
```

## 重定向

有两种方法，但其实一样：

1. ```python
   return redirect('url')
   ```

2. ```python
   response = make_response(redirect('url path'))
   return response
   ```

## 返回 json 数据

默认情况下，return返回的数据的`mimetype`是`text/html`，所以返回`json`需要自己改变返回头。

但是`flask`提供了更方便的`jsonify()`方法

```python
from flask import jsonify

# 不加这一句，中文会乱码
app.config['JSON_AS_ASCII'] = False

@app.route('/')
def test():
    return jsonify({
        'name':'林俊杰',
        'age':18
    })
```

## 错误响应

使用`abort(错误码)`方法返回错误相应

`abort()`之前不需要`return`，且之后的代码不会执行