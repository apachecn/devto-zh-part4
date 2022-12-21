# 第 028 集-多服务实例调整-ASP.NET 核心:从 0 到过度杀伤

> 原文：<https://dev.to/joaofbantunes/episode-028-multiple-service-instances-tweaks-asp-net-core-from-0-to-overkill-4ald>

在这一集中，我们将了解当我们想要运行一个 ASP.NET 核心应用程序的多个实例时需要记住的一些事情，即数据保护配置，以及它如何影响我们的应用程序，即使我们没有直接使用它。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/_-eV5ZcCwag](https://www.youtube.com/embed/_-eV5ZcCwag)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

由于我们的应用程序作为一个整体工作，由于 Docker 和 Docker Compose，我们可以通过一种简单的方式来运行，我们可以利用它来检查一些我们直到现在才考虑的场景，因为我们只是在开发模式下运行所有组件。

要考虑的一个重要场景是让每个组件的多个实例并行运行。这是一个需要考虑的非常重要的问题，尤其是在生产中，不仅要在实例之间共享负载，还要有冗余，以防其中一个实例出现故障。

在一个应用程序有多个实例的情况下，ASP.NET 核心的数据保护 API 的主题浮出水面，但这是我们迄今为止真正没有费心去做的事情。是时候改变了🙂。

## 什么是 ASP.NET 核心中的数据保护，我为什么要关注？

在 ASP.NET 核心中，数据保护的概念(尽管这个术语很模糊)与确保某一条信息不被篡改的需要联系在一起，即使它离开了我们服务器的安全性。

这种需求的一个实际例子是 cookies。当框架设置身份验证 cookie(可能包括用户拥有的权限)时，它需要确保没有人会去更改 cookie，添加比实际更多的权限。为了确保这一点，cookie 被签名(不要与加密混淆)，使用只有服务器知道的秘密。如果有人更改了 cookie，服务器将能够检测到签名不再匹配，因此 cookie 是无效的。

如果我们愿意，我们可以自己使用这些数据保护 API(我们的应用程序到目前为止还没有)，但是即使我们不这样做，框架也会在幕后使用它们，比如 cookies 或 CSRF 令牌。

你可以在 ASP.NET 官方核心文件中读到更多相关信息。

## 为什么一直到现在都很好用？

开箱即用，数据保护 API 将工作得很好，因为密钥将自动生成。存储密钥的位置取决于一些条件。在我们的场景(Linux Docker 容器)中，密钥被生成，但只留在内存中，所以当应用程序关闭时，那组密钥就丢失了(更多细节[在这里](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/configuration/default-settings?view=aspnetcore-2.2))。

到目前为止，这还没有给我们带来太多的麻烦，但我们可以很容易地改变这一点。让我们转到 Docker Compose 文件，为 frontend 添加另一个后端实例，并进行测试。

`Deployment\docker\docker-compose.dev.yml`

```
# ...
bff:
    build: ../../WebFrontend/server/.
    image: codingmilitia/webfrontend/bff:latest
    networks:
        - internal-network
    depends_on:
        - groupmanagement
    environment:
        - ASPNETCORE_ENVIRONMENT=DockerDevelopment
    # this is not the right way to implement multiple nodes with compose, but it's the quickest for the demo we're doing
    another-bff:
    build: ../../WebFrontend/server/.
    image: codingmilitia/webfrontend/bff:latest
    networks:
        - internal-network
    depends_on:
        - groupmanagement
    environment:
        - ASPNETCORE_ENVIRONMENT=DockerDevelopment
# ... 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要调整 HAProxy 配置，将请求路由到新的 BFF 实例。

`Deployment\docker\reverse-proxy\haproxy.cfg`

```
# ...
backend bff
    option forwardfor
    server bff1 bff:80
    server bff2 another-bff:80
# ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们尝试运行应用程序，乍一看，它似乎运行得很好，但是当我们尝试登录时，当 auth 服务重定向回 BFF 时，事情并不像预期的那样工作。这是因为，作为我们正在使用的的 [OpenID 连接流的一部分，传入的一些参数(如状态)是有符号的，此外，当我们得到结果时，还会创建一些 cookies 用于关联。这在以前工作得很好，因为我们只有一个 BFF 实例在运行，所以它总是使用同一套内存中的键来处理它。现在我们有了两个实例，如果流在一个实例中开始，但在另一个实例中结束，那么一切都会失败，因为这两个实例使用不同的数据保护键。](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

## 使用共享目录存储数据保护密钥

我们有一系列选项来跨服务实例共享密钥，比如文件系统、EF 核心、Redis 等等。对于我们当前的场景，我们将采用一种简单的方法，使用一个共享目录。

当我们在 Docker 中运行时，为了让容器共享一个目录，我们需要创建一个卷。让我们回到 Docker Compose 文件并这样做。

`Deployment\docker\docker-compose.dev.yml`

```
# ...
services:
    # ...
    bff:
        # ...
        volumes:
            - bff-data-protection-keys:/var/lib/bff/dataprotectionkeys
    another-bff:
        # ...
        volumes:
            - bff-data-protection-keys:/var/lib/bff/dataprotectionkeys
    # ...
volumes:
    bff-data-protection-keys:
# ... 
```

Enter fullscreen mode Exit fullscreen mode

在文件的结尾，我们声明了一个命名卷。然后，对于`bff`和`another-bff`服务，我们将该卷映射到一个特定的文件夹。

现在我们需要配置应用程序将密钥存储在共享目录中。转到 BFF 项目，我们可以转到`appsettings.DockerDevelopment.json`文件并添加以下内容:

`WebFrontend\server\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\appsettings.DockerDevelopment.json`

```
//  ...  "DataProtectionSettings":  {  "Location":  "/var/lib/bff/dataprotectionkeys"  }  //  ... 
```

Enter fullscreen mode Exit fullscreen mode

然后在`Startup`类:
`WebFrontend\server\src\CodingMilitia.PlayBall.WebFrontend.BackForFront.Web\Startup.cs`

```
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var dataProtectionKeysLocation = _configuration.GetSection<DataProtectionSettings>(nameof(DataProtectionSettings)).Location;
    if (!string.IsNullOrWhiteSpace(dataProtectionKeysLocation))
    {
        services
            .AddDataProtection()
            .PersistKeysToFileSystem(new DirectoryInfo(dataProtectionKeysLocation));
            // TODO: encrypt the keys
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，如果我们再次运行应用程序，BFF 的两个实例将能够共享密钥，因此登录过程现在将按预期工作。

**注意:**正如您从 TODO 注释中可能已经注意到的，这些键将以明文形式存储在已配置的文件夹中。理想情况下，我们应该对它们进行加密，但是此时我不想弄乱证书，所以这将是另一个话题。

## 标识服务器签名凭证

由于我们在共享密钥的主题中，也有身份验证服务中 IdentityServer 使用的签名凭证的情况，这与我们看到的问题完全相同。

首先，如果我们真的部署它，我们将需要实际的签名凭证，我们将把它安全地存储在某个地方。即便如此，如果我们想在 Docker 开发环境中使用开发签名凭证，我们可以使用完全相同的策略——创建一个卷，映射到容器中的一个文件夹并对其进行配置，类似于:

`Auth\src\CodingMilitia.PlayBall.Auth.Web\IoC\IdentityServerExtensions.cs`

```
// ...
if (environment.IsDevelopment() || environment.IsEnvironment("DockerDevelopment"))
{
    builder.AddDeveloperSigningCredential(
        filename: configuration
            .GetSection<SigningCredentialSettings>(nameof(SigningCredentialSettings))
            .DeveloperCredentialFilePath);
}
else
{
    throw new Exception("need to configure key material");
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

这一集就到这里。我们快速地看了一下 ASP.NET 核心数据保护，不是从使用 API 的角度(也许是将来要做的事情)，而是从配置的角度，因为即使我们没有直接使用它，框架肯定是要使用的，并且它对我们的应用程序的顺利运行是至关重要的。

帖子中的链接:

*   [ASP.NET 核心数据保护](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/introduction?view=aspnetcore-2.2)
*   [ASP.NET 核心中的数据保护密钥管理和生命周期](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/configuration/default-settings?view=aspnetcore-2.2)
*   [ASP.NET 核心区的主要存储提供商](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/implementation/key-storage-providers?view=aspnetcore-2.2)
*   [ASP.NET 核心中的静态密钥加密](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/implementation/key-encryption-at-rest?view=aspnetcore-2.2)
*   [OpenID 连接-授权码流-认证请求](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

这篇文章的源代码分布在[“编码民兵:ASP.NET 核心——从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的存储库中，标记为`episode028`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode028)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode028)
*   [部署](https://github.com/AspNetCoreFromZeroToOverkill/Deployment/tree/episode028)

感谢分享和反馈！

谢谢你的来访，西阿兹！