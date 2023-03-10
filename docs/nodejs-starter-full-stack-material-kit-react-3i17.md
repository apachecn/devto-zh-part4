# Node JS Starter -全栈 React 材料包

> 原文：<https://dev.to/sm0ke/nodejs-starter-full-stack-material-kit-react-3i17>

各位编码员好，

最近，为了节省一个重要的资源:**时间**，我在**样板和开始**上花了很多精力。你可能不会在样板文件前使用它，这没关系，但在我的情况下，当我在 **Nodejs** 开始一个新项目时，我会尽可能地避免编写产品的底层代码。这个阶段通常是无聊的，没有创造性的，而且相对耗时。

* * *

这篇文章展示了一个 **[节点 JS 启动器](https://appseed.us/boilerplate-code/nodejs-starter)** ，在过去的几个月里，我用它来加速我用 **Javascript** 编写的一些产品的开发。下面是最终应用的外观&。

* * *

## 什么是[节点 JS](https://nodejs.org/en/)

[Nodejs](https://nodejs.org/en/) 是一个 JavaScript 运行时环境，它包含了在您最喜欢的操作系统中执行用 JavaScript 编写的程序所需的一切。基本上，你可以在浏览器中执行的东西，通过使用 **Nodejs** ，你可以直接在操作系统中执行，就像 Python 或 Ruby 一样。

* * *

## 什么是[样板代码](https://appseed.us/boilerplate-code)

在计算机编程中，**样板代码指的是必须包含在许多地方的代码段，只需很少或不需要修改。写一次，反复使用。你可以在维基百科上读到更多关于[样板](https://en.wikipedia.org/wiki/Boilerplate_code)代码的内容。**

* * *

## [材料工具包反应过来](https://appseed.us/apps/react/express/material-kit-creative-tim)

**Material Kit React** 是一个免费的 Material-UI 套件，以 [React](https://reactjs.org/) 编码，其设计灵感来自谷歌的材料设计。在官方产品页面上阅读更多关于[材料套件 React](https://www.creative-tim.com/product/material-kit-react?ref=appseed) 的信息。

* * *

[Nodejs Starter](https://appseed.us/boilerplate-code/nodejs-starter) 使用解耦架构构建，其中 Material Kit React 前端通过安全 ajax 调用与 **Nodejs** / [Express](https://expressjs.com/) 后端通信。

要使用和启动该启动器，必须遵循几个简单的步骤:

*   克隆、构建并启动 [Nodejs Starter](https://github.com/app-generator/nodejs-starter) 后端
*   克隆、构建并启动[材料包反应](https://github.com/app-generator/material-kit-react)前端
*   更新代码，并在现有样板代码的基础上添加您的魔法。

让我们就这个启动器的每一面说几句话

* * *

## [素材套件反应](https://appseed.us/apps/react/express/material-kit-creative-tim)前端

为了拥有一个全功能的[反应](https://reactjs.org/)前端，我在现有设计的基础上添加了必要的代码来处理认证和注册。用户将输入经典的`email` / `password`信息，如果在后端服务中不存在，帐户将被创建。

> 构建并启动前端

```
$ # clone the react Frontend 

$ git clone https://github.com/app-generator/material-kit-react.git
$ cd material-kit-react
$ yarn # to install the dependencies
$ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，前端应该在您的浏览器中的地址`localhost:8080`处可见。此时此刻，我们有前端应用程序，漂浮在空中。目前还没有连接到真正的后端。让我们做点什么吧。

* * *

## [节点 JS 后端](https://appseed.us/boilerplate-code/nodejs-starter)

[Nodejs Starter](https://appseed.us/boilerplate-code/nodejs-starter) 已经配备了一些基本模块和功能，帮助您跳过基本设置:

*   快递后端
*   ORM Sequelize，在 SQLite 数据库之上
*   护照，JWT 认证

```
$ git clone https://github.com/app-generator/nodejs-starter.git
$ cd nodejs-starter
$ yarn
$ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我将根据大家的反应和评论，用新的信息更新这篇文章。有时，使用来自可信来源的样板文件可以帮助您节省一些宝贵的时间，并加快一点开发速度。

* * *

## 其他节点启动器

*   [Nodejs 启动黑客马拉松](https://github.com/sahat/hackathon-starter)
*   nodejs starter API kit
*   [Nodejs 无服务器启动器](https://github.com/AnomalyInnovations/serverless-nodejs-starter)

* * *

## 相关文章

*   [Nodejs Starter -开源样板代码](https://blog.appseed.us/nodejs-starter-open-source-boilerplate-code/)
*   [Nodejs Starter](https://dev.to/sm0ke/nodejs-starter-javascript-boilerplates-to-start-fast-1024) Javascript 样板快速启动
*   [Nodejs 启动器-全堆叠 Vue 氩气设计](https://dev.to/sm0ke/nodejs-starter-full-stack-vue-argon-design-13bj)

* * *

> 谢谢大家！顺便说一下，我的(昵称)名字是 **Sm0ke** ，我在[推特](https://twitter.com/Sm0keDev)上也很活跃。