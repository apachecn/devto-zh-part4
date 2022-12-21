# 使用 JSON Web 令牌保护 Node.js 应用程序

> 原文：<https://dev.to/themarcba/secure-your-node-js-application-with-json-web-token-4d4e>

*Cross-post from[code wall](https://www.codewall.co.uk/secure-your-node-js-application-with-json-web-token)*
当您构建一个前端和后端分离的 web 应用程序时，将它放在登录之后的一种方法是使用 **JSONWebToken** 。这是一个在 2010 年初被引入时非常流行的概念。在这篇文章中，你将了解什么是 JSON Web Token (JWT ),它是如何工作的，以及如何将它集成到你的 Node.js 应用程序中。我们开始吧！

## JSON Web Token 概括地说

引用[官方网站](https://jwt.io)，“JSON Web Token 是一种开放的、行业标准的方法，用于在双方之间安全地表示**声明”。这意味着，服务器可以确定由客户端发送的信息(以 JSON 格式)是否没有被修改并且已经由所述服务器有效地发布。**

### 令牌包括什么？

JSON Web 令牌由三部分组成:

*   📃 **Header** :包含额外的信息:它是什么类型的令牌(JWT)以及正在使用哪种签名算法(例如 SHA256)。标头 JSON 正在进行 Base64Url 编码。
*   📦**有效载荷**:包含双方想要共享的信息(或“声明”)。这可能包括您想要的任何内容，但共享敏感数据(如密码)从来都不是一个好主意，因为默认情况下，JWT 令牌可以在没有共享机密的情况下被解码。JWT 没有加密数据的目标。我个人一般用用户 ID，角色，发布日期，失效日期。和头 JSON 一样，有效负载 JSON 也用 Base64Url 编码。
*   🔏**签名**:签名包含编码的报头、编码的有效载荷、秘密(只有您的服务器知道)，并由报头中确定的算法签名。

如果散列算法是 SHA256，签名将如下创建:

```
HMACSHA256(
base64UrlEncode(header)
+ "."
+ base64UrlEncode(payload)
,secret) 
```

Enter fullscreen mode Exit fullscreen mode

最后，所有三个部分被连接起来，用“.”隔开:

```
<Header>.<Payload>.<Signature> 
```

Enter fullscreen mode Exit fullscreen mode

这里举一个 JWT 的例子:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZDM5YzI4MjdhZDgyMjBmMTJiMGZkMWIiLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE1NjUyNzE4NzUsImV4cCI6MTU2NjQ4MTQ3NX0.NmcvfTBmUw1cdEI1cNRHzq6q5W0HmPELoh8rd_7EFAc 
```

Enter fullscreen mode Exit fullscreen mode

花一点时间到 [jwt.io](https://jwt.io) 并将令牌粘贴在那里。您将看到它包含的所有信息(除了签名)

[![](img/b50db99c55922c03ab1e0706f6531e3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--stIrPkHo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0pbphwsfchpx1rby96bp.png)

### JWT 令牌是如何工作的？

需要注意的是，JWT 并不是用来交换加密数据的。它不应该包含敏感信息，如密码。JWT 的关键在于令牌的验证。当您尝试验证一个已被篡改的令牌时(可能用户 ID 已被交换)，该令牌将被拒绝。

为什么？因为内容与签名不再匹配。因此，除了你之外，其他人无法创建有效的令牌，除非他们得到了你用来散列签名的秘密。

万一你的 JWT 秘密因为某种原因被黑了，你需要立即改变它。从那时起，所有已经存在的令牌都将无效。这对于一些登录的用户来说可能有点烦人，但是您可以确保没有人能够为您的应用程序生成有效的令牌。

### 服务器上的 JWT 工作流是什么样的？

[![JWT Workflow Between Client And Server](img/2c0a0200f6b22f0c2812ddf2065be0bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u1gDKgK6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aiwyn74kt7vmcxnotj1m.png)

现在我们对 JWT 有了一点了解，让我们来看一个例子，它是如何与客户机-服务器交换一起工作的。

1.  第一步让客户。这可能是一个 web 前端应用程序、一个移动应用程序等。基本上任何试图与后端应用程序交互的东西(例如 REST API)。It **将他们的登录凭证**发送到服务器，以便进行验证。

2.  当服务器收到登录请求时，它首先**确保用户名/电子邮件和密码与存储在数据库**中的信息相匹配。当凭证正确时，对于服务器来说，这意味着这个用户就是他所说的那个人。

3.  接下来，生成 **JWT 令牌**。在这里，识别用户的重要信息被传递到有效载荷中。包括发行日期和有效期也是一个好主意。因此，会话的有效期永远不会超过您指定的时间。一周似乎是个不错的时间跨度。无论如何，用户应该在每次使用后注销应用程序，对吗？但这只是通过避免僵尸登录用户来增加额外的安全性。

4.  然后，令牌**被返回给客户端**作为对其登录尝试的响应。当他收到一个令牌，这意味着他已经成功登录。令牌应该**存储在客户端本地的某个地方**。对于 web 应用程序，这可以是 localStore 对于移动应用程序，这可以是设备变量中的某个位置。

5.  对于与服务器的所有进一步通信，客户机向每个请求添加一个**认证头**。看起来是这样的:
    认证:来人

6.  当对受保护资源的新的**请求到达服务器时，它做的第一件事是检查认证头是否随请求一起传递。是这种情况吗，它试图**验证令牌是否检查出**。如果它不是一个有效的令牌(它已经被篡改，它已经过期，等等。)，要求应立即拒绝。**

7.  然而，如果**令牌有效**,那么对于服务器来说，可以安全地假设用户仍然是他所说的那个人，并且可以将所请求的资源作为响应返回给客户机。

## JWT 在 Node.js 中的应用

在这篇文章中，我不会详细介绍 web 服务器如何在 Node.js 中工作，但是我会向你展示如何在 JavaScript 服务器环境中使用 JWT。

### 准备

为了和 JWT 一起工作，你可以使用方便的`jsonwebtoken`库。这样安装:

```
npm install jsonwebtoken 
```

Enter fullscreen mode Exit fullscreen mode

### 创建令牌

在代码中确定客户机是否提供了正确的登录凭证的地方(可能就在您检查数据库之后)，您可以创建 JSON Web 令牌:

```
const token = jwt.sign(<Your payload>, <Your JWT secret>, { expiresIn: <Expiration Time> }) 
```

Enter fullscreen mode Exit fullscreen mode

在一个真实的例子中，它可能是这样的:

```
const jwt = require('jsonwebtoken')
const token = jwt.sign({ _id: user._id, admin: true }, process.env.JWT_SECRET, { expiresIn: '1 week' }) 
```

Enter fullscreen mode Exit fullscreen mode

注意两件事:

*   如果你不熟悉`dotenv`，`process.env.JWT_SECRET`是你的 JWT 秘密将被放置的地方。在代码中以明文形式存储令牌从来都不是一个好主意，这就是为什么使用(dotenv)[【https://www.npmjs.com/package/dotenv]之类的工具在一个不会上传到 Git 库的文件中定位令牌是一个好主意。
*   expiredIn 属性可以是字符串形式的人类可读时间指示:-*“4 天”-*“7 小时”-*“2 周”-*“6 个月”-*等等。

### 验证令牌

您的客户端应该这样设置身份验证头:Bearer:。因此你首先需要从字符串
中去掉`"Bearer: "`部分

```
const token = req.header('Authorization').replace('Bearer ', '') 
```

Enter fullscreen mode Exit fullscreen mode

*(req.header('Authorization ')是读取授权头的 Express.js 方式)*

然后，您可以验证所提供的令牌:

```
const jwt = require('jsonwebtoken')
try{
    const payload = jwt.verify(token, process.env.JWT_SECRET) 
    console.log(payload._id)
} catch(error) {
    console.error(error.message)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果令牌有效，您就可以访问 payload 变量中的所有有效负载数据。如果令牌无效，JWT 库将抛出一个可以在 catch 中处理的错误。

## 总结

就是这样！如你所见，使用 JWT 并不复杂。最重要的是——我怎么强调都不为过——JWT 没有加密你的数据，所以不要用它来交换敏感信息。JSON Web Token 是一项优秀的技术，可以验证某人声称拥有的信息是否真实可信。
祝编码愉快！