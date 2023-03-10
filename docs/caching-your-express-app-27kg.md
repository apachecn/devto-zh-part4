# 缓存您的快速应用程序

> 原文：<https://dev.to/rubiin/caching-your-express-app-27kg>

# 缓存您的快递应用程序

通过网络获取东西既慢又贵。大型响应需要在客户端和服务器之间进行多次往返，这延迟了响应可用的时间和浏览器处理响应的时间，还会导致访问者的数据开销。因此，缓存和重用以前获取的资源的能力是优化性能的一个关键方面
Redis 是一个开源(BSD 许可的)内存数据结构存储，用作数据库、缓存和消息代理。Redis 总是在服务器的主存中提供和修改数据。影响是系统将快速检索需要的数据。Redis 致力于通过创建一个优秀的内存缓存来减少访问延迟，从而帮助和提高关系数据库或 NoSQL 的加载性能。使用 redis，我们可以使用 SET 和 GET 来存储缓存，此外，Redis 还可以处理复杂类型的数据，如列表、集合、有序数据结构等等。

## 安装

安装 redis 是非常直接的。您可以检查[连杆](https://redis.io/topics/quickstart)进行安装

要将 redis 客户端添加到 nodejs，只需执行

```
npm i redis 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我们将使用这个例子，在这个例子中，我从 express app
中的外部 api 获取一些数据

```
const express = require('express');
const responseTime = require('response-time')

//Load Express Framework
var app = express();

//Create a middleware that adds a X-Response-Time header to responses.
app.use(responseTime());

const axios = require('axios')

async getDogs(req,res) {
  try {
    const dogs = await axios.get('https://dog.ceo/api/breeds/list/all');
    res.send(dogs);
  } catch (error) {
    console.error(error)
    res.send('Something went wrong!!!');
  }
}

app.get('/dogs', getDogs);

app.listen(3000, function() {
  console.log('Your app is running on port 3000 !!!')
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的 express 应用程序，它从狗的 API 中检索数据，无需在端点上放置 Redis。相当简单
然而，不利的一面是，如果我们已经获取了数据，并且如果服务器中的数据没有被修改，那么就没有必要在每次请求时获取数据

这是相同的代码，但这次我们添加了一个带有`Redis`
的缓存层

```
const express = require('express');
const responseTime = require('response-time')
const redis = require('redis');
const client = redis.createClient();

//Load Express Framework

var app = express();

//Create a middleware that adds a X-Response-Time header to responses.

app.use(responseTime());

const axios = require('axios')

async getDogs(req,res) {
  try {

  client.get('dogsdata', (err, result) => {
    if (result) {

     res.send(result);

    } else {
      const dogs = await axios.get('https://dog.ceo/api/breeds/list/all');

    // Set the string-key:dogsdata in our cache. With he contents of the cache
    // Set cache expiration to 1 hour (60 minutes)

    client.setex('dogsdata', 3600, JSON.stringify(dogs));
    }

    });

  } catch (error) {
    console.error(error)
    res.send('Something went wrong!!!');
  }
}

app.get('/dogs', getDogs);

app.listen(3000, function() {
  console.log('Your app is running on port 3000 !!!')
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们首先从带有标签`dogsdata`的缓存中获取数据。如果数据存在于缓存中，我们提供数据，否则我们通过 axios 向 api 发出请求，否则我们通过 axios 发出 api 请求。每次调用 api 时，都会用最新的数据更新缓存。
记住 redis 是一个键值存储，因此你用来设置数据的键必须等于你用来获取数据的键

如果比较这两种实现，您会发现缓存将服务请求的时间减少了将近 99%

在 Github 上关注我:[www.github.com/rubiin](http://www.github.com/rubiin)