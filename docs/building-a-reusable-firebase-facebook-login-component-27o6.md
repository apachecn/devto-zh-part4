# 构建可重用的 Firebase 脸书登录组件

> 原文：<https://dev.to/kris/building-a-reusable-firebase-facebook-login-component-27o6>

#### 了解如何构建一个可重用和可共享的 Firebase 脸书登录组件

[![](img/87a8ff15a1d73a234b4af1a38a67d9fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uTuvQctp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AED7mmgYYeUZkz8iIz2g3SQ.png)

在本教程中，我们将遵循 clone [Firebaseweb-UI 演示](https://fir-ui-demo-84a6c.firebaseapp.com/)的第一章，并使用其中的代码。在这里，我们将学习如何用 React 和[位](https://bit.dev)创建一个可重用和可共享的脸书登录组件。首先，我们将在 React 项目中使用 firebase 集成和身份验证来准备一个 facebook 登录组件。然后，我们就将代码推到位。我们还将学习从使用节点包管理器(NPM)安装的包中导入模块。我们将本教程分解为多个小标题，以便您掌握概念并相应地工作。

### 你会学到什么…？

*   如何创建新的 React 应用？
*   如何在 React 中安装和使用 firebase？
*   如何使用 Firebase 认证？
*   如何创建和设置脸书应用程序？
*   如何安装和使用[钻头](https://bit.dev)。
*   React 中如何传道具？

### 问题

使用 vanillaJS 构建的演示程序没有被分解成组件，这使得它很难维护。由于组件没有被使用，代码可能会在大部分地方被重写，从而创建一个庞大的项目。

### 要求

为了从本教程中有所收获，这里有一个插件、包和服务的完整列表:

*   Nodejs v8.x.x 或更高版本与 NPM/yarn 一起安装。
*   消防基地，位和脸书开发商帐户。
*   已经安装了 React 项目。
*   火基和钻头包。

### 第一步

在处理脸书登录组件之前，我们将完成使用 *App.js* 时所需的每个配置。

首先，试着从 Firebaseweb-UI 演示中抓取 CSS 并粘贴

**firebase-ui . CSS**->-**app . CSS**

这里，我们需要将 firebase CSS 包含到 *App.js* 中。

然后包含一些样式到 *Index.css，*

**style . CSS**->-**index . CSS**

我们需要将它包含在 *index.js* 文件中。

然后，我们需要从 firebaseweb-ui 中获取按钮代码，然后将其添加到我们的 *App.js* 代码中，如下所示: