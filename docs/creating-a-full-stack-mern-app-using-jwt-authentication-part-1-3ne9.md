# 使用 JWT 认证创建全栈 MERN 应用程序:第 1 部分

> 原文：<https://dev.to/bnevilleoneill/creating-a-full-stack-mern-app-using-jwt-authentication-part-1-3ne9>

[![](img/708ddf149b6fb76f6b229bca60285883.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wa0dyZ1V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i3jeauevetn20wmylcp3.jpeg)

我的绿地项目需要通常的歌舞:认证。我们的客户想要时髦一点，并且发现认证中的趋势技术是 [JWT (JSON Web Token)](https://tools.ietf.org/html/rfc7519) ，并且在将它用于所有认证目的时，压力很快就增大了。

作为一个反应快的人，我不介意在这上面工作和学习一项新技术，但是，是的——这就是为什么我在这里写我的经验。

我们正在构建一个与[对话的 React 前端。NET Core API 服务器](https://dotnet.microsoft.com/)。身份验证机制建立在 JWT 上。项目的. NET 端。我正在使用 React 在前端实现 JWT 认证，现在开始。

另外，请注意，我没有添加任何 MongoDB 部分，但我称它为 MERN 堆栈应用程序——抱歉。这不在本文的讨论范围之内。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 什么是 JWT？

JSON Web 令牌是一种开放的、符合行业标准的 RFC 7519 方法，用于安全地表示双方之间的声明。JWT 是一个 [JSON](https://www.json.org/) 响应，在 RFC 7519 中被定义为一种在双方之间表示一组信息的安全方式。令牌由报头、有效载荷和签名组成。

简单地说，JWT 就是以下格式的字符串:

<figure>[![JWT String Format](img/9bda238d0a5ad48687733093e1303116.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bJtCD4So--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/07/jwt-string-format.jpg%3Fssl%3D1)

<figcaption>【JWT】字符串格式。</figcaption>

</figure>

应该注意的是，双引号引起的字符串实际上被认为是有效的 JSON 响应:

```
"header.payload.signature" 
```

Enter fullscreen mode Exit fullscreen mode

### JWT 之流

由于我们在基于微服务的架构中工作，我们有不同的微服务，其中一个是身份验证服务。这是后端服务之一。NET Core API，它基于域 cookie 中的引用令牌检索用户声明，并为该声明生成 JWT。

如果返回有效的 JWT，则调用被转发到相应的服务，并且 JWT 作为 OAuth 承载令牌在请求头中传递，以供后台服务进一步授权。

<figure>[![JWT Auth Sequence](img/3967c0610fdead91cd390063db2d61ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LU8-zi7o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/07/jwt-auth-sequence.jpg%3Fssl%3D1)

<figcaption>【JWT】流程图中表示的 auth 序列。</figcaption>

</figure>

使用下图可以简化上述顺序:

<figure>[![Simplified JWT Auth Sequence](img/3ab08352550856eeb244bbcb9af010ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oEy7D9bC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/07/simplified-jwt-auth-sequence.jpg%3Fssl%3D1)

<figcaption>JWT auth 序列的简化表示，承蒙 [TopTal](https://www.toptal.com/java/rest-security-with-jwt-spring-security-and-java%23jwt-authentication) 。</figcaption>

</figure>

让我们现在开始构建类似的东西。

## 先决条件

正如我所说的，这是一个我们将要构建的 MERN 堆栈应用程序。显而易见的要求是:

*   [Node.js](https://nodejs.org/en/) (请 LTS 最新版本)
*   [Visual Studio 代码](https://code.visualstudio.com/)(与 React 配合得很好，正如[韦斯·博斯](https://wesbos.com/uses/)所解释的那样——很好，相信我)
*   [Google Chrome](https://www.google.com/chrome/) 带有 React DevTools 扩展(我的[首选环境](https://blog.praveen.science/my-personal-development-environment/)的一部分)
*   一个好的终端，像 [iTerm2](https://www.iterm2.com/) (macOS)

我想现在够了。

## 创建 Express.js 服务器

下面是最简单的代码形式:`index.js`运行`node index` :
后，当你打开端口 3000 上的[本地主机时，浏览器上会显示`Hello World!`](http://localhost:3000/)

```
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => res.send("Hello World!"));

app.listen(port, () => console.log(`Example app listening on port ${port}!`)); 
```

Enter fullscreen mode Exit fullscreen mode

## 在服务器上生成 JWT

我们必须从基于 JWT 的认证开始，并尝试验证一些东西。jwt.io 给出了支持 jwt 不同特性的不同库的列表。我试图找到一个支持一切的，但在 JavaScript 中没有。【T2![😔](img/b00dcf55d2ec0fa28b4edd910d6574e0.png)

让我们想想这里需要什么。后端应该能够支持算法 HS512 和 RS512，因为这些是我们的一些银行客户推荐的。

### 选对库！

我计划使用`jsrsasign`，因为这是最接近我的后端团队生成的，并且它支持所有的算法。让我们以这种方式包含它们，但是不要忘记在运行代码之前在本地安装包:

```
const JSRSASign = require("jsrsasign"); 
```

Enter fullscreen mode Exit fullscreen mode

### 实现编码功能

JWT 有效载荷通常被称为声明——不知道为什么。所以我们先创建一个`claims`:

```
const claims = {
  Username: "praveen",
  Password: "Pr@v33n",
  Fullname: "Praveen Kumar"
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我现在从 Users DB 中为我的应用程序所需要的一切。我在这里没有涉及任何数据库概念，所以我跳过了它们。接下来是私钥——顾名思义，让它保持私有，不要在任何客户端代码中使用它。

```
const key = "$PraveenIsAwesome!"; 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**绝对保密这个东西！【T2![🤐](img/b9c4a58093eebee9d5a4399d1291d1c6.png)

现在我们已经准备好了我们的`claims`和`key`。让我们开始签名并生成令牌。在签名之前，我们必须确定我们将使用哪种算法。这些细节将在标题中。所以，让我们创建一个标题。

我们将使用 HS512 (HMAC-SHA512)算法对令牌进行签名，并且我们将生成一个 JWT。您可以生成的其他类型或现有标准有:

*   JSON 网络签名(JWS): [RFC7515](https://tools.ietf.org/html/rfc7515)
*   JSON 网页加密(JWE): [RFC7516](https://tools.ietf.org/html/rfc7516)
*   JSON Web Key (JWK): [RFC7517](https://tools.ietf.org/html/rfc7517)
*   JSON 网络算法(JWA)–[RFC 7518](https://tools.ietf.org/html/rfc7518)
*   JSON Web 令牌(JWT): [RFC7519](https://tools.ietf.org/html/rfc7519)
*   JSON Web Key (JWK)指纹: [RFC7638](https://tools.ietf.org/html/rfc7638)
*   JWS 未编码有效载荷选项: [RFC7797](https://tools.ietf.org/html/rfc7797)
*   CFRG 椭圆曲线签名(EdDSA): [RFC8037](https://tools.ietf.org/html/rfc8037)

让我们继续下一个生成令牌的主要过程。我们已经得到了我们所需要的一切:`header`、`payload` ( `claims`)和`key`。我们需要将上述对象字符串化并发送给生成器。

```
var sHeader = JSON.stringify(header);
var sPayload = JSON.stringify(claims); 
```

Enter fullscreen mode Exit fullscreen mode

`key`已经是一个字符串了，所以不要费心将其字符串化。要生成密钥，我们应该将三个字符串和算法一起传递给`JSRSASign.jws.JWS.sign()`函数，如下所示:

```
const sJWT = JSRSASign.jws.JWS.sign("HS512", sHeader, sPayload, key); 
```

Enter fullscreen mode Exit fullscreen mode

您得到的输出是一个 JWT！可以在 [jwt.io](https://jwt.io/) 验证正确性。最后，将字符串记录到控制台将得到输出:

```
console.log(sJWT); 
```

Enter fullscreen mode Exit fullscreen mode

当我执行上面的代码时，我得到了这个:

```
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJQYXNzd29yZCI6IlByQHYzM24iLCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.nsE9yzUct5vdoNjFZ-2P48vFLGld5-Wr1tne8P3P9eZyCtrRPDSR7dJ8myg30sSV0kcRJVGogzTI92NpOo5pPw 
```

Enter fullscreen mode Exit fullscreen mode

将它和我的签名一起放入在线验证器，我得到了相同的输出，其中包含一条经过签名验证的消息:

<figure>[![Signature Verified](img/ce9b64e90b4c60a3c1dbf272563ca926.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0HIUxCfb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/07/signature-verified.jpg%3Fssl%3D1) 

<figcaption>签名验证无误。</figcaption>

</figure>

呜哇！你可以在这里查看[。](https://jwt.io%2F%23debugger-io%3Ftoken%3DeyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJQYXNzd29yZCI6IlByQHYzM24iLCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.nsE9yzUct5vdoNjFZ-2P48vFLGld5-Wr1tne8P3P9eZyCtrRPDSR7dJ8myg30sSV0kcRJVGogzTI92NpOo5pPw)

## 解码并验证 JWT

第一步是在解码和了解令牌中的内容之前验证令牌。只有在确保令牌未被篡改并且使用相同的私钥进行了签名时，才需要进行验证。**这不应该在客户端进行。**我们先通过实现验证函数来尝试解码 JSON Web Token，然后我们再解码。

### 实现验证功能

既然我们已经基于我的后端团队使用的相同算法成功地生成了令牌，那么让我们尝试验证它并验证它是否有效。为了验证令牌，我们只需要`algorithm`、`key`和生成的`token`。让我们继续干吧。

```
const token = "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJQYXNzd29yZCI6IlByQHYzM24iLCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.nsE9yzUct5vdoNjFZ-2P48vFLGld5-Wr1tne8P3P9eZyCtrRPDSR7dJ8myg30sSV0kcRJVGogzTI92NpOo5pPw";
const algorithm = "HS512";
const key = "$PraveenIsAwesome!"; 
```

Enter fullscreen mode Exit fullscreen mode

语法有点奇怪——原因是，验证器可以尝试在任何算法中进行验证，所以它需要一组算法。以上信息将传递给`JSRSASign.jws.JWS.verifyJWT()`函数，该函数接受`token`、`key`和`{"alg": [algorithm]}`，语法如下:

```
console.log(
  JSRSASign.jws.JWS.verifyJWT(token, key, {
    alg: [algorithm]
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

在控制台上，当我运行这个程序时，我得到了以下结果:

```
true 
```

Enter fullscreen mode Exit fullscreen mode

啊，那是一个甜蜜的成功。现在让我们继续解码令牌。解码部分真的很容易；甚至客户端的浏览器也可以做到这一点，这就是为什么我的客户更喜欢在 React 这样的单页面应用程序框架中安全可靠地传输声明对象。

### 实现解码功能

为了解码 JSON Web 令牌，我们将只传递令牌的第二部分。这包含有效载荷，或`claims`。我们还需要算法，可以从令牌的第一部分获取。

我们需要做的第一件事是分割`.`上的令牌，然后将它转换成一个数组，并得到`Header`和`Claim` :

```
const aJWT = sJWS.split(".");
const uHeader = JSRSASign.b64utos(aJWT[0]);
const uClaim = JSRSASign.b64utos(aJWT[1]); 
```

Enter fullscreen mode Exit fullscreen mode

让我们将它们设为`uHeader`和`uClaim`，因为此时它们是不可信的。现在让我们解析它们。`JSRSASign.b64utos()`将不受信任的 Base64 转换成字符串，由同一个库提供。我们现在将使用函数`JSRSASign.jws.JWS.readSafeJSONString()`，它类似于`JSON.parse()`，但是有更多的异常处理机制。

```
const pHeader = JSRSASign.jws.JWS.readSafeJSONString(uHeader);
const pClaim = JSRSASign.jws.JWS.readSafeJSONString(uClaim); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经得到了解析后的头和声明。让我们尝试记录它们并查看输出。

```
console.log(pHeader);
console.log(pClaim); 
```

Enter fullscreen mode Exit fullscreen mode

呜哇！我们这里有解码版本。

```
{
  "alg": "HS512",
  "typ": "JWT"
}
{
  "Username": "praveen",
  "Password": "Pr@v33n",
  "Fullname": "Praveen Kumar"
} 
```

Enter fullscreen mode Exit fullscreen mode

开始了。现在我们可以用这种方式安全地访问有效载荷(至少不是明文形式的)![![👍🏻](img/2117ef20075b469107d921f1e47bb7f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cr4zg3l2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f44d-1f3fb.png)

## 完成代码和下一部分

下面是完整的代码，包括签名、生成、确认、验证和解码！[![😇](img/0c423f47680d70dd5e88be3e45707378.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BQOzXhNS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f607.png)T3】

```
const JSRSASign = require("jsrsasign");

// Generation
const claims = {
  Username: "praveen",
  Password: "Pr@v33n",
  Fullname: "Praveen Kumar"
};
const key = "$PraveenIsAwesome!";
const header = {
  alg: "HS512",
  typ: "JWT"
};

const sHeader = JSON.stringify(header);
const sPayload = JSON.stringify(claims);
// Generate the JWT
const sJWT = JSRSASign.jws.JWS.sign("HS512", sHeader, sPayload, key);
// Log it to the console.
console.log("JSON Web Token: ", sJWT);

const token =
  "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJQYXNzd29yZCI6IlByQHYzM24iLCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.nsE9yzUct5vdoNjFZ-2P48vFLGld5-Wr1tne8P3P9eZyCtrRPDSR7dJ8myg30sSV0kcRJVGogzTI92NpOo5pPw";
const algorithm = "HS512";

// Log it to the console.
console.log(
  "Verification: ",
  // Validation
  JSRSASign.jws.JWS.verifyJWT(token, key, {
    alg: [algorithm]
  })
);

// Decoding
const sJWS = token;
const aJWT = sJWS.split(".");
const uHeader = JSRSASign.b64utos(aJWT[0]);
const uClaim = JSRSASign.b64utos(aJWT[1]);
const pHeader = JSRSASign.jws.JWS.readSafeJSONString(uHeader);
const pClaim = JSRSASign.jws.JWS.readSafeJSONString(uClaim);
// Decoded objects.
// Log it to the console.
console.log("Header: ", pHeader);
console.log("Claim: ", pClaim); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会给出这样的输出:

```
➜  MockAPIServer node dec-enc.js
JSON Web Token:  eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJVc2VybmFtZSI6InByYXZlZW4iLCJQYXNzd29yZCI6IlByQHYzM24iLCJGdWxsbmFtZSI6IlByYXZlZW4gS3VtYXIifQ.nsE9yzUct5vdoNjFZ-2P48vFLGld5-Wr1tne8P3P9eZyCtrRPDSR7dJ8myg30sSV0kcRJVGogzTI92NpOo5pPw
Verification:  true
Header: { alg: 'HS512', typ: 'JWT' }
Claim: { Username: 'praveen',
  Password: 'Pr@v33n',
  Fullname: 'Praveen Kumar' } 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，我所做的只是一个服务器端生成组件，并不包括使用 POST 请求向您发送令牌的“web 服务器”。

现在我们已经完成了服务器端的工作，让我们开始构建供客户端使用的 REST API 端点。敬请关注第二部分——即将推出！

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[使用 JWT 认证创建全栈 MERN 应用:第 1 部分](https://blog.logrocket.com/mern-app-jwt-authentication-part-1/)最先出现在[的 LogRocket 博客](https://blog.logrocket.com)上。