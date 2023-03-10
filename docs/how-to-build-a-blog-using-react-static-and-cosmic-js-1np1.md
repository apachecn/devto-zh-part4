# 如何使用 React Static 和 Cosmic JS 创建博客

> 原文：<https://dev.to/sumitkharche/how-to-build-a-blog-using-react-static-and-cosmic-js-1np1>

在本教程中，我将向你展示如何使用 [React](https://reactjs.org/) 、 [React Static](https://react-static.js.org/) 和 [Cosmic JS](https://cosmicjs.com/) 创建一个简单但速度极快的博客。喝点咖啡&让我们创建一个很酷的应用程序。

# TL；速度三角形定位法(dead reckoning)

*   [演示](https://cosmicjs.com/apps/react-static-blog)
*   [下载源代码](https://github.com/cosmicjs/react-static-blog)

# 先决条件

你需要安装最新的稳定版本[节点 JS](https://nodejs.org/) 和 NPM。让我们从我们的技术堆栈开始吧。

# 什么是 React Static？

React-Static 是一个非常快速和强大的渐进式静态站点生成器，基于 React 及其生态系统实现。它非常轻便。它非常快，支持 SEO，是这个星球上最友好的静态站点库。

# 宇宙 JS 呢？

Cosmic JS 是 headless CMS，用于构建更快的应用程序。存储数据非常容易，构建这样的应用程序也很容易。

# 创建 React 静态 app

创建 React 静态应用程序非常容易。只需遵循以下步骤:

安装 CLI 工具:

```
$ npm i -g react-static
# or
$ yarn global add react-static 
```

创建一个新项目！

```
react-static create 
```

它会要求项目的名称，然后给它作为**咖啡博客**。之后，它会要求选择模板，所以你必须选择**基本**。

# 在宇宙 JS 中创建新桶

要创建博客应用程序，我们需要数据。为此我们将使用宇宙 JS 的力量。在 [Cosmic JS](https://cosmicjs.com/) 上创建免费账户，并创建新的空桶&，命名为 coffee-blog。每个博客都有三种对象类型:

*   标题
*   内容
*   图像

所以在 bucket 中添加这个属性和一些数据。[按照这些步骤在 Cosmic JS](https://cosmicjs.com/getting-started) 中创建 bucket。

# 将宇宙 JS 集成到 React 静态 App 中

在 coffee-blog 项目中创建名为 config.js 的新文件，该文件将存储 Cosmic JS Bucket、read_key 和 write_key。