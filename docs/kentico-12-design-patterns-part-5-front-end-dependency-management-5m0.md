# Kentico 12:设计模式第 5 部分-前端依赖管理

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-5-front-end-dependency-management-5m0>

<figure>

[![](img/19a9a0ebc578065ba5a481389d7e24be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNitp1zO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f990qahf699vx8vcohri.jpg)

<figcaption>Photo by [chuttersnap](https://unsplash.com/@chuttersnap) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

自从 Kentico CMS 12 发布以来，[ASP.NET MVC 成为基于 Kentico 构建网站和应用的推荐框架](https://devnet.kentico.com/documentation/kentico-12-mvc)，我们有了实现许多开发目标的新方法。

作为。NET 开发者，我们传统上通过 NuGet 包管理我们的库依赖。

我们可以通过哪些方式来管理我们的前端依赖关系？可用选项的利弊是什么？🤔

在这篇文章中，我将讨论开发人员在构建 Kentico 12 MVC 站点时可以使用的两个主要选项，并描述为什么我认为其中一个明显优于另一个。

## 使用`System.Web.Optimization`

当创建一个新的 Kentico 12 MVC 项目时，我们在`App_Start`文件夹中得到几个配置类。其中之一在`BundleConfig.cs`中。

这个`BundleConfig`类将`ScriptBundle`和`StyleBundle`实例添加到由`BundleTable.Bundles`提供的`BundleCollection`中。

```
private static void RegisterJqueryBundle(BundleCollection bundles)
{
    var bundle = new ScriptBundle("~/bundles/jquery")
    {
        CdnPath = "https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.min.js",
        CdnFallbackExpression = "window.jQuery"
    };

    bundle.Include("~/Scripts/jquery-{version}.js");

    bundles.Add(bundle);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后这些包可以在视图中被引用，通常是在`_Layout.cshtml`中，通过用来注册它们的标识符。

```
<body>
  <!-- begin content -->
  <div class="container">
    @RenderBody()
  </div>
  <!-- end content -->

  @Scripts.Render("~/bundles/jquery")
</body> 
```

Enter fullscreen mode Exit fullscreen mode

所有这些类型都可以在`System.Web.Optimization`名称空间中找到，你也可以在 [GitHub](https://github.com/aspnet/AspNetWebOptimization/tree/master/src/System.Web.Optimization) 上找到源代码。🤓

> 你可以在[微软文档](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-bundling-and-minification-with-aspnet-mvc)中阅读更多关于如何使用`BundleCollection`进行捆绑和缩小的信息。

在 ASP.NET，`System.Web.Optimization`和`BundleTable.Bundles`的主要目标是给开发者一个捆绑和缩小 JavaScript 和 CSS 文件集的简单方法。

这些框架特性，为我们提供了开箱即用，“只需工作”。😀

然而，这些工具是在管理客户端依赖关系困难的时候创建的，当时社区还没有建立一致性或最佳实践，并且被管理的依赖关系要简单得多。

## 与`System.Web.Optimization`的问题

所有这些捆绑技术都被 ASP.NET 核心重新包装成集成到 Visual Studio 中的新工具，名为 [LibMan](https://devblogs.microsoft.com/aspnet/library-manager-client-side-content-manager-for-web-apps/) 。

在 LibMan 的描述中提供了一个有用的解释，将它(和 ASP。NET 的“捆绑”方法)的观点，给出了所有可用于构建现代 web 应用程序的工具:

> 如果你乐于使用 npm/yarn/(或其他东西)，我们鼓励你继续这样做。LibMan 不是作为这些工具的替代品开发的。

文档还提到 LibMan 是简单的用例，不需要额外的工具:

> 如果您的项目不需要额外的工具(如 Node、npm、Gulp、Grunt、WebPack 等),而您只是想获得几个文件，那么 LibMan 可能适合您。

由于 ASP.NET 试图简化客户端依赖管理的方式，它导致了一些我不同意的设计模式:

*   👎🏼将客户端依赖视为一小桶脚本和样式
*   👎🏼通过从互联网手动下载文件来管理库版本
*   👎🏼将库提交到源代码控制中，并将它们包含在 ASP.NET 项目中(通常在`\Scripts`或`\Styles`文件夹下)
*   👎🏼不[树摇动](https://en.wikipedia.org/wiki/Tree_shaking)客户端依赖
*   👎🏼没有使用现代的 CSS 工具( [Sass](https://sass-lang.com/) 、 [PostCSS](https://postcss.org/) 、 [stylelint](https://stylelint.io/) )
*   👎🏼没有使用现代的 JavaScript 特性(transpiling、用于依赖管理的 es 模块、ES2015+语言增强)

2019 年的 web 开发世界与 2009 年 ASP.NET MVC 第一次出现时有很大不同——让我们拥抱我们生活的世界！😉

> 有[一些增强 ASP 的工具](https://www.codeproject.com/articles/842961/introducing-dynamic-bundles-for-asp-net-mvc)。NET 的捆绑功能，提供依赖管理，以及更多的基于特性文件夹的方法，但是它们不能解决我上面提到的所有问题。

## 使用客户端工具

那么，我们将用什么来代替`System.Web.Optimization`？

我认为我们应该使用现代的客户端开发工具来管理我们的客户端依赖性。

*   用于包和版本管理的✅ npm
*   ✅·萨斯创建了我们的样式表
*   用于捆绑和缩小的✅ Webpack、GulpJs、ParcelJs 或 SPA CLI
*   ✅ VS 代码的最佳编辑器+工具体验

### 要求

我们需要安装以下工具，以获得最佳的客户端开发体验:

*   [节点 j](https://nodejs.org/en/download/)
*   [VS 代码](https://code.visualstudio.com/)

> 安装和使用 VS 代码的原因将在我的下一篇文章中更加清晰

### 移除`System.Web.Optimization`

首先，我们需要删除所有现有的捆绑代码。🔫🤠💣

删除`App_Start\BundleConfig.cs`和`Global.asax.cs`中对它的引用。

接下来，删除对`Shared\_Layout.cshtml`中`@Scripts.Render()`和`@Styles.Render()`的调用。

我们还将删除`\Scripts`和`\Styles`目录，因为我们所有的客户端库将由`npm`管理，我们的 CSS 文件将从我们的 Sass 文件生成。

### 使用 npm

首先，打开终端并导航到 MVC 项目目录。

假设您安装了 VS Code，您应该能够通过键入以下命令来打开您当前的文件夹:

```
code . 
```

Enter fullscreen mode Exit fullscreen mode

接下来，用`npm` CLI 初始化项目，并接受所有默认设置(您可以稍后更改):

```
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

现在，开始为您想要使用的库安装软件包！在本例中，我们将安装`jquery` :

```
npm install jquery 
```

Enter fullscreen mode Exit fullscreen mode

> 我们希望确保从 npm 安装的库不会被提交到源代码控制。npm 将其所有的包存储在一个`\node_modules`文件夹中，这个文件夹应该被添加到我们的`.gitignore`文件中。

### 创建客户端代码

为了在我们的应用程序中使用`jQuery`,我们需要编写一些现代的 JavaScript 并使用它。😎

创建一个`\src`文件夹，我们将在这里保存客户端源文件的入口点。

> 在下一篇文章中，你将看到我们如何采用基于“特性文件夹”的方法进行客户端开发。

我们将创建的第一个文件`\src\styles.scss`，将是我们所有 [Sass](https://sass-lang.com/) 代码的入口点。补充以下(不是很惊艳)内容:

```
// Yup, we're using Kentico's theme!
$background-color: #f14b00;

body {
    background-color: $background-color;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，用以下内容创建【T0:】T1

```
/*
 * We use this non-standard import 
 * to ensure our Sass is part of the build process
 */
import './styles.scss'; 

import $ from 'jquery';

const PIE = '🍰';

$(() => console.log(`Document loaded! It's easy as ${PIE}`)); 
```

Enter fullscreen mode Exit fullscreen mode

> 要了解更多关于`import`、`const`、`() =>`和`${}`的含义，网上有许多精彩的免费资源，如阿克塞尔·劳施迈尔博士的[探索 ES6](https://exploringjs.com/es6/index.html)、[从巴别塔](https://babeljs.io/docs/en/learn/)学习 ES2015，以及[从 Egghead](https://egghead.io/courses/learn-es6-ecmascript-2015) 学习 ES6

### ParcelJs

如果我们使用像 [ParcelJs](https://parceljs.org/getting_started.html) 这样的工具进行构建和捆绑，我们可以非常快速地运行，但是对于我们可以在多大程度上为客户端依赖性定制我们的构建管道有所限制。

> ParcelJs 是一个很好的开始工具，我们将在我的下一篇文章中探索其他选项。

要使用它，我们需要安装 ParcelJs 作为开发依赖项(使用`-D`选项):

```
npm i parcel-bundler -D 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要定义将与使用 ParcelJs 的 npm 一起运行的命令，因此将您的`package.json`中的`scripts`块替换为以下内容:

```
 "scripts":  {  "start":  "parcel watch src/app.js",  "dev":  "parcel build src/app.js --no-minify",  "prod":  "parcel build src/app.js"  }, 
```

Enter fullscreen mode Exit fullscreen mode

当我们在命令行运行`npm start`时，我们可以看到我们的 JavaScript 和 Sass 被编译到一个`\dist`目录中，sourcemaps 有助于在浏览器开发工具中进行调试。👍

ParcelJs 将继续关注源文件的变化，并在我们保存这些变化时自动生成新的输出。😏

> 要停止这种“观察”模式，请键入`ctrl+c`

运行`npm run dev`将创建与`npm start`相同的文件，但是一旦编译完成，该命令将退出。

如果我们运行`npm run prod`，我们将产生一个“生产”就绪的代码版本。

> 对于任何客户端的构建过程，我们最终都会得到“编译的”/“传输的”输出，这是我们**不想**提交给源代码控制的。这些文件或文件夹应该添加到我们的`.gitignore`文件中。

### 使用客户端构建输出

为了使用这个构建输出，我们需要在我们的`Shared\_Layout.cshtml`中添加对它的引用。

之前我们引用了`jquery`和 CSS 包，现在我们可以引用 ParcelJs 构建的输出:

```
<head>
  <!-- various meta -->
  <link href="/dist/app.css" rel="stylesheet" />
</head>
<body>
  <!-- body content -->
  <script src="/dist/app.js"></script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

### 端到端构建协调

为了确保在 Visual Studio 中构建 ASP.NET 项目时创建客户端资产，我们可以在 MVC 项目的`.csproj`文件中使用 MSBuild 配置。

我们需要它来执行以下步骤:

*   ✅安装 npm 软件包
*   ✅根据构建运行正确的 npm 命令(调试/发布)
*   ✅以正常结束。净构建

> 在 StackOverflow 上有一个聪明的解决方案，它使用文件修改日期来确保如果我们已经安装了所有的东西，就不要安装软件包。🤯

添加到我们的`.csproj`中的以下 MSBuild XML 将服务于我们的目的:

```
<PropertyGroup>
    <!-- File with mtime of last successful npm install -->
    <NpmInstallStampFile>node_modules/.install-stamp</NpmInstallStampFile>
</PropertyGroup>
<ItemGroup>
    <JSFile Include="src\**\*.js" />
    <SCSSFile Include="src\**\*.scss" />
</ItemGroup>
<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|AnyCPU' ">
    <NpmCommand>npm run dev</NpmCommand>
    <NpmOutput>dist\app.js</NpmOutput>
</PropertyGroup>
<PropertyGroup Condition=" '$(Configuration)|$(Platform)' != 'Debug|AnyCPU' ">
    <NpmCommand>npm run prod</NpmCommand>
    <NpmOutput>dist\app.js</NpmOutput>
</PropertyGroup>
<Target Name="NpmInstall" 
    BeforeTargets="NpmBuildClientAssets" 
    Inputs="package.json"
    Outputs="$(NpmInstallStampFile)">
    <Exec Command="npm install" />
    <Touch Files="$(NpmInstallStampFile)" AlwaysCreate="true" />
</Target>
<Target Name="NpmBuildClientAssets"
    BeforeTargets="BeforeBuild" 
    Inputs="@(JSFile);@(SCSSFile)"
    Outputs="$(NpmOutput)">
    <Exec Command="$(NpmCommand)" />
</Target> 
```

Enter fullscreen mode Exit fullscreen mode

> 在 VS 代码中打开项目的一个好处是编辑`.csproj`文件要容易得多😁

现在，当我们在 Visual Studio 中构建我们的项目时，我们保证在站点开始运行之前，客户端构建资产就在`\dist`目录中。👏🏽

## 那么我们完成了什么？

在我们展望我们将何去何从之前，让我们记住我们在哪里！

我们意识到，虽然 ASP.NET 在`System.Web.Optimization`中提供给我们的类在刚出现时有很好的 API 和工具，但 web 和前端开发已经发生了显著的变化。🤔

有一些我们想要避免的软件开发模式，比如将库提交给源代码控制，这是旧方法所鼓励的。😞

使用客户端工具进行客户端开发实际上效果很好！😄

我们还可以将客户端开发过程集成到我们的。NET 开发过程中有一个伟大的端到端的解决方案。💪

## 接下来是什么？

现在我们已经设置了基础部分，我们可以开始探索所有可以改善我们开发体验的出色的前端工具和库。

在我的下一篇文章中，我将讨论这些工具和库，如何将它们集成到 VS 代码中，以及“最佳实践”可能是什么样子。😮

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)系列。