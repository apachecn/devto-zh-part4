# JSON Web Token (JWT)如何保护您的 API

> 原文：<https://dev.to/anthonygore/how-json-web-token-jwt-secures-your-api-3g18>

您可能听说过 JSON Web Token (JWT)是当前保护 API 的最先进技术。

像大多数安全主题一样，如果您打算使用它，理解它是如何工作的(至少在某种程度上)是很重要的。问题是，对 JWT 的大多数解释都是技术性的，令人头痛。

让我们看看我是否能解释 JWT 如何在不引起你注意的情况下保护你的 API。

## API 认证

**某些 API 资源需要受限访问**。例如，我们不希望一个用户能够更改另一个用户的密码。

这就是为什么我们保护某些资源，让用户在允许访问之前提供他们的 ID 和密码-换句话说，我们对他们进行身份验证。

保护 HTTP API 的困难在于请求是无状态的——API 无法知道任何两个请求是否来自同一个用户。

那么，为什么我们不要求用户在每次调用 API 时提供他们的 ID 和密码呢？因为那会是一个糟糕的用户体验。

## JSON Web 令牌

我们需要的是一种方法，允许用户只提供一次他们的凭证，然后在随后的请求中被服务器以另一种方式识别。

为此已经设计了几个系统，当前最先进的标准是 JSON Web Token。

有一篇关于主题的[文章很好地类比了 JSON web 令牌的工作方式:](https://robmclarty.com/blog/what-is-a-json-web-token)

假设您要入住一家酒店，而不是一个 API。“令牌”是你得到的塑料酒店安全卡，允许你进入你的房间和酒店设施，但不能进入其他任何人的房间。

当你结帐离开旅馆时，你把卡还给我。这类似于注销。

## 令牌结构

通常，JSON web 令牌是通过 HTTP 请求的头部发送的。下面是其中一个的样子:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U 
```

Enter fullscreen mode Exit fullscreen mode

其实令牌就是“Authorization: Bearer”之后的部分，也就是 HTTP 头信息而已。

在你断定这是无法理解的胡言乱语之前，有几件事你很容易注意到。

首先，令牌由三个不同的字符串组成，用句点分隔。这三个字符串是 *base 64 编码的*，并且对应于*报头*、*有效载荷*和*签名*。

```
// Header
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9 
```

Enter fullscreen mode Exit fullscreen mode

```
// Payload
eyJzdWIiOiIxMjM0NTY3ODkwIn0 
```

Enter fullscreen mode Exit fullscreen mode

```
// Signature
dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:base 64 是一种转换字符串的方法，以确保字符串在跨 web 传输的过程中不会出错。这不是一种加密，任何人都可以轻易破解它来查看原始数据。

我们可以解码这些字符串来更好地理解 JWT 的结构。

### 表头

下面是从令牌解码的头。报头是关于令牌的元信息。它没有告诉我们太多有助于建立我们基本的理解，所以我们不会进入关于它的任何细节。

```
{
  "alg": "HS256",
  "typ": "JWT"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 有效载荷

有效载荷更令人感兴趣。有效负载可以包含您喜欢的任何数据，但是如果您的令牌的目的是 API 访问认证，您可以只包含一个用户 ID。

```
{
  "userId": "1234567890"
} 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是*有效载荷并不安全*。任何人都可以解码令牌，并准确地看到有效载荷中的内容。出于这个原因，我们通常包括一个 ID，而不是敏感的识别信息，如用户的电子邮件。

尽管这个有效负载是在 API 上识别用户所需要的全部，但它并不提供身份验证的方法。有人可以很容易地找到你的用户 ID，并伪造一个令牌，如果这是所有包括在内。

这就把我们带到了签名，它是认证令牌的关键部分。

## 哈希算法

在我们解释签名如何工作之前，我们需要定义什么是散列算法。

首先，它是一个将字符串转换成新字符串的函数，称为 *hash* 。例如，假设我们想要散列字符串“Hello，world”。下面是我们使用 SHA256 哈希算法得到的输出:

```
4ae7c3b6ac0beff671efa8cf57386151c06e58ca53a78d83f36107316cec125f 
```

Enter fullscreen mode Exit fullscreen mode

哈希最重要的属性是*你不能通过查看哈希*来使用哈希算法识别原始字符串。

> 有许多不同类型的哈希算法，但 SHA256 通常用于 JWT。

换句话说，我们*不可能*取上面的 hash，直接算出原来的字符串是“Hello，world”。哈希非常复杂，猜测原始字符串是不可行的。

## JWT 签名

回到 JWT 结构，现在让我们看看令牌的第三部分，签名。这个其实需要计算:

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  "secret string"
); 
```

Enter fullscreen mode Exit fullscreen mode

下面是对这里发生的事情的解释:

首先，`HMACSHA256`是一个散列函数的名称，它有两个参数；要散列的字符串和“秘密”(定义如下)。

其次，我们哈希的字符串是 base 64 编码的头，加上 base 64 编码的有效负载。

第三，秘密是一段任意的数据，只有服务器知道。

**问:为什么要在签名哈希中包含报头和有效载荷？**

这确保了签名对于这个特定的令牌是唯一的。

**问:有什么秘诀？**

为了回答这个问题，让我们想想你会如何伪造一个令牌。

我们之前说过，不能通过查看输出来确定散列的输入。然而，由于我们知道签名包括头和有效载荷，因为它们是公共信息，如果您知道散列算法(提示:它通常在头中指定)，您可以生成相同的散列。

但是这个只有服务器知道的秘密是*而不是*公开信息。将它包含在哈希中可以防止有人生成自己的哈希来伪造令牌。由于散列掩盖了用来创建它的信息，也没有人能从散列中找出秘密。

> 将私有数据添加到哈希中的过程被称为 *salting* ，这使得破解令牌几乎不可能。

## 认证过程

现在，您对如何创建令牌有了很好的了解。你如何用它来认证你的 API？

### 登录

用户登录时会生成一个令牌，并与用户模型一起存储在数据库中。

*loginController.js*

```
if (passwordCorrect) {
  user.token = generateToken(user.id);
  user.save();
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，在对登录请求的响应中，该令牌被附加为`authorization`头。

*loginController.js*

```
if (passwordCorrect) {
  user.token = generateToken(user.id);
  user.save();
  res.headers("authorization", `Bearer ${token}`).send();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 认证请求

现在客户机有了令牌，他们可以将它附加到任何将来的请求中，以真实地标识用户。

当服务器收到附有授权令牌的请求时，会发生以下情况:

1.  它解码令牌并从有效负载中提取 ID。
2.  它在数据库中查找具有该 ID 的用户。
3.  它将请求令牌与存储在用户模型中的令牌进行比较。如果它们匹配，用户就通过了身份验证。

*auth middleware . js*T2】

```
const token = req.header.token;
const payload = decodeToken(token);
const user = User.findById(payload.id);
if (user.token = token) {
  // Authorized
} else {
  // Unauthorized
} 
```

Enter fullscreen mode Exit fullscreen mode

### 注销

如果用户注销，只需删除附加到用户模型的令牌，现在令牌将不再工作。用户需要再次登录才能生成新令牌。

*logoutController.js*

```
user.token = null;
user.save(); 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

这是关于如何使用 JSON Web 令牌保护 API 的一个非常基本的解释。我希望你的头不会太疼。

不过，这个话题还有很多内容，所以这里有一些额外的阅读材料:

*   [JWT.io](https://jwt.io)
*   什么是 JSON Web 令牌？

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *