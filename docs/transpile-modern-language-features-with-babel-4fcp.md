# 巴别塔的现代语言特征

> 原文：<https://dev.to/4nduril/transpile-modern-language-features-with-babel-4fcp>

## 前言

本文是“发布现代 JavaScript(或 TypeScript)库”系列的第 2 部分。查看简介中的动机和其他部分[的链接。](https://dev.to/4nduril/publish-a-modern-javascript-or-typescript-library-3hc3)

## 为什么是巴别塔，你应该如何在图书馆使用它？

**如果对设定背后的背景和推理不感兴趣，[直接跳到结论](#tmplfwb-conclusion)T3】**

Babel 可以传输 JavaScript 和类型脚本。我认为使用 Babel 而不是 TypeScript 编译器将代码编译成兼容的 JavaScript 更好，因为这样更快。Babel 在编译 TypeScript 时所做的就是丢弃所有不是 JavaScript 的东西。Babel 不做类型检查。此时我们不需要它。

要使用 Babel，你必须先安装它:运行`npm install -D @babel/core @babel/cli @babel/preset-env`。这将安装核心文件，预置你总是需要和命令行界面，这样你就可以在你的终端上运行 Babel。此外，您应该根据自己的需要安装`@babel/preset-typescript`和/或`@babel/preset-react`。我会解释一下它们的用途，但是你可以从它们的名字中想象出在什么情况下你需要它们。

那么，准备时间到了！Babel 是通过配置文件配置的。(详情和特殊情况见[文档](https://babeljs.io/docs/en/config-files)。)项目范围的配置文件应该是`babel.config.js`。看起来至少和这个很像:

```
module.exports = {
  presets: [
    [
      '@babel/env',
      {
        modules: false,
      }
    ],
    '@babel/preset-typescript',
    '@babel/preset-react'
  ],
  plugins: [
    [
      '@babel/plugin-transform-runtime',
      { corejs: 3 }
    ]
  ],
  env: {
    test: {
      presets: ['@babel/env']
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看一下，因为在这个配置中使用了一些假设，我们将需要这些假设用于列表中的其他功能。

## `module.exports = {…}`

该文件被视为一个 CommonJS 模块，并期望返回一个配置对象。也可以导出一个函数，但是这里我们将坚持静态对象。关于功能版本，查看[文档](https://babeljs.io/docs/en/config-files#config-function-api)。

## `presets`

预设是(有时是可配置的)巴别塔插件集，这样你就不必自己管理你需要的插件。你绝对应该用的是`@babel/preset-env`。您已经安装了它。在配置中的`presets`键下，您列出了您的库将要使用的每个预置以及任何预置配置选项。

在上面的配置示例中，有三个预设:

1.  `env`是提到的标准之一。
2.  `typescript`显然只需要编译包含 TypeScript 语法的文件。正如已经提到的，它的工作原理是**扔掉**任何不是 JavaScript 的东西。它不解释甚至不检查 TypeScript。*这是好事。*我们稍后会谈到这一点。如果您的库不是用 TypeScript 编写的，则不需要此预置。但是如果需要的话当然要装:`npm install -D @babel/preset-typescript`。
3.  `react`显然只在 React 项目中需要。它带来了 JSX 语法和转换插件。需要的话用:`npm i -D @babel/preset-react`安装。注意:通过配置选项`pragma`(可能还有`pragmaFrag`)你可以将 JSX 转换成除了`React.createElement`之外的其他函数。参见[文档](https://babeljs.io/docs/en/babel-preset-react#pragma)。

让我们再来看一下`env`预设。值得注意的是`preset-env`的`modules: false`选项。效果是这样的:按照默认的 Babel trans piles es modules(`import`/`export`)到 CommonJS modules ( `require()` / `module.export(s)`)。当`modules`设置为`false`时，Babel 将输出转换后的文件，不改变它们的 ESModule 语法。其余的代码将被转换，只有模块相关的语句保持不变。这(至少)有两个好处:

首先，这是一个图书馆。如果您将它作为单独的文件发布，那么库的用户可以准确地导入他们需要的模块。如果他们使用一个具有 treeshake 能力的捆绑器(也就是说:在捆绑时移除未使用的模块)，他们最终将只能从您的库中获得他们需要的代码位。用 CommonJS 模块是不可能的，他们会把你的整个库打包。

此外，如果你打算以捆绑包的形式提供你的库(例如一个可以通过[unpkg.com](http://unpkg.com)使用的 UMD 捆绑包)，你可以利用树抖动，尽可能地缩小你的捆绑包。

`preset-env`还有另一个可疑的缺失选项，那就是`targets`选项。如果你忽略它，Babel 会把你的代码向下转换到 ES5。这很可能不是你想要的——除非你生活在 JavaScript 黑暗的中世纪时代(或者你知道有人使用 <abbr title="Internet Explorer">IE</abbr> )。如果运行时环境可以处理您的现代代码，为什么要传输一些东西(并生成更多的代码)？你可以做的是提供所说的`targets`键，并给它一个 [Browserslist](https://github.com/ai/browserslist) 兼容的查询(参见 [Babel 文档](https://babeljs.io/docs/en/babel-preset-env#targets))。比如像`"last 2 versions"`或者甚至`"defaults"`这样的东西。在这种情况下，Babel 将使用 browserslist 工具来找出它必须转换哪些特性才能在使用`targets`的环境中运行。

但是我们将使用另一个地方来放置这个配置，而不是使用`babel.config.js`文件。你看，Babel 并不是唯一可以利用 browserslist 的工具。但是任何工具，包括 Babel，如果在正确的位置，都会找到配置。browserslist 的文档建议将其放在`package.json`中，所以我们会这样做。将类似以下的东西加入你牌库的`package.json` :

```
 "browserslist":  [  "last 2 Chrome versions",  "last 2 Firefox versions",  "last 2 Edge versions",  "last 2 Opera versions",  "last 2 FirefoxAndroid versions",  "last 2 iOS version",  "last 2 safari version"  ] 
```

Enter fullscreen mode Exit fullscreen mode

我承认这个问题有点固执己见，也许对你没有好处。你当然可以自己卷，或者如果你不确定，就用这个:

```
 "browserslist":  "defaults"  //  alias  for  "> 0.5%, last 2 versions, Firefox ESR, not dead";  contains  ie  11 
```

Enter fullscreen mode Exit fullscreen mode

我提出上面的查询数组的原因是我想得到一个适合现代浏览器的优化版本。`"defaults"`、`"last 2 versions"`(没有具体的浏览器名称)之类的会包括 Internet Explorer 11、三星 Internet 4 之类的东西。这些古老的浏览器甚至不支持 ES2015。我们最终会得到一个比现代浏览器所需要的大得多的可交付产品。但是你可以做点什么。您可以将现代代码交付给现代浏览器，同时仍然支持 Ancients。我们将在以后的章节中进一步讨论细节，但是有点悬而未决:browserslist 支持多种配置。目前，我们将只针对现代浏览器。

## `plugins`

上面的巴别塔配置定义了一个额外的插件:`plugin-transform-runtime`。使用它的主要原因是帮助器代码的重复数据删除。当 Babel 转换你的模块时，它注入很少(或不那么少)的辅助函数。问题是它在每个需要它们的文件中都这样做。`transform-runtime`插件用`@babel/runtime`包的`require`语句替换所有注入的函数。这意味着**在最终的应用中，必须有这个运行时包**。

要做到这一点，您只需将`@babel/runtime`添加到库的 prod 依赖项(`npm i @babel/runtime`)中。那肯定会有用。但这里我们将把它添加到`package.json`中的`peerDependencies`。这样，你的库的用户必须自己安装它，但另一方面，他们对版本有更多的控制(并且你不必太频繁地更新依赖)。也许他们已经安装了。所以我们只是把它推到一边，确保它在需要的时候就在那里。

回到巴别塔插件。要使用这个插件，你必须安装它:`npm i -D @babel/plugin-transform-runtime`。现在你可以走了。

在我们继续讨论`env`键之前，这里是讨论 polyfills 以及如何在 Babel 中使用它们的合适地方。

## 如何以最佳方式使用聚合填充

我花了几个小时阅读和理解这个问题，目前的解决方案及其弱点。如果你想自己读完，从[巴别塔聚合填充](https://babeljs.io/docs/en/babel-polyfill)开始，继续读[巴别塔转换-运行时](https://babeljs.io/docs/en/babel-plugin-transform-runtime)，然后读[核心-js@3，巴别塔和展望未来](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md)。

但是因为我已经做了如果你不想做的话，你可以不做。好了，让我们从有两种标准的方法将 polyfills 放入你的代码开始。等等，退一步说:为什么是 polyfills？

如果你已经知道，跳到[进口 core-js](#tmplfwb-import-core-js) 。当 Babel 根据您指定的目标环境传输您的代码时，它只是改变语法。目标(浏览器)不理解的代码被改变成(可能更长更复杂)做同样事情并被理解的代码。但是除了语法之外，还有一些东西可能不被支持:特性。比如承诺。或者像`Object.is`或`Array.from`这样的其他内置类型的某些特征，或者像`Map`或`Set`这样的全新类型。因此，我们需要 polyfills 为本身不支持这些功能的目标重新创建这些功能。

另请注意，我们在此仅讨论 ES 功能或一些密切相关的 Web 平台功能的聚合填充(参见此处的[完整列表)。有些浏览器功能，例如全局`fetch`功能，需要单独的聚合填充。](https://github.com/zloirock/core-js/blob/master/README.md#features)

## 导入核心-js

好的，有一个叫做`@babel/polyfill`的 Babel 包，你可以在你的应用程序的入口点导入它，它从一个叫做 [`core-js`](https://github.com/zloirock/core-js) 的库中添加所有需要的 polyfills，以及一个单独的运行时用于`async/await`和生成器函数。**但是从 Babel 7.4.0 开始这个包装器包就被弃用了。相反，你应该安装并导入两个独立的包:`core-js/stable`和`regenerator-runtime/runtime`。**

然后，我们可以从上面的`env`预设中得到一个很好的效果。我们将配置更改为:

```
 [
      '@babel/env',
      {
        modules: false,
        corejs: 3,
        useBuiltIns: 'usage'
      }
    ], 
```

Enter fullscreen mode Exit fullscreen mode

这将转换我们的代码，这样整个`core-js`的导入将被移除，取而代之的是 Babel 在每个需要的文件中注入特定的 polyfills。并且只有我们通过`browserslist`定义的目标环境中需要的聚合填充。所以我们最终只需要最少的额外代码。

这里补充两个注意:(1) 你要显式设置`corejs`到`3`。如果密钥不存在，巴别塔将使用第二版的`corejs`，你不想这样。版本 3 有了很大的改进，尤其是特性方面。而且 bug 已经被修复，包的大小也明显变小了。如果你愿意的话，可以在这里[(概述)](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#what-changed-in-core-js3)和这里[(3 . 0 . 0 版本的变更日志)](https://github.com/zloirock/core-js/blob/master/CHANGELOG.md#300---20190319)。

以及(2)，对于`useBuiltIns`还有另一个可能的值，那就是`entry`。这种变体不会计算出您的代码实际上需要哪些特性。相反，它只会添加给定目标环境中存在的所有*poly fill。它的工作原理是在你的源代码中寻找`corejs`导入(比如`import corejs/stable`，它应该只在你的代码库中出现一次，可能在你的入口模块中。然后，它会将此“元”导入替换为与目标匹配的所有特定多孔填充导入。这种方法可能会产生一个非常非常大的包，其中包含许多不需要的代码。所以我们就用`usage`。(使用`corejs@2`时,`usage`有一些问题，可能会导致关于您需要哪种聚合填充的错误假设。所以在某些情况下`entry`是更安全的选择。但是这些问题在版本 3 中似乎得到了解决。)*

## 告诉 transform-runtime 导入 core-js

获取代码所需的 polyfills 的第二种方法是通过上面的`transform-runtime`插件。您可以配置它，不仅为 Babel 助手注入导入，还为您的代码需要的`core-js`模块注入导入:

```
 plugins: [
    [
      '@babel/plugin-transform-runtime',
      {
        corejs: 3
      }
    ]
  ], 
```

Enter fullscreen mode Exit fullscreen mode

这告诉插件将导入语句插入 corejs 版本 3。这个版本的原因我已经在上面提到过[。](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#what-changed-in-core-js3)

如果你配置插件使用`core-js`，你必须改变运行时依赖:现在`peerDependencies`应该包含`@babel/runtime-corejs3`而不是`@babel/runtime`！

## 应该用哪种方式？

一般来说，手动导入和`env`预置的组合是针对应用程序的，而使用`transform-runtime`的方式是针对库的。其中一个原因是，使用`core-js`的第一种方式会导入“污染”全局名称空间的聚合填充。如果你的库定义了一个全局`Promise`，它可能会干扰你的库的用户使用的其他帮助库。由`transform-runtime`注入的进口被遏制。它们从不设置全局变量的`core-js-pure`导入。

另一方面，使用转换插件并不考虑你的目标环境。也许将来它也可以使用与`preset-env`相同的试探法，但是现在它只是添加理论上你的代码需要的每一个聚合填充。即使目标浏览器不需要它们或者不需要全部。关于那个方向的发展，请看 corejs 维护者的[评论和 Babel](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#babelruntime-for-target-environment) 的 [RFC 问题。](https://github.com/babel/babel/issues/10008)

因此，看起来您必须在一个添加尽可能少的代码的包和一个能够很好地处理周围未知应用程序的包之间做出选择。我尝试了一下不同的选项，并将结果文件与 webpack 捆绑在一起，这是我的结果:

您可以从`preset-env`获得带有`core-js`全局变量的最小包。但是对于一个库来说，弄乱用户的全局名称空间太危险了。除此之外，在不久的将来，transform-runtime 插件也将使用 browserslist 目标环境。所以大小问题将会消失。

## `env`键

使用`env`您可以为特定的构建环境添加配置选项。当 Babel 执行时，它会寻找`process.env.BABEL_ENV`。如果没有设置，它将查找`process.env.NODE_ENV`，如果没有找到，它将返回到字符串`'development'`。完成查找后，它将检查配置是否有一个`env`对象，以及该对象中是否有一个键与之前找到的 env 字符串相匹配。如果存在这样的匹配，Babel 将应用该 env 名称下的配置。

例如，我们将它用于我们的测试跑步者 [Jest](https://jestjs.io/) 。因为 Jest 不能使用 ESModules，所以我们需要一个 Babel 配置来将我们的模块转换成 CommonJS 模块。所以我们只是在环境名`'test'`下为`preset-env`添加了一个替代配置。当 Jest 运行时(我们将使用`babel-jest`。请参阅本系列的后续部分。)它将`process.env.NODE_ENV`设置为`'test'`。所以一切都会好的。

## 巴别塔设置的结论和最终注释

安装所有需要的软件包:

`npm i -D @babel/core @babel/cli @babel/preset-env @babel/plugin-transform-runtime`

将 peerDependency 添加到您的`package.json`中，您的用户应该自行安装:

```
...
  "peerDependencies": {
      "@babel/runtime-corejs3": "^7.4.5", // at least version 7.4; your users have to provide it
  }
... 
```

Enter fullscreen mode Exit fullscreen mode

创建一个至少包含以下内容的`babel.config.js`:T1

```
// babel.config.js

module.exports = {
  presets: [
    [
      '@babel/env', // transpile for targets
      {
        modules: false, // don't transpile module syntax
      }
    ],
  ],
  plugins: [
    [
      '@babel/plugin-transform-runtime', // replace helper code with runtime imports (deduplication)
      { corejs: 3 } // import corejs polyfills exactly where they are needed
    ]
  ],
  env: {
    test: { // extra configuration for process.env.NODE_ENV === 'test'
      presets: ['@babel/env'] // overwrite env-config from above with transpiled module syntax
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你写的是 TypeScript，运行`npm i -D @babel/preset-typescript`并将`'@babel/preset-typescript'`添加到`presets`中。

如果你写了 React 代码，(JSX)运行`npm i -D @babel/preset-react`并将`'@babel/preset-react'`添加到`presets`。

在你的 package.json 中添加一个`browserslist`部分:

```
...  "browserslist":  [  "last 2 Chrome versions",  "last 2 Firefox versions",  "last 2 Edge versions",  "last 2 Opera versions",  "last 2 FirefoxAndroid versions",  "last 2 iOS version",  "last 2 safari version"  ]  ... 
```

Enter fullscreen mode Exit fullscreen mode

如果使用包含不支持生成器函数和/或 async/await 的目标的另一个 browserslist 查询，您必须告诉您的用户:

Babel 的 transform-runtime 插件将导入`regenerator-runtime`。这个库依赖于一个全局可用的 Promise 构造函数。**但是**巴别塔将不会包括一个再生器运行时的承诺聚合填充。可能是因为它只为真正属于你的代码的东西添加聚合填充，而不是外部库代码。这意味着，如果你的用例满足这些条件，你应该在你的自述文件或安装说明中提到，你的库的用户必须确保在他们的应用程序中有一个可用的承诺。

这就是巴别塔的设置。

接下来:用 TypeScript 编译器编译

* * *

非常感谢我的朋友 Tim Kraut 校对了这篇文章！