# cookie 和 session

`session`是加密的`cookie`

## 设置 cookie

使用`response.set_cookie(key, value='', max_age=None, expires=None, path='/', domain=None, secure=False, httponly=False, samesite=None)`方法

| 方法参数 | 说明                                                    |
| -------- | ------------------------------------------------------- |
| key      | 键                                                      |
| value    | 值                                                      |
| max_age  | cookie 被保存的时间，单位为秒；默认会在关闭浏览器时过期 |
| expires  | 具体的过期时间，一个 datetime 对象或UNIX时间戳          |
| path     | 限制 cookie 只在给定的路径可用，默认为整个域名          |
| domain   | 设置 cookie 可用的域名                                  |
| secure   | 如果设为 True ，只有通过 HTTPS 才可用                   |
| httponly | 如果设为 True，禁止客户端 JavaScrip 获取 cookie         |

**示例：**

```python
@app.route('/')
def test():
    response = make_response('hello')
    response.set_cookie('name','hhh')
    return response
```

## 获取 cookie

`request.cookies`是一个字典，里面存有所有`cookie`

## 设置/获取 session

```python
from flask import session
```

要设置`session`，首先要提供一个密钥用来加密

```python
app = Flask(__name__)
app.secret_key = '随机密钥'
```

设置/获取`session`，当作字典用就行

```python
session['is_login'] = True
session['is_login']
```

