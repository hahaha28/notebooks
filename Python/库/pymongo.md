# pymongo

> [参考文章](https://juejin.im/post/5addbd0e518825671f2f62ee)

## 连接Mongodb

```python
import pymongo
client = pymongo.MongoClient(host = 'localhost',port=27017)
```

## 指定数据库和集合

```python
db = client.testdb
collection = db.students
```

## 查询

使用`find_one()`或`find()`方法进行查询

`find_one()`查询得到的是单个结果，返回字典对象

`find()`则返回一个生成器对象

```python
result = collection.find_one({'name':'hhh'})
```

