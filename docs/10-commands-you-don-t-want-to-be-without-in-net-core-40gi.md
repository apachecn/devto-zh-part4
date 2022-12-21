# 你不想失去的 10 个命令。网络核心

> 原文：<https://dev.to/dotnet/10-commands-you-don-t-want-to-be-without-in-net-core-40gi>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

TLDR；这篇文章描述了。希望这里有适合每个人的东西，从绝对的初学者到更有经验的开发人员。

我有很长的. Net 开发背景，其中最棒的部分之一是 Visual Studio，这是一个绝对出色的 IDE，它允许你轻松地编写代码，并带有代码完成功能，易于调试和打包你的应用程序。几年后我第一次开始。网来了。Net Core 以及使用它的能力。Net 跨平台。与。Net Core 也是一个非常强大的 CLI 工具。使用旧的。你知道在后台有一个 MSBuild 命令，但你很少需要关心，Visual Studio 为你处理这一切。

使用。Net Core 虽然您拥有两个世界的最佳之处，但仍然拥有一流的 Visual Studio 体验或令人惊叹的 Visual Studio 代码，并且通过 CLI 工具，我们还拥有一流的终端体验。

> 所以为什么要用终端命令，我是 UI 男？

我知道你们很多人都是这样想的(我以前也是)，答案是原因不止一个。

*   **脚本**是一个非常好的理由，脚本可以让我们轻松设置 CI/CD
*   速度是一个因素，使用终端通常比使用视觉环境更快。
*   **选择你自己的编辑器**，它也允许你使用你选择的最喜欢的编辑器。我们都有自己最喜欢的编辑。。Net 曾经与 Visual Studio 紧密耦合，现在用。Net Core CLI 您可以通过终端非常容易地构建、测试、打包、发布您的项目。

不管你认为最有说服力的原因是什么，了解下面运行的是什么命令，并了解它们为你做了什么都是有好处的。因此，让我们来看看 10 个最重要的命令。Net Core CLI:

## -1- dotnet new

此命令帮助您搭建项目。所以通常你这样称呼:

```
dotnet new <type of project template> - o <name of directory> 
```

一个真实的例子可能是这样的:

```
dotnet new console -o app 
```

上面将在目录`app`中创建一个控制台项目。这个命令还有很多内容，所以请看一下文档页面

> [点网新建命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-new?tabs=netcore22&?wt.mc_id=devto-blog-chnoring)

## -2-点网恢复

这将恢复项目的依赖关系。这到底是什么意思？当您将 NuGet 包添加到您的解决方案中时，它会添加对项目文件的引用，您的`.csproj`。如果您从 GitHub 签出项目，这些包可能没有版本，需要添加到您的项目中。此时运行`restore`将从 NuGet 存储库中获取包。

该命令可以通过两种方式运行:

*   **显式**，你输入它，它获取你需要的包
*   **隐式**，它作为正在执行的另一个命令的一部分运行

下面是隐式运行`restore`的命令的完整列表:

*   网络新闻
*   点网构建
*   点网络构建-服务器
*   网络运行
*   网络测试
*   网络出版
*   网络包

要了解有关此命令的更多信息，请在此处阅读更多信息:

> [点网恢复命令](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-restore?tabs=netcore2x&wt.mc_id=devto-blog-chnoring)

## -3 点网构建

此命令构建一个项目及其所有依赖项。如果你在根目录下，并且你有一个解决方案，那么它将构建整个解决方案，因此解决方案中的所有项目。如果你在一个特定的项目目录中，那么它只会构建那个目录，即重要的*。除此之外还有很多关于`build`命令的内容，所以请点击以下链接查看更多细节:*

> [点网构建命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-build?wt.mc_id=devto-blog-chnoring)

## -4 点网运行

`run`命令是用来执行代码的。

要使用它，只需将自己放在想要运行的项目的项目目录中，并键入:

```
dotnet run 
```

请注意，它是在项目的上下文中使用的，而不是在生成的程序集的上下文中使用的。如果你正试图运行一个依赖于框架的应用程序 DLL，你必须使用不带命令的`dotnet`。例如，要运行 myapp.dll，请使用:

```
dotnet myapp.dll 
```

要了解有关此命令的更多信息，请查看以下链接:

> [点网运行命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-run?tabs=netcore21&wt.mc_id=devto-blog-chnoring)

## -5 点网测试

此命令用于执行测试项目的测试。这带来了相当多的参数，使您能够运行特定的测试或整个测试套件。

要了解有关此命令的更多信息，请查看以下链接:

> [点网测试命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-test?tabs=netcore21&wt.mc_id=devto-blog-chnoring)

如果您完全是测试新手，可以看看我写的这篇关于测试入门的文章:

> [https://dev.to/dotnet/testing-in-net-core-ojh](https://dev.to/dotnet/testing-in-net-core-ojh)

## -6 点网络包

现在我们进入了一个有趣的领域，即创建你自己的 NuGet 包。您可以使用这个命令创建一个包，您可以将包放在本地存储库中，也可以放在正式的全局存储库中。要了解这方面的更多信息，请查看官方文档页面:

> [点网络打包命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-pack?wt.mc_id=devto-blog-chnoring)

另外，查看我写的一篇关于如何从头开始做这件事的文章，并学习如何发布您的包以便其他人可以下载它:

> [在 NuGet 中创建您的第一个包](https://dev.to/dotnet/how-you-can-create-reusable-packages-for-net-core-using-vs-code-c-and-nuget-3kn9)

## -7- dotnet 清理

此命令清除项目的输出。这意味着它带走了 obj 和 bin 文件夹的内容。该命令带有几个参数，因此您可以选择仅清理特定的运行时或框架。在官方文档中阅读有关该命令的更多信息:

> [点网清理命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-clean?wt.mc_id=devto-blog-chnoring)

## -8 点网 sln

这是您用来管理解决方案的命令。如果你对 dotnet 完全陌生，一个解决方案可以跟踪许多项目，这是管理一组不同项目的好方法，这些项目在逻辑上属于一起做一些事情，如构建或发布可执行文件。因为这个命令管理围绕解决方案的一切，所以知道如何开始是有好处的。

要创建一个解决方案，你只需把自己放在你选择的目录中，然后运行

```
dotnet new sln 
```

这将创建一个与目录同名的解决方案文件。例如:

```
cd hello
dotnet new sln 
```

这将创建一个`hello.sln`文件。要管理该解决方案，您可以使用以下命令:

*   **添加**，这将向您的解决方案添加一个项目。
*   **移除**，这将从您的解决方案中移除一个项目
*   **list** ，这列出了一个解决方案中的所有项目

要了解有关此命令的更多信息，请查看官方文档页面:

> [dotnet sln 命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-sln?wt.mc_id=devto-blog-chnoring)

另外，看看我写的教你如何创建解决方案、项目以及如何开始的博文。网络核心

> 从...开始。网络核心

## -9- dotnet 添加/删除参考

此命令会将项目添加到您的项目中。例如，目的是让您的项目使用另一个项目的代码。要做到这一点，首先需要在编译器和 IDE 识别它之前添加对该项目的引用。例如，您当前正在开发控制台项目`app`，并且您想要将项目`lib`添加到`app`中。您可以通过以下两种方式之一实现这一点:

1.  **从解决方案目录**添加一个引用，如果你站在解决方案目录(app 目录的上一级)中，那么你输入`dotnet add app/app.csproj reference lib/lib.csproj`
2.  **从当前目录**添加一个引用，然后输入`dotnet add reference lib1/lib1.csproj`

要了解有关此命令的更多信息，请查看文档页面:

> [点网添加参考命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-reference?wt.mc_id=devto-blog-chnoring)

## -10- dotnet 添加/删除包

现在我们又开始谈论 NuGet 包了。`add`命令允许我们从 NuGet 添加一个包，通过指定它的名字:

```
dotnet add package Newtonsoft.Json 
```

这将把`Newtonsoft.Json`包添加到我们的项目文件中。它还将运行一个`dotnet restore`命令，该命令将从 NuGet 的存储库中获取包

要了解有关此命令的更多信息，请查看以下链接:

> [点网添加包命令](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package?wt.mc_id=devto-blog-chnoring)

## 参考文献

以下是一些你可能需要的参考资料:

*   [安装 dotnet](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
    这将带您进入的安装页面。网芯。当你在那里的时候，去 https://dotnet.microsoft.com 看看。这是一个伟大的网站。Net，全是教程。

*   [。](https://dev.to/azure/net-core-series-serverless-graphql-nuget-testing-entity-framework-and-more-5dgk)网芯系列
    看看这个系列。Net 核心文章，从你的第一步开始。Net 核心，到无服务器甚至 GraphQL