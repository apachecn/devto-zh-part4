# 奥斯古德和库什布

> 原文：<https://dev.to/tlhunter/osgood-and-couchdb-125k>

我们最近宣布了一个新的开源项目， [Osgood](https://github.com/IntrinsicLabs/osgood) ，它旨在成为一个在服务器上运行 JavaScript 的安全平台。这个平台将最小特权的*原则应用于应用程序代码。我们加强这一点的方法之一是限制应用程序可以执行的操作类型。例如，不能建立任意网络连接，不能执行子进程。*

由于有了`fetch()` API，出站 HTTP 请求是一等公民。这意味着 **CouchDB** ，一个带有 HTTP API 的 NoSQL 数据库，是用 Osgood 执行应用程序持久性的完美匹配。

Osgood 的最大优势之一是能够在每个路由的基础上指定策略。这允许非常精细的安全执行，允许每个 Osgood 工作人员只执行预先批准的操作。

## 样本积垢应用

考虑一个简单的 CRUD 应用程序。这个应用程序代表了一个更大的组织内的微服务。该服务本质上是其他服务前面的一个门面。它对提供的数据进行验证，比如强制用户名长度。它限制了数据库交互，比如防止任意破坏性查询的运行。该应用程序还通过将数据转换为理想的格式，将应用程序代码与数据库实现分离。它还处理数据库身份验证，将凭证保存在可信的内部服务上，而不在客户端中。

该微服务将有五个端点:

*   列出用户(`GET /users`)
*   创建用户(`POST /users`)
*   获取用户(`GET /users/{user_id}`)
*   删除用户(`DELETE /users/{user_id}`)
*   更新用户(`PUT /users/{user_id}`)

> 注意:如果你想了解这个例子，这个项目的代码是开源的，可以在我们的资源库中找到:[IntrinsicLabs/osgood/examples/couch db-rest](https://github.com/IntrinsicLabs/osgood/tree/master/examples/couchdb-rest)

## 应用配置:`app.js`

Osgood 应用程序是使用 JavaScript 配置的。有一个名为`app`的全局对象可用于设置属性。第一个是`interface`，是我们希望应用程序绑定到的接口的名称。第二个是`port`，是我们想要监听的端口。

在`app`对象上也有一些方法可以根据 HTTP 方法和路径模式来执行传入 HTTP 请求的路由。例如，要将传入的`GET`请求路由到`/users`端点，可以调用`app.get('/users', ...)`。路由函数的第二个参数是 Osgood Worker 文件的路径。第三个参数是用于配置路由策略的函数。

在策略配置功能中，我们指定可以请求哪些 URL。这些可以通过调用如下方法来配置:`policy.outboundHttp.allowMETHOD(urlPattern)`。`urlPattern`使用了`glob`语法。

> 注意:这个 API 在我们的 [API 文档](https://github.com/IntrinsicLabs/osgood/wiki/Osgood-Application-File#route-pattern)中有更详细的描述。

这是我们的 CouchDB 应用程序的 Osgood 应用程序文件的样子:

```
app.interface = '0.0.0.0';
app.port = 8000;

app.get('/users', 'list.js', policy => {
  policy.outboundHttp.allowGet('http://localhost:5984/users/_all_docs');
});

app.get('/users/:user_id', 'view.js', policy => {
  policy.outboundHttp.allowGet('http://localhost:5984/users/*');
});

app.delete('/users/:user_id', 'delete.js', policy => {
  policy.outboundHttp.allowGet('http://localhost:5984/users/*');
  policy.outboundHttp.allowDelete('http://localhost:5984/users/*');
});

app.post('/users', 'create.js', policy => {
  policy.outboundHttp.allowPost('http://localhost:5984/users');
});

app.put('/users/:user_id', 'update.js', policy => {
  policy.outboundHttp.allowPut('http://localhost:5984/users/*');
}); 
```

我们现在已经描述了所有的功能，并在一个文件中完全配置了我们的应用程序。使用这种配置，我们的应用程序*将不能*向`http://evil.co`发送 HTTP 请求，也不能`GET /users`路由对 CouchDB 中的`users`集合执行`DELETE`操作。

预先描述功能是有益的，原因有二。简单的原因是它是安全的。一个副作用是应用程序代码现在更容易审计。想象一下，如果你的所有其他应用程序都有这个 I/O 列表，那些乏味的 GDPR 审计会有多快。

## 创建用户工作者:`create.js`

我们的应用程序可以执行五个操作。在这篇文章中，我们将只关注其中的一个:用户的创建(如果你想看其他的例子，请看 GitHub 上的[示例应用程序](https://github.com/IntrinsicLabs/osgood/tree/master/examples/couchdb-rest))。

这个路由将接受一个传入的 POST 请求，将主体转换成 JSON，执行一些最简单的验证，然后将数据传递给 CouchDB(以及 auth 凭证)。然后，它会根据操作是否成功将信息传递给客户端。

```
const AUTH = `Basic ${btoa('osgood_admin:hunter12')}`;

export default async (request) => {
  try {
    var user = await request.json();
  } catch (e) {
    return json({"error": "CANNOT_PARSE_REQUEST"}, 400);
  }

  if (user.id || user._id) {
    return json({"error": "CANNOT_OVERRIDE_ID"}, 400);
  }

  if (!user.username || typeof user.username !== 'string'
      || user.username.length < 3 || user.username.length > 20) {
    return json({"error": "USERNAME_INVALID"}, 400);
  }

  const payload = await fetch(`http://localhost:5984/users`, {
    method: 'POST',
    headers: {
      Authorization: AUTH,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(user)
  });

  const obj = await payload.json();

  if (obj.error) {
    return json({"error": "UNABLE_TO_INSERT"}, 500);
  }

  return json({ok: true});
}

function json(obj, status = 200) {
  const headers = new Headers({
    'Content-Type': 'application/json'
  });

  const body = JSON.stringify(obj);

  const response = new Response(body, { headers, status });

  return response;
} 
```

如果您曾经使用过服务工作者、Lambda 函数或 Express.js 控制器，那么这些代码可能看起来很熟悉。该文件导出一个接受`request`和`context`参数的默认函数。`request`参数是现代浏览器中可用的[请求](https://developer.mozilla.org/en-US/docs/Web/API/Request)对象的一个实例。`context`参数有一些我们在这个例子中不需要的额外细节。函数本身可以是一个`async`函数或者返回一个承诺。如果承诺被拒绝，Osgood 将用一个`500`错误响应客户。如果它解析了一个`string`或一个简单的对象，那么 Osgood 将回复一个`200`和一个适当的内容类型。但是，对于细粒度的控制，可以返回一个[响应](https://developer.mozilla.org/en-US/docs/Web/API/Response)对象，允许手动设置 HTTP 状态代码和其他头。

## 运行奥斯古德

要运行 Osgood，首先[为你的平台下载一个版本](https://github.com/IntrinsicLabs/osgood/releases)。一旦完成，将`osgood`二进制文件提取到某个地方，最好是在您的`$PATH`中。

然后，下载这个项目的六个文件(`app.js`、`list.js`、`create.js`、`delete.js`、`update.js`、`view.js`)。最后，运行这个命令:

```
$ osgood app.js 
```

这将启动 Osgood 应用程序，并将请求路由到五个 Osgood Workers。当然，如果没有 CouchDB 实例与之对话，该服务就不会太有用。以下命令将在 Docker 容器中运行 couch db:

```
$ docker run \
  -e COUCHDB_USER=osgood_admin \
  -e COUCHDB_PASSWORD=hunter12 \
  -p 5984:5984 \
  --name osgood-couch \
  -d couchdb
$ curl \
  -X PUT \
  http://localhost:5984/users 
```

之后，我们准备好与应用程序进行交互。下一个命令将向 Osgood 应用程序发送 POST 请求，并创建我们的第一个用户:

```
$ curl \
  -X POST \
  http://localhost:8000/users \
  -d '{"username": "osgood"}' \
  -H "Content-Type: application/json" 
```

## 更多信息

Osgood 是开源的。它是用 Rust 编写的，使用快速的 V8 引擎运行 JavaScript。

源代码托管在 GitHub 上，可从 [IntrinsicLabs/osgood](https://github.com/IntrinsicLabs/osgood) 获得。欢迎拉请求！