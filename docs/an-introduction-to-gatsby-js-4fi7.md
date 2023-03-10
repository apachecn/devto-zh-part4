# 盖茨比简介. js

> 原文：<https://dev.to/bagwaa/an-introduction-to-gatsby-js-4fi7>

现代 web 开发不是可以很快掌握的东西，在当前的 web 开发环境下，作为开发人员，我们需要学习的东西太多了，这种学习周期是每个开发人员生活的一大部分。

我们有前端框架，如 [vue.js](https://vuejs.org) 、 [react](https://reactjs.org) 和 [angular](https://angularjs.org) ，以及用于创建我们的开发和生产版本的 [webpack](https://webpack.js.org) ，在大多数现代应用程序中还需要考虑 [es6](https://es6.io) 和 [babel](https://babeljs.io) ，以及使用 [PostCSS](https://postcss.org) 和 [tailwindcss](https://tailwindcss.com/) 等工具进行 CSS 预处理。

除此之外，我们还需要考虑后端来支持这些应用，如 [Node.js](https://nodejs.org/) 、 [Laravel](https://laravel.com/) 、API、数据库模式、部署策略、开发环境、cdn...咻！只是一吨重的东西。

## 盖茨比是什么？

在一个非常基础的层面上，Gatsby 是一个站点/应用生成器，旨在通过开箱即用的合理默认来解决上述所有问题，然而它还不止于此。

因为我们所生成的并不完全是静态的，我们本质上是在生成一个完全工作的 react 应用程序，它可以被托管在任何我们可以放下 JavaScript 和 HTML 的地方，所以在像 [Netlify](https://netlify.com) 这样的服务上托管这些应用程序是一个微不足道的任务。

Gatsby 还可以在构建时从各种来源获取数据，因此我们只需要在构建我们的网站时该 API 在线并可用，这将导致网站构建仅包含 HTML、CSS 和 JavaScript，所有 API 数据保存在 [GraphQL](https://graphql.org/) 中，这几乎就像是网站构建的静态数据层。

这样做的两大优势是速度和安全性，因为我们托管 HTML 和 JavaScript，所以您的网站不需要在运行时向服务器发出 HTTP 请求，这些数据只是保存在 GraphQL 中，出于同样的原因，没有黑客伤害您的真正途径，他们肯定会“破坏”您的网站，但您需要做的只是用一个命令创建一个新的构建并重新部署。

所以我可以写一整篇博文来解释为什么盖茨比很有趣，但是我想我们应该开始写一些代码，所以让我们开始安装程序吧。

## 安装

如果您在过去安装过任何 node / npm 包，那么您在这里就像在家一样，安装 Gatsby 就是用一个简单的 npm 命令在您的开发机器上全局安装它。(*你也可以用纱线* )

```
npm install -g gatsby-cli 
```

这将在您的 shell 提示符中给出一个 gatsby 命令，您可能需要注销您的 shell 并重新登录才能看到它，但是您应该能够立即访问它。

要创建我们的第一个 Gatsby 站点，我们只需要运行下面的命令。

```
gatsby new my-blog 
```

运行上面的命令将创建一个名为 my-blog 的新目录，其中将包含您全新的 Gatsby 网站，接下来我们需要做的是启动一个本地开发环境，幸运的是这也已经为我们完成了！

我们只需要运行下面的命令，就可以开始运行了...

```
cd my-blog
gatsby develop 
```

Gatsby 随后将启动一个 web 开发服务器，其中包含开箱即用的热重装功能，http://localhost:8000 开箱即用的另一个妙招是，如果 8000 已经被占用，它甚至会为您重新配置到另一个端口，太好了！

此时，您应该能够浏览到您的新开发 URL，并在您保存文件时看到您闪亮的新 Gatsby 安装以及热重装。

[![Gatsby Up and Running](img/8ebc953a69ac115a7af575da65659071.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zwVtcd_1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8aeg5zx4tgfcy5mknalz.png)

## 创造我们与盖茨比的第一页

与所有站点生成器一样，我们需要注意的第一个任务是创建我们的第一个页面，在应用程序目录结构中，您会注意到一个 src/pages 文件夹。您在这里创建的 js 文件将被翻译成页面，路由会为您处理，所以您需要担心的只是创建这个文件。

让我们来看看新页面最基本的 hello world 示例，在这个 pages 目录中创建一个文件，并将其命名为 hello.js

```
import React from "react"

export default () => (
  <>
    <h1>Hello, World!</h1>
    <p>Welcome to your first Gatsby page</p>
  </> ) 
```

此时，只要我们的小开发服务器还在运行，您就会看到添加这个文件触发了一个新的构建，然后您将能够导航到[http://localhost:8000/hello](http://localhost:8000/hello)

幸运的话，你会看到我们辉煌的新创造诞生在这个世界上！

[![Hello, World](img/bb47192b10f1ea20e2017530f69af6ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EUqipUpQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iaw0y0cnt02barpftxhp.png)

正如你所看到的，这个过程非常简单，如果你不知道 react，那么你可能对创建这个页面的代码有一些疑问，所以让我们快速地看一下。

```
import React from "react" 
```

由于 Gatsby 是基于 react 的，我们将需要 react 库，它包含在我们的项目中，只有一条 import 语句，在这一点上，我们真的不需要任何其他依赖项，只需要 react。

```
export default () => (
  <>
    <h1>Hello, World!</h1>
    <p>Welcome to your first Gatsby page</p>
  </> ) 
```

这里发生了一些事情，首先，我们导出代码块的以下内容，以便我们应用程序的其他部分可以使用它，把它看作一种组件，我们提供以下内容供 Gatsby 使用，以便它可以在屏幕上呈现它。

接下来你可以看到这些有趣的小空括号，这些是 [react 片段](https://reactjs.org/docs/fragments.html)，默认情况下，我们只允许从这个函数返回一个 div 或元素，这意味着如果我们的内容中有兄弟元素(就像我们对 h1 和 p 标签所做的那样)，那么我们需要将它们包装到一个 div 中。过去实现这一点的传统方法是实际使用 div 来包装内容，但是 div 最终出现在 HTML 中，并且不提供任何语义目的。

以这种方式使用 react 片段允许我们在这个示例中包装 h1 和 p 标签，但是在最终的 HTML 构建中不会生成包装 div 元素，因此避免了在代码中有大量无用包装元素的 *"div soup"* 场景。

## 页面间的链接

在我们放下基础知识之前，我想回顾的最后一点是关于页面之间的链接，例如，传统上你可以通过在每个页面上创建一个简单的锚标记来链接页面。

```
<a href="/">Home</a> 
```

然而，这种方法有一个问题，我们使用类似 Gatsby 的东西，那就是上面的内容会强制整个页面重新加载，所以当你点击链接时，整个页面会在显示你的内容之前重新加载。

相反，我们想做的是为用户提供点击链接的能力，并且只重新加载应用程序中已经改变的部分(在这种情况下，是内容)。

这意味着我们的页面将更快地重新加载，因为它从来没有发出 http 请求，这将立即增加几百毫秒，相反，我们的应用程序将获取它想要加载的页面作为一个组件，并在屏幕的主要内容部分交换它。

让我们来看一个实际例子。

```
import React from "react"
import { Link } from "gatsby"

export default () => (
  <>
    <h1>Hello, World!</h1>
    <p>Welcome to your first Gatsby page</p>
    <Link to="/">Home</Link>
  </> ) 
```

正如您所看到的，我们只是从 Gatsby 中导入了链接组件，这是免费提供给我们的，然后我们会以几乎完全相同的方式使用它，除了我们使用属性 to 而不是 href

这就是你在页面之间链接所需要做的全部工作，现在当我们点击这些链接时，页面将动态地重新加载到我们的应用程序中，我们将不会从任何后端服务器获取我们应用程序的新实例，神奇！

## 结论

所以，让我们来看看我们在这篇博文中涵盖了什么

*   盖茨比是什么
*   盖茨比想解决什么问题？
*   用节点包管理器安装(Yarn 非常类似)
*   使用热重装运行我们的开发环境
*   从头开始创建我们的第一页
*   反应碎片
*   页面之间的链接以及为什么不重新加载整个页面更好

当然，还有很多部分要讨论，但这些是基础，在后面的文章中，我将讨论 GraphQL、渲染、创建文章列表、实现 tailwindcss 等等。