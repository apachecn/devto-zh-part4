# 正在使用哪些许可证？

> 原文：<https://dev.to/dotnet/what-licenses-are-in-use-528n>

你有没有想过什么许可证的依赖？NET 项目是什么？在开源软件使用的不断扩大的世界中，确保您符合您的依赖项的许可变得越来越困难。你有左拷贝依赖吗？你遵守他们的许可条款了吗？也许有一些像 [WTFPL](http://www.wtfpl.net/) 这样的合法灰色许可证你需要小心。

当您研究瞬态依赖时，这可能变得更加棘手。你知道你直接**消费了哪些依赖项，但是你间接*消费了哪些依赖项，因为它们是依赖项的依赖项？***

## 输入`dotnet-delice`

**TL；博士**:我创建了一个 [`dotnet`全局工具](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools?WT.mc_id=devto-blog-aapowell)叫做 [`dotnet-delice`](https://www.nuget.org/packages/dotnet-delice) 来帮你做这个。

`dotnet-delice`或简称为`delice`，是一个工具，它将查看您的项目中的依赖项，并尝试确定它们使用什么许可证，并为您显示结果。这是 Node.js 实用程序 [`delice`](https://github.com/cutenode/delice) 的一个端口，由 [Tierney Cyren](https://github.com/bnb) 创建。

可以从 NuGet:
安装

```
> dotnet tool install --global dotnet-delice --version 1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

然后通过指向一个文件夹、一个解决方案文件或者一个`csproj` / `fsproj`文件:
来运行它

```
> dotnet delice ~/github/DotNetDelice/DotNetDelice.sln 
```

Enter fullscreen mode Exit fullscreen mode

下面是它将生成的输出的快照:

```
Project dotnet-delice
License Expression: MIT
├── There are 10 occurances of MIT
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

`delice`将扫描项目的依赖图，并以人类可读的格式输出许可信息(如上)或生成 JSON(到标准输出或文件)。如果检测到意外的许可证，可以在构建管道中使用 JSON 来使构建失败。

如果你想亲自研究一下，你可以在 GitHub 上找到源代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [阿朗波威尔](https://github.com/aaronpowell) / [ dotnet-delice](https://github.com/aaronpowell/dotnet-delice)

### 📑帮助您深入了解项目许可的 CLI

<article class="markdown-body entry-content container-lg" itemprop="text">

[![CI build](img/5f12518cb33eaf985017066ab67c40ff.png)](https://github.com/aaronpowell/dotnet-delice/actions/workflows/ci.yml)[![Release build](img/4d437b2824b3045618958470d9e50077.png)](https://github.com/aaronpowell/dotnet-delice/actions/workflows/release.yml)[![NuGet Badge](img/a6ec306855d9fd583721527bdf654ecb.png)](https://www.nuget.org/packages/dotnet-delice)[![The MIT License](img/a9baf9e18783e22f1e375d53ab32201b.png)T11】](http://opensource.org/licenses/MIT)

# dotnet-delice

delice 是一个工具，用于确定项目/解决方案中引用的包的许可信息。这是 Node.js 实用程序 [`delice`](https://github.com/cutenode/delice) 的一个端口，由 [Tierney Cyren](https://github.com/bnb) 创建。

**注意** : `dotnet-delice`只支持 C#、F#和 VB.NET 的 SDK 项目文件(虽然在 VB.NET 上我不确定，没试过！)，而不是传统的“MSBuild 样式”项目文件(仅支持。NET 完整框架)。如果您仍在使用遗留项目文件，该工具将会失败。我鼓励你尝试和升级(使用工具，如 [CsprojToVs2017](https://github.com/hvanbakel/CsprojToVs2017) )。

# 使用

该工具作为 [`dotnet`全局工具](https://docs.microsoft.com/dotnet/core/tools/global-tools?WT.mc_id=dotnet-00000-aapowell)发货，可以这样安装:

```
dotnet tool install -g dotnet-delice 
```

然后你可以这样使用它:

```
dotnet delice [folder, sln, csproj, fsproj] 
```

## 命令

*   `-?|-h|--help`布尔。显示帮助。
*   `-j|--json`布尔。将结果输出为 JSON 而不是 pretty-print。
*   `--json-output [path]`弦。文件的路径…

</article>

[View on GitHub](https://github.com/aaronpowell/dotnet-delice)

## 关于软件包许可证的一个说明

当我在研究这是如何工作的时候，我遇到了这个新问题。

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) 自带 NuGet 包-许可 #4628](https://github.com/NuGet/Home/issues/4628) 

[![maartenba avatar](img/69f2d896f942902259d6d75608450690.png)](https://github.com/maartenba) **[maartenba](https://github.com/maartenba)** posted on [<time datetime="2017-02-16T08:13:27Z">Feb 16, 2017</time>](https://github.com/NuGet/Home/issues/4628)

更新(2019 年 1 月 8 日@ anangaur)-**有用链接**:

*   功能发布:[将图标、许可证和文档打包到 nupkg 中](https://github.com/NuGet/Announcements/issues/29)
*   `licenseUrl`:[‘license URL’作为包元数据的弃用声明现已弃用，请使用‘license’代替](https://github.com/NuGet/Announcements/issues/32)
*   Spec wiki: [在 nupkg 内打包许可证](https://github.com/NuGet/Home/wiki/Packaging-License-within-the-nupkg)
*   讨论问题:[独立的 NuGet 包–许可证号 4628](https://github.com/NuGet/Home/issues/4628)
*   nuspec 中`license`元数据的文档:[https://docs . Microsoft . com/en-us/nu get/reference/nu spec # license](https://docs.microsoft.com/en-us/nuget/reference/nuspec#license)
*   在项目文件中使用 MSBuild 属性的`license`元数据的文档:[https://docs . Microsoft . com/en-us/nuget/reference/MSBuild-targets # packing-a-license-expression-or-a-license-file](https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets#packing-a-license-expression-or-a-license-file)

* * *

现在，通过在元数据中提供`licenseUrl`属性，每个包都可以链接到一个许可证。这很棒，但也不那么棒。请允许我解释一下我的想法。

每个包的所有者都可以为他们的包的每个版本附加一个许可证。到目前为止，一切顺利，因为它允许在版本之间切换许可。到目前为止，一切顺利。

现在想象使用一个 NuGet 包。为了影响，让我们以`Newtonsoft.Json`为例，这是一个非常流行的带有许可的 OSS 包。有一天，作者决定在引用的许可证 URL 上更新 HTML 内容。那是...有问题！

哪种许可证适用？我在软件包安装时阅读(并同意)的那个？还是现在显示在许可证 URL 上的当前版本？

没有办法计算出在安装和使用软件包之间许可证的变化，也没有办法证明它在第一次安装时是许可的。

请考虑将许可证信息嵌入到包中，因为包本身被认为是不可变的。

[View on GitHub](https://github.com/NuGet/Home/issues/4628)

这个问题提出了一个关于 [nuspec 文件](https://docs.microsoft.com/en-us/nuget/reference/nuspec?WT.mc_id=devto-blog-aapowell)中的许可信息仅仅是`licenseUrl`的问题。由于许可在包之外*，URL 可能会在不改变包的情况下改变，因此在你不知情的情况下改变了你最初同意的许可。*

这导致了[对`licenseUrl`的贬值，而有利于`license`资产](https://github.com/NuGet/Announcements/issues/32)。

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)【license URL】作为包元数据现在已被弃用，请使用“许可证”代替 #32](https://github.com/NuGet/Announcements/issues/32) 

[![anangaur avatar](img/0b30c211b91aaf952dddabacfc11ce83.png)](https://github.com/anangaur) **[anangaur](https://github.com/anangaur)** posted on [<time datetime="2018-12-17T21:57:06Z">Dec 17, 2018</time>](https://github.com/NuGet/Announcements/issues/32)

正如[早些时候宣布我们的目标是从包](https://github.com/NuGet/Announcements/issues/29)中移除内容 URL(许可证、图标和降价文档)并使这些元数据成为包的一部分，我们现在**已经反对使用`licenseUrl`** 作为包元数据。您现在可以使用 nuspec 中的`license`字段来代替 **[。](https://docs.microsoft.com/en-us/nuget/reference/nuspec#license)**

作为参考，以下是该功能的计划里程碑:**里程碑 1(今天)**:

*   NuGet.exe 版本 4.9.2 和 dotnet.exe 版本(附带。NET SDK–2 . 1 . 502 和 2.2.101)支持使用新的“许可证”属性创建包
*   不赞成在打包期间提供“LicenseURL ”,如果使用，将显示警告。
*   NuGet.org 接受包含“许可证”属性的包，包括许可证文件。
*   许可证信息(表达式或文件)通过 Visual Studio 中的包管理器 UI 公开。
*   Visual Studio 2017 15.9.4 及以上版本支持浮出新的“许可证”属性。

**里程碑 2:**

*   我们将监控能够理解“许可证”属性的客户端的采用，当采用达到阈值时，NuGet.org 将禁用 licenseUrl 的使用。

**链接:**

*   功能发布:[将图标、许可证和文档打包到 nupkg 中](https://github.com/NuGet/Announcements/issues/29)
*   Spec wiki: [在 nupkg 内打包许可证](https://github.com/NuGet/Home/wiki/Packaging-License-within-the-nupkg)
*   讨论问题:[独立的 NuGet 包–许可证号 4628](https://github.com/NuGet/Home/issues/4628)
*   nuspec 中`license`元数据的文档:[https://docs . Microsoft . com/en-us/nu get/reference/nu spec # license](https://docs.microsoft.com/en-us/nuget/reference/nuspec#license)
*   在项目文件中使用 MSBuild 属性的`license`元数据的文档:[https://docs . Microsoft . com/en-us/nuget/reference/MSBuild-targets # packing-a-license-expression-or-a-license-file](https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets#packing-a-license-expression-or-a-license-file)

[View on GitHub](https://github.com/NuGet/Announcements/issues/32)

现在的解决方案是存储许可表达式(理想的是以 [spdx](https://spdx.org) 格式)*或者*将许可文件嵌入包中。[下面是我的项目](https://github.com/aaronpowell/dotnet-delice/blob/120cb43096c27739da9cc1cc7d925ee1b5294d38/src/DotNetDelice/DotNetDelice.fsproj#L10)中的场景。

通过采用这种方法，许可证现在与软件包的发布捆绑在一起，因此您不太可能在不知道的情况下更改它，因为更改需要更新的软件包。

由于这是对 NuGet 生态系统的一个相当大的改变，许多软件包仍然使用传统的许可格式。这使得`delice`更难确定一个包使用什么许可证。目前，该工具有一个已知许可证 URL 的“缓存”，以及它映射到哪个许可证(以及使用它的包)，但当发现一个未知的许可证时，它将被标记为*“无法确定”*，并在输出中显示该 URL。请随意提交 PR，将 URL 添加到缓存中！

希望，增加的可见性将有助于鼓励软件包作者更新他们的许可信息或鼓励人们提交 PR 来更新。

## 结论

`delice`旨在为您提供信息，以便您了解您的项目在使用什么，并对您使用的依赖项做出适当的决定。

GitHub repo 项目有一点路线图，但我想听听你想从这个工具中得到什么。