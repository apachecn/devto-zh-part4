# 使用 Python 的 MongoDb

> 原文：<https://dev.to/petercour/mongodb-with-python-550a>

可以配合 Python 使用 MongoDb。那么什么是 MongoDb 呢？

*“MongoDB 是一个免费开源的跨平台面向文档的数据库程序。被归类为* **NoSQL 数据库** *程序的 MongoDB
使用带有模式的类似 JSON 的文档。MongoDB 由
MongoDB Inc"* 开发

[![](img/f76d02707b58054a76d4299ffb44ceb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sevgavLI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse4.mm.bing.net%252Fth%253Fid%253DOIP.8TWOAcQaXSC9aXM_rK_hHQHaFw%2526pid%253DApi%26f%3D1)

### 安装

让我们来看看。第一步是安装。

如果没有安装 MongoDb，首先安装它。你可以在这里找到一份很好的 ubuntu 安装指南。

安装后，验证它是否正在运行:

```
sudo systemctl status mongod
● mongod.service - High-performance, schema-free document-oriented database
   Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
   Active: active (running) since Wed 2019-07-17 14:27:56 CEST; 5s ago
     Docs: https://docs.mongodb.org/manual
 Main PID: 13083 (mongod)
   CGroup: /system.slice/mongod.service
           └─13083 /usr/bin/mongod --quiet --config /etc/mongod.conf 
```

服务器正在运行吗？太好了。

### 积垢

创建一些 Python 代码。将创建一个新的收藏集“我的朋友”。
然后记录将被插入。完成后，它会抓取每一条记录。

```
#!/usr/bin/python3
import pymongo

mongo_client=pymongo.MongoClient('localhost',27017)

db=mongo_client.myDB                                                                                                                                   
my_collection=db['myFriends']

print("="*50)

tom={'name':'Tom','age':38,'sex':'Male','hobbies':['Cooking','Golf','Tennis']}
alice={'name':'Alice','age':39,'sex':'Female','hobbies':['Programming','TV','Motorcycle']}
tom_id=my_collection.insert(tom)
alice_id=my_collection.insert(alice)
print(tom_id)
print(alice_id)

print("="*25)
cursor=my_collection.find()
print(cursor.count())
for item in cursor:
    print(item) 
```

输出:

```
2
{'_id': ObjectId('5d2f15d742a6c25c8dd7e5bd'), 'name': 'Tom', 'age': 38, 'sex': 'Male', 'hobbies': ['Cooking', 'Golf', 'Tennis']}
{'_id': ObjectId('5d2f15d842a6c25c8dd7e5be'), 'name': 'Alice', 'age': 39, 'sex': 'Female', 'hobbies': ['Programming', 'TV', 'Motorcycle']} 
```

那是用来插入和读取记录的。插入是一个 [JSON](https://pythonbasics.org/json/) like 结构。另外两个常见的数据库操作是更新和删除。

[![](img/3299f553e7e7979da26ae0377554185b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uHoFOUfl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fthumbs.gfycat.com%252FIncredibleRaggedAmphiuma-size_restricted.gif%26f%3D1)

要更新和删除:

```
#!/usr/bin/python3
import pymongo

mongo_client=pymongo.MongoClient('localhost',27017)

db=mongo_client.myDB
my_collection=db['myFriends']

print("="*25)
my_collection.update({'name':'Alice'},{'$set':{'hobbies':['Music']}})
for item in my_collection.find():
    print(item)

print("="*25)
my_collection.remove({'name':'Tom'})
for item in my_collection.find():
    print(item) 
```

输出:

```
=========================
{'_id': ObjectId('5d2f15d742a6c25c8dd7e5bd'), 'name': 'Tom', 'age': 38, 'sex': 'Male', 'hobbies': ['Cooking', 'Golf', 'Tennis']}
{'_id': ObjectId('5d2f15d842a6c25c8dd7e5be'), 'name': 'Alice', 'age': 39, 'sex': 'Female', 'hobbies': ['Music']}
=========================
{'_id': ObjectId('5d2f15d842a6c25c8dd7e5be'), 'name': 'Alice', 'age':    39, 'sex': 'Female', 'hobbies': ['Music']} 
```

相关链接:

*   [MongoDb 网站](https://www.mongodb.com/)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)