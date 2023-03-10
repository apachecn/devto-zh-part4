# 这不是魔术，这是网络包。

> 原文：<https://dev.to/crishanks/it-isn-t-magic-it-s-webpack-3ca4>

# 捆扎机

也许这篇文章更好的标题应该是“这不是魔术，这是网络包(或者 Browserify，Brunch 或者 package，等等)。)."这些被称为*模块捆绑器*，[将其定义为](https://github.com/ronami/minipack/blob/master/src/minipack.js)代码包，*“将小段代码编译成可以在网络浏览器中运行的更大更复杂的东西。”*本质上，这会将所有的样式表打包到一个包中，将类型脚本打包到另一个包中，将所有的 JavaScript 文件打包到另一个包中，等等。

模块捆绑器本质上计算出代码的哪些部分依赖于代码的其他部分(换句话说，捆绑器识别依赖性),并确保代码按照它需要的顺序运行。捆绑器最终创建一个**依赖图**，从一个根开始(它没有依赖关系)，其中一个捆绑代码在图中越往下，它就有越多的依赖关系。更下面的代码在加载自己之前，会等待它的依赖项先被加载。

# 那么，Webpack 是什么？

即使在简单的应用程序中，我们也在许多单独的文件中编写许多代码。我们使用像`@import`和 ES6 这样的语法。我们使用像 TypeScript 这样的助手，让我们能够编写更清晰的代码并更快地捕捉错误。想想用基于组件的库和框架构建的应用程序，比如 [React](https://reactjs.org) ，其中我们导入依赖于应用程序中其他地方编写的代码的组件或函数。我们的代码以浏览器能够理解的方式被解析、打包和执行，这看起来简直不可思议。但事实并非如此。**这是一款名为 Webpack 的捆扎机。**

然而，使 Webpack 脱颖而出的是它收集所有依赖项的能力，不仅包括代码，还包括图像、样式表(包括预处理程序，如 [sass](https://sass-lang.com/guide) 、 [typescript](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html) 等等)等资产，并创建前面提到的依赖图。这是关键-*web pack 的核心是构建依赖图。*

# 构建依赖图

依赖图由几个关键部分组成。这里我将重点介绍:入口、输出、加载器和插件。你可以运行`yarn add webpack webpack-dev-server --save-dev`并在你的应用根目录下创建一个`webpack.config.js`文件来开始。别忘了更新你的`package.json`剧本:

`"start": "webpack-dev-server",
"build": "webpack"`

### 1。建立一个切入点

Webpack 做的第一件事是建立一个入口点。这将是依赖图的根。我们可以通过创建一个包含指向源文件的对象的变量来做到这一点。这通常是`index.js`。您可以用一个简单的字符串创建对象，但是为了可伸缩性和可能需要多个入口点，我们使用一个对象。

[![entry point example](img/c8325771877a27941492f09f1017b3b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQaGYYPa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6ggk618bslok55kv0s5.png)

很简单。

### 2。建立输出

一旦 Webpack 完成了代码的捆绑和依赖树的创建，就需要告诉它把成品放在哪里；这是我们的产出。请记住，您可以随意命名 filename 参数。通常这将被命名为类似于`main.js`的东西。

[![output example](img/d2e269920840f1b24947962cad603997.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iwgb3ziX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z0w0zrnle4th0225iht3.png)

有趣的是，这是应用程序捆绑代码所需的基本信息。这样，你可以用`yarn start`启动你的本地服务器，Webpack 将开始做它的事情。

### 3。加载器:绑定 JavaScript 之外的文件类型

这就是 Webpack 变得如此酷的原因。

这是我最近的一个名为[小队](https://github.com/crishanks/squad-frontend)的 React 项目的`index.js`文件中的一些导入截图。

[![imports example](img/b9c6c81884f243fda1925dc7a27d5ee3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h0ZIBY8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogyxjw9p592j6ivh2kop.png)

这是我们一天要做 100 次的事情。但是幕后真正发生的是什么呢？我们正在告诉 Webpack 关于依赖关系`index.js`的需求。例如，行`import ./index.css`告诉 Webpack 获取那些样式。

在我们的`webpack.config.js`文件中，我们添加了一个模块对象，如下例所示(参见 [webpack 概念，加载器](https://webpack.js.org/concepts/))。

[![loaders module](img/37859c69fc587805f1f6a71a023914e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DsnxpIsE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/me9hqw5i05ryrb1f8hy0.png)

这个对象使用一个[正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)来识别某些文件类型，然后告诉 Webpack 在捆绑之前使用哪个加载器。我们说，“Webpack 编译器当你在一个导入中找到一个以`.css`结尾的路径时，在打包时使用`css-loader`和`style-loader`来转换/加载它们。”

**重要提示:**“转换”是指解析文件(而不是`.js`文件)，并将其翻译成 Webpack 和浏览器可以理解的其他东西。

几个例子:

*   你曾经在你的 CSS 文件中使用过`@import`或者类似`url('./icon.png')`的语法吗？Webpack 的 [css-loader](https://webpack.js.org/loaders/css-loader/) 就是原因！它解析你的`.css`文件并处理它。*这就是为什么你能`import Icon from ./icon.png;`和后来的`element.appendChild(Icon)`！*如 [Webpack 文档](https://webpack.js.org/guides/asset-management/)中所述，“加载器将识别这是一个本地文件，并用输出目录中图像的最终路径替换`./icon.png`路径。html-loader 以同样的方式处理`<img src="./icon.png" />`。是不是很酷？
*   TypeScript 是 JavaScript 的超集，允许 JS 开发人员编写更多静态类型的代码，以便在编写代码时捕捉错误。但是 Webpack 不会说 TypeScript。 [ts-loader](https://github.com/TypeStrong/ts-loader) 解析类型脚本文件，并将它们转换成 Webpack 可以读取的 JavaScript。
*   那么像类、`let`或`const`变量、箭头函数、默认值、析构等任何 [ES6 语法](https://www.w3schools.com/js/js_es6.asp)呢？ [babel-loader](https://webpack.js.org/loaders/babel-loader/) 解析你写的代码，识别 ES6 语法，加载并传输到浏览器理解的 ES5。

您可以了解更多关于加载器如何工作的信息，甚至如何在这里编写您自己的加载器[。](https://webpack.js.org/contribute/writing-a-loader/)

### 4。插件与加载程序

**装载机**

*   在文件级别工作
*   帮助创建包(在包生成期间或之前)

**插件**

*   在系统层面上工作
*   影响创建的包(包或块级别)
*   关注优化( [uglifyJS](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/#root) 取`bundle.js`并最小化它以减小文件大小)

代码实现如下所示:

[![plugins example](img/9825bcc6e42e3bf25193d83e7f128b77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mqggmp5G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jfur4zom47hn3mzbpvjo.png)

下面的[图](https://stackoverflow.com/questions/37452402/webpack-loaders-vs-plugins-whats-the-difference)可能也有助于形象化这种差异:

[![plugin vs. loader diagram](img/654b4cb94092f5f6e33538ae7a6a693c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ruFAvVAw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/177xjxju6es1511hnwfw.png)

# 总结

Webpack 是许多神奇秘方背后的罪魁祸首，这些秘方允许我们使用语法和库来使代码更干净、更清晰、更具可伸缩性。从 imports 到 ES6，如果没有 Webpack 这样的捆绑器，开发应用程序将会很困难。