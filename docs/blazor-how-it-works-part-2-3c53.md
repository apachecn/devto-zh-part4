# Blazor 工作原理第 2 部分

> 原文：<https://dev.to/rembou1/blazor-how-it-works-part-2-3c53>

# Blazor 是如何工作的？第 2 部分:建筑

*这篇博客文章基于 net core SDK 3 . 0 . 100-preview 5-011568，一些内容可能会在未来发生变化。*

这篇博文是我关于 blazor(客户端)如何工作的系列文章的第二部分，从构建过程到 UI 的更新。[第一部分](https://dev.to/Blazor-how-it-works-part-1/)以一个“简单”的命令“dotnet run”开始，以生成代表 razor 文件的类结束。现在我们来看看构建过程的其余部分，以及处理与 monowasm 集成的部分。

为了写第一篇博文，我从“dotnet build”命令的输出开始，试图寻找与组件和 Blazor 相关的东西。我把博文停在了名为“RazorGenerateComponentDeclaration”的那一步。在这一步之后，我们可以找到许多与 Blazor 相关的步骤:

*   RazorCompileComponentDeclaration
*   _ resolvecomponentorazorgenerateinputs
*   _ CheckForIncorrectComponentsConfiguration
*   _ PrepareBlazorOutputConfiguration 配置
*   _DefineBlazorCommonInputs
*   _GenerateLinkerDescriptor
*   _ CollectBlazorLinkerDescriptors
*   _LinkBlazorApplication
*   _ BlazorResolveOutputBinaries
*   _ResolveBlazorBootJsonInputs
*   _GenerateBlazorBootJson
*   _ BlazorCopyFilesToOutputDirectory
*   _BlazorBuildReport

## RazorCompileComponentDeclaration

这是在“Sdks\Microsoft。NET . SDK . razor \ build \ NET standard 2.0 \ Microsoft。NET . SDK . razor . component . targets”的第 189 行。这个任务基本上是一个 roslyn (c#编译器)任务。它将之前生成的组件和其他 c#类构建到一个程序集中。文件的列表位于变量“_ RazorComponentDeclaration”中。结果程序集将位于 obj 文件夹(文件 Sdks\Microsoft 上的变量 BaseIntermediateOutputPath)中。NET.Sdk\targets\Microsoft。NET.DefaultOutputPaths.targets 第 37 行)。我不会在这里深入 roslyn 编译器的细节，这超出了我的技能范围。

## _ resolvecomponentorazorgenerateinputs

此任务设置 2 个 MSBuild 元素:

*   item group RazorGenerateWithTargetPath 是为包含生成组件类的路径而设置的
*   property group _ RazorComponentDeclarationAssemblyFullPath 包含上一步生成的程序集的路径。

## _ checkforincorrecomponentsconfiguration

这个任务只做一件事:显示一个警告(RAZORSDK1005:“一个或多个 Razor 组件文件(。razor ),但是该项目没有配置为编译 Razor 组件。通过定位 RazorLangVersion 3.0 或更新版本来配置项目。有关更多信息，请参见[https://go.microsoft.com/fwlink/?linkid=868374."](https://go.microsoft.com/fwlink/?linkid=868374.%E2%80%9D))如果项目上有任何 razor 文件，并且项目没有配置为构建 razor 文件。

如果移除项目文件上的标签“RazorLangVersion ”,就会发生这种情况。

## _ preparblazooutput configuration

这个任务没有在 Sdk 中定义，如果你在你的 Sdk 文件中寻找任务名称，你不会找到它，但是它在哪里呢？如果您查看构建日志，可以看到日志中有任务位置(我的窗口位于法语中):

" dans le fichier " C:\ Users \ remi . nu get \ packages \ Microsoft . aspnetcore . Blazor . build \ 3 . 0 . 0-preview 5-19227-01 \ targets \ Blazor。mono runtime . targets " du projet " C:\ Users \ remi \ Source \ Repos \ Toss \ Toss。客户\折腾。Client.csproj " "

所以，这个任务是在一个 nuget 包中定义的，但是它是如何工作的呢？

*   首先你需要添加一个对 Blazor 的引用。为每个 Blazor 项目构建 nuget 包
*   布拉索。Build nuget 包有一个“Build”文件夹，按照惯例，该文件夹由 MSBuild 读取，它包含。目标文件
*   这个文件的内容很简单，包括位于 nuget 包的“targets”文件夹中的 All.targets 文件。
*   这个文件和它包含的其他文件(Blazor。MonoRuntime.targets 和 Publish.targets)定义了 Blazor 运行所需的所有其他内容
*   task _ PrepareBlazorOutputConfiguration 配置具有属性“AfterTargets ”,该属性将一个任务与其他任务挂钩。这是 MSBuild 的扩展机制之一。

这个任务被很好地注释了，并且为构建的“最终”输出准备了所有需要的路径(Blazor。MonoRuntime.targets 行 119)。基本上，它创建了许多路径(如 blazor.boot.json 路径)并创建了“中间”输出路径“/obj/configuration/target framework/blazor/”。

在这个任务上我们可以看到 mono.wasm 和 mono . js(web assembly 上的 clr 实现)取自 nuget 包 Microsoft . aspnetcore . blazor . mono .这是由\Microsoft 中的变量 MonoWasmRuntimePath 定义的。软件包上的 AspNetCore.Blazor.Mono.props。

我们现在可以理解 aspnet 核心团队是如何为 Blazor 更新 monowasm 运行时的了:

*   他们建立了微软。AspNetCore.Blazor.Mono，带有最新的 monowasm 版本
*   他们发布了一个新的包，包括 mono.wasm
*   他们更新了布拉索。构建最新的 Blazor 包。单声道版本
*   他们发布了新版本的布拉索。建设

## _ DefineBlazorCommonInputs

该任务写入 3 个文件:

*   一个包含所有依赖项的散列、当前程序集、链接和调试设置(\ obj \ Debug \ net standard 2.0 \ blazor \ inputs . basic . cache)
*   一个包含所有依赖项的路径(\ obj \ Debug \ net standard 2.0 \ blazor \ inputs . copy local . txt)
*   另一个包含当前版本的链接选项(\ obj \ Debug \ net standard 2.0 \ blazor \ inputs . linker switch . cache)

## _GenerateLinkerDescriptor

这个任务为“链接器”创建一个 XML 配置文件(我们很快就会看到它是什么):\ obj \ Debug \ net standard 2.0 \ blazor \ linker . descriptor . XML。

这个文件看起来是这样的

```
<linker>
   <assembly fullname="Toss.Client" />
</linker> 
```

它只是生成的程序集的全名。

## _ CollectBlazorLinkerDescriptors

此任务使用所有依赖程序集和链接器设置的哈希创建一个文件(\ obj \ Debug \ net standard 2.0 \ blazor \ inputs . linker . cache)。在某种程度上，我不明白为什么他们需要所有这些带有 hash 的文件，似乎 inputs.basic.cache 中的 hash 也差不多。但是我一定是错了:)

## _LinkBlazorApplication

这是调用“链接器”的地方。首先，它收集项目程序集文件名、其依赖项和 BCL(基本类库，它包含运行时的基本类，如 DateTime、String 或 file)。然后它用这样的命令调用链接器:

```
dotnet ${Blazor.Mono path}/tools/illink/illink.dll {path to output directory blazor/linker} {path to all the assemblies} {path to previously created XML with assemblyname} {additionnal options} 
```

链接器源代码在这里定义([https://github.com/mono/linker](https://github.com/mono/linker))。自述文件详细说明了这一点“IL 链接器是一种工具，可以用来只传送一组程序运行可能需要的最少 IL 代码和元数据，而不是完整的库。”。根据文档，链接过程如下:

*   它从给定的上下文(程序集)中加载所有的类型、字段和方法
*   对于所有它们和它们使用的其他元素(递归地)，它将决定如何对其进行注释:是否链接，是否处理。
*   然后，根据之前的决定，一些元素(类型、方法……)将被移除
*   然后，新的程序集将基于这个决定被创建。我认为这有点像角度/节点项目中的树摇动。

## _ blazoresolveoutputbinaries

此任务由另外两个任务组成:_CollectLinkerOutputs 或 _CollectResolvedAssemblies

_CollectLinkerOutputs 从链接器输出中输出程序集列表，并在“bin \ Debug \ net standard 2.0 \ dist _ framework _ bin”中创建它们的目标路径。

_CollectResolvedAssemblies 与 _CollectLinkerOutputs 相同，但仅当链接器被禁用并从依赖关系列表中读取程序集列表时才运行。

## _ ResolveBlazorBootJsonInputs

此任务将一组设置(配置、程序集和 pdb 路径、启用链接器、启用调试)写入“obj \ Debug \ net standard 2.0 \ blazor \ input . boot JSON . cache”中的文本文件。

这个任务用程序集和 pdb 路径写(再次)一堆文件。我想我可以在这个构建过程中找到至少 10 个带 dll 路径文件:)。然后它用第一个参数“write-boot-json”调用[这个程序](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Blazor/Build/src/Cli/Program.cs)。该参数导致执行[该命令](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Blazor/Build/src/Cli/Commands/WriteBootJsonCommand.cs)，该命令读取文件输入(来自之前的 steo)并在“obj \ Debug \ net standard 2.0 \ blazor”中创建 json 文件

## _ BlazorCopyFilesToOutputDirectory

这一步非常简单:在整个构建过程中，一个名为 BlazorItemOuput 的变量被填充了一个源文件及其目的地的列表。这个任务读取这个变量，然后将源文件复制到它的目的地，主要是从 obj 复制到 bin。这用于在运行时仅保持 obj 文件夹中的文件有用。

## _BlazorBuildReport

此任务显示先前复制的文件数量，并列出所有上述文件。

# 结论

写这篇文章很有趣，因为它有助于理解微软人脑子里在想什么。这也有助于更好地理解。net 项目。

当你在 sdk / target 文件或其他东西上有错误时，它也有助于调试。也许这将是人们在 blazor 构建过程中寻找扩展点的一个起点(改变 dll 扩展？).

我知道我可能在一些步骤上含糊不清，如果你需要更多的细节，请在下面提问，我很乐意挖掘更多。

下一篇博文将是关于运行时的。