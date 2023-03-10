# 第 029 集-用 proxy kit-ASP.NET 核心简化 BFF:从 0 到过度杀伤

> 原文：<https://dev.to/joaofbantunes/episode-029-simplifying-the-bff-with-proxykit-asp-net-core-from-0-to-overkill-33k2>

在这一集里，我们将为前端实现修改当前的后端，引入 ProxyKit 来简化请求路由到后台 API，放弃手动实现一切的需要。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/Wgu97TKaRiI](https://www.youtube.com/embed/Wgu97TKaRiI)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在这一点上，我们已经为未来的开发创建了一个稳定的基础设施，但是就实际的应用程序特性而言，我们仍然处于非常基础的水平，所以是时候回到实现它们了。

不过，我们面临的一个问题是当前前端对后端的实现，维护起来会很痛苦。

当我们在第 020 集中引入 BFF 时，我们有一个主要目标:有一个地方来实现特定于给定前端的特性，允许它和其他客户端应用程序可能使用的支持 API 之间某种程度的解耦。

这仍然是有意义的，但问题是，并不是每一个与后台 API 的交互都需要定制。正如我们到目前为止在组管理 API 中看到的，有些东西可以直接流向 API，BFF 除了向请求添加授权令牌之外没有做太多事情。

在第 020 集里，我们抓住机会玩了 HTTP 客户端，但是如果我们继续像这样实现所有东西，我们在 API 里做的每一个改变，都需要我们改变 BFF。我们可以给 BFF 取另一个名字 [API gateway](https://microservices.io/patterns/apigateway.html) ，如果我们考虑一下，我们会开始想象如果我们需要手动配置前端和后端之间的所有交互，即使不需要定制，我们会有多少工作量。

记住这一点，这一集我们将通过引入 [ProxyKit](https://github.com/damianh/ProxyKit) 来简化 BFF，它将负责将任何 API 请求路由到正确的支持服务。

> **边注:**在这篇文章中我不会真的谈论它们，但只是为了让那些关注这个系列并检查代码的人知道，我对 BFF 的路线做了一些小的调整，即认证端点不再在`api/auth`上，而只是在`auth`上。由于这一更改，还调整了反向代理配置、auth service 配置的重定向端点和单页应用程序开发服务器代理设置。

## 将 ProxyKit 引入 BFF

介绍 [ProxyKit](https://github.com/damianh/ProxyKit) 的最好方式可能就是从它的 [GitHub 页面](https://github.com/damianh/ProxyKit)中复制描述:

> 一个创建代码优先的 HTTP 反向代理的工具包，托管在 ASP.NET 核心作为中间件。这使得代码优先的代理可以嵌入到现有的 ASP.NET 核心应用程序中，或者作为独立的服务器部署。

我认为这个描述让我们理解了为什么它是我们问题的一个很好的解决方案，但是让我们仔细分析一下。

通过成为我们可以引入 ASP.NET 核心的另一个中间件，这意味着它只是管道中存在的另一个东西，而不是使其他一切无用。简而言之，其他一切都将照常工作，因此我们可以在 BFF 中添加额外的端点来实现特定的功能，就像认证过程一样。

### 快看 ProxyKit vs Ocelot

我们必须实现的另一个选项是 [Ocelot](https://github.com/ThreeMammals/Ocelot) 。尽管我们可以用它们来实现相似的行为，但它们的侧重点是不同的。

ProxyKit 专注于成为一个反向代理，就像我们一直使用的 HAProxy 一样，但托管在 ASP.NET 核心应用程序中，这允许我们利用其他框架功能和我们选择的编程语言来定制东西。

Ocelot 专注于成为一个 API 网关，所以开箱即用，除了类似的路由功能之外，还有其他对这一特定场景有用的功能，如请求聚合、缓存、重试和其他功能。

乍一看，Ocelot 可能是我们最好的朋友的更好的选择，拥有所有这些额外的功能。与 ProxyKit 相比，它的不足之处在于简单代理场景中的性能，这正是我们目前所拥有的。事实上，这在 ProxyKit 的 [GitHub 页面](https://github.com/damianh/ProxyKit#7-comparison-with-ocelot)中有更好的解释。

> Ocelot 是一个 API 网关，也运行在 ASP.NET 核心上。API 网关和通用反向代理之间的一个关键区别是，前者倾向于基于消息，而反向代理是基于流的。也就是说，API 网关通常会缓冲每个请求和响应消息，以便能够执行转换。这对于 API 网关来说很好，但是不适合一般的反向代理性能，也不适合分块编码的响应。请参阅不支持的 Ocelot 文档。
> 
> 将 ProxyKit 与 Ocelot 相结合将为各种场景提供一些不错的选择。

这最后一个短语特别有趣，但一点也不意外。因为两者都是 ASP.NET 核心应用程序中的中间件，所以我们可以自由组合各自的优点。不过现在，我们将使用 ProxyKit，将来我们也可能在应用程序中引入 Ocelot。

### 安装和配置

安装 ProxyKit 只是安装一个 NuGet 包的问题。

```
dotnet add package ProxyKit 
```

Enter fullscreen mode Exit fullscreen mode

现在使用它，假设它是一个中间件，你可能已经能想象出步骤:转到`Startup`类，在`ConfigureServices`中配置所需的服务，在`Configure`中配置管道。

要配置 DI，很简单:

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
services.AddProxy(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须配置管道，所以我们稍微深入到`Configure`方法。我们会把它加到流水线的末尾，所以在`app.UseMvc()`之后。这允许我们用控制器来处理一些请求，但是如果没有匹配的，它将通过管道到达代理中间件。

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
app.Map("/api/groups", api =>
{
    api.RunProxy(async context =>
    {
        var forwardContext = context
            .ForwardTo("http://groupmanagement:80/groups")
            .CopyXForwardedHeaders();

        var token = await context.GetAccessTokenAsync();
        forwardContext.UpstreamRequest.SetBearerToken(token);

        return await forwardContext.Send();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

从上面的代码中我们可以看到，我们不需要太多的配置就可以让它工作。我们使用`app.Map`和组管理 API 的基本路由，所以它只路由正确的请求，而不是所有的请求。

在我们对管道进行分支之后，我们可以使用`RunProxy`扩展方法来配置代理中间件，使用`ForwardTo`扩展方法来开始请求的转发，其中我们指明了目标端点。这个方法返回一个`ForwardContext`实例，通过它我们可以进行额外的配置，并最终转发请求。

除了所需的目标端点之外，我们还将其配置为复制外部反向代理(HAProxy)可能包含在请求中的任何`X-Forwarded-*` HTTP 头。最后，在实际转发请求之前，我们需要包含 API 识别用户所需的 JWT 令牌。以前我们是在`HttpClient`级(用`DelegatingHandler`)完成的，但是有了 ProxyKit，我们可以直接在中间件中完成。注意，我们也可以配置 ProxyKit 使用的`HttpClient`，因此也可以使用前面的方法(更多信息[在这里](https://github.com/damianh/ProxyKit#24-configuring-proxykits-httpclient))。

有了这些，BFF 现在可以自动地将请求转发给组管理 API。

### 准备更多支持 API

您可能会注意到，我们只是为组管理 API 硬编码配置。如果我们只是这样做，我们将不得不在管道中为每个后台服务添加一个中间件，这是非常不理想的。

相反，让我们重新设计中间件配置，使其更容易代理更多的服务。

首先，我们将向`appsettings`添加一些东西，允许我们将一个 API 映射到它的端点。

`appsetings.DockerDevelopment.json`

```
"ApiRoutes":  {  "groups":  "http://groupmanagement:80"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要一种方法来将请求路由匹配到端点，就像我们在设置文件中配置的那样。为此，我创建了一个名为`ProxiedApiRouteEndpointLookup`的新类。我不会深入讨论实现的细节，因为我认为这是下一集的一个有趣的话题(我喜欢在准备上一集的时候想到下一集的想法🙂)，但我还是会把代码放在这里供参考。

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\ApiRouting\ProxiedApiRouteEndpointLookup.cs`

```
public class ProxiedApiRouteEndpointLookup
{
    // The double dictionary strategy can be simplified if we're able to lookup directly with a ReadOnlySpan<char>
    // Work in progress here -> https://github.com/dotnet/corefx/issues/31942

    private readonly Dictionary<string, string> _routeToEndpointMap;
    private readonly Dictionary<int, string[]> _routeMatcher;

    public ProxiedApiRouteEndpointLookup(Dictionary<string, string> routeToEndpointMap)
    {
        _routeToEndpointMap = routeToEndpointMap ?? throw new ArgumentNullException(nameof(routeToEndpointMap));
        _routeMatcher = _routeToEndpointMap
            .Keys
            .GroupBy(
                r => r.GetHashCode(),
                r => r)
            .ToDictionary(
                g => g.Key,
                g => g.ToArray());
    }

    public bool TryGet(PathString path, out string endpoint)
    {
        endpoint = null;
        var pathSpan = path.Value.AsSpan();
        var basePathEnd = pathSpan.Slice(1, pathSpan.Length - 1).IndexOf('/');
        var basePath = pathSpan.Slice(1, basePathEnd > 0 ? basePathEnd : pathSpan.Length - 1);

        // when we upgrade to .NET Core 3.0, we can use string.GetHashCode(basePath)
        // to get the hashcode directly from the span, which will be much better for allocations
        if (_routeMatcher.TryGetValue(basePath.ToString().GetHashCode(), out var routes))
        {
            var route = FindRoute(basePath, routes);
            return !(route is null) && _routeToEndpointMap.TryGetValue(route, out endpoint);
        }

        return false;
    }

    private static string FindRoute(ReadOnlySpan<char> route, string[] routes)
    {
        for (var i = 0; i < routes.Length; ++i)
        {
            var currentRoute = routes[i];
            if (MemoryExtensions.Equals(route, currentRoute, StringComparison.InvariantCultureIgnoreCase))
            {
                return currentRoute;
            }
        }
        return null;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们将在第 030 集看到更多细节，但现在重要的是`ProxiedApiRouteEndpointLookup`在构造函数中接收到路由映射，然后能够通过调用`TryGet`方法将请求路径映射到 API 路由。

为了在中间件中使用它，我们可以在依赖注入容器中配置它:

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
services.AddSingleton(
    new ProxiedApiRouteEndpointLookup(
        _configuration.GetSection<Dictionary<string, string>>("ApiRoutes"))); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以调整中间件配置:

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
app.Map("/api", api =>
{
    api.RunProxy(async context =>
    {
        var endpointLookup = context.RequestServices.GetRequiredService<ProxiedApiRouteEndpointLookup>();
        if (endpointLookup.TryGet(context.Request.Path, out var endpoint))
        {
            var forwardContext = context
                .ForwardTo(endpoint)
                .CopyXForwardedHeaders();

            var token = await context.GetAccessTokenAsync();
            forwardContext.UpstreamRequest.SetBearerToken(token);

            return await forwardContext.Send();
        }

        return new HttpResponseMessage(HttpStatusCode.NotFound);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`app.Map`现在映射到任何基于`/api`的路由，这样我们就可以匹配任何 API 服务，而不仅仅是组管理。

然后，我们使用`ProxiedApiRouteEndpointLookup`尝试将请求路径匹配到支持服务。如果有匹配，我们像以前一样做，只是使用提供的端点而不是硬编码的端点。如果没有匹配，我们用 404 响应。

## 调整基于 MVC 的行为

代理部分已经完成，但我们还没有完成。因为以前我们基本上是在一个控制器中手动代理请求(`GroupsController`)，所以我们已经准备好使用 MVC，即通过使用 MVC 过滤器(`AuthorizeFilter`)以及防伪令牌验证(`AutoValidateAntiforgeryTokenAttribute`)来强制授权。

随着这种在 MVC 之外做事情的移动，这将不再起作用，所以我们需要把这些也放入一个中间件，所以它适用于 BFF 中的一切，不管是不是 MVC。

### 在中间件级别实施认证

为了在中间件级别实施身份验证，我们...创建一个中间件！🙃

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Security\EnforceAuthenticatedUserMiddleware.cs`

```
public class EnforceAuthenticatedUserMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        if (!context.User.Identity.IsAuthenticated)
        {
            await context.ChallengeAsync();
            return;
        }

        await next(context);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我想很简单。我们在《T2》第 008 集中学到了实现中间件，所以实现`IMiddleware`和`InvokeAsync`方法应该看起来很熟悉。

至于在每个请求上运行的代码，我们只是检查用户是否经过身份验证。如果用户没有通过身份验证，我们调用`HttpContext`上的`ChallengeAsync`扩展方法来发出登录请求，这可能导致 401 或重定向到 auth 服务，这取决于请求的目标端点。

我们使用中间件所要做的就是将它添加到 DI 和管道中:

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
// in ConfigureServices
services.AddSingleton<EnforceAuthenticatedUserMiddleware>();

// ...

// in Configure, between app.UseAuthentication() and app.UseMvc()
app.UseMiddleware<EnforceAuthenticatedUserMiddleware>(); 
```

Enter fullscreen mode Exit fullscreen mode

### 在中间件层验证防伪令牌

为了验证防伪令牌，我们实现了另一个中间件。

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Security\ValidateAntiForgeryTokenMiddleware.cs`

```
public class ValidateAntiForgeryTokenMiddleware : IMiddleware
{
    private readonly IAntiforgery _antiforgery;

    public ValidateAntiForgeryTokenMiddleware(IAntiforgery antiforgery)
    {
        _antiforgery = antiforgery;
    }

    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        if (ShouldValidate(context))
        {
            await _antiforgery.ValidateRequestAsync(context);
        }

        await next(context);
    }

    private static bool ShouldValidate(HttpContext context)
    {
        // as seen on https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Mvc/Mvc.ViewFeatures/src/Filters/AutoValidateAntiforgeryTokenAuthorizationFilter.cs

        var method = context.Request.Method;
        return !(HttpMethods.IsGet(method)
                 || HttpMethods.IsHead(method)
                 || HttpMethods.IsTrace(method)
                 || HttpMethods.IsOptions(method));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，没什么大不了的。对于每个请求，如果它应该被验证，我们就这样做，否则我们就让它过去。是否需要验证取决于请求的方法，因为有些方法被认为是安全的，不需要验证。

使用方法同`EnforceAuthenticatedUserMiddleware`:

`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
// in ConfigureServices
services.AddSingleton<ValidateAntiForgeryTokenMiddleware>();

// ...

// in Configure, after the use of EnforceAuthenticatedUserMiddleware
app.UseMiddleware<ValidateAntiForgeryTokenMiddleware>(); 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

另一集的结尾。如果我们现在运行应用程序，利用我们的 Docker Compose 环境，我们应该看到应用程序像以前一样运行。

我们没有在为应用程序添加更多功能方面取得进展，但我们在未来使这一点变得更容易，不需要为后端服务上的每一件小事而与 BFF 发生冲突。

然而，如果我们愿意，我们确实保留了在 BFF 级别实现事物的能力，所以这是敏捷性和灵活性之间的一个很好的中间地带。

与本系列中的其他内容一样，我没有探究 ProxyKit 提供的所有内容，而是只研究了我想做的事情所需要的内容。要了解更多细节，请查看其 GitHub 页面。

帖子中的链接:

*   [ProxyKit](https://github.com/damianh/ProxyKit)
*   [豹猫](https://github.com/ThreeMammals/Ocelot)
*   [模式:API 网关/前端后端](https://microservices.io/patterns/apigateway.html)
*   [保护 spa 的另一种方法(使用 ASP.NET 核心、OpenID 连接、OAuth 2.0 和 ProxyKit)](https://leastprivilege.com/2019/01/18/an-alternative-way-to-secure-spas-with-asp-net-core-openid-connect-oauth-2-0-and-proxykit/)
*   [第 020 集-后端对前端和 http client-ASP.NET 核心:从 0 到矫枉过正](https://blog.codingmilitia.com/2019/05/05/aspnet-020-from-zero-to-overkill-backend-for-frontend-httpclient)
*   [第 008 集——中品——ASP.NET 核心:从 0 到矫枉过正](https://blog.codingmilitia.com/2018/12/05/aspnet-008-from-zero-to-overkill-middlewares)

这篇文章的源代码分布在[“编码民兵:ASP.NET 核心——从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的存储库中，标记为`episode029`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode029)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode029)
*   [部署](https://github.com/AspNetCoreFromZeroToOverkill/Deployment/tree/episode029)

感谢分享和反馈！

谢谢你的来访，西阿兹！