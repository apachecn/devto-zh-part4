# 第 022 集-整合身份服务器 4 -第 2 部分-认证服务-ASP.NET 核心:从 0 到过度杀戮

> 原文：<https://dev.to/joaofbantunes/episode-022-integrating-identityserver4-part-2-auth-service-asp-net-core-from-0-to-overkill-2383>

在这一集中，我们开始查看在我们的应用程序中集成 IdentityServer4 所需的代码，即与我们之前开发的身份验证服务集成。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/EWFsiLSWMjg](https://www.youtube.com/embed/EWFsiLSWMjg)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在关于将 IdentityServer(或者更准确地说，身份验证和授权)集成到 PlayBall 应用程序的子系列文章的第一部分中，我们将了解如何配置它以与 ASP.NET 核心身份很好地配合，设置 OpenId Connect / OAuth 2.0 位，以及确保它的依赖关系得到处理(就像一个必需的数据存储)。

尽管它在子系列的标题中，但这是唯一明确使用 IdentityServer 的部分。这个子系列的其他部分只需要知道 auth 服务是一个符合 OpenId Connect 的身份提供者。这意味着，只要我们使用另一个 OpenId Connect 实现身份提供者，就应该很容易替换 auth 服务，而不会影响其他服务。

## 配置身份服务器 4

因此，让我们开始配置 IdentityServer。当然，这将非常侧重于我们在本系列中构建的示例应用程序，因此它不会利用 IdentityServer 为我们提供的所有配置选项。

### 获取数据包

我们需要安装一些包来使用 IdentityServer。核心包简单命名为 [IdentityServer4](https://www.nuget.org/packages/IdentityServer4/) 。正如所料，它包含了 IdentityServer4 的核心特性。然而，IdentityServer4 是非常可扩展的，因此我们可以添加基于它的其他包(如果需要，也可以创建我们自己的包)。

在接下来的部分中，根据需要，我们将看到一些其他的包被添加了额外的特性。

### 配置的第一位

和许多其他与 ASP.NET 核心集成的东西一样，IdentityServer4 是通过`Startup`类在`ConfigureServices`和`Configure`方法中添加到应用程序中的。

从`Startup.Configure`开始，这是我们向请求处理管道添加 IdentityServer 的地方，因此它可以为我们提供 OpenId Connect / OAuth 2.0 端点。完成这一部分只需要调用`app.UseIdentityServer();`。

在`Startup.ConfigureServices`中，我们将为 IdentityServer 配置其他所有东西，但我没有让`Startup`类继续像这样增长，而是用扩展方法将一些已经存在的东西提取到辅助类中，在`IoC`文件夹中——ASP.NET 核心身份配置被移到了`IdentityExtensions`，本地化配置被移到了`LocalizationExtensions`，MVC 配置有了自己的`MvcExtensions`。按照这种方法，IdentityServer 的配置在`IdentityServerExtensions`类中创建。

为了快速参考，`ConfigureServices`现在看起来是这样的:

`Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddConfiguredMvc()
        .AddConfiguredLocalization()
        .AddConfiguredIdentity(_configuration)
        .ConfigureApplicationCookie(options =>
        {
            options.LoginPath = "/Login";
            options.LogoutPath = "/Logout";
            options.AccessDeniedPath = "/AccessDenied";
        })
        .AddConfiguredIdentityServer(_environment, _configuration);
} 
```

Enter fullscreen mode Exit fullscreen mode

在接下来的部分中，我们将看到`IdentityServerExtensions.AddConfiguredIdentityServer`的实现。

### AddIdentityServer

在`IdentityServerExtensions`类中，我们为`IServiceCollection`提供了一个扩展方法，名为`AddConfiguredIdentityServer`。安装了 IdentityServer4 NuGet 包后，当我们点击`IServiceCollection`时，我们可以访问`AddIdentityServer`，这是配置的入口点。这个方法有几个重载，一个接收一个`Action<IdentityServerOptions>`，另一个获得一个`IConfiguration`，这个`IConfiguration`应该映射到一个`IdentityServerOptions`。现在，为了学习的简单性，我们将使用基于`Action`的重载，但是另一个应该是配置使用的更好的选择。

`AddIdentityServer`与`IdentityServerOptions`允许我们配置一堆东西，但目前我们配置的唯一东西是允许 IdentityServer 引发事件，因此我们可以在构建应用程序时更好地理解发生了什么。这些事件不像日志，无论如何都会被记录，当然这取决于配置的级别。这些事件更特定于 IdentityServer 在身份验证流程方面所做的工作，例如当用户通过身份验证并创建令牌时，或者当用户同意所请求的信息时，等等。更多信息[在这里](http://docs.identityserver.io/en/latest/topics/events.html)。

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(options =>
            {
                options.Events.RaiseErrorEvents = true;
                options.Events.RaiseInformationEvents = true;
                options.Events.RaiseFailureEvents = true;
                options.Events.RaiseSuccessEvents = true;
            })
            // ... 
```

Enter fullscreen mode Exit fullscreen mode

作为参考，下面我们可以看到一个打印到控制台的示例事件:

```
info: IdentityServer4.Validation.TokenRequestValidator[0]
      Token request validation success
{
        "ClientId": "WebFrontend",
        "GrantType": "authorization_code",
        "AuthorizationCode": "46eb980eebde8b886b2b0fa3d4da465f9c3bfb374e8adc3b4342eaf0dbe7c04e",
        "Raw": {
          "client_id": "WebFrontend",
          "client_secret": "***REDACTED***",
          "code": "46eb980eebde8b886b2b0fa3d4da465f9c3bfb374e8adc3b4342eaf0dbe7c04e",
          "grant_type": "authorization_code",
          "redirect_uri": "http://localhost:5000/signin-oidc"
        }
      } 
```

Enter fullscreen mode Exit fullscreen mode

### 配置用户信息、API 和客户端

在调用了`AddIdentityServer`之后，我们得到了一个`IIdentityServerBuilder`的实例，我们用它来配置更多的东西。我们将从一些最有趣的部分开始，即可供客户端使用的用户信息、将使用 IdentityServer 提供的令牌访问的 API 以及将使用 auth 服务作为身份验证方式的客户端(或 OpenId Connect 中的依赖方)。

虽然可能不是生产就绪应用程序的理想选择，但我们将在所有这些配置的内存存储中使用，在探索过程中保持简单。

#### 用户信息

让我们从可供客户端应用程序使用的用户信息开始。我们通过调用`AddInMemoryIdentityResources`，传入一个`IdentityResource`的集合来进行配置。这些`IdentityResource`表示将提供给客户的声明。这些资源的两个主要属性是名称和它将提供的声明列表。然后可以配置客户机，将`IdentityResource`的名称指定为它想要访问的范围，获得与该资源相关联的声明。

我们可以使用现成的，也可以自己创建。在这种情况下，我们只是简单地使用两个已经提供的资源，`OpenId`和`Profile`。

这方面的代码如下:

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(/*...*/)
            .AddInMemoryIdentityResources(GetIdentityResources())
            // ...
    }

    private static IEnumerable<IdentityResource> GetIdentityResources()
    {
        return new IdentityResource[]
        {
            new IdentityResources.OpenId(),
            new IdentityResources.Profile { Required = true }
        };
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

只是快速不上`Required = true`上面看到的。这使得该范围是强制性的，否则用户可以禁止客户端应用程序访问该范围，并且我们希望确保应用程序能够完全访问所需的数据。

`OpenId`范围以`sub`的形式提供声明，这是用户的唯一标识符。`Profile`范围将提供`name`、`nickname`、`picture`等索赔。

#### 原料药

配置 API 资源与配置身份资源非常相似。在`IIdentityServerBuilder`上，我们可以调用`AddInMemoryApiResources`，为它提供代表我们想要保护的 API 的`ApiResource`。

让我们快速看一下代码:

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(/*...*/)
            // ...
            .AddInMemoryApiResources(GetApis())
            // ...
    }

    private static IEnumerable<ApiResource> GetApis()
    {
        var apiResource = new ApiResource("GroupManagement", "Group Management");
        apiResource.Scopes.First().Required = true;
        return new[]
        {
            apiResource
        };
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

当创建一个`ApiResource`时，我们给它起的名字将是客户端应用程序将用作作用域的名字，除非我们设置了`Scopes`属性。如果我们想要创建一个 API 的多个作用域，例如，一个只读作用域和另一个允许写入的作用域，这可能是有用的。

我们可以在`ApiResource`中设置更多的东西，比如应该包含在发送给 API 的访问令牌中的用户声明；秘密，所以如果需要的话，API 可以使用自省 API 来验证访问令牌——通常当使用 [JWT](https://jwt.io/) 访问令牌(可能更常见)时，这是不需要的，因为它可以在不发出请求的情况下被验证，但是如果我们要使用[引用令牌](http://docs.identityserver.io/en/latest/topics/reference_tokens.html)，那么我们将需要这个额外的请求。

#### 客户端

最后，让我们配置客户端应用程序(依赖方)。再次，`IIdentityServerBuilder`为我们提供了对`AddInMemoryClients`的访问，我们向其提供了一组客户端。

让我们检查一下代码，然后浏览一遍:

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(/*...*/)
            // ...
            .AddInMemoryClients(GetClients())
            // ...
    }

    private static IEnumerable<Client> GetClients()
    {
        return new[]
        {
            new Client
            {
                ClientId = "WebFrontend",
                AllowedGrantTypes = GrantTypes.Code,
                ClientSecrets = {new Secret("secret".Sha256())},
                RedirectUris = new[] {"http://localhost:5000/signin-oidc"},
                RefreshTokenUsage = TokenUsage.OneTimeOnly,
                AllowedScopes =
                {
                    IdentityServerConstants.StandardScopes.OpenId,
                    IdentityServerConstants.StandardScopes.Profile,
                    "GroupManagement"
                },
                AllowOfflineAccess = true,
                AccessTokenLifetime = 60,
                RefreshTokenExpiration = TokenExpiration.Sliding
            }
        };
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，在这篇文章中，我们可以看到的选项只是其中的一部分，还有更多可供配置。让我们快速浏览一下我们在`Client`中设置的属性。

*   `ClientId` -客户端的标识符，它将在启动认证流时使用。
*   `AllowedGrantTypes` -客户被允许的授权类型。正是通过这些授权，我们指定了客户端可以使用的流的种类。`GrantTypes`类提供了一些常见的授权类型，通过使用`GrantTypes.Code`,我们说客户端可以使用我们在上一集讨论过的授权代码流。
*   `ClientSecrets` -在客户端和授权服务之间的一些交互中使用的客户端秘密，例如，当用授权码交换访问令牌时。
*   `RedirectUris` -认证流程成功后，客户端应用程序可能用作重定向目标的 URIs。
*   `RefreshTokenUsage` -指示刷新令牌是在使用后保持原样，还是在使用后提供替换(寿命不受影响，只是返回不同的`string`来表示相同的令牌)。
*   `AllowedScopes` -客户端可能请求访问的范围。注意，我们使用了两个更通用的接口，外加一个提供组管理 API 访问的接口。
*   `AllowOfflineAccess` -这个名字不是很明显，但是将它设置为`true`将允许客户端使用刷新令牌。
*   `AccessTokenLifetime` -访问令牌有效的时间(秒)。当然这 60 秒有点太短了，但这只是为了让我们看到一切都在工作。
*   `RefreshTokenExpiration` -指示刷新令牌是在特定时间点过期，还是在每次使用时延长其生命周期。

### 融入 ASP.NET 核心身份

让我们继续看看 IdentityServer4 配置及其与 ASP.NET 核心身份的集成。

由于我们希望将 IdentityServer 与我们已经准备好的用于身份验证的 ASP.NET 身份位集成在一起，我们可以添加 [IdentityServer4。AspNetIdentity](https://www.nuget.org/packages/IdentityServer4.AspNetIdentity) 包为我们解决了这个问题。然后，我们需要做的就是在我们拥有的`IIdentityServerBuilder`实例上调用`AddAspNetIdentity`。

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(/*...*/)
            // ...
            .AddAspNetIdentity<PlayBallUser>()
            // ...
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

### 将运营门店与 EF 核心整合

到目前为止，我们的配置一直使用内存中的一切。虽然它在我们到目前为止看到的案例中对测试有效，但如果我们不使用持久性，即使我们只是在测试，也有其他事情会令人讨厌。一个这样的例子是操作数据。

IdentityServer 中的操作数据是关于刷新标记、引用标记、临时流数据等信息。如果我们不为所有这些配置一个持久存储，它将在内存中，并且每次我们重新启动 auth 服务或者如果我们使用它的多个实例，它将不会很好地工作，所以我们设置这一点很重要。

我们可以实现自己的操作存储，但是 IdentityServer 已经提供了基于实体框架核心的实现。要使用它，我们需要安装[身份服务器 4。EntityFramework](https://www.nuget.org/packages/IdentityServer4.EntityFramework) NuGet 包。

现在要使用它，我们需要经历几个步骤:

*   在我们的`IdentityServerExtensions`类中配置它的用法
*   创建迁移，以便创建和更新数据库

#### 配置运营门店实现

让我们从最简单的部分开始，将可操作的商店配置添加到启动过程中。

同样，我们利用了`IIdentityServerBuilder`，这里我们有可用的`AddOperationalStore`扩展方法。在这个方法中，我们可以设置`DbContext`。

在下面的代码中，我们使用添加到配置中的连接字符串来配置`DbContext`，并将迁移程序集设置为当前程序集(auth service 项目)。需要这个迁移汇编位，因为它不同于包含`DbContext`的汇编。

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(/*...*/)
            // ...
            .AddOperationalStore(options =>
            {
                options.ConfigureDbContext = b =>
                    b.UseNpgsql(configuration.GetConnectionString("PersistedGrantDbContext"),
                        npgOptions =>
                            npgOptions.MigrationsAssembly(
                                typeof(IdentityServerExtensions).Assembly.GetName().Name));
            })
            .AddInMemoryCaching();

        // ...
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

另一个值得注意的有趣的事情是对`AddInMemoryCaching`的调用。这只是为了避免对数据库的持续打击(虽然我认为这不是用于运营商店，只是其他一些)。

#### 创建运营门店迁移

为运营商店创建迁移与我们在[第 011 集](https://blog.codingmilitia.com/2019/01/16/aspnet-011-from-zero-to-overkill-data-access-with-entity-framework-core)中看到的任务类似。

创建用于操作存储的初始迁移的命令如下:

```
dotnet ef migrations add InitialIdentityServerPersistedGrantDbMigration -c PersistedGrantDbContext -o "Migrations\IdentityServer\PersistentGrantDb" 
```

Enter fullscreen mode Exit fullscreen mode

在该命令中，我们指出了我们希望为其创建迁移的`DbContext`，以及我们希望将生成的文件放入的位置。

同时，我们还可以将迁移执行添加到应用程序启动中。要做到这一点，我们只需要对`DatabaseExtensions`类做一个小小的调整，在`AuthDbContext`迁移执行之后添加`PersistedGrantDbContext`迁移执行。

`StartupHelpers\DatabaseExtensions.cs`

```
internal static class DatabaseExtensions
{
    internal static async Task EnsureDbUpToDateAsync(this IWebHost host)
    {
        using (var scope = host.Services.CreateScope())
        {
            var hostingEnvironment = scope.ServiceProvider.GetRequiredService<IHostingEnvironment>();
            if (hostingEnvironment.IsDevelopment() || hostingEnvironment.IsEnvironment("DockerDevelopment"))
            {
                var authDbContext = scope.ServiceProvider.GetRequiredService<AuthDbContext>();
                await authDbContext.Database.MigrateAsync();

                var grantDbContext = scope.ServiceProvider.GetRequiredService<PersistedGrantDbContext>();
                await grantDbContext.Database.MigrateAsync();
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 签署凭证

我们用 IdentityServer 配置的最后一样东西是签名凭证。

IdentityServer 使用签名凭据对令牌进行签名，因此可以检查它们是否被篡改。auth 服务将使用私钥对令牌进行签名，令牌的接收者可以使用公钥对其进行验证。

现在我不想花时间在这上面(尽管我们最终会需要)，所以在这种情况下我们可以使用`AddDeveloperSigningCredential`扩展方法，这将创建一些开发时间签名凭证。

`IoC/IdentityServerExtensions.cs`

```
public static class IdentityServerExtensions
{
    public static IServiceCollection AddConfiguredIdentityServer(/*...*/)
    {
        var builder = services.AddIdentityServer(/*...*/)
            // ...

        if (environment.IsDevelopment())
        {
            builder.AddDeveloperSigningCredential();
        }
        else
        {
            throw new Exception("need to configure key material");
        }

        // ...
    }
    // ... 
```

Enter fullscreen mode Exit fullscreen mode

将来我们需要添加一些真实的凭证，使用`AddSigningCredential`扩展方法，我们可以提供一个证书、一个 RSA 密钥或其他通过重载可用的替代物。

### 同意屏幕

我们不需要同意屏幕，因为 auth 服务将仅由 PlayBall 应用程序的其他组件使用，而不是作为通用的 SSO 服务(像 Google 或 Microsoft 登录)。无论如何，为了看到它的运行，我基于 IdentityServer 人员提供的代码创建了一个简单的(远非完美的)屏幕。

有一个包含 UI 元素的存储库可用于 IdentityServer， [IdentityServer4。快速入门. UI](https://github.com/IdentityServer/IdentityServer4.Quickstart.UI) 。它包含一个基于 ASP.NET 核心 MVC 的实现。因为我们在 auth 服务中使用 Razor 页面，所以我基于可用的代码进行开发，根据需要调整 Razor 页面。

如果你想检查它，代码在`Pages\Consent`文件夹中，但我不会在这里写，因为它有很多无聊的代码，设置表单，检查用户的选项，进行重定向以继续流程等等。如果您看一下代码，请随意提出您遇到的任何问题。

## 其他

这一集到此结束，我们已经在认证服务端准备好了 IdentityServer4 集成。在下一集，我们将了解如何配置组管理 API，以要求令牌来认证每个请求。

帖子中的链接:

*   [身份服务器 4 文档](http://docs.identityserver.io/en/latest/index.html)
*   [身份服务器 4 文档-事件](http://docs.identityserver.io/en/latest/topics/events.html)
*   [身份服务器 4 文档-引用令牌](http://docs.identityserver.io/en/latest/topics/reference_tokens.html)
*   [JWT](https://jwt.io/)
*   [身份服务器 4。快速启动. UI 库](https://github.com/IdentityServer/IdentityServer4.Quickstart.UI)
*   [身份服务器 4 获取包](https://www.nuget.org/packages/IdentityServer4/)
*   [身份服务器 4。AspNetIdentity NuGet 包](https://www.nuget.org/packages/IdentityServer4.AspNetIdentity)
*   [身份服务器 4。实体框架 NuGet 包](https://www.nuget.org/packages/IdentityServer4.EntityFramework)
*   [第 011 集——实体框架核心的数据访问——ASP.NET 核心:从 0 到矫枉过正](https://blog.codingmilitia.com/2019/01/16/aspnet-011-from-zero-to-overkill-data-access-with-entity-framework-core)。

这个子系列帖子的源代码分散在[“编码民兵:ASP.NET 核心-从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的一堆仓库中，标记为`episode021`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode021)
*   [群组管理](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode021)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode021)

感谢分享和反馈！

谢谢你的来访，西阿兹！