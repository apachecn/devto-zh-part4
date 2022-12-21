# 使用 Svelte 和 Cosmic JS 构建一个简单的 ToDo 应用程序

> 原文：<https://dev.to/sumitkharche/build-simple-todo-application-using-svelte-and-cosmic-js-3lpm>

在本教程中，我将向你展示如何使用 [Svelte](https://svelte.dev/) 和 [Cosmic JS](https://cosmicjs.com/) 创建一个简单的 Todo 应用程序。所以让我们喝杯咖啡，开始编码吧。

# TL；速度三角形定位法(dead reckoning)

*   [苗条待办事项应用程序演示](https://cosmicjs.com/apps/svelte-todo-app)

*   [下载源代码](https://github.com/cosmicjs/svelte-todo)

# 先决条件

你需要安装最新稳定版本的节点 JS 和 NPM。如果您对以下内容有基本的了解，那么学习本教程不会有任何问题:

*   HTML、CSS 和 JavaScript
*   导入和导出语法(ES6 模块)
*   异步/等待语法
*   像组件这样的概念
*   axios API

让我们从我们的技术堆栈开始吧。

# 什么是苗条？

[Svelte](https://svelte.dev/) 是 Rich Harris 创建的 JavaScript UI 库。几个月前发布了 Svelte 3。所以你认为，如果我们有反应/角度/Vue，为什么我们需要学习苗条。这些语言/框架还不够吗？有一篇很棒的文章发表在《苗条》杂志上，它告诉你发展苗条身材背后的原因。这里是链接:[苗条-3-反思-反应](https://svelte.dev/blog/svelte-3-rethinking-reactivity)

以下是一些关于苗条身材的有趣之处:

*   Svelte 是一个**编译器**，而不是像 React 或 Vue 那样的依赖
*   对于 React 需要多 40% LOC 的相同事情，Svelte 似乎需要更少的代码
*   Svelte 没有虚拟 DOM，可以编译成最简单的“普通”JavaScript，看起来比其他库性能更好
*   你的应用程序速度非常快。如果你看到[这个 JS 框架基准](https://github.com/krausest/js-framework-benchmark)，Svelte 比使用 Angular、React、Vue 等构建的应用要快得多。这是因为 Svelte 不过是香草味的 JS。

如果你想了解更多关于苗条身材的信息，请浏览[苗条 REPL](https://svelte.dev/repl/) 。

# 宇宙 JS 呢？

Cosmic JS 是一个 [API 优先的 CMS](https://cosmicjs.com/) ，帮助开发者和内容编辑团队更快地构建应用。它提供了许多有助于轻松管理数据的强大功能。 [Cosmic JS](https://cosmicjs.com/) 是一个令人惊叹的内容管理系统，它提供了以对你有意义的方式对数据进行分类和分发的选项。

# 在宇宙 JS 中创建新桶

要创建应用程序，我们需要获取或存储数据。为此我们将使用宇宙 JS 的力量。在 [Cosmic JS](https://cosmicjs.com/) 上创建一个免费账户，并创建新的空桶&命名为 todo-app。
对于基本待办事宜 app 会有两种对象类型:

*   名字
*   已完成

所以在 bucket 中添加这个属性和一些初始数据。[按照这些步骤在 Cosmic JS](https://cosmicjs.com/getting-started) 中创建 bucket。

# 创建苗条的应用程序

创建苗条的应用程序是非常容易的。我们使用默认模板来创建待办事项列表应用程序。

```
npx degit sveltejs/template todo-app
cd todo-app
npm install 
```

要在本地机器上运行它，请点击下面的命令。

```
npm run dev 
```

默认情况下，Svelte 应用程序在 [http://localhost:5000](http://localhost:5000) 上运行。

因此您可以在项目中看到 rollup.config.js 文件。像 webpack 一样， [rollup.js](https://rollupjs.org/) 是 JavaScript 的模块捆绑器，它将小段代码编译成更大更复杂的东西，比如一个库或应用程序。

Svelte app 的起点是 main.js 文件。所有苗条的部分都有。纤细的伸展。如果你看看周围的文件，你会看到 App.svelte 文件，这是根组件。因此，一个基本的苗条组件被分成 3 个部分:

*   在 **script** 标签下，你必须编写你的 JavaScript 代码。
*   在**风格**标签下，你必须定义你的风格。它使用 CSS-in-JS 风格来定义你的 CSS。
*   然后你可以写 HTML 标记。

现在首先我们使用 Cosmic JS Rest api 从 Cosmic JS 中获取 Todos。因此，要将您的应用程序与 Cosmic JS Bucket 连接起来，请在 src 文件夹和代码下面创建 config.js 文件。