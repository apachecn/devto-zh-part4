# 分布式平台 API 的 JSON-RPC 与 REST

> 原文：<https://dev.to/radixdlt/json-rpc-vs-rest-for-distributed-platform-apis-3n0m>

# 什么是休息？

关于休息，你可能已经读过一千遍了，但是我们再来一遍:

让我们先说 REST 代表表述性状态转移，本质上是指一种控制客户机和服务器行为的 web 架构风格。

遵循 REST 原则的 API 不需要客户了解 API 的任何结构。相反，服务器需要提供客户机与服务交互所需的任何信息。HTML 格式的表单就是一个明显的例子:服务器指定资源的位置和必需的字段。浏览器事先不知道向哪里提交信息，也不知道提交什么信息。这两种形式的信息完全由服务器提供[1]。

REST APIs 无处不在，分布式账本技术也不例外。这是一个使用[比特币 Rest API](https://blockchain.info/api) :
获取添加到比特币区块链的最新块的哈希的通用示例

```
// Request
curl -X GET https://blockchain.info/q/latesthash
// Result
0000000000000000002d9c2483cd87685ef41e7b8d1ca718206120e9b1bfee43 
```

# 那么，JSON-RPC 呢？

RPC 代表远程过程调用，是一种协议，一个程序可以使用它向位于网络上另一台计算机中的程序请求服务，而不必了解网络的细节。RPC 使用客户机-服务器模型。请求程序是客户机，服务提供程序是服务器。与常规或本地过程调用一样，RPC 是一种同步操作，要求请求程序暂停，直到远程过程的结果返回。然而，使用共享相同地址空间的轻量级进程或线程允许多个 RPC 并发执行[2][3]。

RPC 的概念已经在软件开发领域存在了半个多世纪(是的，时间过得很快)，但是尽管如此，它仍然没有像预期的那样广为人知。

话虽如此，考虑到当前大多数软件都是基于网络的，而 REST 实际上是按照网络优先的思想设计的，这实际上并不令人惊讶。当 REST 出现时，RPC APIs 仍然使用 XML，这是有问题的。在 XML 中，许多属性只是字符串，有必要通过添加描述诸如哪些字段属于哪种类型的元数据层来确保数据类型。

如今，许多已知的去中心化平台使用基于 JSON-RPC 的 API:[以太坊](https://github.com/ethereum/wiki/wiki/JSON-RPC)、[涟漪](https://ripple.com/build/rippled-apis/#websocket-and-json-rpc-apis)和[比特币](https://en.bitcoin.it/wiki/API_reference_(JSON-RPC))等等。

这里我们可以看到一个如何使用以太坊的 JSON-RPC API 获取给定账户余额的例子:

```
// Request
curl -X POST —data ‘{
  “jsonrpc”: “2.0”,
  “method”: “eth_getBalance”,
  “params”: [ "0x407d73d8a49eeb85d32cf465507dd71d507100c1" , "latest" ],
  "id": 1
}’
// Response
{
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58" // 158972490234375000,
  "id": 1
} 
```

# 但是为什么 JSON-RPC 用于分布式账本技术 API 呢？[5][6]

# 对协议不可知

这一点怎么强调都不为过。如果我只能选择一个原因来解释为什么我会选择 JSON-RPC 而不是 REST 的话，那就是使用 JSON-RPC，我们可以拥有现成的 API，客户端可以使用任何协议与这些 API 进行通信。这有额外的好处，允许我们使用纯 TCP(或 WebSockets)提高 API 的性能，并消除 HTTP 开销。

# 休息动词有限制

为了使用 REST 执行操作，有必要定义一个 HTTP 方法，比如 GET(默认)、PUT、POST、PATH 或 DELETE [4]。但是正如这个方法的名字所暗示的，REST 是面向 CRUD 操作的。那么如果这不能满足我们的需求会怎么样呢？例如，如果我们必须在保存实体之前对其进行验证。尽管在 REST 中总有解决这类问题的变通方法，但事实是，如果我们知道有更好的替代方法:JSON-RPC，我们就不想走这条路。

使用 REST 方法，我们将得到类似这样的结果:

```
// Request
curl -X POST https://localhost:8080/api/entity/validate —data ‘{…}’ 
```

但是这看起来完全违背直觉，因为我们将一个动作公开为一个错误的资源。另一方面，如果我们使用 JSON-RPC，结果将更加不言自明:

```
// Request
curl -X POST --data '{
  "jsonrpc": "2.0",
  "method": “validateEntity”,
  "params": [ {…} ],
  "id": 1
}’ https://localhost:8080/api 
```

我们有一个明显的赢家。

# 传递参数比较复杂

使用 REST 传递参数有四种方式:HTTP 头、HTTP 体、URL 路径和 URL 查询。

# 网址查询:

```
// Request
curl -X PUT https://localhost:8080/api/users?name=Piers 
```

# URL 路径:

```
// Request
curl -X PUT https://localhost:8080/api/users/Stephen 
```

# HTTP 正文:

```
// Request
curl -X PUT https://localhost:8080/api/users { “name”: “Josh” } 
```

这可能有点让人不知所措，有时我们不确定使用哪一个；但是 JSON-RPC 可以帮助我们解决这个问题，因为它提供了一种更简单的方法:

```
// Request
curl -X POST —data ‘[{
  "jsonrpc": "2.0",
  “method”: “addUser”,
  “params”: { “name”: “Angad” },
  “id”: 1
}]’ https://localhost:8080/api 
```

# 批量操作

在 REST 中，没有一种简单又好的方式来进行批量请求，但是使用 JSON-RPC，这是小菜一碟:

```
// Request
curl -X POST—data ‘[{
  "jsonrpc": "2.0",
  “method”: “addUser”,
  “params”: { “name”: “Zalan” },
  “id”: 1
}, {
  "jsonrpc": "2.0",
  “method”: “addUser”,
  “params”: { “name”: “Dan” },
  “id”: 1
}, {
  "jsonrpc": "2.0",
  “method”: “addUser”,
  “params”: { “name”: “Edgars” }
}]’ https://localhost:8080/api 
```

# JSON-RPC 的其他好处

除了以上所述，RPC 还有其他一些优于 REST 的优点:

-只有一种发送请求的方法-通常是 HTTP 的 POST 或 WebSockets 的 send。
-请求的内容完全分离给传输机制。所有错误、警告和数据都在请求的有效负载中。
-仅使用一个响应代码来确认消息的正确接收-通常 HTTP 为 200 OK。
——可以很容易地在不同的传输通道之间移植或共享，比如 HTTP/S、WebSockets、XMPP、telnet、SFTP、SCP 或 SSH。

JSON-pure API 中消息和传输通道的分离使得 API 通常更快，而且几乎总是更可靠、更易于使用、更易于移植和调试[7]。

# 总结

关于在 REST 上使用 JSON-RPC 的争论很有趣，通常的回答是:“这要看情况”。它们都是使用客户机-服务器模型远程提供内容的架构风格。也没有理由不同时使用 RPC 和 REST，但是您应该有一个这样做的好理由。

在 Radix，我们认为，如果您必须提供 web 内容，那么使用 REST API 方法可能会更方便，因为它是为在 HTTP 上工作而构建的，并且有许多可用的框架和库可以让您在几个小时内建立一个最小的可行 API。

但事实是，如果您构建的 API 建模起来更复杂(HTTP 动词是不够的)，它需要在其他传输通道上工作和/或保持连接打开(WebSockets)，或者性能很关键，那么最好使用 JSON-RPC。在分布式账本技术领域，性能是一个基本因素，在 Radix 中更是如此，因为我们已经需要在测试网络上每秒处理数千个事务。

[1][https://stack overflow . com/questions/671118/到底什么是 restful 编程](https://stackoverflow.com/questions/671118/what-exactly-is-restful-programming)

[2][https://en.wikipedia.org/wiki/Remote_procedure_call](https://en.wikipedia.org/wiki/Remote_procedure_call)

[3][https://search micro services . tech target . com/definition/Remote-Procedure-Call-RPC](https://searchmicroservices.techtarget.com/definition/Remote-Procedure-Call-RPC)

[4][https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)

[5][http://wookieb . pl/why-you-should-consider-RPC-for-internal-API](http://wookieb.pl/why-you-should-consider-rpc-for-internal-api)

[6][https://www . smashingmagazine . com/2016/09/understanding-rest-and-RPC-for-http-APIs/](https://www.smashingmagazine.com/2016/09/understanding-rest-and-rpc-for-http-apis/)

[7][https://mmikowski.github.io/the_lie/](https://mmikowski.github.io/the_lie/)

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询