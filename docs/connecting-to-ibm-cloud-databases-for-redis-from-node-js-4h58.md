# 从 Node.js 连接到 Redis 的 IBM 云数据库

> 原文：<https://dev.to/ibmdeveloper/connecting-to-ibm-cloud-databases-for-redis-from-node-js-4h58>

这篇博文解释了如何从一个 [Node.js](https://nodejs.org/en/) 应用程序连接到一个[IBM Cloud Databases for Redis](https://www.ibm.com/cloud/databases-for-redis)实例。与开源数据库的本地实例相比，IBM Cloud Databases for Redis 实例所需的连接细节存在(小)差异。这是因为所有 IBM 云数据库都使用了[安全 TLS 连接](https://cloud.ibm.com/docs/services/databases-for-redis?topic=databases-for-redis-external-app#driver-tls-and-self-signed-certificate-support)和[自签名证书](https://en.wikipedia.org/wiki/Self-signed_certificate)。

*我一直碰到这个问题(并且忘记如何解决它*🤦‍♂️ *)，所以我在这里记录了这个解决方案，以帮助我自己(和其他可能遇到这个问题的人……*🦸‍♂️

## 连接到 Redis(无 TLS 连接)

大多数 Node.js 应用程序使用`redis` [NPM 库](https://www.npmjs.com/package/redis)与数据库的实例进行交互。这个库有一个返回客户端实例的`createClient` 。Node.js 应用程序将一个连接字符串传递给`createClient`方法。该字符串包含数据库实例的主机名、端口、用户名和口令。

```
const redis = require("redis"),
const url = 'redis://user:secret@localhost:6379/'
const client = redis.createClient(url); 
```

一旦建立了[连接](https://github.com/NodeRedis/node_redis#connection-and-other-events)，客户端就触发一个`connect`事件，或者如果遇到[问题](https://github.com/NodeRedis/node_redis#connection-and-other-events)，则触发一个`error`事件。

## 用于 Redis 服务凭证的 IBM 云数据库

IBM Cloud Databases for Redis 通过[实例管理控制台](https://cloud.ibm.com/docs/services/databases-for-redis?topic=databases-for-redis-connection-strings#the-_service-credentials_-panel)提供服务凭证。服务凭证是带有客户端库、CLI 和其他工具的连接属性的 JSON 对象。Node.js 客户端库的连接字符串在`connection.rediss.composed`字段中可用。

***所以，我只是复制这个字段的值并与`redis.createClient`方法一起使用？没那么快...***

IBM Cloud Databases for Redis 使用 TLS 来保护到 Redis 实例的所有连接。这由使用`rediss://` URL 前缀而不是`redis://`的连接字符串来表示。使用该连接字符串(没有进一步的连接属性)，将导致 Node.js 应用程序抛出以下错误。

```
Error: Redis connection to <id>.databases.appdomain.cloud:port failed - read ECONNRESET
  at TCP.onread (net.js:657:25) errno: 'ECONNRESET', code: 'ECONNRESET', syscall: 'read' 
```

如果`createClient`强制使用 TLS 连接`createClient(url, { tls: {} })`，这个错误将被替换为一个关于自签名证书的不同错误。

```
Error: Redis connection to <id>.databases.appdomain.cloud:port failed failed - self signed certificate in certificate chain
    at TLSSocket.onConnectSecure (_tls_wrap.js:1055:34)
    at TLSSocket.emit (events.js:182:13)
    at TLSSocket._finishInit (_tls_wrap.js:635:8) code: 'SELF_SIGNED_CERT_IN_CHAIN' 
```

嗯，如何解决这个问题？🤔

## 连接到 Redis(带 TLS 连接)

所有到 IBM 云数据库的连接都使用自签名证书通过 TLS 进行保护。签名机构的公共证书在服务凭据中以 Base64 字符串的形式提供。这些证书可以在客户端构造函数中提供，以支持自签名 TLS 连接。

下面是在客户端库中使用这些自签名证书所需的步骤...T3】

*   从服务凭证中提取`connection.rediss.certificate.certificate_base64`值。

[![Redis Service Credentials](img/cd6cc93504fe2794032072462607d9f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6OoWY0-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ecu7eduv9u6f343kkurn.png)

*   解码 Node.js 中的 Base64 字符串以提取 PEM 证书字符串。

```
const ca = Buffer.from(cert_base64, 'base64').toString('utf-8') 
```

*   提供证书文件字符串作为客户端构造函数的`tls`对象中的`ca`属性。

```
const tls = { ca };
const client = redis.createClient(url, { tls }); 
```

*   …放松！😎

*`tls`属性被传递给 Node.js 中的`tls.connect` [方法](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)，用于设置 TLS 连接。该方法支持一个`ca`参数来扩展系统中预装的可信 CA 证书。通过使用此属性提供自签名证书，将不会看到上述错误。*

## 结论

我花了一段时间[研究出](https://compose.com/articles/ssl-connections-arrive-for-redis-on-compose/)如何从 Node.js 应用程序连接到 TLS 保护的 Redis 实例。[在客户端构造器中提供自签名证书](https://stackoverflow.com/questions/10888610/ignore-invalid-self-signed-ssl-certificate-in-node-js-with-https-request/39099130#39099130)比不得不[禁用所有未授权的 TLS 连接](https://stackoverflow.com/a/21961005/1427084)要好得多！

由于我不经常编写新的 Redis 客户端代码，所以我总是忘记正确的构造函数参数。将这个解决方案变成一篇博文将(希望)嵌入我的大脑(或者至少提供一种找到答案的方法，而不是必须浏览旧的项目代码)。这甚至可能对其他人搜索这些错误信息的解决方案有用...