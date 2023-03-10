# Vue JS 中的延迟加载

> 原文：<https://dev.to/bnevilleoneill/lazy-loading-in-vue-js-1610>

[![](img/f90fe2b15e5ff439999627766e0bdad0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rhOEXWol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1r0k07zuz48p4rpqv0g9.png)

## 视图

Vue JS 是一个非常进步的 JavaScript 框架，由[尤雨溪](https://twitter.com/youyuxi?lang=en)和 Vue 核心团队创建，并有超过 [230 名开源社区爱好者](https://github.com/vuejs/vue)的贡献。 [Vue](http://vuejs.org/) 被超过 87 万人使用，在 GitHub 上[被打星 14 万次。它由一个只关注视图层的可接近的核心库和一个支持库的生态系统组成，支持库可以帮助您处理大型单页面应用程序中的复杂性。](https://github.com/vuejs/vue)

在这篇文章中，将向你介绍在 Vue JS 中延迟加载组件的方法。

## 开始前

这篇文章适合使用 Vue JS 的所有阶段的开发者，包括初学者。在阅读本文之前，您应该已经具备了一些先决条件。

您将需要以下内容:

*   已安装 node . js 10 . x 及以上版本。您可以通过在终端/命令提示符下运行以下命令来验证您是否知道:

```
node -v 
```

Enter fullscreen mode Exit fullscreen mode

*   还安装了节点程序包管理器 6.7 或更高版本(NPM)
*   代码编辑器:强烈推荐使用 Visual Studio 代码
*   Vue 的最新版本，全球安装在您的机器上
*   安装在您机器上的 Vue CLI 3.0。为此，请先卸载旧版本的 CLI:

```
npm uninstall -g vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

*   然后，安装新的:

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是懒装？

要理解延迟加载，你首先要知道急切加载是什么意思。**急切加载**是将 JavaScript 代码加载到 DOM 的默认方法，对于 Vue JS 来说，这意味着使用 import 语句将一个组件引入 app.vue 文件。另一方面，延迟加载指的是这样一种方法，当应用程序启动时，所有的脚本并不都加载到 DOM 上，而是只在请求时才加载，这使得 JavaScript 包在初始加载时非常小。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 为什么懒加载很重要？

随着您的 Vue 项目的规模和复杂性的增长，急切加载的方法使得 JS 包变得非常麻烦，这对于那些可能在移动设备上或没有高速互联网连接的情况下访问您的应用程序的最终用户来说，可能会成为一个大问题。

惰性加载保证了您的 JavaScript 包按照重要性的顺序(从最需要的组件到最不需要的组件)被提供给 DOM。这种方法确保了开发人员能够控制用户的整个体验，包括资源加载到 DOM 时的最初等待时间。

## Vue JS 如何处理懒加载

Vue JS 通过路由来处理组件的延迟加载，所以在 DOM 上，只有在需要时才可以通过路由加载组件。这是通过将每个路由的组件分割成独立于初始化时加载的主块的块来实现的，这样发送到 DOM 的包大小就不会变得太大。Vue JS 结合了[异步组件特性](https://vuejs.org/guide/components.html#Async-Components)和 webpack 的[代码分割特性](https://webpack.js.org/guides/code-splitting-async/)来延迟加载路由组件。

## 演示:您将构建什么

您将使用新的 Vue CLI 3 构建一个 Vue JS 应用程序，并在配置阶段添加路由，同时手动添加第三条路由，以确保不熟悉 CLI 的开发人员能够使用。

作为 Vue JS 开发人员使用 Vue CLI 总是明智的，因为默认情况下，辅助路由已经配置为延迟加载，但是，在本文中，您将看到如何手动加载。

## 启动一个 Vue 项目

在你选择的目录中打开一个终端，用这个命令创建一个新项目:

```
vue create test_project 
```

Enter fullscreen mode Exit fullscreen mode

你会看到一个类似这样的后续问卷:

```
? Please pick a preset: Manually select features
? Check the features needed for your project: 
 ◉ Babel
 ◯ TypeScript
 ◯ Progressive Web App (PWA) Support
❯◉ Router
 ◯ Vuex
 ◯ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing 
```

Enter fullscreen mode Exit fullscreen mode

确保使用空格键选择路由器选项，就像上面选择的一样。然后，为了节省空间，您可以选择将所有配置存储在 package.json 文件中。项目创建完成后，您将看到一条成功消息，此时将目录更改为新的项目文件夹，并在开发环境中运行应用程序:

```
cd test_project
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

[![vue js app](img/a36d24af86fcdfb4a3ea019700ba75d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MK5stG9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/07/vuejsapp.png%3Fresize%3D1418%252C874%26ssl%3D1)

它有一个 about 组件，视图上有路线导航。应用程序一初始化，两个组件`Home.vue`和`About.vue`就被加载到 DOM 上。这是一个标准的过程，对于拥有最少资产的小型项目来说非常好，但是在拥有大量组件的大型应用程序中，这可能会导致加载非常慢。这就是代码分割的用武之地。打开 router.js 文件，应该是这样的:

```
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/Home.vue'
import About from './views/About.vue'
Vue.use(Router)
export default new Router({
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    },
    {
      path: '/about',
      name: 'about',
      component: About
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

对于 about 组件部分，这可能看起来有所不同，因为新的 CLI 3 包括了次要路由的延迟加载，如我们示例中的 about 组件。

## 手动添加新路线

您现在有两条路线，一条是指向主视图的主路线，另一条是指向关于视图的关于路线。让我们添加第三个视图，然后为其注册一条路线。在 views 文件夹中，创建一个新文件并将其命名为 extra.vue，然后将 about.vue 文件的内容复制到 extra.vue 文件中。应该是这样的:

```
<template>
<div class=”about”>
<h1>This is an extra page</h1>
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

要注册路由，打开 router.js 文件，在 routes 数组下添加额外的代码块:

```
export default new Router({
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    },
    {
      path: '/about',
      name: 'about',
      component: About
    },
   {
      path: '/extra',
      name: 'extra',
      component: Extra
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

让它显示在 DOM 中的最后一步是用 router-link 将其添加到主 App.vue 文件中。打开 app.vue 文件，将模板部分更改为下面的代码块:

```
<template>
<div id=”app”>
<div id=”nav”>
<router-link to=”/”>Home</router-link> |
<router-link to=”/about”>About</router-link> |
<router-link to=”/extra”>Extra</router-link>
</div>
<router-view/>
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

## 懒洋洋地加载我们的组件

现在所有的路由都已经设置好了，您现在可以将所有的路由配置为根据请求延迟加载。打开 router.js 文件，将下面的代码块复制到其中:

```
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)
function lazyLoad(view){
  return() => import(`@/views/${view}.vue`)
}
export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'home',
      component: lazyLoad('Home')
    },
    {
      path: '/about',
      name: 'about',
      component: lazyLoad('About')
    },
    {
      path: '/extra',
      name: 'extra',
      component: lazyLoad('extra')
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

下面是为使这些视图延迟加载所做的工作:

*   删除导入语句:最初你会注意到有 Vue、Router、Home、About 和 extra 的导入语句。这些语句是急切加载组件的主要驱动因素，因此当您改变方法时，您必须删除那些导入语句。
*   lazyLoad 函数:创建这个函数是为了处理干净的 import 语句，如果没有这个函数，每个组件都会有一个很长的 import 语句。然而，使用它，您只需调用该函数，并将路由作为参数传入。
*   Webpack import 语句:告诉 Webpack 延迟加载组件的 import 语句必须嵌入到 routes 部分，而不是脚本开头的 import 部分。

为了测试加载策略，在开发中再次运行应用程序:

```
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中的 [http://localhost:8080/](http://localhost:8080/) 打开它，并打开开发者工具。单击网络部分，然后选择 JS 选项卡。

[![](img/644c8fa65002526f32519965ee5f4fde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nyzGlnra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/07/aboutpage.gif%3Fresize%3D730%252C243%26ssl%3D1)

您可以看到加载到 DOM 上的 JavaScript 块是按时间顺序编号的，其中第一个(0.js)是主视图，第二个(1.js)是 about 视图，只在单击时才添加到 DOM 中，第三个(2.js)是额外的视图，也可以根据请求添加到 DOM 中。

在这个阶段，您还可以通过使用命令:
构建生产应用程序来确认这一点

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

这三个块将由 Webpack 适时地表示为单独的、独立的、延迟加载的块。

## 结论

这是一篇关于 Vue JS 如何借助 Webpack 代码分割在抽象的路由级别处理延迟加载的介绍。在小型 Vue 项目中，延迟加载组件的重要性可能不容易感觉到，但是随着项目变得复杂，这一点变得非常明显，因此建议在工作流中实现延迟加载，以提高效率并优化资源的使用。黑客快乐！

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/441375fdfee1c124c713d0800bb5e0d5.png)](https://logrocket.com/signup/)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

Vue JS 中的[懒加载这个帖子最先出现在](https://blog.logrocket.com/lazy-loading-in-vue-js/)[日志博客](https://blog.logrocket.com)上。