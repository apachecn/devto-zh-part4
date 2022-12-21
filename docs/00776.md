# 在 Azure 容器实例中运行 Gatling 第 2 部分——添加 API 来控制 Gatling

> 原文：<https://dev.to/dantheman999301/running-gatling-in-azure-container-instances-part-2-adding-an-api-to-control-gatling-3b1o>

[第 1 部分-让加特林在 Azure 上运行](https://dev.to/dantheman999301/running-gatling-in-azure-container-instances-2i59)

在上一篇文章中，我介绍了一种在 Azure 容器实例中运行 Gatling 的方法，方法是挂载某些 Azure 存储并编辑配置文件。这是一个让它运行的有点粗糙的方法，当然可以改进。

在这篇文章中，我将介绍一种自动化的方法——在上面添加一个 ASP.NET API。

# 第一步——创建自定义 Docker 运行时容器

第一步是使用原始的 Gatling docker 容器( [denvazh/gatling](https://github.com/denvazh/gatling/blob/master/3.0.3/Dockerfile) )并以它为基础创建一个映像，其中包含了 dotnet 依赖项。

这是一个相当简单的过程，从[运行时 deps](https://github.com/dotnet/dotnet-docker/blob/master/2.2/runtime-deps/alpine3.8/amd64/Dockerfile) 映像添加部件，然后添加 ASP.NET[运行](https://github.com/dotnet/dotnet-docker/blob/master/2.2/aspnet/alpine3.8/amd64/Dockerfile)所需的部件。

你得到了这个文档。

```
FROM denvazh/gatling:3.0.3

RUN apk add --no-cache \
        ca-certificates \
        \
        # .NET Core dependencies
        krb5-libs \
        libgcc \
        libintl \
        libssl1.1 \
        libstdc++ \
        lttng-ust \
        tzdata \
        userspace-rcu \
        zlib

# Configure web servers to bind to port 80 when present
ENV ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    # Set the invariant mode since icu_libs isn't included (see https://github.com/dotnet/announcements/issues/20)
    DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=true

# Install ASP.NET Core
ENV ASPNETCORE_VERSION 2.2.7

RUN wget -O aspnetcore.tar.gz https://dotnetcli.blob.core.windows.net/dotnet/aspnetcore/Runtime/$ASPNETCORE_VERSION/aspnetcore-runtime-$ASPNETCORE_VERSION-linux-musl-x64.tar.gz \
    && aspnetcore_sha512='d3c1cc27998fc8e45fbf0c652a8d8694e999a3cd5909f83fb11b1e5cf713b93f4e7614c4b74c92d6c04f0b0759373b6b6ff7218d9d143d36bb9b261ef8161574' \
    && echo "$aspnetcore_sha512  aspnetcore.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -zxf aspnetcore.tar.gz -C /usr/share/dotnet \
    && rm aspnetcore.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet 
```

Enter fullscreen mode Exit fullscreen mode

我最近在 [dockerhub 这里](https://hub.docker.com/r/dantheman999/gatling-aspnet)发表了这篇文章。

这样我们就有了运行时映像。

# 第二步-创建 API

Visual Studio 已经提供了一些不错的 docker 模板，所以我从一个支持 Docker 的空白 API 开始。在这之后，我需要在 docker 文件中做的唯一改变就是把基础改成我自己的。

```
FROM dantheman999/gatling-aspnet AS base # Change the base image
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /src
COPY ["Gatling.Runner.csproj", ""]
RUN dotnet restore "./Gatling.Runner.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "Gatling.Runner.csproj" -c Release -o /app

FROM build AS publish
RUN dotnet publish "Gatling.Runner.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Gatling.Runner.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

然后是创建 API 本身的问题。对此，我考虑了两种方法:

1.  继续我最初的计划，在创建容器时挂载 Azure 存储，这样就有可能在多个容器之间共享它。
2.  当开始新的测试时，将模拟直接添加到容器中。

我最终选择了选项 2。它提供了更多的灵活性，例如，您可以让容器在运行的同时运行多个不同的测试，然后再将其拆除，或者让测试同时运行，如果您愿意的话。

下面我将浏览一些用来实现这一点的代码。你可以跟着它走，也可以看看回购。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [丹瑟姆 999301 ](https://github.com/dantheman999301) / [加特林。亚军](https://github.com/dantheman999301/Gatling.Runner)

<article class="markdown-body entry-content container-lg" itemprop="text">

# Azure +加特林——在云端运行加特林

repo 中的项目包含有助于在 Azure 中以自动化方式运行 Gatling 的应用程序。

目前有:

*   `base-image`:包含加特林 Docker 容器的文件夹，也可以运行。NET 核心应用程序。
*   在机器上运行加特林测试的 API。结合上面的加特林容器，这允许在云中远程运行加特林测试。
*   `src/Gatling.Orchestrator`:一个持久的函数，用于跨多个 Azure 容器实例编排运行 Gatling 测试，返回一个组合结果。

</article>

[View on GitHub](https://github.com/dantheman999301/Gatling.Runner)

### 控制器(GatlingController.cs)

控制器有 3 个端点:

*   `Start`
*   `StartAsync`
*   `GetResults`

`Start`将测试上传、存储文件、运行测试并返回结果作为一个操作。`StartAsync`和`GetResults`做同样的事情，除了`StartAsync`将返回`runId`,当测试在后台运行时，它可以用来查询结果。

从最简单的方法开始，`Start`。