# Apollo Server(less)、REST APIs、DynamoDB 和缓存

> 原文：<https://dev.to/hotgazpacho/apollo-server-less-rest-apis-and-caching-5jf>

我的团队今天遇到了一个有趣的问题，关于 Apollo 服务器、数据源和缓存层的配置。它源于我们的架构决策，以及看似合理的默认设置如何导致失败。幸运的是，它以不到十几行代码的修复获得了圆满的结局。

让我们从外面开始，从里面开始...

## 最外层:阿波罗服务器

出于各种原因，我很乐意在另一篇文章中详述，我的团队决定在 AWS 上为我们的应用程序采用完全无服务器的架构。这意味着我们所有的代码都部署在 Lambda 函数中。我们喜欢它，但它也有利弊。在撰写本文时，其中最大的问题是与某些其他只能部署在 VPC *(虚拟私有云)*中的 AWS 服务进行交互。服务，比如 RDS *(关系数据库服务)*，以及对本文来说很重要的 elastic cache*(托管 Redis & Memcache)* 。

默认情况下，当您部署 Lambda 函数时，它不会部署到 VPC 中。调用该函数的 API 调用对互联网开放，但受 IAM 授权保护。您可以选择指定一个 VPC 来附加该函数。然而，这里是权衡的地方。首先，您需要配置 VPC，使其具有多个足够大的 IP 子网，以容纳您希望连接到该 VPC 的所有函数的最大并发性*(您的函数的多少个实例可以同时执行)*。其次，您需要配置安全组，允许 Lambda 子网中的函数与其他子网中的资源(如 Redis)进行对话。第三，您需要为您使用的任何不是本地驻留在 VPC 中的服务设置 VPC 端点，比如 S3 和 DynamoDB。最后，如果您访问互联网上的任何 API，您还需要设置一个 NAT 网关。这需要大量的网络设置和大量的错误配置！

即使您正确地设置了 VPC，您很快就会发现冷启动*(第一次运行函数实例)*的时间非常长，在您的代码可以开始执行之前大约需要 10-20 秒。这是因为将 Lambda 附加到 VPC 的机制需要为函数的每个实例设置一个 ENI *(弹性网络接口)*。ENIs 不是为快速安装和拆卸而设计的。仅仅因为这个原因，将一个函数附加到一个 VPC 上对于一个服务于 web 应用的 API 来说是不可能的。

出于这个原因，我们选择放弃任何需要 VPC 的东西，独立部署我们的阿波罗服务器λ。这意味着 Redis 和 Memcache 不可能用于缓存。

## 缓存

当我在这里谈论缓存时，我指的是[数据源缓存](https://www.apollographql.com/docs/apollo-server/features/data-sources/)，特别是它如何与 [RESTDataSource](https://www.apollographql.com/docs/apollo-server/features/data-sources/#rest-data-source) 交互。允许你在解析器函数中创建一个调用 REST APIs 的抽象。它的接口允许 Apollo GraphQL 引擎在进程中插入一个缓存，这样重复的请求将只访问网络一次，重复的请求将使用缓存。默认情况下，这是一个内存中的缓存，但是如果您运行的并发性大于 1，那么您将需要使用一个外部缓存服务，比如 Redis。然而，Redis 需要部署在 VPC 内部，正如前面所讨论的，这对于我们的架构来说是不可行的。

进入[Apollo-server-cache-dynamodb](https://www.npmjs.com/package/apollo-server-cache-dynamodb)，这是我写的一个节点模块，使用 DyanmoDB 作为键值缓存，具有自动密钥过期功能。您对此进行配置，并将其插入到 Apollo 服务器配置中，它会负责将其注入到您的数据源中。所有数据源`GET`请求都将缓存在 DynamoDB 中，使用请求 url 作为键。

## 缓存键

如果想到使用请求 url 作为缓存键时，您的蜘蛛感觉受到了刺激，那么您是对的。事实上，这是我们问题的根源。我们查询 API 的方式导致了一个很长的查询字符串。事实上，它已经超过了分区键的 2048 字节的 DynamoDB 限制[。当`RESTDataSource`类在 DynamoDB 中缓存这些**超长 URL**之一的响应时，它会引发一个错误并导致整个 GraphQL 请求失败。](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html#limits-partition-sort-keys)

对我们来说幸运的是，`RESTDataSource`提供了许多方法来挂钩有趣的请求和响应事件。例如，有 [`willSendRequest`](https://github.com/apollographql/apollo-server/blob/df33704cee56603e580bce025a40bacd64967853/packages/apollo-datasource-rest/src/RESTDataSource.ts#L72) ，它允许您[为每个请求](https://www.apollographql.com/docs/apollo-server/features/data-sources/#intercepting-fetches)设置一个授权头。还有 [`cacheKeyFor`](https://github.com/apollographql/apollo-server/blob/df33704cee56603e580bce025a40bacd64967853/packages/apollo-datasource-rest/src/RESTDataSource.ts#L63-L70) ，它允许您为请求计算自己的缓存键。这是我们需要的钩子，以便生成适合用作 DynamoDB 分区键的缓存键。

## 缓存键计算

我们决定使用散列函数来计算请求 url 的唯一标识符。我们很快意识到，如果我们不小心使用稳定的排序算法对查询字符串参数进行排序，我们将使我们的缓存无效。事实证明，WHAT-WG `URLSearchParams`接口提供了一个 [`sort`方法](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/sort):

> `URLSearchParams.sort()`方法对该对象中包含的所有键/值对进行排序，并返回`undefined`。排序顺序是根据键的 unicode 码位。此方法使用稳定的排序算法(即，具有相同键的键/值对之间的相对顺序将被保留)。

巴赞加！一旦我们有了对查询参数进行稳定排序的方法，为请求生成标识符就非常简单了。最初，我们选择了直接的十六进制摘要，但最终我们选择了 UUID 版本的 T2。

下面是我们的`cacheKeyFor`实现的样子，使用了 [`uuid`包](https://www.npmjs.com/package/uuid) :

```
cacheKeyFor(request){
  const requestUrl = new URL(request.url);
  requestUrl.queryParams.sort();
  return uuidv5(requestUrl.toString(), uuidv5.URL);
} 
```

Enter fullscreen mode Exit fullscreen mode