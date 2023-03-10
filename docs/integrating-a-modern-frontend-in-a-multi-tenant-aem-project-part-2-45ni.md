# 在多租户 AEM 项目中集成现代前端(第 2 部分)

> 原文：<https://dev.to/anfibiacreativa/integrating-a-modern-frontend-in-a-multi-tenant-aem-project-part-2-45ni>

是时候发布这个系列的第二部分了。并进入实际的前端设置！

## 前端构建模块

如果你还没有，我建议你通过阅读[第一部分](https://dev.to/anfibiacreativa/integrating-a-modern-frontend-in-a-multi-tenant-aem-project-2l20)开始回顾提议的结构。重要的是其他一切都有意义。

如前所述，前端构建将在一个完全独立的 Maven 模块中。为什么？首先，因为这样我们的后端对手就可以跳过这部分编译(例如，通过实现 Maven profile)。前端构建时间现在成倍增长，有时对于一些后端特性来说是不必要的。

另一个原因，关注点分离。最好封装一些完全专门化的代码。就像我们处理函数一样，对吗？

因此，我们将通过添加一个名为 frontend 的新模块来扩展我们当前的结构，就像这样。

[![](img/54732cfd8e185f5d65bb4a08d1a2996a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0itLPK4d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/37c6mi5rqdplcfj3ct5o.png)

这使得前端的一个，一个文件夹。但是对于 Maven 来说，为了让它成为一个模块，您需要向它添加一个 pom.xml 文件。我们稍后将讨论这个 pom 文件(或者更确切地说，是在下面的 package 2 文件夹中的那个文件)，但是现在，让我们只关注前端问题。

## 前端模块作为一个 npm 包

对于 maven 来说，这将是一个模块。但是对于 npm 来说，这将是一个包。所以让我们继续前进，让它与`npm init`合二为一。像往常一样，我们回答几个问题，然后得到我们的`package.json`。这里我假设您已经在全局或本地安装了节点和 npm。现在，我们有可能开始安装依赖项。

## 什么依赖关系？

嗯，这在很大程度上取决于你选择的堆栈，以及你必须支持的浏览器。但是因为我们在本系列的第 1 部分中决定将 javascript 写成 ES6，将 css 写成。scss，我们将需要加载器和插件来加载、转换/编译这些语言。如果我们提到加载器和插件，是因为我们正在使用 webpack，所以我们也需要它。我们还说过我们不会使用任务管理器，比如 Gulp 或 Grunt，而是使用 npm CLI 直接从我们的 package.json 运行脚本

所以至少，这些是你需要作为开发依赖的一些节点模块。

*   网络包
*   webpack-cli
*   NPM-运行-全部

掌控整场演出。您至少需要

*   @babel/core
*   @babel/preset-env
*   巴别塔装载机

传递你的信念。和

*   节点-sass

运行你的 scss 代码。

## 棉绒

此外，因为您关心代码质量，您将希望拥有适当的林挺配置和工具，例如

*   埃斯林特
*   eslint 加载器
*   eslint-插件-导入
*   eslint-config-airbnb-base(这是一个行业标准，也是我所遵循的标准，在这里和那里做了一些调整)

此外，当然，你会想留意你的风格

*   stylelint

你将需要

*   postcss
*   postcss-loader 和
*   浏览器列表

经营

*   自动贴合

并确保跨浏览器支持。

因为您将不得不遍历您的结构来到处收集 clientlib 条目，所以您还需要

*   快速环球(或环球，但快速环球是...嗯，更快更轻)

一旦你有了所有这些，运行和`npm install`。

注意:您是否将每个都作为依赖项或 devDependency，将在很大程度上取决于您是否在构建时或运行时需要它们。做有意识的决定！

现在你需要把注意力放在这个包裹里的其他物品上。所以我的建议是你创建一系列的文件夹来存储

*   配置
*   任务
*   其他脚本
*   公用事业

因此该文件夹的内容如下所示

[![](img/299e8f7d46a7e8b519be27b5b3f2263f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6GUR3asK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v190z1xdqrlbj0a0myr1.png)

## 配置

是时候打配置现场了。webpack 的可配置方面是它迄今为止最好的特性，当涉及到大型企业平台时，就像我们正在讨论的，它是必不可少的。

但是由于您有如此多的工具，并且您可能希望将关注点分开，以便它们更易于维护，我建议您在您的包中创建一个 configs 文件夹，并将它们(或相关成员)导入到 webpack config 中。

这样，该文件夹中就会有几个配置，如下所示:

*   babel.config.js
*   clientlibs.config.js
*   p object . paths . config . js
*   project.alias.config.js
*   webpack.css.config.js
*   web pack . js . config . js-网路套件
*   wepack.config.js

最重要的配置文件之一，是你存储完成任务的所有路径的地方(我们在这里称之为`poject.paths.config.js`)。既然你要去收钱。scss 和 js 入口文件来自不同的组件和模块，您希望建立某些模式，然后遍历您的结构来获取它们。
让你有个想法，大概是这样的