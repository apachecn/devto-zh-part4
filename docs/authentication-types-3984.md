# 这么多 Auths！

> 原文：<https://dev.to/caffiendkitten/authentication-types-3984>

当您尝试访问需要身份验证才能访问的 web 资源时，web 容器(浏览器)会激活专门为该类型资源配置的身份验证机制。服务器使用这种 HTTP 身份验证来质询客户端请求，客户端使用这种身份验证来提供身份验证信息。
[![](img/98d80a974329d79e1e2d069a96c008bf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HL2cPQo4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.svgrepo.com/show/233986/security.svg)

身份验证的主要类型有:

*   基础
*   基于表单的认证
*   持票人
*   摘要
*   HOBA
*   相互的
*   客户

# HTTP 基本认证

HTTP 基本身份验证(BA)是一种简单的技术，用于实施对 web 资源的访问控制。它使用的模型是，客户端需要通过 HTTP `Authentication`头使用每个受保护空间的用户 id 和密码来验证自己。

**流程**
[![basic process](img/2fd075fa7d5ed0e09ee1c4cffbe8be61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NUvTABDz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/moYwc.png)

1.  客户端(浏览器或其他代理)向服务器请求受保护的信息。

    ```
    GET /securefiles/ HTTP/1.1
    Host: www.somewebsitehere.com 
    ```

2.  服务器拒绝该请求，并以一个表示访问被拒绝的`401`状态代码和一个`WWW-Authenticate`报头进行响应。

    ```
    HTTP/1.1 401 Access Denied
    WWW-Authenticate: Basic realm="My Server"
    Content-Length: 0 
    ```

    注意响应中的`Basic`和`realm`。这些用于指导客户端，并告诉您认证类型是`Basic`，`realm`是试图访问的特定安全区域。

3.  此时，用户可能会收到一个登录对话框，并使用它来发送他们的用户名和密码。[![login box](img/8f25069b69603166b2987a763d292839.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Q1CWvf4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0x5jonr9tg1migls939x.png)

4.  用户凭证现在由浏览器发送，以重试对`Authorization`请求头的请求。

    ```
    GET /securefiles/ HTTP/1.1
    Host: www.somewebsitehere.com
    Authorization: Basic bXl1c2VybmFtZTpteXBhc3N3b3Jkcw== 
    ```

    `Authorization`请求头还将包含`Basic`关键字来指定认证机制，以及用户凭证的 base64 编码，如`<username>:<password>`。

BA 可以消除握手的需要，并且它不需要 cookies、会话标识符或登录页面(因为 BA 使用标准的 HTTP 头字段。)，但并不安全。(3)

BA 使用 base64 编码来生成包含用户凭证的加密字符串，并通过 HTTP 请求发送它。Base64 编码不是加密，很容易解码。此外，通过 HTTP 请求协议向未经身份验证的目标(通常是服务器)发送任何 base64 编码的字符串都是不安全的，因为这种传输容易受到(hu)中间人攻击。(1)

**解决方案？**
一个可能的解决方案是只使用 BA 和其他安全机制，比如 HTTPS/SSL。如果客户端和服务器之间的连接是安全的，则 HTTP 基本身份验证方案可以被认为是安全的。(13)

# 基于表单的认证。

基于表单的身份验证允许开发人员通过定制 HTTP 浏览器呈现给最终用户的登录屏幕和错误页面来控制登录身份验证屏幕的外观。当声明基于表单的身份验证时，用户对话框的内容以纯文本形式发送(类似于基本身份验证)，并且目标服务器没有经过身份验证。这可能会暴露用户名和密码，除非通过 SSL 连接。(6)

[![](img/b012e57d7f8eb3b1bb4cef10184207e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4px9btus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://docs.oracle.com/cd/E19226-01/820-76img/security-formBasedLogin.gif)

1.  客户端请求访问受保护的资源。
2.  如果客户端未经身份验证，服务器会将客户端重定向到登录页面。
3.  客户端将登录表单提交给服务器。
4.  服务器尝试对用户进行身份验证。

# 无记名认证

承载认证是一种基于令牌的系统，用于访问 OAuth 2.0 保护的资源。不记名令牌是一个随机字符串，仅由服务器使用，它可以是一个短的十六进制字符串，也可以是一个更结构化的令牌，如 JSON Web 令牌。(10)

# HTTP 摘要访问认证

摘要式访问身份验证使用 MD5 散列法来确保不以明文形式向服务器发送用户名、密码、HTTP 方法或请求的 URIs。(8) HTTP 摘要访问认证是一种更复杂的认证形式，因为对于所需的每个调用，客户端必须进行 2 次。但是，尽管 Digest 使用加密，它仍然容易受到中间人攻击。

步骤 1:客户端向服务器发送请求
步骤 2:服务器用一个特殊的代码(称为 nonce，即只使用一次的数字)，另一个表示领域的字符串(哈希)进行响应，并要求客户端进行身份验证
步骤 3:客户端用这个 nonce 和用户名、密码和领域的加密版本(哈希)进行响应
步骤 4:如果客户端哈希与自己的用户名、密码和领域哈希匹配，则服务器用所请求的信息进行响应，如果不匹配，则返回错误。(1)

# HTTP 起点认证(HOBA)

HOBA 是一种基于数字签名的 HTTP 身份验证设计，它使用嵌入在 HTML 中的基于 JavaScript 的身份验证，是需要密码的 HTTP 身份验证方案的替代方案。(11)
通过使用数字签名作为认证机制，客户端为其认证的每个主机创建新的公钥-私钥对。在 HOBA 中，HTTP 客户端使用这些密钥通过 HTTP 协议或 Javascript 身份验证程序向服务器进行身份验证。密钥不存储在公钥证书中，而是存储在来自 PKIX [RFC5280]的 subjectPublicKeyInfo 结构中。因为这些通常是“裸密钥”，所以没有 PKI 证书的语义开销，特别是在命名和信任锚方面。因此，对于拥有相应私钥的用户，客户端公钥(“CPK”)没有任何公开可见的标识符，也没有客户端使用 CPK 的网络来源(12)

# 相互认证

相互认证是两个实体相互认证的过程/技术。例如，客户端通过交换证书来验证服务器，反之亦然。通过这种方式，网络用户可以确信他们正在与合法的实体做生意，并且服务器可以确信所有试图获得资源访问权的用户都是合法的。
[![](img/eb2ad07b2c7d188a9214afd4a9ae6141.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--d5bMFMNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://origin-symwisedownload.symantec.com/resources/webguides/managementcenter/1.11.1.1/Content/Resourcimg/5_ControlAccess/diagram.png)

# HTTPS 客户端认证。

使用 HTTPS 的客户端身份验证是一种强大的身份验证机制，因为它要求用户拥有公钥证书(PKC)或双因素身份验证项。安全套接字层(SSL)为 TCP/IP 连接提供数据加密、服务器验证、消息完整性和可选的客户端验证⑤
[![](img/ad85d9b41b4e480fba2587249a07a00e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LRCztRHA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://docs.oracle.com/cd/E19226-01/820-76img/security-sslBMAWithCertificates.gif)

1.  客户端请求访问受保护的资源。
2.  web 服务器将其证书提交给客户端。
3.  客户端验证服务器的证书。
4.  如果成功，客户端将其用户名和密码发送到服务器，服务器验证客户端的凭证。
5.  如果验证成功，服务器授予对客户端所请求的受保护资源的访问权。

# 使用哪种类型？

作为开发人员，您选择的身份验证机制对资源的安全性非常重要。

身份验证类型在很大程度上取决于传输的信息类型。任何包含敏感信息的内容都应使用基于客户端的身份验证、相互身份验证或基于摘要的身份验证，因为这些使用 SSL，大多数都需要某种令牌来进行身份验证，并且在传输凭据之前，它们会通过加密哈希函数进行加密，并与任意数值一起使用以防止重放攻击，因为这些值只使用一次。(8)

#### 参考文献

1.  [http://mark-Kirby . co . uk/2013/如何验证-API-http-basic-vs-http-digest/](http://mark-kirby.co.uk/2013/how-to-authenticate-apis-http-basic-vs-http-digest/)
2.  [https://stack overflow . com/questions/9534602/digest-and-basic-authentic ation 的区别是什么](https://stackoverflow.com/questions/9534602/what-is-the-difference-between-digest-and-basic-authentication)
3.  [https://en.wikipedia.org/wiki/Basic_access_authentication](https://en.wikipedia.org/wiki/Basic_access_authentication)
4.  [https://en.wikipedia.org/wiki/Digest_access_authentication](https://en.wikipedia.org/wiki/Digest_access_authentication)
5.  [http://java.boot.by/wcd-guide/ch05s03.html](http://java.boot.by/wcd-guide/ch05s03.html)
6.  [https://docs . Oracle . com/CD/e 19226-01/820-7627/BNC bq/index . html](https://docs.oracle.com/cd/E19226-01/820-7627/bncbq/index.html)
7.  [https://docs . Oracle . com/CD/e 19226-01/820-7627/BNC bk/index . html](https://docs.oracle.com/cd/E19226-01/820-7627/bncbk/index.html)
8.  [https://www . techopedia . com/definition/13609/digest-authentic ation](https://www.techopedia.com/definition/13609/digest-authentication)
9.  [https://developer . Mozilla . org/en-US/docs/Web/HTTP/authentic ation # Basic _ authentic ation _ scheme](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#Basic_authentication_scheme)
10.  [https://oauth.net/2/bearer-tokens/](https://oauth.net/2/bearer-tokens/)
11.  [https://tools.ietf.org/html/rfc7486](https://tools.ietf.org/html/rfc7486)
12.  [https://tools.ietf.org/id/draft-ietf-httpauth-hoba-00.html](https://tools.ietf.org/id/draft-ietf-httpauth-hoba-00.html)
13.  [https://www . IBM . com/support/knowledge center/SSGMCP _ 5 . 1 . 0/com . IBM . CICS . ts . internet . doc/topics/dfh tl2 a . html](https://www.ibm.com/support/knowledgecenter/SSGMCP_5.1.0/com.ibm.cics.ts.internet.doc/topics/dfhtl2a.html)

###### 请注意，我是学生，还在学习。如果我说的不正确，请告诉我。我很想了解更多我可能不完全了解的东西。