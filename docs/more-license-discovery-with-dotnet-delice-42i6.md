# 使用 dotnet-delice 发现更多许可证

> 原文：<https://dev.to/dotnet/more-license-discovery-with-dotnet-delice-42i6>

在我的上一篇文章中，我向你介绍了一个查找许可证的工具。NET 项目称为`delice`。

本周我发布了第一个更新，[版本 1.1.0](https://www.nuget.org/packages/dotnet-delice/1.1.0) ，它为许多 NuGet 包的遗留许可格式的许可检测带来了很大的改进。

## 通过 GitHub API 确定许可

总的来说，我们所依赖的依赖是 OSS 项目的输出，而这个项目通常是托管在 GitHub 上的。因此，我们可以使用 [GitHub 许可 API](https://developer.github.com/v3/licenses/) 来尝试获取项目的许可信息，现在`delice`支持:

```
$> dotnet delice --check-github ~/my-project 
```

Enter fullscreen mode Exit fullscreen mode

其工作方式是，当`delice`使用遗留的`licenseUrl` nuspec 属性找到一个项目，并且该许可证在缓存中不是硬编码的，它将查看 URL 并确定它是否是 GitHub 上的许可证的 URL。

以包`Microsoft.AspNetCore`的许可为例，它给出了一个 URL`https://raw.githubusercontent.com/aspnet/AspNetCore/2.0.0/LICENSE.txt`。从这里，`delice`将获取存储库所有者(`aspnet`)和存储库名称(`AspNetCore`)，然后调用 GitHub API 并获取许可信息，这将告诉我许可是`Apache-2.0`。`delice`然后会在内部缓存它，这样如果 URL 在多个包中使用，或者该包在解决方案的多个项目中被引用，它将只访问 API 一次。

做这件事的代码可以在[这里](https://github.com/aaronpowell/dotnet-delice/blob/6fd4a45e13eed9ff176b9e87f50e0e0d1432a07e/src/DotNetDelice.Licensing/LicenseCache.fs#L168-L211)找到。

值得注意的是，API 检查有一个已知的限制，它将**总是**对照`master`检查许可证，而不是任何其他分支或标签。这是一个设计选择，所以虽然这确实意味着如果一个项目被跨版本重新许可，你*可能*得到一个不同的许可，但相对于支持它所需的努力，我预计这种可能性很低。毕竟，这是对那些没有更新到使用新的 nuspec 格式的包的一种变通方法，而不是一个合适的解决方案。

### 避免速率限制

默认情况下，API 调用是匿名进行的，这意味着每小时最多只能调用 50 次 GitHub API，如果您有很多传统风格的 NuGet 包，这种情况可能会在一次运行中消失。最好为调用提供一个 GitHub 个人访问令牌，如下:

```
$> token=abc123...
$> dotnet delice --check-github --github-token $token ~/my-project 
```

Enter fullscreen mode Exit fullscreen mode

当您提供令牌时，速率限制增加到每小时 5000 个呼叫，这应该适用于大多数情况。如果你遇到了限速问题，我想知道，这样我们可以制定出更聪明的解决方案(我有想法，但我只想在实际需要时投入精力！).

## 对比常用模板

依赖 GitHub API 并不是万无一失的，不是所有的许可证都能被 GitHub 检测到，也不是所有的项目都托管在 GitHub 上。此外，如果您将许可证文件嵌入到 NuGet 包中，而不是仅仅提供一个 SPDX 标识符，仍然不可能知道许可证是什么。

为了解决这个问题，我借鉴了 GitHub 用于许可证检测的方法，并以[s rensen–Dice coefficient](https://en.wikipedia.org/wiki/S%C3%B8rensen%E2%80%93Dice_coefficient)的形式实现了一些复杂的数学运算，以将许可证与已知模板
进行比较

```
$> dotnet delice --check-license-content ~/my-project 
```

Enter fullscreen mode Exit fullscreen mode

当您提供这个标志时，`delice`将从 URL 下载文件内容，并将其与自身存储的一些模板进行比较。如果有一个足够接近的匹配(阈值是 90%) `delice`将假定那是项目的许可。

做这件事的代码可以在[这里](https://github.com/aaronpowell/dotnet-delice/blob/6fd4a45e13eed9ff176b9e87f50e0e0d1432a07e/src/DotNetDelice.Licensing/LicenseCache.fs#L215-L257)找到，包括 Dice 系数的实现(我在网上找到的😝).

目前我只有 MIT 和 Apache-2.0 作为比较的模板，因为它们似乎是我在做研究时遇到的最常见的模板，但是如果有其他的模板，请告诉我或者给我发一份 PR。

## 显示许可一致性

当试图与蒂尔尼的 [`delice`](https://github.com/cutenode/delice) 保持一致时，我需要添加一个缺失的部分，显示许可证与 SPDX 列表的一致性。我已经将它添加到了 1.1.0 版本的`delice`中，现在您可以看到您的哪些项目使用了 OSI 或 FSF 批准的许可，以及是否有任何许可使用了废弃的许可格式:

```
Project dotnet-delice
License Expression: MIT
├── There are 10 occurances of MIT
├─┬ Conformance:
│ ├── Is OSI Approved: true
│ ├── Is FSF Free/Libre: true
│ └── Included deprecated IDs: false
└─┬ Packages:
  ├── FSharp.Core
  ├── Microsoft.NETCore.App
  ├── Microsoft.NETCore.DotNetAppHost
  ├── Microsoft.NETCore.DotNetHostPolicy
  ├── Microsoft.NETCore.DotNetHostResolver
  ├── Microsoft.NETCore.Platforms
  ├── Microsoft.NETCore.Targets
  ├── NETStandard.Library
  ├── Newtonsoft.Json
  └── System.ComponentModel.Annotations 
```

Enter fullscreen mode Exit fullscreen mode

这将有助于您确保通过快速扫描了解正在使用的许可证的更多信息，而不必了解每种许可证类型的详细信息。

## 提取出核心

我在 1.1.0 版本中引入的另一个变化是将`delice`的核心提取到一个单独的 NuGet 包中， [DotNetDelice。许可](https://www.nuget.org/packages/DotNetDelice.Licensing/)。虽然您在安装 CLI 工具时不需要这个包(工具是独立的)，但是如果您想将它集成到您正在构建的任何其他工具或系统中，它是可用的。

请注意，虽然我是用 F#写的，但它使用了 F#的样式和类型，所以请准备好处理它们。

## 结论

这是 1.1.0 版中的新特性，所以请一定要去看看，因为它有望让您更多地了解您的项目中有哪些许可。

如果你正在使用它，我很想知道你对这些更新的看法，以及你希望从`delice`开始看到的其他功能。