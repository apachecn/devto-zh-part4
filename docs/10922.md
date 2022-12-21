# 在 NodeJS 中创建 HMAC 授权头

> 原文：<https://dev.to/antonioperez/create-a-hmac-authorization-header-in-nodejs-4ajg>

当尝试对 API 进行认证时，有大量的 HTTP 授权方法可以使用。一个这样的方案是 [**散列消息认证码(HMAC)**](https://tools.ietf.org/html/draft-ietf-oauth-v2-http-mac-00) 认证方案。该方法为服务器提供了一种加密验证请求的方法，即首先对请求本身进行哈希处理，然后根据 HTTP 方法、请求 URI、主机名和请求体哈希创建 MAC。

[HMAC 节点示例要点链接](https://gist.github.com/antonioperez/2c17117eb34bf8d6c2a1be05155699a4)

您尝试验证的服务器将为您提供三个关键数据。

1.  MAC 密钥 id
2.  带有预期哈希算法的 MAC 密钥。如果密钥是 base 64 编码的。在生成 HMAC 时，您必须对其进行解码。
3.  你的证件的签发时间。

一般来说，服务器会使用 MAC key id 在他们的终端查找你的证书。MAC 密钥将用于在向服务器发送请求之前生成 HMAC。服务器将使用这个相同的密钥来计算 mac。如果有不匹配，请求将被拒绝。

生成的 HMAC 标头可能如下所示..

```
Authorization: "MAC id='SERVER-PROVIDED-ID', nonce='6573561:WINTERBOOTS', bodyhash='pWZ7PIoST1E8QP70NAbNfSVfl/U0BFWk596zZuBuh84=', mac='qh4lj5GpmJd5yvxX8BUXU5j6orDUsCYBMNhjIFOA21E='" 
```

让我们将 MAC 报头分解成几个主要部分

*   **id**–服务器提供的 ID
*   **nonce**–从提供的发布时间开始的秒数，与一个随机字符串连接，用“:”字符分隔。
*   **正文哈希**—`=BASE64(HASH(requestText))`的结果
*   **MAC**–`=BASE64(HMAC(macString, secretKey))`

MAC 在创建之前需要被格式化并保存为普通的文本字符串，以便服务器和客户端可以通过完全相同的值来计算请求 MAC。每段数据由一个新的行字符(\n)分隔。让我们逐一查看这 7 项数据。这个顺序必须在字符串中保持。

## MAC 正常化字符串

1.  为请求生成的随机数值。
2.  大写的 HTTP 请求方法。比如:“头”、“得”、“后”等。
3.  HTTP 请求路由 ex) '/users '
4.  HTTP 请求中包含的主机名
5.  HTTP 的端口 ex) 80 或 HTTPS 的端口 ex)443
6.  请求有效负载正文哈希，否则为空字符串。
7.  “ext”“Authorization”请求头字段的值，否则为空字符串。

```
${nonce}\n ${method}\n ${route}\n ${hostname}\n ${PORT}\n ${bodyHash}\n ${ext}\n 
```

太好了。现在我们有了字符串，我们现在可以计算请求的 MAC 部分。结果是 HMAC(string，secretKey)的 base 64 编码结果。请记住，如果密钥是 base64 编码的，您必须解码它。下面是一个节点 js 要点，它利用加密库为您的 API 集成生成散列、HMAC 和 auth 头。

该代码示例为位于 example.com 的/users POST 端点创建了一个 HMAC。结果是一个可以在 Axios 或大多数 HTTP 库中使用的 header 对象。请求被转换成字符串化的 json。

编码快乐！

[HMAC 节点示例要点链接](https://gist.github.com/antonioperez/2c17117eb34bf8d6c2a1be05155699a4)