---
layout: post
title:  "mongodb (pymongo) find 检索返回的指针莫名丢失"
date:   2020-08-30 22:11:00 +0800
categories: MongoDB Python
tags:  PyMongo
author: GliderHX
mathjax: true
---
* content
{:toc}









## 问题
最近在数据库里写一段代码时发现

```python
docs = db.web_materials_check.find({"pretty_formula":'MnO2'},{"struct_id":True,"relaxed_structure":True})   # web_materials_check 是数据库中collection的名字
ids = [i["struct_id"] for i in docs]
if len(ids) == 0:
    db.web_materials_check.insert_one(insertdoc) 
    # 插入一个document,只做示意
else:
    for doc in docs:
        print(doc["relaxed_structure"])
    # 对检索返回的 relaxed_structure 做进一步操作                            
```
然而此时却发现并没有返回任何`relaxed_structure` 信息


## 原因
pymongo里的[find](https://api.mongodb.com/python/current/api/pymongo/collection.html#pymongo.collection.Collection.find)返回的是[Cursor](https://api.mongodb.com/python/current/api/pymongo/cursor.html#pymongo.cursor.Cursor)实例，并且只能进行一次迭代操作，所以在刚开始的第一个`for`对其进行迭代操作后，Cursor就已经失效了。官方文档中是这么写的：

*Future iterating performed on this cursor will cause new queries to be sent to the server, even if the resultant data has already been retrieved by this cursor.*

## 解决方案

此时我们有两种解决方案

### 在第二个for之前使用rewind函数：

*Rewind this cursor to its unevaluated state. Reset this cursor if it has been partially or completely evaluated. Any options that are present on the cursor will remain in effect.*

```python
docs = db.web_materials_check.find({"pretty_formula":'MnO2'},{"struct_id":True,"relaxed_structure":True}) 
ids = [i["struct_id"] for i in docs]
if len(ids) == 0:
    db.web_materials_check.insert_one(insertdoc) 
else:
    docs.rewind()  #此时docs将回到初始状态#
    for doc in docs:
        print(doc["relaxed_structure"])      
```
此时将返回正常的`relaxed_structure` 信息


### 使用list方法将Cursor转化储存起来
```python
db = connection.struct
docs = list(db.web_materials_check.find({"pretty_formula":'MnO2'},
{"struct_id":True,"relaxed_structure":True}))
ids = [i["struct_id"] for i in docs]
if len(ids) == 0:
    db.web_materials_check.insert_one(insertdoc)
else:
    for doc in docs:
        print(doc["relaxed_structure"])
```
此时所有的检索返回数据将会存入内存，故也不会存在Cursor丢失的问题了



## 参考
* [cursor – Tools for iterating over MongoDB query results](https://api.mongodb.com/python/current/api/pymongo/cursor.html#pymongo.cursor.Cursor)<br>
* [Reuse Cursor object in MongoDB](https://stackoverflow.com/questions/41053502/reuse-cursor-object-in-mongodb)<br>
* [Python for each loop is not working](https://stackoverflow.com/questions/42117384/python-for-each-loop-is-not-working#)<br>
* [How to convert a pymongo.cursor.Cursor into a dict?](https://stackoverflow.com/questions/28968660/how-to-convert-a-pymongo-cursor-cursor-into-a-dict)<br>