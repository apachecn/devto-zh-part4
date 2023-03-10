# 第 026 集 Docker 入门-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-026-getting-started-with-docker-asp-net-core-from-0-to-overkill-152>

在这一集中，我们将为我们的应用程序创建 Docker 容器，目的是使它们更容易作为一个整体运行。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/GZmGTjAam6w](https://www.youtube.com/embed/GZmGTjAam6w)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在过去的几集里，我们一直在一点一点地构建我们的 PlayBall 应用程序，最终在最新的几集里所有的东西都可以一起工作了(尽管离有用的东西还很远)。

尽管我们现在可以让所有东西都协同工作，但我们必须单独开始，这意味着:

*   确保 PostgreSQL 已启动并正在运行
*   启动身份验证服务
*   启动组管理 API
*   为前端启动后端
*   启动单页应用程序

当我们正在开发，想要调试和所有这些，这是有意义的，但如果我们只是想让它运行起来，看看它是如何作为一个完整的应用程序工作的，它会变得令人厌倦。

一种可能的方法是将我们的应用程序容器化，这样我们就可以用一个命令轻松地启动整个运行过程。

Docker 可能会在本系列的晚些时候出现，但这是一个非常有趣的话题，它可以简化我们的生活，我认为我们应该现在就完成它。考虑到 Docker 容器的部署也是开发 ASP.NET 核心应用程序时的一个可能目标，这并不是浪费工作🙂。

在这一集里，我们将从创建当前所有应用程序(auth、group management、BFF 和 SPA)的容器开始，然后在下一集里，我们将通过引入 [Docker Compose](https://docs.docker.com/compose/) 让它们作为一个完整的应用程序运行。

要安装 Docker，请检查他们的[文档](https://docs.docker.com/install/)。当我在 Ubuntu 和 MacOS 上安装时，我遵循了这些文档。对于 Windows，我的故事并不简单，因为在我写这篇文章的时候，Docker for Windows 需要 Hyper-V，但是我想要 VirtualBox...而且它们在同一个系统上也不能很好地工作，所以我最终选择了一个更“hacky”的解决方案，基于 vagger 和 VirtualBox。有了 WSL2，Hyper-V 就不再需要了([看这里](https://engineering.docker.com/2019/06/docker-hearts-wsl-2/)，所以我真的很期待它的发布。

## 初步调整

在开始这篇文章之前，给普通读者一个提示。

你可能还记得，在最近几集里，当所有的东西都一起工作时，我们在代码里有一堆硬编码的配置，例如，在 BFF 里，我们直接在`Startup`类里有组管理 API 的端点。

当在不同的环境中运行应用程序时，这些硬编码的配置将不起作用，就像 Docker 的情况一样(正如我所说的那样)。出于这个原因，我将这样的配置移到了`appsettings.json`文件(或`appsettings.Development.json`)中，以允许我们根据环境的需要进行调整。

我不打算一一介绍，但仅举一个例子，BFF 中的 OpenID Connect 配置现在看起来像这样:

`Startup.cs`

```
// ...
.AddOpenIdConnect("oidc", options =>
{
    var authServiceConfig = _configuration.GetSection<AuthServiceSettings>("AuthServiceSettings");

    options.SignInScheme = "Cookies";

    options.Authority = authServiceConfig.Authority;
    options.RequireHttpsMetadata = authServiceConfig.RequireHttpsMetadata;

    options.ClientId = authServiceConfig.ClientId;
    options.ClientSecret = authServiceConfig.ClientSecret;
    options.ResponseType = OidcConstants.ResponseTypes.Code;

    options.SaveTokens = true;
    options.GetClaimsFromUserInfoEndpoint = true;

    options.Scope.Add("GroupManagement");
    options.Scope.Add(OidcConstants.StandardScopes.OfflineAccess);

    options.CallbackPath = "/api/signin-oidc";

    options.Events.OnRedirectToIdentityProvider = context =>
    {
        if (!context.HttpContext.Request.Path.StartsWithSegments("/api/auth/login"))
        {
            context.HttpContext.Response.StatusCode = 401;
            context.HandleResponse();
        }

        return Task.CompletedTask;
    };
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

并且`appsettings.Development.json`有一个新的段`AuthServiceSettings`。

`appsettings.Development.json`

```
//  ...  "AuthServiceSettings":  {  "Authority":  "http://localhost:5005",  "RequireHttpsMetadata":  true,  "ClientId":  "WebFrontend",  "ClientSecret":  "secret"  }  //  ... 
```

Enter fullscreen mode Exit fullscreen mode

请注意，为了保持简单，我没有移动现在可能成为配置的所有内容，只是移动了现在根据环境真正需要配置的内容。随着我们继续构建应用程序，可能会提取更多的东西。

你可以看看[“编码民兵:ASP.NET 核心——从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)中的库的所有变化，标记为`episode026`

这个问题解决了，让我们从 Docker 开始吧！

## 将 ASP.NET 核心应用程序容器化

。NET Core 带来的变化使它比“经典”更适合容器化的环境。NET 框架应用程序。首先，它是跨平台的，非常适合基于 Linux 的 Docker 容器(也有[基于 Windows 的 Docker 容器](https://www.docker.com/products/windows-containers)，但我认为这不是最常用的解决方案)。其他有趣的区别是不需要 IIS 来托管应用程序，因为它可以是自托管的，也不需要配置基础架构，更加模块化并与环境变量进行现成的集成，这是配置容器化应用程序的常见方式。

闲话少说，我们开始吧！由于我们有 3 个 ASP.NET 核心应用程序，所有这些程序的步骤基本上是相同的，所以在本文中我们只看一个，使用组管理 API。ASP.NET 核心文档也有一些关于创建 Docker 容器的信息。

为了配置我们希望如何构建容器，我们创建了一个 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 来描述从代码到容器的步骤。

有了后面这些话(“从代码到容器”)，我们面临需要做出的第一个决定(尽管我认为这是一个容易做出的决定)。我们可以在将代码放入容器之前将代码构建到二进制文件中，或者我们可以在容器中构建它。我认为后者是更好的选择，有几个主要原因:

*   将代码构建为容器构建步骤，将整个构建保持在一起，而不是一部分在 Docker 之前，另一部分在 Docker 之后。
*   可能更有趣的是，这意味着构建过程将在其中运行的机器不需要安装任何特定的技术栈，只需要 Docker，因为所需的工具将是容器的一部分，通过基于另一个容器或简单地在容器内直接安装我们需要的东西。这不仅简化了部署，还简化了构建服务器甚至开发机器，因为开发人员可以只安装用于特定开发的工具，而使用由同事开发的、托管在容器中的组件。

考虑到所有这些，我们将在 group management API 存储库的根目录下创建一个名为`Dockerfile`的文件，在其中我们将负责构建代码和最终容器。

### 基础图像

我们开始浏览文件吧。

```
FROM  mcr.microsoft.com/dotnet/core/sdk:2.2-alpine  AS  builder 
```

Enter fullscreen mode Exit fullscreen mode

在第一行中，我们定义了我们想要使用的基本容器图像。因为我们将在容器构建过程中编译代码，所以我们需要一个带有。NET SDK，不然运行时就够了。我们使用的是 2.2 版本，以及基于 Alpine Linux 的映像，因为这些会占用更少的磁盘空间。你可以结帐。NET Core SDK 镜像[这里](https://hub.docker.com/_/microsoft-dotnet-core-sdk/)。

每次我们在`Dockerfile`中添加一个`FROM`，我们就定义了容器映像构建的一个阶段。容器映像构建过程可以有多个阶段，我们稍后会看到为什么这是相关的。记住这一点，我们可以给阶段起一个名字，就像我们对`AS builder`部分所做的那样，这样我们就可以在文件的后面引用它。

### 将应用程序放入图像

```
WORKDIR /app

# Copy solution and restore as distinct layers to cache dependencies
COPY ./src/CodingMilitia.PlayBall.GroupManagement.Web/*.csproj ./src/CodingMilitia.PlayBall.GroupManagement.Web/
COPY ./src/CodingMilitia.PlayBall.GroupManagement.Business/*.csproj ./src/CodingMilitia.PlayBall.GroupManagement.Business/
COPY ./src/CodingMilitia.PlayBall.GroupManagement.Business.Impl/*.csproj ./src/CodingMilitia.PlayBall.GroupManagement.Business.Impl/
COPY ./src/CodingMilitia.PlayBall.GroupManagement.Data/*.csproj ./src/CodingMilitia.PlayBall.GroupManagement.Data/
COPY ./src/CodingMilitia.PlayBall.Shared.StartupTasks/*.csproj ./src/CodingMilitia.PlayBall.Shared.StartupTasks/
COPY *.sln ./
RUN dotnet restore 
```

Enter fullscreen mode Exit fullscreen mode

在构建代码之前，我们需要将它放入图像中。我们首先创建一个工作目录，我们将在其中复制所有内容。

然后我们可以开始复制我们的资源。请注意，我们没有一次复制所有内容，而是从只复制`csproj`和`sln`文件开始，保留文件夹结构，然后执行`dotnet restore`。复制一切都可以，但这样做可以让我们利用 Docker 的层功能。

Docker 图像可以由多个层组成，甚至可以由不同的图像重用。例如，如果我们有两个基于 Alpine Linux 映像的映像，我们构建的第一个映像将下载基础层，但是第二个映像可以重用它们。

构建图像时，有些命令会导致创建新图层。`COPY`和`RUN`就是这种情况。通过创建层，如果层的内容在连续的构建之间没有变化，则可以使用层的缓存结果，而不用再次构建该层。切入正题，这意味着只要我们不改变复制到映像中的`csproj`和`sln`文件，我们就可以使用带有恢复的 NuGet 包的层的缓存版本，从而加快构建速度。如果我们一次复制所有文件，对源文件的任何更改都会导致缓存层不可用。

### 构建应用程序

```
# Publish the application
COPY . ./
WORKDIR /app/src/CodingMilitia.PlayBall.GroupManagement.Web
RUN dotnet publish -c Release -o out 
```

Enter fullscreen mode Exit fullscreen mode

恢复了所有的依赖项后，我们可以继续将项目的所有文件复制到映像中。然后，我们将工作目录更改为 runnable 项目的文件夹，并发布它。

### 设置应用程序运行

```
# Build runtime image
FROM  mcr.microsoft.com/dotnet/core/aspnet:2.2-alpine  AS  runtime
WORKDIR /app
COPY --from=builder /app/src/CodingMilitia.PlayBall.GroupManagement.Web/out .
ENTRYPOINT ["dotnet", "CodingMilitia.PlayBall.GroupManagement.Web.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的最后一件事是设置应用程序运行。这就是我们使用多阶段构建特性的地方。在前面的部分中，我们已经构建了应用程序，我们可以像上面看到的那样设置`ENTRYPOINT`，而不需要其他的。但是，如果您没记错的话，我们使用了。NET Core SDK 映像作为基础，它比实际运行应用程序所需的东西更多。

我们希望作为实际运行的容器基础的映像是一个运行时映像，特别是针对 ASP.NET 核心应用程序优化的映像。这就是我们在上面的`Dockerfile`部分看到的图像。通过利用 Docker 的多阶段构建，我们可以为构建的一部分使用一个映像，而为其他部分使用另一个映像。在这种情况下，我们使用 SDK 映像来构建应用程序，然后使用运行时映像来实际托管它。

`FROM`指令用 ASP.NET 运行时映像初始化一个新的阶段(同样，基于 Alpine Linux，以保持其小)。然后注意在`COPY`指令中，我们使用了`--from=builder`，正如你所记得的，这是我们给构建的第一阶段起的名字，所以我们将构建的应用从基于 SDK 的映像复制到基于运行时的映像，保持我们的容器尽可能的轻量级。

### 构建镜像并运行容器

既然我们已经准备好了`Dockerfile`，剩下要做的就是构建它，看看是否一切顺利。我们可以在组管理 API 存储库的根中使用以下命令来构建容器映像。

```
docker build -t codingmilitia/groupmanagement:latest . 
```

Enter fullscreen mode Exit fullscreen mode

`-t`是我们可以设置图像的名称和标签的方式，所以我们给它命名为`codingmilitia/groupmanagement`，并设置标签`latest`(这是容器图像最新版本的常用标签)。

现在，要运行基于这个映像的容器，我们可以使用下面的命令。

```
docker run -d --name groupmanagement codingmilitia/groupmanagement:latest 
```

Enter fullscreen mode Exit fullscreen mode

表示我们希望容器以分离模式运行，所以它的 id 被打印到控制台，控制权立即返回。如果没有这个，我们将在前台运行容器，并会看到它的 stdout 打印消息。`--name`让我们给容器起一个名字。最后，我们指出我们想要的新容器的图像。

您会注意到，如果您这样做了，在组管理项目的当前状态下，容器将会启动，但随后会立即停止，因为我们的应用程序会因出错而停止。这是因为我们需要做一些事情来使它正常工作(设置 ASP.NET 核心环境，通过正确的配置，拥有一个可访问的 PostgreSQL 实例...).这将是下一集的主题。在这个例子中，我们只是要构建容器。

需要注意的是，如果我们想在应用程序停止之前查看它打印的内容，我们可以执行`docker logs groupmanagement`。这并不是全部，但至少我们得到了应用程序启动的确认，只是由于一些与 Docker 无关的错误而停止了。

## 容器化 Vue.js SPA

### 建筑容器

既然我们已经看到了如何从 ASP.NET 核心应用程序创建 Docker 容器，现在是时候为我们的 Vue.js SPA 做同样的事情了。现在我不会详细介绍，因为概念完全相同，只是堆栈不同而已。

但有一点不同的是，在 ASP.NET 核心应用程序的情况下，它实际上是在容器中运行的服务器端应用程序，而 SPA 基本上是一组静态文件，应该在浏览器上实际运行。

我们可以通过几种方式实现这一点:

*   我们可以托管来自 ASP.NET 应用程序(即 BFF)的 SPA 文件。
*   将文件托管在专用服务器中。

由于我更喜欢将事情分开，我们将采用第二种方法。有一些替代方法，但我打算使用 [Nginx](https://www.nginx.com/) ，基于它创建一个 Docker 映像，并将静态文件复制到其中。

为了获得静态 SPA 文件，我们需要构建 Vue.js 应用程序，所以我们将再次求助于 Docker 多阶段构建，从 Node.js 基本映像开始。

我们将在 WebFrontend 存储库的客户机应用程序文件夹中创建一个新的`Dockerfile`。

```
# builder
FROM  node:lts-alpine  as  builder 
```

Enter fullscreen mode Exit fullscreen mode

与我们在 ASP.NET 看到的完全一样，从“建筑商”形象开始。

```
WORKDIR /app

## Storing node modules on a separate layer will prevent unnecessary npm installs at each build
COPY package*.json ./
RUN npm install 
```

Enter fullscreen mode Exit fullscreen mode

为了恢复 npm 包，我们使用与 NuGet 相同的技巧，只复制`package.json`文件。

```
COPY . .
RUN npm run build 
```

Enter fullscreen mode Exit fullscreen mode

为了构建应用程序，我们复制剩余的源代码，然后运行`npm run build`。

```
# Build runtime image
FROM  nginx:alpine  as  runtime 
```

Enter fullscreen mode Exit fullscreen mode

随着应用程序的构建，我们可以开始一个新的阶段，这一次基于 Nginx 图像(Alpine 照常)。

```
## Copy our default nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

## Remove default nginx website
RUN rm -rf /usr/share/nginx/html/*

## From ‘builder’ stage copy over the artifacts in dist folder to default nginx public folder
COPY --from=builder /app/dist /usr/share/nginx/html 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们复制一个`nginx.conf`文件，通过一些配置，我们需要让应用程序得到正确的服务(我们一会儿就会看到)。然后，我们删除默认 Nginx 网站文件夹中的任何内容，将 SPA 的静态文件复制到其中。

正如我们之前看到的，为了构建容器映像，我们可以执行`docker build -t codingmilitia/webfrontend/spa:latest .`。

为了运行容器，我们可以执行`docker run -d -p 80:80 --name spa codingmilitia/webfrontend/spa:latest`。`-p`表示我们希望将容器的端口 80 映射为主机的端口 80。

如果我们转到浏览器，我们将看到应用程序正在运行，尽管由于我们还没有把所有东西都放好，所以有点混乱。

### Nginx 配置

作为节目的总结，我们先来看一下`nginx.conf`，非常简单。

如果我们不提供任何配置文件，应用程序将主要使用 Nginx 的默认配置。要求我们添加配置的是处理应用程序路由的需要。

在 Nginx 默认配置下，只要我们通过它的基本 url(例如 [http://localhost](http://localhost) )进入应用程序，它就会按预期工作。然而，如果我们试图从另一条路径开始(例如 [http://localhost/groups](http://localhost/groups) )，Nginx 将返回 404，因为它无法将它与静态文件匹配。

为了能够正确地访问路由，我们需要 Nginx 在找不到静态文件时回退到`index.html`。下面的`nginx.conf`文件添加了那个配置。

```
server {
  listen 80;
  root /usr/share/nginx/html;

  location / {
    try_files $uri $uri/ /index.html; # fallback to index.html when a static alternative is not found
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

准备好一些 Docker 容器图像后，我们就可以结束这一集了。

我们已经看到了如何为 ASP.NET 核心和 Vue.js 应用程序创建一些简单的 Docker 映像，包括在容器中编译所述应用程序本身以简化整个过程。

在下一集，我们将使用 Docker Compose 来轻松运行完整的 PlayBall 应用程序。

帖子中的链接:

*   [码头工人](https://docs.docker.com/)
*   [坞站组成](https://docs.docker.com/compose/)
*   [Docker 窗口容器](https://www.docker.com/products/windows-containers)
*   [安装对接器](https://docs.docker.com/install/)
*   [坞站 WSL 2](https://engineering.docker.com/2019/06/docker-hearts-wsl-2/)
*   [Docker 多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)
*   [docker 文件参考](https://docs.docker.com/engine/reference/builder/)
*   [。NET Core SDK Docker 映像](https://hub.docker.com/_/microsoft-dotnet-core-sdk/)
*   [ASP.NET 核心运行时 Docker 图像](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)
*   [Nginx](https://www.nginx.com/)

这篇文章的源代码分布在[“编码民兵:ASP.NET 核心——从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的存储库中，标记为`episode026`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode026)
*   [群组管理](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode026)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode026)

感谢分享和反馈！

谢谢你的来访，西阿兹！谢谢你的来访，西阿兹！