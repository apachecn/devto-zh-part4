# 休息 vs 肥皂

> 原文：<https://dev.to/vikashagrawal/rest-vs-soap-47ei>

REST(表述性状态转移)是一种架构风格，而 SOAP(简单对象
访问协议)是一种协议。
·REST 非常简单，虽然
也支持其他格式，例如简单文本，但它提供了更轻量级的数据(JSON)。，HTML，XML 等，而 SOAP 构建在信封内，其中包含 XML 格式的数据。
REST 附带了 SSL 安全和 https，而 SOAP 也附带了 WS-Security。
·SOAP 具有内置的 ACID 合规性，这就是它成为敏感数据(如财务数据)首选的原因
。与 SOAP 相比，REST 更轻，因此需要的带宽更少。
数据只能在 REST 中缓存，不能在 SOAP 中缓存。
SOAP 有严格的通信契约。
REST 使用 URL 和 URI 访问 API，而 SOAP 使用服务接口(WSDL 文件)向客户端应用程序公开其功能
。
如果服务需要从一个请求到另一个请求保持状态，那么 SOAP 是
首选。
“WSDL 文件”是客户端和服务器之间的合同。这个文件中的内容包含了所有关于被公开的服务的信息，包括字段名、字段类型。如果
有任何参数的改变，都会对所有客户端
的执行产生巨大的影响。使用 wsimport，您可以从 wsdl 文件中生成所有的类。Wsdl 文件是一个包含模式的 xml 文件。它可用于创建包括认证细节的数据对象，并通过网络发送。基于 rest
的服务和基于 soap 的服务之间的请求签名是相似的，唯一的区别是:

```
o SOAP Action
o Soapenv
o soapenv: Body 
```

状态代码:

```
o 200: Request got executed successfully.
o 201: resource has been created successfully.
o 202: Accept, means the request has been accepted for execution asynchronously.
o 203: Non Authoritative information, means the content has been delivered from 
third party
o 400: Bad request because of syntax error.
o 401: Authentication is needed.
o 403: Authenticated but don't have enough access for the given request.
o 404: Resource not found 
```