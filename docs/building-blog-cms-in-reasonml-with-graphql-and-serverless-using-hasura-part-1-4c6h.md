# 用 GraphQL 和 Hasura 构建无服务器的博客内容管理系统

> 原文：<https://dev.to/hasurahq/building-blog-cms-in-reasonml-with-graphql-and-serverless-using-hasura-part-1-4c6h>

这是博客文章系列的第一部分，我们将使用 Hasura 为 GraphQL API 创建博客 cms，为逻辑创建无服务器函数，在客户端我们将使用 ReasonML 语法编写现代且健壮的代码。让我们开始吧。

# 原因介绍

首先，在进入实际的代码编写之前，我们先讨论一下为什么 ReasonML？尽管这是一篇独立博文的主题，我还是会试着给你一个简单的概述。ReasonML 给了我们一个由 Ocaml 支持的奇妙的类型系统，但是就语法而言，它看起来非常接近 Javascript。它是由发明 React 的乔丹·沃克(Jordan Walke)发明的，并被脸书信使公司用于生产。最近，各种各样的公司也采用了 Reason，并在生产中使用它，因为它真的是一个很酷的范例:“如果它能编译，它就能工作。”
这个短语是一个非常大胆的说法，但事实上，因为 Reason 基本上是 OCaml 语言的一个新语法，它使用了 [Hindley Milner](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system) 类型系统，所以它可以在编译时推断类型。

### 对于我们这些开发者来说意味着什么？

这意味着我们通常不会像在 TypeScript 中那样编写那么多类型，并且可以信任编译器来推断这些类型。

说到编译，Reason 可以被编译成 OCaml，而 OCaml 又可以编译成各种目标，比如二进制、ios、android 等，我们还可以在 Bucklescript 编译器的帮助下编译成人类可读的 JavaScript。事实上我们会在博客中。

### 那么在 JavaScript 领域我们习惯的 npm 和所有这些包呢？

事实上，BuckleScript 编译器为我们提供了强大的外部函数接口 [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface) ，它允许您在原因代码中使用 javascript 包、全局变量甚至原始 JavaScript。您唯一需要做的就是准确地对它们进行类型化，以便从类型系统中获益。

顺便说一句，如果你想了解更多关于 ReasonML 的内容，我在 Youtube 上直播了 10h 现场编码训练营，你可以在我的[频道](https://www.youtube.com/c/VladimirNovickDev)观看

# 原因反应

当使用 Reason 进行前端开发时，我们将使用 [ReasonReact](https://reasonml.github.io/reason-react/) 。也有一些 VueJs 的社区绑定，但主要是，当开发 web 时，我们将使用 ReasonReact。如果你过去听说过 Reason 和 ReasonReact，那么最近 ReasonReact 得到了一个巨大的更新，使它更容易编写，所以现在创建 Reason 组件的语法不仅非常流畅，而且看起来比 JavaScript 好得多，这在过去是不存在的。此外，随着钩子的引入，创建 ReasonReact 组件和管理状态变得更加容易。

## 入门

在官方 ReasonReact 文档中，创建新项目的建议方式是从`bsb init`命令开始，但是让我们面对它。您可能想知道如何从 JavaScript 和 Typescript。因此，在我们的示例中，我们将从使用 create-react-app 创建项目开始。

我们将从运行以下命令开始:

```
npx create-react-app reason-hasura-demo 
```

Enter fullscreen mode Exit fullscreen mode

它将用 JavaScript 创建我们的基本 React 应用程序，我们现在将把它改为 ReasonReact。

### 安装

如果这是你第一次在你的环境中设置 ReasonML，它将像安装 bs-platform 一样简单。

```
yarn global add bs-platform 
```

Enter fullscreen mode Exit fullscreen mode

同样，通过安装合适的[编辑器插件](https://reasonml.github.io/docs/en/editor-plugins)来配置你的 IDE

为此我使用了 [reason-vscode](https://marketplace.visualstudio.com/items?itemName=jaredly.reason-vscode) 扩展。我也强烈建议使用`"editor.formatOnSave": true,` vscode 设置，因为 Reason 有一个名为`refmt`的工具，它基本上是为 Reason 内置的，所以你的代码在保存时会被正确格式化。

### 向您的项目添加原因

现在是时候添加 ReasonML 了。我们将安装`bs-platform`和`reason-react`依赖项。

```
yarn add bs-platform --dev --exact
yarn add reason-react --exact 
```

Enter fullscreen mode Exit fullscreen mode

进入配置。对于具有以下配置创建的`bsconfig.json`文件:

```
{  "name":  "hasura-reason-demo-app",  "reason":  {  "react-jsx":  3  },  "bsc-flags":  ["-bs-super-errors"],  "sources":  [  {  "dir":  "src",  "subdirs":  true  }  ],  "package-specs":  [  {  "module":  "es6",  "in-source":  true  }  ],  "suffix":  ".js",  "namespace":  true,  "bs-dependencies":  [  "reason-react"  ],  "ppx-flags":  [],  "refmt":  3  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们也将编译和观察脚本添加到我们的包中

```
"re:build": "bsb -make-world -clean-world",
"re:watch": "bsb -make-world -clean-world -w", 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这些脚本，基本上会发生的事情是，您项目中的所有`.re`文件将与您的`.re`文件一起被编译成 javascript。

### [T1】开始配置我们的根端点](#start-configuring-our-root-endpoint)

让我们编写第一个原因文件，将 index.js 从
改为

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister(); 
```

Enter fullscreen mode Exit fullscreen mode

到