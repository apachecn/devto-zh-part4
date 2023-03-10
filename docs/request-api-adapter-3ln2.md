# 请求 Api 适配器

> 原文：<https://dev.to/jetrockets/request-api-adapter-3ln2>

开发客户端应用程序时，经常需要向服务器发送请求。

```
// ...
client({ url: "/users.json", method: "GET" }).then(...) 
```

我们可以让我们的生活变得轻松一点。一个方便的抽象是 apiAdapter

```
// apiAdapter.js
function getUsers() {
  return { url: "/users.json", method: "GET" };
}

const apiAdapter = createAdapter(client, {}, {
  getUsers,  
}) 
```

通过在一个地方定义一个请求，您现在可以简单地调用您想要的适配器方法。

```
import apiAdapter from './apiAdapter'

apiAdapter.getUsers().then(...) 
```

为所有请求指定基本设置，以及处理错误和成功的请求，这也是一个有用的选项。

```
const apiAdapter = createAdapter(
  client,
  { withCredentials: true },
  {
    getUsers,  
  },
  successHandler,
  errorHandler, 
) 
```

[Github](https://github.com/atamurana/request-api-adapter)
T3】活生生的例子