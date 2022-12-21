# 如何用一个命令部署您的“CreateReactApp”

> 原文：<https://dev.to/frankzickert/how-to-deploy-your-createreactapp-with-a-single-command-3ked>

**TL；DR: `create-react-app`让 React 的入门变得简单。但是如何将 React 应用程序部署到 AWS 呢？这篇文章告诉你怎么做！**

脸书的`create-react-app`提供了一种便捷的 React 入门方式。您不必为配置 Webpack、Typescript 之类的东西而烦恼。你刚刚开始你的深潜到反应！

只有一个问题:**你如何部署你的应用？**

如果一个网络应用不在线，它就不会真正有用，对吗？

我们开始吧！

## 解决方案概述

在这篇文章的最后，你将能够部署你的 React 应用程序:

**T2`npm run deploy-my-app`**

而且也不是太复杂。答应了。

我们将使用以下内容:

*   **Node.js** 是一个 Javascript 运行时。它允许您在开发机器上运行基于 Javascript 的脚本。
    `create-react-app`就是这样一个需要 Node.js (8.10+)的脚本。

*   **npx** 是执行节点包的工具。

*   npm 是 Node.js 的一个包管理器，它允许你从一个中心库安装包和库。

*   **`create-react-app`** 是一个创建 React 项目样板文件的脚本，它不需要任何构建配置。

*   基础设施-组件允许你通过一组 React 组件而不是配置文件来定义 React 应用的架构。

*   **基础设施脚本**是允许您在没有任何配置的情况下启动和部署基于基础设施组件的项目的脚本。

*   **Code-Architect** 是基础设施组件的托管服务。它允许您将应用程序部署到托管 AWS。您不需要设置自己的帐户。

## 你可能已经做了什么...

-如果您已经在本地安装并运行了应用，您可以跳过本章-

-您想设置一个新项目吗？看看[我的另一篇帖子](https://www.infrastructure-components.com/page?ref=devto_deploycra&dest=https://codeburst.io/how-to-create-and-deploy-a-serverless-react-app-af40aee52420)—

因为这篇文章是关于如何部署一个`create-react-app`项目的，我假设你已经准备好了你的项目。

如果没有，下面是所需步骤的简要总结:

1.  安装 Node.js 和 npm。这里是如何在你的机器上安装 Node.js 的概述。npm 会自动安装 Node.js。

2.  运行脚本`npx create-react-app my-app`。这将创建一个子目录`my-app`，并将您的样板代码放在那里。[这里](https://github.com/facebook/create-react-app)是官方页面。

到目前为止，您应该已经得到了下面的项目结构:

```
─my-app
  ├─node_modules
  ├─public
  ├─src
  │ ├─App.css
  │ ├─App.js
  │ ├─App.test.js
  │ ├─index.css
  │ ├─index.js
  │ ├─logo.svg
  │ └─serviceWorker.js
  ├─.gitignore
  ├─package.json
  └─README.md 
```

你的 app 的切入点是`src/index.js`。该文件呈现您的主要 React 组件`src/App.js`。

您应该能够使用`npm start`来(在本地)启动您的项目。

## 建筑作为一种功能

库[infra structure-components](https://www.infrastructure-components.com/page?ref=devto_deploycra&dest=https://github.com/infrastructure-components/infrastructure-components)提供了 React 组件，让我们可以通过 React 组件轻松定义 React 应用的架构。

您可以轻松安装基础设施组件:

```
npm install --save infrastructure-components 
```

现在，我们创建一个新的条目文件(`index.tsx`)，放入项目的`src`文件夹中。在这个文件中，我们定义了

*   我们应用的架构:`<SinglePageApp />`
*   可部署环境:`<Environment />`
*   我们希望我们的应用服务的路线:`<Route />`

正如您在下面的例子中看到的，root- `<Route />` ( `path="/"`)组件将我们的`App`作为参数。这意味着我们的根路径(“/”)将服务于这个组件。

此外，我们将`index.css`的导入从`index.js`(我们不再使用的旧入口点)移动到新入口点文件`index.tsx`