# 第 024 集-整合身份服务器 4 -第 4 部分-正面背后-ASP.NET 核心:从 0 到过度杀戮

> 原文：<https://dev.to/joaofbantunes/episode-024-integrating-identityserver4-part-4-back-for-front-asp-net-core-from-0-to-overkill-58cp>

在这一集里，我们看一下前端的后端，以及它处理用户认证、重定向到身份提供者(IdentityServer4 powered auth 服务)、在进行 API 调用时包含访问令牌、刷新所述令牌和处理 CSRF 令牌所需的更改。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/bK1N-C5zI1Q](https://www.youtube.com/embed/bK1N-C5zI1Q)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在过去的几期中，我们看到了如何将 IdentityServer4 集成到我们的 auth 服务中，然后准备了组管理 API 来利用它在每个请求中获得的访问令牌(特别是 JWT)对用户进行身份验证和授权。

在这一集里，我们将开始调整前端，也就是它的后端对前端组件，将身份验证委托给 auth 服务，然后在每个对组管理 API 的请求中包含用户的访问令牌。

鉴于我们将使用 cookies 对 BFF 的每个 SPA(单页应用程序)请求进行认证，我们还将包括对[跨站点请求伪造](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))令牌的支持。

## 配置 BFF 对用户进行认证/授权

我们已经看到了在 ASP.NET 核心中实现身份验证的一些方法，我们将看到另一种与 OpenID Connect 集成的方法。

要点与我们在之前关于[组管理 API](https://blog.codingmilitia.com/2019/06/16/aspnet-023-from-zero-to-overkill-integrating-identityserver4-part3-api) 的帖子中看到的一样——我们将在`Startup.ConfigureServices`中使用`AddAuthentication`和一些特定的 OpenID 连接配置，并在请求处理管道定义中调用`app.UseAuthentication`。

`AddAuthentication`这次有了更多的配置，让我们直接进入主题吧。

### 添加认证

让我们从查看代码开始，然后一步一步地浏览它。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = "Cookies";
        options.DefaultChallengeScheme = "oidc";
    })
    .AddCookie("Cookies", options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    })
    .AddOpenIdConnect("oidc", options =>
    {
        options.SignInScheme = "Cookies";

        options.Authority = "https://localhost:5005";

        options.ClientId = "WebFrontend";
        options.ClientSecret = "secret";
        options.ResponseType = OidcConstants.ResponseTypes.Code;

        options.SaveTokens = true;
        options.GetClaimsFromUserInfoEndpoint = true;

        options.Scope.Add("GroupManagement");
        options.Scope.Add(OidcConstants.StandardScopes.OfflineAccess);

        options.Events.OnRedirectToIdentityProvider = context =>
        {
            if (!context.HttpContext.Request.Path.StartsWithSegments("/auth/login"))
            {
                context.HttpContext.Response.StatusCode = 401;
                context.HandleResponse();
            }
            return Task.CompletedTask;
        };
    });
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

对`AddAuthentication`的实际调用并没有那么多，配置默认的认证方案和质询方案。在这个调用之后，在返回的`AuthenticationBuilder`实例上，我们配置方案以匹配我们所配置的。

`Cookies`认证方案是用`AddCookie`配置的，尽管没有发生很多事情，但这是很重要的一点。我们将在下一节中更详细地了解这是如何工作的，但是设置`options.EventsType`允许我们提供`CookieAuthenticationEvents`类的替代实现。顾名思义，通过这样做，我们可以定义在 cookie 身份验证流程的特定时刻会发生什么。在这种情况下，正如我们稍后将看到的，我们将连接到一个事件，以确保我们拥有的令牌(即访问和刷新令牌)仍然有效，用户可以继续使用该应用程序，而无需重新认证。

名为`oidc`的挑战方案配置有对`AddOpenIdConnect`的调用。当我们考虑给它添加`IdentityServer4`(第 022 集中的[)时，这看起来很熟悉，因为它与我们在认证服务中配置的相匹配。](https://blog.codingmilitia.com/2019/06/13/aspnet-022-from-zero-to-overkill-integrating-identityserver4-part2-auth-service)

我们从设置`SignInScheme`开始，它负责保存我们将从认证过程中获得的用户信息。

然后，我们用身份验证服务端点设置`Authority`，加上`ClientId`、`ClientSecret`和`ResponseType`，它们的值与我们在身份验证服务中配置客户端时设置的值相同。

`SaveTokens = true`表示令牌将存储在 cookie 中。默认情况下，它是`false`，以保持 cookie 更小，所以将来我们可能应该将它提取到某个服务器端会话，但目前它工作正常。

将`GetClaimsFromUserInfoEndpoint`设置为`true`，意味着在 OpenID 连接流完成并且令牌被检索之后，向用户信息端点发出额外的请求以获取额外的用户声明。

我们添加了一些额外的作用域(除了默认的 OpenID Connect 作用域)，`GroupManagement`和`offline_access`，这样我们就可以访问组管理 API 并使用刷新令牌。

最后，我们注册了`OnRedirectToIdentityProvider`事件。由于这个应用程序将被 SPA 用作 API，而不是作为一个完整的 MVC 应用程序，所以当用户没有登录时，将 API 请求重定向到 auth 服务是没有意义的，而是用 401(未授权)来响应。为了实现这一点，在我们提供的事件处理程序中，所有发送到非`/auth/login`端点的未经身份验证的请求，我们将状态代码设置为 401，并将响应标记为已处理，否则让它照常进行。

### 要求用户进行身份验证

在上一节中，我们已经看到了如何确保对 API 的所有请求都经过身份验证，所以这是完全相同的，但我还是把它放在这里作为参考🙂。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddMvc(options =>
        {
            var policy = new AuthorizationPolicyBuilder()
                .RequireAuthenticatedUser()
                .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            // ...
        })
        // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我们调用了`AddMvc`,在配置 lambda 中，我们创建并添加了一个带有我们需要的策略的`AuthorizeFilter`,在本例中是为了对用户进行身份验证。

### 登录

您可能已经注意到，当我们在`OnRedirectToIdentityProvider`上注册 OpenID Connect 身份验证质询方案时，我们让它对除了`/auth/login`路由之外的所有请求用 401 进行响应。这是因为我们将使用这个路径在 SPA 中放置一个登录链接。

总之，登录策略如下:

*   我们公开一个 SPA 可以调用的`/auth/info`端点。如果它得到 401，SPA 显示登录的链接，否则它使用返回的信息。
*   我们公开一个`/auth/login`端点，唯一的目的是重定向到 auth 服务，然后，当认证完成时，重定向到所需的 SPA url。

让我们看看`AuthController`的代码，它是为实现上述登录策略而开发的。

`Features/Auth/AuthController.cs`

```
[Route("auth")]
public class AuthController : ControllerBase
{
    // ...

    [HttpGet]
    [Route("info")]
    public ActionResult<AuthInfoModel> GetInfo()
    {
        // CSRF token stuff we'll see later ...

        return new AuthInfoModel
        {
            Name = User.FindFirst("name").Value
        };
    }

    [HttpGet]
    [Route("login")]
    public IActionResult Login([FromQuery] string returnUrl)
    {
        /*
        * No need to do anything here, as the auth middleware will take care of redirecting to IdentityServer4.
        * When the user is authenticated and gets back here, we can redirect to the desired url. 
        */
        return Redirect(string.IsNullOrWhiteSpace(returnUrl) ? "/" : returnUrl);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`/auth/info`端点只是返回一些当前用户的信息(加上一些 CSRF 令牌逻辑，我们将在后面看到)。对于未经身份验证的用户，它返回 401 是由框架自动处理的，正如我们在身份验证质询方案中添加授权过滤器和转换 401 中的重定向时看到的那样。

`/auth/login`端点获得一个返回 url，因此它可以将用户重定向回 SPA 中的正确位置。同样，对未认证用户的处理，重定向到认证服务，是由框架自动完成的。

## 支持跨站请求伪造令牌

为了增加对跨站点请求伪造令牌的支持，我们比前几集 MVC 和 Razor 页面做了更多的工作。这是因为我们使用的是 SPA 而不是 Razor 生成的 HTML，所以我们有更多的手工工作要做。

先从`Startup.ConfigureServices`说起吧。在这里，我们需要做一些修改:添加一个过滤器来验证所有应该拥有防伪令牌的请求中是否存在防伪令牌(因此，除了`GET`、`HEAD`、`OPTIONS`和`TRACE`之外的所有请求)，并添加辅助防伪服务。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddMvc(options =>
        {
            // ...
            options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute());
        })

    // ...

    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
} 
```

Enter fullscreen mode Exit fullscreen mode

在配置 MVC 的 lambda 中，我们可以看到添加了新的过滤器来验证防伪标记。

过滤之后，我们可以通过调用`AddAntiforgery`扩展方法来查看防伪服务的配置。我们设置的`HeaderName`属性是 SPA 将与防伪令牌一起发送的头的名称，因此它可以在服务器端进行验证。如果我们不这样设置，服务器只会试图在提交的表单数据中查找令牌，这是 MVC/Razor Pages 应用程序中的典型行为。

现在，为了让 SPA 能够设置标头，它需要从某个地方获取它。这就是我们回到`AuthController`的地方。在`GetInfo`动作中，我们将生成防伪令牌，并用它设置一个 cookie，以便 SPA 能够使用它。

`Features/Auth/AuthController.cs`

```
[Route("auth")]
public class AuthController : ControllerBase
{
    private readonly IAntiforgery _antiForgery;

    public AuthController(IAntiforgery antiForgery)
    {
        _antiForgery = antiForgery;
    }

    [HttpGet]
    [Route("info")]
    public ActionResult<AuthInfoModel> GetInfo()
    {
        var tokens = _antiForgery.GetAndStoreTokens(HttpContext);
            HttpContext.Response.Cookies.Append(
                "XSRF-TOKEN", 
                tokens.RequestToken, 
                new CookieOptions() {HttpOnly = false}); //allow JS to grab the cookie to put it in the request header

            return new AuthInfoModel
            {
                Name = User.FindFirst("name").Value
            };
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在在构造函数中得到一个依赖项，`IAntiforgery`,它允许我们生成防伪令牌。我们在`GetInfo`动作中使用了它，在这里我们可以看到，除了生成令牌之外，该服务还存储了令牌，因此当客户端应用程序在报头中发回令牌时，可以对其进行验证。

为了让 SPA 获得令牌，我们添加了 cookie 并将其`HttpOnly`属性设置为`false`，否则浏览器将不允许应用程序访问它。

## 提供 API 调用的访问令牌

当我们认为我们必须在每个对 API 的请求中发送访问令牌时，我们想到的第一件事可能是去我们发出请求的地方并添加头。这当然可行，但是我们可以使用一个更干净的解决方案，这是非常可重用的:创建一个从`DelegatingHandler`继承的类，并配置我们想要使用的`HttpClient`。

一个`DelegatingHandler`允许我们拦截`HttpClient`的请求，因此我们可以在请求之前、之后甚至替代请求添加一些逻辑。在这种情况下，我们希望在发送请求之前添加访问令牌头。下面的类`AccessTokenHttpMessageHandler`实现了这个逻辑。

`AuthTokenHelpers/AccessTokenHttpMessageHandler.cs`

```
public class AccessTokenHttpMessageHandler : DelegatingHandler
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public AccessTokenHttpMessageHandler(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        var accessToken = await _httpContextAccessor.HttpContext.GetTokenAsync("access_token");
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        return await base.SendAsync(request, cancellationToken);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

没什么进展，但让我们快速浏览一遍。我们只是覆盖了`SendAsync`方法，在这里我们可以实现我们需要的逻辑。我们注入了一个`IHttpContextAccessor`实例，我们在`SendAsync`中使用它从`HttpContext`中获取访问令牌(记得我们将它存储在 cookies 中，并可以用这种方式获取它)。

有了令牌，我们可以将它添加到请求头中，然后将其传递给`SendAsync`的基本实现，让请求的其余部分照常流动。

现在我们只需要在`Startup.ConfigureServices`中做一些改变，来使用处理程序:在依赖注入容器中注册它，并用期望的`HttpClient`配置它。

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services
        .AddTransient<AccessTokenHttpMessageHandler>()
        .TryAddSingleton<IHttpContextAccessor, HttpContextAccessor>();
    //...
    services
        .AddHttpClient<GroupsController>(/*...*/)
        // ...
        .AddHttpMessageHandler<AccessTokenHttpMessageHandler>();
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，没有太多事情要做，我们只需要记住去做😛。向 DI 容器注册`AccessTokenHttpMessageHandler`，同时也注册`IHttpContextAccessor`，因为在 ASP.NET 核心中默认情况下它不在那里。之后，我们可以通过调用`IHttpClientBuilder`上的`AddHttpMessageHandler`扩展方法来使用处理程序。

> 注意:注册`IHttpContextAccessor`的一个更好的方法是调用`AddHttpContextAccessor`扩展方法，但是当我最初编写这段代码时，我并不知道它🙂。

## 刷新访问令牌

现在，我们需要做的最后一件事是确保在访问令牌到期时(或即将到期时)刷新它。

为了处理这个问题，正如我简单提到的，我们有一个替代的`CookieAuthenticationEvents`实现和一些额外的助手类。这意味着在收到的每个请求中，我们将检查访问令牌的有效性，如果过期，我们将刷新它。

检查访问令牌有效性的另一种可能的方法是在我们实现的`HttpClient`的委托处理程序中进行。主要区别在于，对于 cookie 认证事件，我们对 BFF 的每个请求进行一次检查，而如果我们在委托处理程序中进行检查，我们对后备 API 的每个请求进行一次检查。两者都有优点和缺点，但是这似乎是一个很好的机会来检查我们可以挂钩到框架的可扩展性点的更多方法，所以我们将使用这个😉。

先说`CustomCookieAuthenticationEvents`类。当从`CookieAuthenticationEvents`继承时，我们有一堆可以覆盖的方法。在这种情况下，我们想要覆盖`ValidatePrincipal`——如果访问令牌有效或者我们能够刷新它，我们可以让它正常流动(可能有一些调整)，如果不是，我们拒绝主体并强制注销，因此用户需要重新认证。

`AuthTokenHelpers/CustomCookieAuthenticationEvents.cs`

```
public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly ITokenRefresher _tokenRefresher;

    public CustomCookieAuthenticationEvents(ITokenRefresher tokenRefresher)
    {
        _tokenRefresher = tokenRefresher;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var result = await _tokenRefresher.TryRefreshTokenIfRequiredAsync(
            context.Properties.GetTokenValue("refresh_token"),
            context.Properties.GetTokenValue("expires_at"),
            CancellationToken.None);

        if (!result.IsSuccessResult)
        {
            context.RejectPrincipal();
            await context.HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
        }
        else if (result.TokensRenewed)
        {
            context.Properties.UpdateTokenValue("access_token", result.AccessToken);
            context.Properties.UpdateTokenValue("refresh_token", result.RefreshToken);
            context.Properties.UpdateTokenValue("expires_at", result.ExpiresAt);
            context.ShouldRenew = true;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

仍然有一些缺失的部分，但是我们能够理解`CustomCookieAuthenticationEvents`中发生了什么。我们在构造函数中获得了一个`ITokenRefresher`的实例，我们将会更详细地看到它，但是顾名思义，它负责刷新(如果需要的话)一个访问令牌。如果令牌刷新失败，我们拒绝主体并强制注销。如果令牌被刷新，那么我们需要更新存储在 cookie 中的值，并为用户更新 cookie(因此有了`context.ShouldRenew = true`)。另一个场景是令牌可能仍然有效，因此我们不需要做任何事情(因此没有针对该场景的特定代码)。

在查看`ITokenRefresher`及其实现之前，让我们先看看由`TryRefreshTokenIfRequiredAsync`返回的`TokenRefreshResult`类。

`AuthTokenHelpers/TokenRefreshResult.cs`

```
public class TokenRefreshResult
{
    private static readonly TokenRefreshResult NoRefreshNeededResult =
        new TokenRefreshResult(true, false, null, null, null);

    private static readonly TokenRefreshResult FailedResult =
        new TokenRefreshResult(false, false, null, null, null);

    protected TokenRefreshResult(
        bool isSuccessResult,
        bool tokensRenewed,
        string accessToken,
        string refreshToken,
        string expiresAt)
    {
        IsSuccessResult = isSuccessResult;
        TokensRenewed = tokensRenewed;
        AccessToken = accessToken;
        RefreshToken = refreshToken;
        ExpiresAt = expiresAt;
    }

    public bool IsSuccessResult { get; }
    public bool TokensRenewed { get; }
    public string AccessToken { get; }
    public string RefreshToken { get; }
    public string ExpiresAt { get; }

    public static TokenRefreshResult Success(
        string accessToken,
        string refreshToken,
        string expiresAt)
    {
        return new TokenRefreshResult(true, true, accessToken, refreshToken, expiresAt);
    }

    public static TokenRefreshResult Failed() => FailedResult;

    public static TokenRefreshResult NoRefreshNeeded() => NoRefreshNeededResult;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，除了在`CustomCookieAuthenticationEvents`类中有必要的属性来检查结果之外，我们还有一些助手方法，`ITokenRefresher`实现可以使用它们来获得更好的可读性。

`ITokenRefresher`接口公开了一个方法`TryRefreshTokenIfRequiredAsync`,它...顾名思义😛

`AuthTokenHelpers/ITokenRefresher.cs`

```
/// <summary>
/// Provides an easy way to ensure the user's access token is up to date. 
/// </summary>
public interface ITokenRefresher
{
    /// <summary>
    /// Tries to refresh the current user's access token if required.
    /// </summary>
    /// <param name="refreshToken">The current refresh token.</param>
    /// <param name="expiresAt">The current token expiration information.</param>
    /// <param name="ct">The async cancellation token.</param>
    /// <returns><code>True</code> if refresh is not needed or executed successfully, <code>False</code> otherwise.</returns>
    Task<TokenRefreshResult> TryRefreshTokenIfRequiredAsync(
        string refreshToken,
        string expiresAt,
        CancellationToken ct);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看它的实现。

`AuthTokenHelpers/TokenRefresher.cs`

```
/// <inheritdoc />
public class TokenRefresher : ITokenRefresher
{
    private static readonly TimeSpan TokenRefreshThreshold = TimeSpan.FromSeconds(30);

    private readonly HttpClient _httpClient;
    private readonly IDiscoveryCache _discoveryCache;
    private readonly ILogger<TokenRefresher> _logger;

    public TokenRefresher(
        HttpClient httpClient,
        IDiscoveryCache discoveryCache,
        ILogger<TokenRefresher> logger)
    {
        _httpClient = httpClient;
        _discoveryCache = discoveryCache;
        _logger = logger;
    }

    /// <inheritdoc />
    public async Task<TokenRefreshResult> TryRefreshTokenIfRequiredAsync(
        string refreshToken,
        string expiresAt,
        CancellationToken ct)
    {
        if (string.IsNullOrWhiteSpace(refreshToken))
        {
            return TokenRefreshResult.Failed();
        }

        if (!DateTime.TryParse(expiresAt, out var expiresAtDate) || expiresAtDate >= GetRefreshThreshold())
        {
            return TokenRefreshResult.NoRefreshNeeded();
        }

        var discovered = await _discoveryCache.GetAsync();
        var tokenResult = await _httpClient.RequestRefreshTokenAsync(
            new RefreshTokenRequest
            {
                Address = discovered.TokenEndpoint,
                ClientId = "WebFrontend",
                ClientSecret = "secret",
                RefreshToken = refreshToken
            }, ct);

        if (tokenResult.IsError)
        {
            _logger.LogDebug(
                "Unable to refresh token, reason: {refreshTokenErrorDescription}",
                tokenResult.ErrorDescription);
            return TokenRefreshResult.Failed();
        }

        var newAccessToken = tokenResult.AccessToken;
        var newRefreshToken = tokenResult.RefreshToken;
        var newExpiresAt = CalculateNewExpiresAt(tokenResult.ExpiresIn);

        return TokenRefreshResult.Success(newAccessToken, newRefreshToken, newExpiresAt);
    }

    private static string CalculateNewExpiresAt(int expiresIn)
    {
        // TODO: abstract usages of DateTime to ease unit tests
        return (DateTime.UtcNow + TimeSpan.FromSeconds(expiresIn)).ToString("o", CultureInfo.InvariantCulture);
    }

    private static DateTime GetRefreshThreshold()
    {
        // TODO: abstract usages of DateTime to ease unit tests
        return DateTime.UtcNow + TokenRefreshThreshold;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

代码并不复杂，但是让我们仔细阅读一下，因为其中有一些有趣的地方。

看一下构造函数，注入的依赖项中有两个是众所周知的，但另一个却不是。`IDiscoveryCache`是在`IdentityModel` NuGet 包中提供的一个助手，允许我们从 OpenID Connect 提供者的发现端点获取信息(并缓存它),这样我们就可以在对提供者的其他请求中使用它，在本例中，用于令牌刷新。

进入`TryRefreshTokenIfRequiredAsync`方法实现，我们有:

*   检查我们是否有刷新令牌，如果没有，这是一个失败。
*   检查令牌到期日期是否低于阈值，如果不是，我们可以立即返回一个`NoRefreshNeeded`。
*   用`_discoveryCache`获取提供商信息。
*   请求刷新令牌，也使用我们在`Startup`类中配置的信息(是的，它应该来自配置，而不是硬编码在这里🙃).注意，`RequestRefreshTokenAsync`是来自`IdentityModel` NuGet 包的扩展方法，简化了刷新令牌请求的创建。
*   如果令牌刷新成功，则返回包含所需信息的成功，否则返回失败。

对于令牌刷新逻辑来说应该是这样的。虽然不是很多，但仍然是很好的一段代码。如果我们需要在 PlayBall 应用程序的其他组件中重用它，也许我们可以将它放在一个共享包中。

> PS:不要忘记在 DI 容器中注册所有需要的服务。

## 其他

最后，我们看到了如何将用户身份验证委托给 auth 服务，同时获取和维护令牌以访问后台 API(目前只有组管理 API)。我们还看到了如何让 CSRF 令牌基础设施准备好使用 SPA，而不是使用 Razor 进行常见的服务器端渲染。在下一集里，我们将总结这个子系列，通过看到前端的变化来很好地与 BFF 一起玩，并集成我们到目前为止开发的所有东西。

作为提醒(我可能会在下一集重复)，所有这些只是解决这个问题的(可能)许多可能方法中的一种，所以如果你有不同的想法，并且认为它更好，那就去做吧🙂(让我知道，这样我可以改进我的)。

帖子中的链接:

*   [跨站点请求伪造(CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))
*   [第 023 集-整合身份服务器 4 -第 3 部分-API-ASP.NET 核心:从 0 到过度杀伤](https://blog.codingmilitia.com/2019/06/16/aspnet-023-from-zero-to-overkill-integrating-identityserver4-part3-api)

这个子系列帖子的源代码分散在[“编码民兵:ASP.NET 核心-从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的一堆仓库中，标记为`episode021`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode021)
*   [群组管理](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode021)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode021)

感谢分享和反馈！

谢谢你的来访，西阿兹！