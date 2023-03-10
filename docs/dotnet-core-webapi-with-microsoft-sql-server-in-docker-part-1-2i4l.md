# Docker 中带有 Microsoft SQL Server 的 Dotnet Core Webapi 第 1 部分

> 原文：<https://dev.to/mrcsharp/dotnet-core-webapi-with-microsoft-sql-server-in-docker-part-1-2i4l>

> 这篇博文最初出现在[我的博客](https://blog.mrcsharp.com.au)

我听到很多人谈论 docker 及其带来的好处，他们让我对这项技术产生了兴趣。然而，我最近才设法抽出一些时间和 Docker 一起玩。

我试图做的是拿一个我正在构建的 DotNet Core WebApi 项目，并将其容器化。WebApi 项目最初并没有容器化的意图，令人惊讶的是在 docker 中安装和运行它是如此的容易。

该项目还使用了一个微软 SQL 数据库，对我来说，这是一个有点挑战性的设置，并让 WebApi 与它正确通信。

在这篇文章中，我将解释我用示例 docker 文件和 docker 编写配置设置它的步骤。

## 需要什么？

首先，您需要在开发机器上安装 docker。我用的是 Windows 10 的机器。你可以在这里阅读[安装说明](https://docs.docker.com/docker-for-windows/install/)

## 为 WebApi 项目创建 DockerFile

dockerfile 将负责为 WebApi 项目设置图像。如果你不知道 docker 图片是什么，我强烈建议你在这里阅读 Docker [入门部分。](https://docs.docker.com/get-started/)

在这个 docker 文件中，当我们通过 *dotnet* cli 构建和发布任何 dotnet 核心应用程序时，我们将执行我们在机器上执行的相同命令。

通常我们这样做(在项目的根):

```
 > dotnet restore
> dotnet publish -c Release -o out 
```

然后，当我们检查项目根目录中的 *out* 目录时，我们将找到我们项目的已编译程序集，然后我们可以简单地执行这个命令来运行应用程序/webapi:

```
 > dotnet MyProjectEntryPointAssembly.dll 
```

但是我们将在 docker 文件中执行一些额外的命令来设置基本图像和结果图像的入口点。

首先，让我们看看我的项目的文件夹结构:

/root
-src
—web pi
【dock file】
【docker】化合物. yml

让我们看看我的 docker 文件，我将解释每个命令在做什么:

```
FROM mcr.microsoft.com/dotnet/core/sdk as build
WORKDIR /app
COPY src/. .
RUN dotnet restore
RUN dotnet publish -c Debug -o out
FROM mcr.microsoft.com/dotnet/core/aspnet as runtime
WORKDIR /app
COPY --from=build /app/webapi/out ./
EXPOSE 80
CMD [ "dotnet", "webapi.dll" ] 
```

好了，我们从第一行开始:

```
FROM mcr.microsoft.com/dotnet/core/sdk as build 
```

这告诉 docker 我们将使用微软的 *DotNet Core SDK* 映像作为我们的基础映像。你可以在 Docker Hub 中找到图片[。](https://hub.docker.com/_/microsoft-dotnet-core-sdk)

*...因为 build* 是一个构建阶段的别名。当您想要从另一个构建阶段引用一个构建阶段时，这会更容易，因为使用名称会更友好。默认情况下，docker 不会命名构建阶段。您仍然可以引用没有使用整数定义的别名的构建阶段，该整数从第一个构建阶段的 *0* 开始。

docker 文件的下一步是在我们刚刚从 docker hub 获取的图像中设置一个工作目录。工作目录名可以是任何名称，如果基本映像中不存在该目录，docker 将创建该目录。我选择将我的工作目录命名为 *App* ，但是您可以随意使用任何您喜欢的名称。要设置一个工作目录，我们执行这个命令:

```
WORKDIR /app 
```

接下来，我们需要将我们的项目文件从本地开发环境复制到基本映像。您可以将我们刚刚提取的基础映像想象为一个虚拟机(它们实际上不是虚拟机，但在使用它们时可能会有所帮助)，docker 将初始化并运行该虚拟机，因此它不会访问我们的文件。我们通过执行以下命令将文件复制到基本映像:

```
COPY src/. . 
```

这是将/src 目录中的所有内容复制到我们之前指定的工作目录中。

好了，现在我们有了基础映像中的所有源代码，我们准备构建它了。由于我们仍然在 DotNet 核心 SDK 映像中，shell 中的 *dotnet* 命令是可用的，所以我们将告诉 docker 执行几个 *dotnet* cli 命令来构建项目:

```
RUN dotnet restore
RUN dotnet publish -c Debug -o out 
```

这里的 *RUN* 关键字告诉 docker 在基础映像的 shell 中执行以下命令。所以这将恢复 nuget 包，然后执行发布命令并将结果输出到 *out* 目录。记住，我们仍然在我们指定的工作目录中，所以一切都发生在 */app* 下，我们的代码在 */app/webapi/中。*而 outout 文件夹会在 */app/webapi/out/里。*

太棒了。现在我们已经成功地编译和发布了代码(在基础图像中),我们将进入下一个构建阶段。

我们在这个 dockerfile 文件中使用多个 stages 的原因以及使用它们的原因(至少在本例中)是为了避免创建大的图像。

微软 DotNet Core SDK 基础镜像大约是 *400MB* 。虽然我们可以构建项目，也可以在这个映像中运行它，但是在我的 webapi 项目中使用 *~400MB* + *~75MB* 似乎是一种浪费，因为我们可以使用比它小得多的 Microsoft DotNet Core 运行时基础映像来运行我的 webapi。

为了在 dockerfile 文件中开始一个新的阶段，我们使用来自语句的*。在这个阶段，我们将通过执行命令
来获取在这里找到的微软 DotNet 核心运行时映像*

```
FROM mcr.microsoft.com/dotnet/core/aspnet as runtime 
```

我们将这个阶段命名为*运行时*，并将它的工作目录也设置为 */app* :

```
WORKDIR /app 
```

下一步，我们需要从上一阶段获取构建输出文件，并将其复制到当前阶段。我们通过执行命令
来做到这一点

```
COPY --from=build /app/webapi/out ./ 
```

*- from=[stage name]* 指定文件的来源来自另一个映像/阶段。 */app/webapi/out* 是该步骤中文件的来源，*。/* 是目的地(本步骤中工作目录的根目录)。

一旦复制完成，由于这是一个将在*端口 80* 上运行的 webapi 项目，我们需要告诉 docker，我们的端口 80 应该被发送到传入的网络流量:

```
EXPOSE 80 
```

简单，太简单了！

我们运行的最后一个命令实际上是运行我们的 webapi。为此，我们执行:

```
CMD [ "dotnet", "webapi.dll" ] 
```

现在我们有了一个完整的 docker 文件，我们可以通过使用 docker cli 构建最终的映像并运行它来测试它。

为此，打开一个终端，将工作目录设置为项目的根目录(dockerfile 所在的位置)并运行:

```
docker build --tag=MyWebApiProject . 
```

*build* 命令将执行我们创建的 dockerfile 文件中的命令。 *- tag=[name]* 给生成的图像命名。命令将构建上下文设置为当前目录后的时间段。docker 将尝试在当前上下文中查找 docker 文件，docker 文件中的所有命令都将在该上下文中运行。这对于我们将文件复制到 SDK 映像非常重要，因为我们使用的是相对路径。所有相对路径将依赖于我们设置的构建上下文。

如果构建成功，您的机器上将存储一个 docker 映像。

使用 *docker image ls* 列出机器上的图像。您可以看到您创建的图像及其大小。

在我的机器上，SDK 映像是 *1.74GB* ，但是 webapi 映像只有 *283MB* ，因为它使用了上面多阶段 docker 文件中的运行时映像。

现在是运行容器化应用程序的时候了，这很简单:

```
docker run -p 5000:80 MyWebApiProject 
```

*docker run* 是运行容器化应用程序映像的 CLI 命令。

使用*-p[OS port]:【Container port】*我将我的操作系统上的一个端口(在本例中为端口 5000)绑定到我们之前在 dockerfile 文件中公开的端口(端口 80)。这个绑定将允许我们使用 HTTP REST 客户端，比如[失眠症](https://insomnia.rest/)，向容器中的 webapi 应用程序发送 HTTP 请求。

就是这样！这就是我如何将一个现有的和正在开发的 DotNet Core WebApi 项目打包到 docker 中的。

在下一部分中，我将解释如何设置一个容器化的 MS-SQL 服务器，并创建一个 docker swarm 来让我的 WebApi 项目与容器化的 MS-SQL 服务器对话。