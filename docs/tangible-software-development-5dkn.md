# 有形软件开发

> 原文：<https://dev.to/cascade-energy/tangible-software-development-5dkn>

刚开始专业写软件的时候，对工具上的固恋很困惑。几年后，我开始意识到我们使用的工具极大地影响了我们工作的效率和满足感。相比之下，想象一下一个木匠不用电动工具搭建房子。这是可以做到的，但是需要更多的时间和努力。

在 Cascade Energy，我们是一个小型开发运营团队，有很多工作要做。这使得我们对工具的选择很重要。在过去的一年里，我们引入了自动化部署、静态分析和重新格式化(分别使用`CodePipeline`、`eslint`和`prettier`)。这篇文章将关注一个稍微更具实验性的工具，它正在重新定义我们的工作流程:**热重装**。

最近，我接受了一项任务，向我们面向客户的 React 应用程序添加一个新特性。以下是我反复经历的过程:

1.  决定一个我一次能做的小改变
2.  通读代码，点击生产应用程序，了解它当前是如何工作的
3.  读取与此组件相关的测试
4.  对代码本身做一点小小的修改
5.  保存文件
6.  切换到我的网页浏览器
7.  刷新页面
8.  从下拉菜单中选择一个示例客户，以在页面上显示数据
9.  向下滚动页面到我正在处理的组件
10.  单击组件
11.  看看我的改变是否有效
12.  如果没有，重复

许多这些步骤是不可避免的。例如，大多数开发人员会告诉你，他们花在阅读代码上的时间比写代码的时间还多。但是，我们可以使用热重装合并步骤 6-10。在配置了热重新加载的情况下，我所做的每一个小更改都会自动注册到 web 浏览器中，并保留周围的上下文。要做到这一点，有很多管道，但一旦设置好了，它就是神奇的。

如今，当创建一个新的前端应用程序时，您可以使用预先配置的 starter pack，它已经具有热重新加载和其他现成的生产力特性(`vue-cli`、`create-react-app`等)。在这种情况下，我们不能依靠这些工具，因为这是一个带有一些自定义配置的现有应用程序。

我们的设置是一个节点后端层，它处理与其他服务的连接，并为我们的前端 React 应用程序提供服务。我们使用`webpack`作为我们的前端构建系统。

`webpack`团队维护预打包的`webpack-dev-server`模块，但是它不适合我们的目的，因为我们应用程序的后端和前端是交织在一起的。如果我们的应用程序的后端是使用`express`构建的，那么我们可以配置服务器直接使用`webpack-dev-middleware`(由`webpack-dev-server`使用)。然而，我们使用的是`hapi`，它不支持 Express 风格的中间件。

只是略感气馁，我深吸一口气，继续前行。围绕`webpack-dev-middleware`编写一个包装器仍然是可能的。幸运的是，我找到了一篇让我起步的文章——为`webpack` 编写`hapi`中间件适配器的[教程。](https://medium.com/@tkh44/setting-up-your-front-end-dev-environment-with-webpack-with-hapi-b352ab8b2f9c)

我借用了挂钩到`hapi`的 onRequest 和 onPreResponse 生命周期扩展点的基本前提来拦截请求并将它们传送到`webpack`,这样它就可以处理热重载。然而，我并没有发现作者对`webpack-dashboard`的建议比`webpack`的内置日志功能更有帮助，而且它掩盖了我们的 API 日志，这些日志通常被路由到同一个控制台。

稍加修改，我就能把`webpack-dev-middleware`接到`hapi`上。这大概就是我们的处境。

```
// Install dev middleware
server.ext("onRequest", (request, reply) => {
  if (passThroughRequest(request)) {
    return reply.continue();
  }

  devMiddleware(request.raw.req, request.raw.res, err => {
    if (err) {
      return reply(err);
    }
    return reply.continue();
  });
});

// Install hot middleware (for module reloading without reloading the page)
  server.ext("onPostAuth", (request, reply) => {
    if (passThroughRequest(request)) {
      return reply.continue();
    }

    hotMiddleware(request.raw.req, request.raw.res, err => {
      if (err) {
        return reply(err);
      }
      return reply.continue();
    });
  });

  // Make sure react-router can handle our actual routing
  server.ext("onPreResponse", (request, reply) => {
    if (passThroughRequest(request)) {
      return reply.continue();
    }

    return reply.file("public/index.html");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

(`passThroughRequest`忽略了几条需要跳过`webpack`直接到后端的路径。)

有了这个设置，我尝试保存对模块的更改。然而，我没有热重装，而是在控制台中得到一个警告:

```
Ignored an update to unaccepted module ./client/components/project/tileView/ProjectTile.js -

…

process-update.js?e135:104 [HMR] The following modules couldn't be hot updated: (Full reload needed)
This is usually because the modules which have changed (and their parents) do not know how to hot reload themselves. See https://webpack.js.org/concepts/hot-module-replacement/ for more details.
… 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，我们不仅需要连接`webpack`进行热重装，我们还必须教会我们的前端组件自己进行热重装。这样，当`webpack`的 watch 进程注意到组件已经改变时，它可以只注入改变的代码，而不用重新加载整个页面。每个框架对此都有不同的方法。React 有`react-hot-loader`，丹·阿布拉莫夫的得意之作，尽管还处于实验阶段，却得到了很好的支持和积极的参与。阿布拉莫夫写了大量关于其背后概念的文章，这篇文章非常值得一读。本质上，您必须将您的顶级应用程序组件标记为热导出:

```
App.js
import React from "react";
import { hot } from "react-hot-loader/root";

import Routes from "./Routes";
import CascadeTheme from "./theme/Cascade";
import { AppContainer } from "./sharedComponents";

const App = () => (
  <CascadeTheme>
    <>
      <AppContainer>
        <Routes />
      </AppContainer>
    </>
  </CascadeTheme>
);

export default hot(App); 
```

Enter fullscreen mode Exit fullscreen mode

我们还必须对`webpack`配置进行一些修改，以加载`react-hot-loader`和`webpack-hot-middleware`客户端。这是相关部分:

```
if (process.env.npm_lifecycle_event
 === "start") {
  config = merge(common, {
    devtool: "cheap-module-eval-source-map",
    plugins: [new webpack.HotModuleReplacementPlugin()],
    module: {
      rules: [
        {
          // Mark everything matching this rule as "cold" (e.g. not hot exported)
          // This will allow hot reloading to work as expected for the rest of the
          // application
          test: /\.js?$/,
          include: /node_modules/,
          exclude: /node_modules\/@sensei\/shared-components\/src/,
          use: ["react-hot-loader/webpack"],
        },
      ],
    },
  });

  config.entry.app = ["webpack-hot-middleware/client", ...common.entry.app];
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该配置仅适用于通过 npm start(也称为开发中)运行的应用程序。

所以我开始工作，公关和合并。然后我们的另一个前端开发人员注意到一个奇怪的现象——在开发中使用热重装时注销功能被破坏了。该应用程序对已注销的用户仍然可见，但处于崩溃状态，所有对后端的调用都失败了。我意识到我们所有的`webpack` dev/hot 中间件调用都通过了，不管用户是否通过了身份验证。

我有一种挥之不去的感觉，在我的热重载实现中有一个基本的安全缺陷，每次我做了一个改变，我不得不手动刷新页面。不管怎样，我坚持了下来。

我试着调试扩展点并检查 auth 对象，但是似乎 cookies 还没有被解析。我的第一个直觉不是很有用——我试图导入我们的`hapi`授权插件，并将其重新注入到新的服务器请求扩展事件方法中。这导致了对`hapi`内部的多次尝试，这开始产生一股不好的代码味道。

所以我深吸一口气，后退一步，重新阅读了`hapi`的文档。最有用的部分是[请求生命周期](https://hapijs.com/api/16.1.1#request-lifecycle)。

事实证明，在上面发布的例子中，作者依赖于 onRequest 扩展点，这些扩展点位于请求生命周期中的身份验证之前。解决方案是使用 onPostAuth 扩展点来连接`webpack-dev-middleware`和`webpack-hot-middleware`，这样我们普通的认证中间件仍然可以处理每个请求。然而，我仍然需要 onPreResponse 处理程序来为 HTML 文件提供服务，因此我们也需要在这一点上检查授权。

这是我们最后得到的结果:

```
/* eslint-disable consistent-return */
const config = require("config");
const webpack = require("webpack");
const webpackDevMiddleware = require("webpack-dev-middleware");
const webpackHotMiddleware = require("webpack-hot-middleware");

const webpackConfig = require("./webpack.config");

const IGNORE_PATTERN = /^\/(api|styles|img)\//;

function installWebpackHapiMiddleware(server) {
  // If we're not running under `npm start`, bail
  if (process.env.npm_lifecycle_event !== "start") {
    return server;
  }

  console.log(
    "You appear to be running a development server. Initializing webpack dev/hot middleware..."
  );

  const compiler = webpack(webpackConfig);

  const devMiddleware = webpackDevMiddleware(compiler, {
    port: config.port,
    historyApiFallback: true,
    publicPath: webpackConfig.output.publicPath,
  });

  const hotMiddleware = webpackHotMiddleware(compiler);

  // Install dev middleware
  server.ext("onPostAuth", (request, reply) => {
    if (passThroughRequest(request)) {
      return reply.continue();
    }

    devMiddleware(request.raw.req, request.raw.res, err => {
      if (err) {
        return reply(err);
      }
      return reply.continue();
    });
  });

  // Install hot middleware (for module reloading without reloading the page)
  server.ext("onPostAuth", (request, reply) => {
    if (passThroughRequest(request)) {
      return reply.continue();
    }

    hotMiddleware(request.raw.req, request.raw.res, err => {
      if (err) {
        return reply(err);
      }
      return reply.continue();
    });
  });

  // Make sure react-router can handle our actual routing
  server.ext("onPreResponse", (request, reply) => {
    if (passThroughRequest(request)) {
      return reply.continue();
    }

    return reply.file("public/index.html");
  });

  return server;
}

function passThroughRequest(request) {
  const isNotAuthenticated = request.auth.mode === "required" && !request.auth.isAuthenticated;
  return isNotAuthenticated || IGNORE_PATTERN.test(request.path);
}

module.exports = installWebpackHapiMiddleware; 
```

Enter fullscreen mode Exit fullscreen mode

软件开发经常让人感觉是无形的，因为我们编写的代码看起来与它最终变成的直接机器指令非常不同，通过复杂的过程，我们很少有人完全理解。最终，热重装的即时性使我们的日常工作流程更接近于木匠使用物理工具的工作流程——做出改变，并立即看到它的反映。我赞美有形的软件开发和使之成为可能的工具！