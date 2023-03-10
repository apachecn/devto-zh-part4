# 介绍奥斯古德

> 原文：<https://dev.to/tlhunter/introducing-osgood-4k1m>

今天编写的服务有一个共同的缺陷:特权过多。例如，Node.js 应用程序能够执行子进程、发送网络请求、写入文件系统以及向其他进程发送信号。典型的应用程序需要这些功能的一小部分，随着[恶意模块](https://medium.com/intrinsic/common-node-js-attack-vectors-the-dangers-of-malicious-modules-863ae949e7e8)越来越流行，这些通常不必要的功能被滥用的风险只会增加。

在 [Intrinsic](https://intrinsic.com) ，我们花费了数年时间来构建一个产品，将最小特权的*原则应用于 Node.js。我们的核心产品允许我们的客户提供一个策略列表，描述应用程序能够做什么。这包括可以执行哪些二进制文件，如何与文件系统交互，以及应用程序可以请求哪些 URL。如果一个 I/O 操作没有被列入白名单，那么它就会失败。*

最近，我们问自己:如果一个新的平台是为构建中间件/微服务而设计的，一个遵循最小特权原则的平台，并提供足够的功能来安全地取代最常见的微服务用例，这样的系统会是什么样子？

[Osgood](https://github.com/IntrinsicLabs/osgood) 成为我们搭建这样一个平台的尝试。然而，我们不希望开发人员对这样的工具完全陌生。因此，我们接触了很多人已经熟悉的技术。

该平台使用 Rust 构建，这是一种以其安全性著称的语言，JavaScript 运行在 V8 中，这是一种速度快得离谱的 JavaScript 引擎。

## 它是如何工作的？

Osgood 是一个静态链接的二进制文件，可以为 Linux 和 MacOS 下载。然后可以使用 JavaScript 应用程序的路径调用该二进制文件，该应用程序定义了高级配置、如何将传入请求路由到不同的 Osgood 工作器，以及每个工作器所需的安全策略。

[![Osgood Screencast](img/f6147aaf63d98b0371614873603770d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yoep1qk9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p9raiah7dr5s723gclo7.gif)

该二进制文件可以很容易地在笔记本电脑上运行，以便进行本地开发。一旦应用程序准备就绪，代码以及 Osgood 二进制文件就可以上传到生产服务器。

Osgood 通常适用于需要在服务器上执行逻辑的情况，或者需要对客户端保密的情况，或者唯一需要的出站 I/O 是通过 HTTP 请求的情况。

Osgood 提供了必要的 API 来直观地构建 CRUD 应用程序，这些应用程序由像 [CouchDB](https://couchdb.apache.org/) 或 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 这样的技术支持。出于安全目的以及防止紧密耦合，通常不应该给予客户端(如移动应用程序和 web 浏览器)对数据库的自由访问。使用 Osgood 维护数据库凭证是一种更安全的方法，将数据转换成通用格式有助于避免供应商锁定。

另一个用例是在现有的后端 API 服务之前提供一个 HTTP facade。例如，如果一个移动应用程序想要访问来自两个内部服务的数据，Osgood 可以代表客户端进行这两个调用，并转换结果数据。这也使得 Osgood 作为 GraphQL API 是可行的。

## 指导原则

在设计 Osgood 时，有几个原则帮助指导我们。因为我们是一家安全公司，所以平台的安全非常重要。如果 Osgood 比其他技术慢的话，没人会想用它，所以它需要像 T2 一样快。最后，我们希望将最小特权的*原则*带给更多的程序员。这要求我们将这样的实现变得极其简单。

### Osgood 应用程序是安全的

使用 JavaScript 函数在 Osgood 应用程序文件中定义策略。这些函数使用与 Node.js HTTP 策略的[固有语法相同的语法。](https://intrinsic.com/docs/latest/policy-outbound-http.html)

下面是一个策略文件的例子，它能够与一些选定的 GitHub APIs 以及 CouchDB 数据库进行交互:

```
// app.js

// global configuration
app.interface = '0.0.0.0';
app.port = 3000;

app.get('/user/:username', './worker.js', (policy) => {
  policy.outboundHttp.allowGet('https://api.github.com/users/*/gists');
  policy.outboundHttp.allowGet('https://api.github.com/users/*/repos');

  policy.outboundHttp.allowGet('http://couchdb.local:5984/users/*');
  policy.outboundHttp.allowPut('http://couchdb.local:5984/users/*');
  policy.outboundHttp.allowPost('http://couchdb.local:5984/users');
  policy.outboundHttp.allowDelete('http://couchdb.local:5984/users/*');
}); 
```

许多后端数据库通过基于 HTTP 的 API 公开功能——想想 CouchDB 和 Elasticsearch。许多第三方服务也通过 HTTP 公开它们的 APIs 比如 GitHub 和 Stripe。不用说，这些中间层微服务中的很多*可以通过专门的 HTTP 通信来构建。*

### 奥斯古德速度很快

通过一个简单的`Hello, World!`基准测试，Osgood 每秒能够处理大约 4 万个请求。

```
$ wrk -c 100 -d 60 http://localhost:3000/hello
Running 1m test @ http://localhost:3000/hello
  2 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     4.61ms   13.36ms 219.02ms   97.42%
    Req/Sec    20.36k     3.17k   25.04k    91.00%
  2422992 requests in 1.00m, 265.74MB read
Requests/sec:  40360.32
Transfer/sec:      4.43MB 
```

### 奥斯古德很简单

Osgood 是一个静态链接的二进制文件，大小约为 20MB。执行二进制文件唯一需要的参数是一个 JavaScript 文件。

让我们来看一个 Osgood 应用程序示例。该应用代表微服务中的一个常见任务。应用程序接受请求中的输入值，使用该值发出多个出站 HTTP 请求，以某种方式转换结果数据，并使用组合数据集进行响应。这是 API facade 模式，经常用于减少客户端发出的请求。

```
// worker.js

export default async (_request, context) => {
  const username = context.params.username;

  const [gists_res, repos_res] = await Promise.all([
    fetch(`https://api.github.com/users/${username}/gists`),
    fetch(`https://api.github.com/users/${username}/repos`),
  ]);

  const [gists, repos] = await Promise.all([
    gists_res.json(),
    repos_res.json(),
  ]);

  return { username, gists, repos };
} 
```

一旦你[下载了一个版本](https://github.com/IntrinsicLabs/osgood/releases)，你就可以使用下面的命令运行这个 Osgood 应用程序:

```
$ osgood app.js 
```

* * *

在我们的下一篇文章中，我们将看看[托管一个静态站点，以及与 Osgood](https://dev.to/tlhunter/hosting-a-static-site-and-contact-form-with-osgood-5c1g) 的联系方式。