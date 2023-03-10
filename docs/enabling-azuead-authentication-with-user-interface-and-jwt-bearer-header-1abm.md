# 使用用户界面和 JWT 载体报头启用 AzueAD 认证

> 原文：<https://dev.to/andbe/enabling-azuead-authentication-with-user-interface-and-jwt-bearer-header-1abm>

的。NET 核心框架允许多种身份验证方案，以便针对 AzureAD 和脸书进行身份验证。典型的基于 cookie 的选项非常适合用户界面，JWT 是 API(无用户)认证的首选。如果您想混合这些方案的身份验证方式，该怎么办？

我已经建立了一个解决方案，它的用户界面由 Razor Pages 和几个端点构建，当从 Azure Function 应用程序调用时，我打算运行这些端点。当然，我可以将应用程序拆分成几个应用程序，从某些角度来看，这很有意义，但会增加单个应用程序的复杂性。这种方法还需要一个额外的 Azure 应用服务，但我并不真的需要。

配置应用程序以支持多种方案，结果比我最初预期的要不直观。我的第一种方法包括设置不同的方案，并尝试用 API 控制器上的 PolicyBuilder 和/或[Authorize]属性来实施它们，但没有成功。

结果发现拼图少了一小块。和往常一样，当在 StackPath 上偶然发现[解决方案时，很明显缺少了什么。](https://stackoverflow.com/a/51897159/446730)

关键是添加另一个 PolicyScheme 并检查授权 HTTP 头，如果它以“Bearer”开头，则转发方案选择器:

```
services.AddPolicyScheme("dynamic", "Dynamic Policy scheme", options =>
{
    // https://stackoverflow.com/questions/45695382/how-do-i-setup-multiple-auth-schemes-in-asp-net-core-2-0/51897159#51897159
    options.ForwardDefaultSelector = context =>
    {
        var header = context.Request.Headers["Authorization"].FirstOrDefault();
        if (header?.StartsWith("Bearer") == true)
        {
            return JwtBearerDefaults.AuthenticationScheme;
        }

        return OpenIdConnectDefaults.AuthenticationScheme;
    };
}) 
```

将动态方案选择器设为默认:

```
services.AddAuthentication(options =>
{

    options.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultAuthenticateScheme = "dynamic";
    options.DefaultChallengeScheme = "dynamic";
}) 
```

现在，每个请求将首先通过`DefaultAuthenticationScheme`，它将决定要使用的认证方案。

还有其他方法可以达到这个目的吗？你知道更好的解决办法吗？请分享！