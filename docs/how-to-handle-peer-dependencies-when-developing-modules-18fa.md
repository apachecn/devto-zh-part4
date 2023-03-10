# 开发模块时如何处理对等依赖

> 原文：<https://dev.to/yvonnickfrin/how-to-handle-peer-dependencies-when-developing-modules-18fa>

## 什么是同伴依赖，它有什么问题？

[对等依赖](https://nodejs.org/es/blog/npm/peer-dependencies/#the-solution-peer-dependencies)是一种对可重用模块非常有用的特殊依赖:

*   要求用户安装模块需要工作的依赖项，而不要特别指定版本
*   防止在用户的应用程序 node_modules 中有同一个模块的多个版本
*   减小 javascript 文件的大小，以便在浏览器端加载，这对移动用户特别有用

对等依赖的问题是`npm`和`yarn`根本不安装。对于生产目的来说，这是正确的行为，但是当你在开发时，你可能需要在一个主机应用程序中测试你的模块。`npm`和`yarn`提供了一个名为`link`的命令来实现它，这个命令基本上创建了一个到主机应用程序`node_modules`的符号链接到你的模块源文件夹。它工作得很好，但是您还需要在需要这些依赖项的模块中执行任务。例如，您可能想要执行测试。因为它们不在你的模块的`node_modules`中，你会遇到这样的错误:

```
Cannot find module 'react' from 'index.js' 
```

Enter fullscreen mode Exit fullscreen mode

`yarn`和`npm`不提供为您的开发环境安装对等依赖项的工具。自 2016 年 10 月 27 日以来`yarn`储存库上有一个未解决的问题，但是`yarn`有一个特殊的脚本叫做`prepare`，它在依赖项安装后仅在开发模式下执行，也许我们可以用它做些什么🤔。让我们想办法在周围建立一个工作！

## 解决问题

我制作了一个包含所有资源的[库](https://github.com/frinyvonnick/handling-peer-dependencies)来重现这个问题。

我们有如下的文件夹结构:

```
.
├── LICENSE
├── README.md
├── app
│   ├── node_modules
│   ├── package.json
└── lib
    ├── node_modules
    └── package.json 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将建立一个链接，使应用程序的`node_modules`指向 lib 文件夹。

你必须安装依赖项，你可能还需要先构建你的模块

```
cd lib
yarn link
cd ../app
yarn link lib 
```

Enter fullscreen mode Exit fullscreen mode

此时，如果您启动您的应用程序，您可以毫无困难地使用您的模块。当你试图执行模块文件夹中的东西时，问题就出现了，就像我之前说的，比如测试。为了解决这个问题，我们将使用[准备来自`yarn`的](https://yarnpkg.com/en/docs/package-json#toc-scripts)脚本和[安装包-对等-cli](https://github.com/alexindigo/install-peers-cli) 。

是一个安装软件包的对等依赖项的 cli。应该在依赖项安装后调用它。幸运的是`prepare`是在依赖项安装后调用的，并且只在你开发时调用，所以当用户安装你的模块时，它不会安装对等依赖项。

首先安装`install-peers-cli`包:

```
yarn add -D install-peers-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的模块的`package.json`中添加`prepare`脚本，并在其中调用`install-peers-cli`:

```
// package.json
{
  "scripts": {
    "prepare": "install-peers"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以启动任何使用对等依赖关系的东西，而不会遇到错误。除非你的模块用了`React`和他新加的`Hooks`...

## 为使用钩子进行反应

如果您按照前面的步骤操作，您应该会看到一条错误消息“钩子只能在函数组件的主体内部调用”。当 node_modules 文件夹中有多个`React`副本时，会出现此错误。在这篇[评论](https://github.com/facebook/react/issues/14257#issuecomment-439967377)中，丹·阿布拉莫夫解释了这个众所周知的问题的解决方案。让我们把它付诸实践。

您应该进入模块的`node_modules`中的`react`文件夹并创建一个链接。然后返回到您的主机应用程序文件夹并使用此链接。

```
cd lib/node_module/react
yarn link
cd ../../../app
yarn link react 
```

Enter fullscreen mode Exit fullscreen mode

现在错误应该被修复，你可以在你的主机应用程序中使用你的模块了！

你都准备好了🙌！

*看到错别字？不要犹豫，在[文章的存储库](https://github.com/frinyvonnick/handling-peer-dependencies)* 上提出问题或请求