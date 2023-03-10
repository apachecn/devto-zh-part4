# 创建一个简单的缓存系统

> 原文：<https://dev.to/romainlanz/create-a-simple-cache-system-47om>

加速应用程序的最直接的优化方法之一是使用缓存来避免大量的数据计算、数据库查询或 API 调用。

术语“缓存”的意思是“*允许快速访问数据的临时存储空间或内存*”(【dictionary.com】T2)。在另一方面，**认为它是简单的键/值存储**。

有很多不同的缓存系统。其中最著名的是 [Redis](https://redis.io/) 。这是一个非常好的内存数据结构存储，但是对于中小型应用程序来说，这种存储方式有时有些过头了。

请注意，我们将在本文中使用的代码没有任何限制，如果不小心使用，可能会造成服务器内存不足的错误。如果您的应用程序严重依赖缓存，如果可以，您最好直接使用 Redis，而不是创建一个自制的抽象。

我们将学习如何构建我们的缓存系统，而不是依赖于第三方库。

从 ES2015 开始，JavaScript 有了 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 对象，它是类固醇上的`Object`，可以很容易地用于缓存。

## 高速缓存入门

如简介中所述，缓存是一个简单的键/值存储——类似于`Map`。

```
const cache = new Map() 
```

Enter fullscreen mode Exit fullscreen mode

我们的`Map`开始是空的，我们会一次又一次地用数据填充它。

```
// Startup of our application...

// We create the cache and fill the key "mykey"
// with the value returned by veryIntensiveCPUFunction()
const cache = new Map()
cache.set('mykey', veryIntensiveCPUFunction())

// ...

const data = cache.has('mykey')
  ? cache.get('mykey')
  : veryIntensiveCPUFunction() 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将避免对`veryIntensiveCPUFunction()`的调用，因为我们已经在应用程序启动时运行了它，并将返回值存储在缓存中(您可能还想看看[记忆技术](https://en.wikipedia.org/wiki/Memoization))。

## 创造真实的例子

让我们进一步创建一个 Node.js HTTP 服务器:

```
// index.js
const { createServer } = require('http')

createServer((res, req) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' })
  res.end('Hello World')
}).listen(8080)

console.log('Listening on port 8080') 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用`node index.js`运行文件时，你会看到`Listening on port 8080`，但是代码永远不会退出。

**节点将保持运行**，并将等待端口 8080 上的任何请求。这意味着**我们所做的一切都将被保存在记忆中**！

让我们添加一些代码来减慢我们的服务器。

```
// index.js
const { createServer } = require('http')
const { sleep } = require('sleep') // https://www.npmjs.com/package/sleep
const cache = new Map()

createServer((req, res) => {
  if (!cache.has('alreadyRan')) {
    sleep(1)
    cache.set('alreadyRan', true)
  }

  res.writeHead(200, { 'Content-Type': 'text/plain' })
  res.end('Hello World')
}).listen(8080)

console.log('Listening on port 8080') 
```

Enter fullscreen mode Exit fullscreen mode

打开浏览器，点击`localhost:8080`。该请求大约需要 1 秒钟来显示`Hello World`。然后，如果你刷新页面，它应该是即时的，因为我们再也不会到达`sleep`语句。

当我们分解这段代码时，会发生以下情况:

1.  我们创建我们的缓存(`cache`)；
2.  我们创建一个监听端口 8080 的节点服务器；
3.  当我们点击我们的服务器时，我们检查`alreadyRan`是否在缓存中；
    *   如果不在缓存中；等待 1 秒钟；并将`alreadyRan`设置为真；
    *   如果在缓存中:继续。

## 移动到一个 Adonis 应用程序

既然我们已经在 Node.js 中看到了内存缓存系统的基础，我们将优化 Adonis 应用程序。

我们将使用[阿多尼斯博客演示](https://github.com/adonisjs/adonis-blog-demo) :

```
> npx degit https://github.com/adonisjs/adonis-blog-demo adonis-blog-demo
> cd adonis-blog-demo
> cp .env.example .env
> npm i
> adonis migration:run
> adonis bundle
> adonis serve --dev 
```

Enter fullscreen mode Exit fullscreen mode

让我们也添加`sleep`包来减慢我们的应用程序。

```
> npm i sleep 
```

Enter fullscreen mode Exit fullscreen mode

首先创建文件`app/Cache.js`，并写入以下内容:

```
// app/Cache.js
module.exports = new Map() 
```

Enter fullscreen mode Exit fullscreen mode

然后，打开`PostController`，要求`sleep`和我们的`cache` :

```
'use strict'
// ...
const { sleep } = require('sleep')
const Cache = use('App/Cache')
// ... 
```

Enter fullscreen mode Exit fullscreen mode

我们将缓存我们的帖子:

```
async index ({ view }) {
  if (!Cache.has('posts')) {
    const posts = await Post.all()
    sleep(3) // Faking long running queries
    Cache.set('posts', posts.toJSON())
  }

  return view.render('posts.index', { posts: Cache.get('posts') })
} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们所做的与示例中完全相同。

1.  检查关键字`posts`是否被填充在缓存中；
2.  如果没有，则获取帖子并填充缓存；
3.  发回缓存的帖子。

第一次到达`/`时，您的请求将需要大约 3 秒钟运行。所有下一个请求都不会慢，因为我们正在使用缓存。

我们加快了博客的速度，但是**我们也增加了一个不受欢迎的行为**。由于我们在存储帖子时不会清除缓存，任何新帖子都不会显示在我们的网站上。

您可以通过在每次写新帖时清除缓存来解决这个问题(您还需要用其他方法清除缓存，如`update`或`destroy`)。

```
// PostController.js
async store ({ session, request, response }) {
  // ...

  await Post.create(data)
  Cache.delete('posts')

  return response.redirect('/')
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用时间戳自动清除缓存

在上一个例子中，我们决定了何时应该清除缓存。我们还可以使用时间戳和缓存的预期生命周期来实现自动化。

我们在[洛桑体育电子竞技 WS](https://github.com/Lausanne-eSports/api.els.team) 中使用了这种技术，以避免对 [Twitch API](https://dev.twitch.tv) 提出太多质疑。

假设我们需要来自第三方 API 的数据，我们被限制在每小时 60 次查询。这意味着我们需要在每次调用之间将数据在缓存中保留至少一分钟。

```
const got = require('got') // https://www.npmjs.com/package/got
const Cache = use('App/Cache')

// ...

if (!Cache.has('example.users')) {
  const response = await got('https://api.example.com/users')

  Cache.set('example.users', [response.body, Date.now()])
} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们添加了一个数组作为缓存的值。它包含响应体和缓存填满时的时间戳。

当我们读取缓存时，我们还会检查缓存的生存期是否超过一分钟。

```
// requires...

if (Cache.has('example.users')) {
  const [users, timestamp] = Cache.get('example.users')

  if ((Date.now() - timestamp) / 1000 <= 60) {
    // Cache is still valid
    return users
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在第 6 行，我们检查数据缓存的时间是否少于 60 秒，如果是这样，我们可以返回缓存的数据。

## 更进一步

为了让我们的生活更容易，我们可以将我们的缓存封装到一个对象中，这个对象将为我们实现自动化。

让我们从创建一个包装我们的缓存开始。

```
// app/Cache.js
const cache = new Map()

module.exports = {
  has(key) {
    return cache.has(key)
  },

  set(key, value) {
    return cache.set(key, [value, Date.now()])
  },

  get(key) {
    return cache.get(key)[0]
  },

  delete(key) {
    return cache.delete(key)
  },

  clear() {
    return cache.clear()
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，缓存会自动将时间戳添加到任何值集中。我们需要做的最后一件事是创建另一个名为`isExpired`的助手。

```
// app/Cache.js
module.exports = {
  // ...
  isExpired(key, seconds) {
    const [_, timestamp] = cache.get(key)

    return (Date.now() - timestamp) / 1000 > seconds
  },
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这段代码，我们现在可以用下面的代码更新我们的例子:

```
const got = require('got') // https://www.npmjs.com/package/got
const Cache = use('App/Cache')

// ...

if (!Cache.has('example.users') || Cache.isExpired('example.users', 60)) {
  const response = await got('https://api.example.com/users')

  Cache.set('example.users', response.body)
}

return Cache.get('example.users') 
```

Enter fullscreen mode Exit fullscreen mode