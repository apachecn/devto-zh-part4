# 升级旧的 NativeScript 应用程序以使用 Webpack

> 原文：<https://dev.to/toddanglin/upgrading-older-nativescript-apps-to-use-webpack-3e15>

“老”app 听起来好消极。

我也不喜欢“遗产”、“遗产”或“衰老”

让我们来看看“深受喜爱”的本地脚本应用程序。👍不管你怎么称呼他们，他们都是在 NativeScript 完全拥抱 webpack 之前就开始了，现在他们需要加入 webpack 党来不断获得最新的 NativeScript 更新。

在 NativeScript 5.4 中，NativeScript 应用的默认构建模式改为使用 [Webpack](https://webpack.js.org/) 。随着[本周 NativeScript 6.0 的发布](https://www.nativescript.org/blog/announcing-nativescript-6.0---webpack-all-the-way-seamless-app-updates-new-core-theme-and-a-lot-more)🎉，Webpack 是唯一支持的构建模式。Webpack 构建确实提供了很大的好处，比如减少应用包大小和提高应用性能(加载更少的 JavaScript ),但是随之而来的是更复杂的构建配置...特别是对~~来说，一个更老的~~一个很受欢迎的应用程序，它不是从 Angular 或 Vue 模板(一开始就与 webpack 一起工作)开始的。

我最近将我的智能家居控制器应用从 NativeScript 5.1 更新到 5.4，并对 webpack 进行了大切换。我的 app 是 2015 年开始的，所以不用 Angular 或者 Vue，但是确实用了 TypeScript。我的升级一点也不顺利，但希望我的痛苦会成为你的收获。这里有一些提示，希望能帮助您更容易地将 NativeScript 升级到 webpack。

## 开始升级

显然，我们需要做的第一件事是将我们的环境和项目升级到 NativeScript 的最新版本。截至本周，这是{N} 6.0。使用
升级 CLI

```
$ npm install -g nativescript 
```

*注意:当我经历这个过程时，我更新到了{N} 5.4，但是大部分建议仍然适用于第一次使用{N} 6.0 采用 webpack 的{N}项目。*

然后，在您的项目中，更新`package.json`以使用最新版本的`tns-ios`、`tns-android`和`tns-core-modules`。此时考虑升级其他依赖项和插件通常也是一个好主意。事实上，在{N} 6.0 中，有一个新的 CLI 命令可以为您自动更新这些 NativeScript 依赖项！

```
$ tns migrate 
```

在进行框架升级时，我总是删除生成的`platforms`、`hooks`和`node_modules`文件夹，以尽量减少缓存资产带来的问题。一旦这些文件夹被删除，它们可以通过
重新生成

```
$ tns install 
```

如果您的项目是第一次添加 webpack，安装过程将询问您是否要切换到 webpack 构建，并将生成所需的`webpack.config.js`并更新`package.json`中的开发依赖项。

## 删除旧生成的 JavaScript 文件

当您将 TypeScript 与 NativeScript *pre-webpack* 一起使用时，构建过程会自动编译工作目录中的`.ts`到`.js`，然后将`.js`同步到设备或模拟器。使用 webpack 构建，`.js`文件不会在工作目录中生成。太好了！但是...如果您忘记清除旧的生成的`.js`文件，它会造成一些真正的调试头痛。(问我怎么知道的...)

所以，首先要做的是。良好的卫生习惯。从工作目录中清除生成的文件。如果您使用的是 git，这可以通过如下命令快速完成:

```
$ git clean -fX 
```

此命令将从工作目录中删除所有被忽略的文件。

如果您切换到 webpack 版本，并且似乎您在 TypeScript 中所做的更改在设备上不起作用，这可能是一个原因。

## 重命名文件以注册为模块

**这是一个大问题。**如果您正在升级一个非 Angular/Vue NativeScript 项目，那么您很有可能在项目中有自己的命名文件和视图的约定。**使用默认的 NativeScript webpack 配置，模块文件名必须包含`page`或`root`。**如果这两个字中有一个不在文件名中，就不会注册为模块。

编辑:在{N} 6.0 的最终版本中，默认的 webpack 配置被更改为包含项目中的所有模块。这提高了向后兼容性，意味着您不必重命名文件。👍如果您采用升级到{N} 5.4 的 webpack，所有这些仍然适用。而且，如果您仍然决定要采用标准的{N}文件命名约定，下面的提示应该可以帮助您更容易地做到这一点。

有两种解决方案:

1.  修改 webpack 配置(在`webpack.config.js`中)以使用现有文件名
2.  重命名所有视图文件名，以便将它们注册为模块

虽然重命名一堆文件令人望而生畏(并且有一些级联影响)，但我最终选择了这条路。这是一次性税收，它将使未来的本地脚本升级更容易。

我的项目以前有这样的文件:

*   `settings.xml`
*   `settings.css`
*   `settings.ts`

要使用 webpack，这些文件现在是:

*   `settings-page.xml`
*   `settings-page.css`
*   `settings-page.ts`

我的项目也有许多在运行时动态组合的“部分”视图。在这些文件名中使用`page`或`root`感觉不对，所以我稍微修改了一下`webpack.config.js`，以容纳一个`widget`选项:

```
registerModules: /(root|page|widget)\.(xml|css|js|ts|scss)$/ 
```

这种改变是在`webpack.config.js` > `config` > `module` > `rules` >加载器选项中做出的。

同时，不与视图**关联的 TypeScript 文件不需要**重命名。它们将被单独的`ts-loader` webpack 配置拾取并正常工作。说归说，你没必要给每个文件都重新命名*。*

### 收拾重命名的烂摊子

您刚刚重命名了与应用中的视图相关联的所有文件。如果您使用以下任何需要文件名和路径的 API，很可能会破坏一些代码:

*   `.navigate`
*   `.showModal`
*   `builder.load`或`builder.parse`

在您的代码库中搜索这些 API 调用，并更新文件名以匹配新的 webpack 友好名称。这将捕捉到许多文件重命名可能对您的代码产生的常见问题。如果您正在以编程方式`import` ing(或`require()` ing)任何视图，那么这些路径也必须更新。

## 相对路径变化

有了 webpack，相对路径解析*可以*以不同于 webpack 之前版本的方式工作。这主要适用于在`.xml`视图中解析相对路径的方式。幸运的是，相对于根语法的“`~/`”在 webpack 的任何地方都能很好地工作，所以任何使用相对路径导航的地方都可以转换成使用`~/`语法。

例如，我的应用程序有几个视图像这样导入一个本地定制组件:

```
<Page xmlns:ui="../../shared/ui/loadingSpinner" 
```

为了让 webpack 能够工作，现在看起来是这样的:

```
<Page xmlns:ui="~/shared/ui/loadingSpinner-widget" 
```

(别忘了文件重命名。)

这在类型脚本文件中也适用于`import`语句。到处使用`~/`可能是个好主意，但是 webpack 不会破坏代码中的相对路径。

## `Frame`元素现在是必需的

这应该是一个快速简单的解决方案，但仍然至关重要。大约在{N} 5.0 的时候，增加了对多个`frame`元素的支持，这样就可以用 NativeScript 实现更复杂的视图(想想:拆分视图中的独立导航)。到目前为止，如果您没有手动添加一个`frame`元素到您的`.xml`视图，NativeScript 可以隐式地创建它。对于 webpack，缺少`frame`元素将导致运行时错误。

对于大多数应用程序，在“根”视图中只需要一个`frame`。这是所有其他“页面”视图将加载的视图。如果您的应用程序没有“根”视图:

1.  创建一个名称为“root”的新顶级视图(例如:`myapp-root.xml`)
2.  在新的根视图中，添加以下 XML(带有对默认“页面”的引用)

```
<Frame id="my-app-root" defaultPage="views/myApp-page"></Frame> 
```

1.  更新您的`app.ts`以在您的应用启动时加载`root`模块

```
app.run({ moduleName: "views/myapp-root" }); 
```

正如您所知，webpack 构建的 NativeScript 就像是在“严格”模式下运行 NativeScript。

## 建造者`parse` vs `load`

如果您在运行时使用`builder` API 动态加载模块，您可能需要更新您的代码来使用`builder.parse`而不是`builder.load`。Webpack 在运行时预先注册并加载 JavaScript 和 XML，这可能会给`builder`带来麻烦。简而言之，`builder`试图在运行时加载已经加载(通过 webpack)的东西。(本期有更多关于 GitHub [的讨论。)](https://github.com/NativeScript/nativescript-dev-webpack/issues/620)

为了解决我的代码中的这个问题，我修改了这个:

```
let newViewJs = require("views/dynamicView.js");
let newView = builder.load("views/dynamicView.xml", newViewJs); 
```

对此:

```
let newViewJs = require("~/views/dynamicView.ts");
let newView = builder.parse(<string>require("~/views/dynamicView-widget.xml"), newViewJs); 
```

我们现在正在寻找这些 TypeScript 和 XML 路径(在 webpack 中注册),并从 webpack 编译的、缩小的运行时资源中加载资源。

坦白地说，这感觉很糟糕。可能有更好的方法。看起来这个项目正在围绕这些 API 积极地重构。

## 调试错误

即使是最彻底的升级，当你第一次启动你的应用程序时，你也可能会遇到一些新的与 webpack 相关的运行时错误。以下是我遇到的两个常见错误，以及我是如何克服它们的:

### 修复`Module not found`错误

当我第一次尝试运行我的 webpack 构建的 NativeScript 项目时，我经常遇到这个错误*。简而言之，这个错误表明在您的应用程序中配置的一些路径(比如您的`app.ts`在启动时使用的`moduleName`或导航路径)在注册的 webpack 模块中找不到。解决步骤:*

 *1.  **确保该模块已实际注册。**我没有意识到我在第一次升级时必须更改文件名才能正确注册模块，所以这些知识应该已经为您节省了一些时间。使用 webpack“构建报告”也帮助我排除了故障。

    `$ tns build ios --env.report`

    这就创建了一个名为`report`的文件夹，其中有一个 HTML 和 JSON 文档，可以用像这样的工具[进行分析。对于相对 webpack n00bs(像我一样)来说，这个报告有助于理解 webpack 在做什么。](http://webpack.github.io/analyse/)

2.  **确保代码中的文件名正确。**假设你走了我的路，重命名了文件，确保代码正在寻找带有`page`、`root`或任何你添加到`webpack.config.js`中的额外标记的新文件名

3.  **确保 JSON 导入包含文件扩展名。**在我的应用程序中，有几个地方我直接`require`-了一个 JSON 文件(通常是加载一些静态配置值)。在 webpack 之前，这些导入不需要显式的文件扩展名，因此这是可行的:

    `let config = require("../shared/config");`

    这与 webpack 的注册路径不匹配，因此现在要工作，必须添加文件扩展名:

    `let config = require("../shared/config.json");`

    顺便说一下，如果您使用本地 HTTP(而不是显式地`require()` ing)在项目中加载静态 JSON 资源，那么在构建应用程序时，您将需要进一步更新`webpack.config.js`来加载这些静态资源。

```
 new CopyWebpackPlugin([
                { from: { glob: "assets/*.json" } }, //<- Add this
                { from: { glob: "fonts/**" } },
                { from: { glob: "**/*.jpg" } },
                { from: { glob: "**/*.png" } },
                ... 
```

### 修复`Module parse failed`错误

我遇到的另一个常见错误是这个`Module parse failed`错误，通常伴随着`Unexpected token`或`Unexpected character`日志消息。

在我的例子中，所有这些错误都是由于意外地将文件注册为不需要的模块造成的。像这样进行 NativeScript 升级可能是一种边缘情况，但是如果您发现自己对这个错误感到头疼，请仔细检查您是否无意中将文件作为模块加载。

作为参考，我的项目中导致这些意外错误的不可靠的文件试图动态地将`require`文件如下:

```
let instance = require(`${dynamicPath}`); 
```

不要用 webpack 这样做。

### 仔细检查桶文件中的循环引用

我不可能在我的应用程序中添加循环引用。

我更清楚。我不会那么做的。

让 webpack 来给我做些谦卑的馅饼吧。某人添加的循环引用(检查贡献者计数:1😬)在 webpack 之前没有对应用程序产生任何负面影响，但一旦 Strict 先生加入了该党，他们就会在启动时触发应用程序崩溃。

这些循环引用通过[“桶”文件](http://tattoocoder.com/angular2-barrels/)悄悄进入我的代码。基本上，便利模块重新导出许多相关模块，以简化以后的导入。这是从 Angular 借用的概念。例如，下面是我的应用程序中的一个桶文件:

`app-factories.ts`

```
export * from "./BaseController.factory";
export * from "./SceneProvider.factory";
export * from "./CameraProvider.factory";
export * from "./ThermostatProvider.factory"; 
```

使用这个桶文件简化了导入，如下所示:

```
import { BaseController, SceneProvider, CameraProvider } from "~/providers/app-factories"; 
```

我的错误是不小心在一个由桶文件导出的文件中使用了一个桶文件快捷方式。Webpack 让我意识到了我的错误，但是错误消息很隐晦，问题也不是很明显。吸取的教训:webpack 可能会暴露代码中的错误，而这些错误之前并没有阻止你的应用程序运行。

## 下一步

唷。这需要一些繁重的工作，但经过一点调试，我的应用程序现在运行在 NativeScript 5.4 上，并与 webpack 版本一起工作。它还准备好了 NativeScript 6.0，现在应该是一个快速而简单的升级。🤞

在升级到{N} 6.0 时，NativeScript 团队[在本周](https://www.nativescript.org/blog/nativescript-6.0-application-migration)的一篇新博客文章中强调了一些潜在的“问题”，因此请务必查看其他提示。

随着我的应用程序基础的更新，我的下一步是解决应用程序后端并开始与 Azure 连接。现在真正的乐趣可以开始了！在那之前，我希望这些从痛苦中吸取的教训能让你为广受欢迎的 NativeScript 应用程序升级 webpack 变得更加容易。*