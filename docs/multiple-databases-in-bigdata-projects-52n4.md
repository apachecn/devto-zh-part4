# 大数据项目中的多个数据库

> 原文：<https://dev.to/oudmane/multiple-databases-in-bigdata-projects-52n4>

我参与了多个 NodeJS 项目，这些项目必须同时连接到多个数据库和软件。每当我开始一个新项目，我首先需要编写配置数据库(MongoDB，ElasticSearch，Redis)客户端的代码...)，确保它连接成功，然后继续做我想做的事情。

## 问题

问题是每个客户端都有自己的方式来配置客户端/连接，还有自己的方式来检查连接是否成功。

*   [`mongodb`](https://www.npmjs.com/package/mongodb) 你用回调(错误，客户端)检查(支持承诺过)。
*   [`elasticsearch`](https://www.npmjs.com/package/elasticsearch) 你初始化客户端，然后你需要调用 client.ping()来知道它是否工作
*   [`redis`](https://www.npmjs.com/package/redis) 你需要收听`connect`，`error`事件

## 一个想法

在开始我想做的事情之前，我需要确保我已连接到所有服务。当我写代码时，我更喜欢使用承诺而不是回调，所以我考虑将配置步骤包装在一个承诺中，当它成功时解析客户端/连接实例，当它失败时拒绝错误，如下例:

```
import mongodb from 'mongodb'
import elasticsearch from 'elasticsearch'
import redis from 'redis'

Promise.all([
  // mongodb
  new Promise(
    (resolve, reject) => {
      mongodb.MongoClient.connect(mongodbURL, function (err, client) {
        if (err)
          reject(err)
        else
          resolve(client.db(dbName))
      })
    }
  ),
  // elasticsearch
  new Promise(
    (resolve, reject) => {
      var client = new elasticsearch.Client({
        host: 'localhost:9200'
      })
      client.ping({
        // ping usually has a 3000ms timeout
        requestTimeout: 1000
      }, function (error) {
        if (error)
          reject(error)
        else
          resolve(client)
      })
    }
  ),
  // redis
  new Promise(
    (resolve, reject) => {
      var client = redis.createClient()
      client.on("error", function (error) {
        reject(error)
      })
      client.on("connect", function (error) {
        resolve(client)
      })
    }
  )
]).then(
  ([mongodbClient, elasticsearchClient, redisClient]) => {
    // here I write what I want to do
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

当我在一个文件中编写脚本时，上面的解决方案对我有效。当我的项目变得复杂时，我喜欢将它重构为多个文件/模块，例如，一个带有`express`的 API 有多个路径，我更喜欢分别编写它们，这样在调试时很容易知道去哪里找。

现在，

## 我如何从其他文件访问客户端？

在`express`的例子中，我们可以使用中间件来包含`req`中的客户端，并在每个路由中轻松地访问它，但这只是一个项目的例子，当我们没有中间件作为选项时该怎么办？

老实说，你可以弄清楚，这取决于你的项目，你想做什么，你打算怎么做，当你调用其他函数时，把客户端作为参数传递，当你初始化对象时，把它们传递给构造函数，你总是需要决定把它们传递到哪里。

我是一个懒惰的开发人员，我想专注于解决方案，我讨厌让客户的包袱变得更加复杂。我想要的东西，将很容易设置，可以在任何地方使用！

以下是我决定要做的:

## 解

我定义了包装数据库/软件客户端时要遵循的接口

```
class DriverInterface {
  // methods

  // configureWithName is to support multiple configurations of the same software
  static configureWithName(name, ...clientOptions) // return Promise<client,error>

  // this just an alias that calls this.configureWithName('default', ...clientOptions)
  static configure(...clientOptions) // return Promise<client,error>

  // get client by name
  static getClient(name) // returns client

  // properties
  static get client() // an alias to this.getClient('default')

  static get clients() // returns all clients Map<string,client>
} 
```

Enter fullscreen mode Exit fullscreen mode

我从`mongodb`开始，在 npm 发布为 [`@oudy/mongodb`](https://www.npmjs.com/package/@oudy/mongodb) 可以这样用

**例题**

```
import MongoDB from '@oudy/mongodb'

MongoDB.configure('test', 'mongodb://localhost:27017').then(
 database => {
   const users = database.collection('users').find()
 }
) 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果你的项目被重构为多个文件/模块，你可以使用`MongoDB.client`访问客户端

**例题**

```
// models/getUsers.js
import MongoDB from '@oudy/mongodb'

export default getUsers(limit = 20, skip = 0) {
  return MongoDB.client
    .collection('users')
    .find()
    .limit(limit)
    .skip(skip)
    .toArray()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 多个数据库

您可以使用 [`@oudy/mongodb`](https://www.npmjs.com/package/@oudy/mongodb) 轻松连接多个数据库

**例题**

```
import MongoDB from '@oudy/mongodb'

Promise.all([
  MongoDB.configureWithName('us', 'myproject', 'mongodb://us_server:27017'),
  MongoDB.configureWithName('eu', 'myproject', 'mongodb://eu_server:27017')
]).then(
 ([US_region, EU_region]) => {
   // get from US
   US_region.collections('files').find().forEach(
     file => {
       // do our changes and insert to v2
       EU_region.collections('files').insertOne(file)
     }
   )
 }
) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想从其他文件访问`us`或`eu`数据库，你可以使用`MongoDB.getClient()`

**例题**

```
// models/files.js
import MongoDB from '@oudy/mongodb'

export default getFiles(region, limit = 20, skip = 0) {
  return MongoDB.getClient(region)
    .collection('files')
    .find()
    .limit(limit)
    .skip(skip)
    .toArray()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 现在，下一步是什么

我已经在其他包[`@oudy/elasticsearch`](https://www.npmjs.com/package/@oudy/elasticsearch)[`@oudy/mysql`](https://www.npmjs.com/package/@oudy/mysql)[`@oudy/amqp`](https://www.npmjs.com/package/@oudy/amqp)[`@oudy/redis`](https://www.npmjs.com/package/@oudy/redis)上实现了同样的接口。我还在努力记录它们。
我们已经以这种方式在多个项目中使用数据库两年了，特别是在 [CRAWLO](https://www.crawlo.com) (领导一个基于大数据的软件，通过监控内部和外部因素来帮助电子商务网站增加销售额)。

我在这里发布了知识库[github.com/OudyWorks/drivers](https://github.com/OudyWorks/drivers)。请检查一下，如果您有建议或发现错误，请考虑投稿。

这只是我构建的一个很酷的项目(我认为它是很酷的:D)，基于此，我还制作了其他用于构建 restful APIs、GraphQL 服务器甚至 web 应用的包。他们已经在这里[github.com/OudyWorks](https://github.com/OudyWorks)公开了(还没有记录)。我计划将它们记录下来，并写更多的文章来解释我为什么要制作它们背后的故事。

我为你可能发现的任何错别字感到抱歉，这是我第一次发表关于我的工作的文章，我非常兴奋地与你分享我一直在做的事情。

如果你对 NodeJS 的 cools 项目感兴趣，请随时留下评论并关注我。