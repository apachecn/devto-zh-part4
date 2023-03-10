# 第 023 集-整合身份服务器 4 -第 3 部分-API-ASP.NET 核心:从 0 到过度杀戮

> 原文：<https://dev.to/joaofbantunes/episode-023-integrating-identityserver4-part-3-api-asp-net-core-from-0-to-overkill-55mc>

在这一集里，我们来看一下组管理服务，以及它使用访问令牌(JWT)来执行请求认证所需的更改。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/_DDJKFW8LxQ](https://www.youtube.com/embed/_DDJKFW8LxQ)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在上一篇文章中，我们已经看到了如何在 auth 服务中配置 IdentityServer4。在本文中，我们利用 ASP.NET 核心的内置特性，在成功认证后，使用认证服务向客户端应用程序提供的 [JWT (JSON Web 令牌)](https://jwt.io/)来认证对组管理 API 的请求。

## 配置认证和授权

将应用程序配置为要求 JWT 身份验证相当简单，就像变魔术一样😛，因为 ASP.NET 核心已准备好处理这种开箱即用的情况。我们将从了解如何执行这些配置开始，但稍后我们将尝试阐明一些神奇之处。

### 设置 JWT 认证

配置身份验证将需要在`Startup`类(或它调用的方法)中通常的 2 个步骤:配置服务和配置请求处理管道。

从最简单的部分开始，请求处理管道，我们只需要添加`app.UseAuthentication()`，就像我们在 auth 服务中配置认证时一样。

现在让我们转到服务配置。我们有一个`ServiceCollectionExtensions`类，在那里我们已经提取了配置服务的方法，所以我们将在那里创建一个名为`AddConfiguredAuth`的新类(尽管这应该在以后进行重构，以避免在同一个地方有所有不相关的配置)。

`IoC\ServiceCollectionExtensions.cs`

```
public static class ServiceCollectionExtensions
{
    // ...
    public static IServiceCollection AddConfiguredAuth(this IServiceCollection services)
    {
        services
            .AddAuthentication("Bearer")
            .AddJwtBearer("Bearer", options =>
            {
                options.Authority = "https://localhost:5005";
                options.Audience = "GroupManagement";
            });

        return services;
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

看着上面的代码，我想很容易明白为什么我说它实现简单，4 或 5 行代码处理一切(因为 ASP.NET 核心实现了当然是困难的部分)。

让我们快速浏览一下正在发生的事情:

*   我们调用`AddAuthentication`来添加认证所需的核心服务，以及我们想要用于认证的默认方案。
*   我们调用`AddJwtBearer`来添加 JWT 认证所需的服务。我们提供了方案名，因此它可以与我们传递给`AddAuthentication`的内容相匹配，并且还提供了一些处理 jwt 的选项。
    *   `Authority`是 auth 服务的 url，因此应用程序可以获取一些必需的信息来验证令牌。
    *   `Audience`用于令牌验证，以确保令牌旨在由该特定应用程序使用。

乍一看，有人可能会问，这怎么足以验证令牌呢？尽管看起来不多，`Authority`在这方面发挥了巨大的作用，因为 OpenId Connect 规范包括[发现关于提供者](https://openid.net/specs/openid-connect-discovery-1_0.html)的信息，这意味着 OpenId Connect 提供者公开了依赖方可以获取信息的众所周知的端点。我们将在下一节看到一个例子。

### 基于认证用户设置授权

在[第 019 集](https://blog.codingmilitia.com/2019/04/29/aspnet-019-from-zero-to-overkill-roles-claims-policies)(甚至在[第 016 集](https://blog.codingmilitia.com/2019/02/24/aspnet-016-from-zero-to-overkill-authentication-with-identity-and-razor-pages))我们看了一下 ASP.NET 核心中的授权。我们在这个应用程序中需要的是非常相似的。

基本上，我们需要对用户进行身份验证，并且有一个名为`GroupManagement`的范围被授权访问 API。我们可以对不同的端点使用不同的作用域，但是现在不需要走这条路。

为了执行这些配置，我们使用了`ServiceCollectionExtensions.AddRequiredMvcComponents`方法并添加了一些内容:

```
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddRequiredMvcComponents(this IServiceCollection services)
    {
        // ...
        var mvcBuilder = services.AddMvcCore(options =>
        {
            // ...
            var policy = new AuthorizationPolicyBuilder()
                .RequireAuthenticatedUser()
                .RequireClaim("scope", "GroupManagement")
                .Build();

            options.Filters.Add(new AuthorizeFilter(policy));
        });

        // ...
        mvcBuilder.AddAuthorization();
        return services;
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在第 019 集看到了类似的代码，但是为了快速浏览，我们正在构建一个授权过滤器/策略来添加到 MVC 的过滤器中。在构建策略时，我们指出了我们需要什么，即需要进行身份验证并拥有名为`scope`且值为`GroupManagement`的声明的用户。假设在 JWT 验证中，我们确保`GroupManagement`是目标受众，那么在这个简单的场景中可以跳过这个范围检查，如果我们想要在同一个 API 中有多个范围，就像前面提到的那样，可以使用它。

## OpenId 连接配置发现端点

我甚至不会尝试详细介绍 OpenId Connect discovery，因为我并没有完全掌握整个协议，但是我认为对正在发生的事情有一些了解是很有趣的，而不仅仅是依赖于 4 或 5 行代码所产生的“魔力”。

正如我前面提到的，向 JWT 验证服务提供 auth 服务 url 有很长的路要走，因为 OpenId Connect 指定了使用众所周知的端点收集所需信息的方法。

如果我们获取 auth 服务 url 并将其追加到`/.well-known/openid-configuration`中，我们将得到一个包含上述信息的`JSON`响应。

`https://AUTH-SERVICE/well-known/openid-configuration`

```
 {  "issuer":  "http:\/\/localhost:5005",  "jwks_uri":  "http:\/\/localhost:5005\/.well-known\/openid-configuration\/jwks",  "authorization_endpoint":  "http:\/\/localhost:5005\/connect\/authorize",  "token_endpoint":  "http:\/\/localhost:5005\/connect\/token",  //...  "frontchannel_logout_supported":  true,  //...  "scopes_supported":  [  "openid",  "profile",  "GroupManagement",  "offline_access"  ],  "claims_supported":  [  "sub",  "name",  //...  ],  "grant_types_supported":  [  "authorization_code",  "client_credentials",  //...  ],  "response_types_supported":  [  "code",  "token",  //...  ],  "response_modes_supported":  [  "form_post",  "query",  "fragment"  ],  "token_endpoint_auth_methods_supported":  [  "client_secret_basic",  "client_secret_post"  ],  "subject_types_supported":  [  "public"  ],  "id_token_signing_alg_values_supported":  [  "RS256"  ],  "code_challenge_methods_supported":  [  "plain",  "S256"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

为了避开巨大的`JSON`墙，我删掉了一些内容，但是我想指出一些有趣的地方:

*   此处显示了提供者公开的端点
*   `jwks_uri`端点提供了一组公钥，应用程序可以使用这些公钥来验证由 auth 服务创建的 jwt，并使用其私钥进行签名
*   除了端点，还有支持的能力的指示，如`frontchannel_logout_supported`，支持的范围和声明等

## 样 JWT

由于我们正在检查一些“幕后”的东西，我们可以忽略，因为它主要是为我们处理的，但它总是好的，我们知道在我们的应用程序中发生了什么，我们可以看看一个样本 JWT。

请记住，JWT 没有加密，只是签名，所以任何篡改检测，所以我们可以抓住它，看看有什么。如果我们抓取一个 JWT 并将其放入 [jwt.io](https://jwt.io/) ，我们可以看到它的数据。

```
{  "nbf":  1560702568,  "exp":  1560702628,  "iss":  "http://localhost:5005",  "aud":  [  "http://localhost:5005/resources",  "GroupManagement"  ],  "client_id":  "WebFrontend",  "sub":  "bbfbc630-6209-43e9-abeb-d26b92d7c76a",  "auth_time":  1560702553,  "idp":  "local",  "scope":  [  "openid",  "profile",  "GroupManagement",  "offline_access"  ],  "amr":  [  "pwd"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

我不会讲述整个事情，但我们可以看一看一些熟悉的事情:

*   通过`client_id`,我们可以看到令牌是为了供`WebFrontend`客户端使用而发出的
*   我们可以在`aud`房产`GroupManagement`看到一个熟悉的观众
*   在`scope`数组中，我们看到了我们在上一篇文章中讨论过的作用域

## 其他

这应该可以在组管理 API 中集成基于 JWT 的身份验证。正如我们所看到的，从这个 API 的角度来看，身份服务器是提供者还是其他什么都不重要，只要它提供了所需的特性。

帖子中的链接:

*   [JWT](https://jwt.io/)
*   [OpenId 连接发现](https://openid.net/specs/openid-connect-discovery-1_0.html)
*   [第 019 集——角色、主张和政策——ASP.NET 核心:从 0 到过度杀戮](https://blog.codingmilitia.com/2019/04/29/aspnet-019-from-zero-to-overkill-roles-claims-policies)
*   [第 016 集——用身份和剃刀页认证——ASP.NET 核心:从 0 到矫枉过正](https://blog.codingmilitia.com/2019/02/24/aspnet-016-from-zero-to-overkill-authentication-with-identity-and-razor-pages)

这个子系列帖子的源代码分散在[“编码民兵:ASP.NET 核心-从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的一堆仓库中，标记为`episode021`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode021)
*   [群组管理](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode021)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode021)

感谢分享和反馈！

谢谢你的来访，西阿兹！