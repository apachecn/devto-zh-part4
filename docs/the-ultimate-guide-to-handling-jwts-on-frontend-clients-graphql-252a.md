# 在前端客户机上处理 jwt 的最终指南(GraphQL)

> 原文：<https://dev.to/hasurahq/the-ultimate-guide-to-handling-jwts-on-frontend-clients-graphql-252a>

> *这是发表在[blog . hasura . io](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql)T3 上的一篇原创帖子的节选*

jwt(JSON Web Token，发音为“jot”)正在成为一种流行的处理 auth 的方式。这篇文章旨在揭开什么是 JWT 的神秘面纱，讨论它的优缺点，并涵盖在客户端实现 JWT 的最佳实践，同时牢记安全性。我们保留了与 GraphQL 客户端特别相关的例子。

*   [JWT 简介](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#intro)
    *   [安全注意事项](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#jwt_security)
    *   [JWT 结构](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#jwt_structure)
    *   [为什么是 JWT 而不是会话令牌](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#jwt_vs_session)
*   [登录](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#login)
    *   [是否持久令牌？](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#jwt_persist)
    *   [客户端上的令牌使用情况](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#login_usage)
*   [使用 JWT 的 GraphQL 客户端设置](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#basics_client_setup)
    *   [处理令牌过期](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#basics_client_setup_expiration)
    *   [错误处理](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#basics_client_setup_expiration)
*   [注销](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#logout)
    *   [令牌失效](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#logout_token_invalidation)
    *   [将令牌列入黑名单](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#logout_token_invalidation)
*   [无声刷新](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#silent_refresh)
    *   刷新令牌是如何工作的？
    *   [在哪里存储刷新令牌？](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#refresh_token_persistance)
    *   [使用刷新令牌的登录流程](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#refresh_token_login)
    *   [令牌过期时刷新](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#refresh_token_expiry)
*   [持续会话](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#persistance)
    *   [安全注意事项](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#persistance_security)
    *   [错误处理](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#error_handling)
*   [强制注销](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#force_logout)
*   [服务器端渲染(SSR)](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql#ssr)

## 简介:什么是 JWT？

有关 jwt 的详细技术描述，请参考这篇[文章](https://jwt.io/introduction/)。

出于身份验证的目的，JWT 是由服务器颁发的令牌。令牌有一个 JSON 负载，其中包含特定于用户的信息。客户端可以在与 API 对话时使用该令牌(通过将其作为 HTTP 头发送),以便 API 可以识别令牌所代表的用户，并采取特定于用户的操作。

#### 但是客户端就不能创建一个随机的 JSON 负载来模拟用户吗？

好问题！这就是为什么 JWT 也包含一个签名。这个签名是由发布令牌的服务器(比如您的登录端点)创建的，接收这个令牌的任何其他服务器都可以独立地验证这个签名，以确保 JSON 有效负载没有被篡改，并且拥有由合法来源发布的信息。

#### 但是如果我有一个有效的、签名的 JWT，有人从客户端偷走了它，难道他们不能永远使用我的 JWT 吗？

是啊！如果一个 JWT 被偷了，那么小偷可以继续使用 JWT。接受 JWTs 的 API 进行独立的验证，而不依赖于 JWT 源，因此 API 服务器无法知道这是否是一个被盗的令牌！这就是 jwt 有到期值的原因。并且这些值保持较短。通常的做法是保持 15 分钟左右，这样任何泄露的 jwt 将很快失效。但是，也要确保 jwt 不会被泄露。

> 这就是为什么不把 JWT 存储在客户机上也很重要，比如通过 cookies 或 localstorage。这样做使你的应用程序容易受到 CSRF、T4、T2、XSS 和 T3 的攻击，恶意的表单或脚本会使用或窃取你在 cookies 或本地存储中的令牌。

#### 那么 JWT 是否有特定的结构呢？它看起来像什么？

一辆 JWT 看起来像这样，当它被*序列化*时:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.XbPfbIHMI6arZ3Y922BhjWgQzWXcXNrz0ogtVhfEd2o`

如果你解码 base64，你会得到 JSON 的 3 个重要部分:*头*、*载荷*和*签名*。

[![](img/7f4e2694f9f3cd749c56e24c7f414cbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jG_Dq1oi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/08/Group.png)

序列化形式采用以下格式:

`[ base64UrlEncode(header) ] . [ base64UrlEncode(payload) ] . [signature ]`

> JWT 没有加密。是*基 64* **编码**和**签名**。因此任何人都可以解码令牌并使用其数据。JWT 的签名用于验证它确实来自合法来源。

简单来说，下面是一个 JWT 是如何发出(`/login`)然后用于对另一个服务(`/api`)进行 API 调用的示意图:

[![](img/0abc8cd6d1afeb0d8ecf260261401190.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ApIB4zyl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/08/Screen-Shot-2019-08-29-at-12.54.53.png)

#### 唉！这似乎很复杂。为什么我不应该坚持使用旧的会话令牌呢？

这是网上一个痛苦的讨论。我们简短的(也是固执己见的)回答是，后端开发人员喜欢使用 jwt，因为 a)微服务 b)不需要集中式令牌数据库。

在微服务设置中，每个微服务可以独立验证从客户端接收的令牌是否有效。微服务可以进一步解码令牌并提取相关信息，而无需访问集中式令牌数据库。

这就是 API 开发者喜欢 jwt 的原因，我们(在客户端)需要弄清楚如何使用它。然而，如果您可以使用由您最喜欢的整体框架发布的会话令牌，那么您完全可以使用它，并且可能不需要 jwt！

[在这里继续阅读](https://blog.hasura.io/best-practices-of-using-jwt-with-graphql/#login)