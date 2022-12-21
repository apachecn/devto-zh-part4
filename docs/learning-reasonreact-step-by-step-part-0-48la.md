# 学习原因逐步反应第 0 部分

> 原文：<https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-0-48la>

**更新**:

reason ml+BuckleScript 现为 [Rescript](https://rescript-lang.org/blog/bucklescript-is-rebranding) 。随着围绕这些工具的生态系统发生了变化，这篇博文不再准确。

* * *

我的目标是[在公共场合](https://www.swyx.io/writing/learn-in-public)了解更多，这样也可以帮助别人。

我已经给了**[react](https://reasonml.github.io/reason-react/en/)**第一次尝试。我读了一点文档，偷看了一下[探索原因](http://reasonmlhub.com/exploring-reasonml/)，发现了一些[有用的](https://jaredforsyth.com/posts/a-reason-react-tutorial/) [博客](https://dev.to/seif_ghezala/reasonml-for-production-react-apps-part-1-3nfk) [帖子](https://jamesfriend.com.au/a-first-reason-react-app-for-js-developers)。

让我们试着在 React 中建立一个表单！

这是一个简单的项目，但是表单是 web 应用程序中最常见的东西之一。

# 安装&设置

通过 [npm 或 yarn 安装是无痛的](https://reasonml.github.io/docs/en/installation) :

```
$ yarn add -g bs-platform 
```

Enter fullscreen mode Exit fullscreen mode

创建新项目:

```
$ bsb init reason-form -theme react-hooks 
```

Enter fullscreen mode Exit fullscreen mode

这个命令使用 React 钩子启动一个名为“React-form”的新 React 项目。

进入目录，安装必要的模块:

```
$ cd reason-form && yarn install 
```

Enter fullscreen mode Exit fullscreen mode

# 清理 Webpack 等。

使用 npm 或 yarn 运行项目的默认配置很麻烦。您必须在两个独立的终端中运行两个脚本。

我同时使用[和](https://www.npmjs.com/package/concurrently)来并行运行几个脚本。

```
$ yarn add --dev concurrently 
```

Enter fullscreen mode Exit fullscreen mode

修改`package.json`。删除脚本并替换为以下内容:

```
 "scripts":  {  "start":  "concurrently -k \"yarn run start:bsb\"  \"yarn run start:webpack\"",  "start:bsb":  "bsb -clean-world -make-world -w",  "start:webpack":  "webpack-dev-server --port 3000",  "build":  "concurrently -k \"yarn run build:webpack\"",  "build:webpack":  "NODE_ENV=production webpack",  "format":  "refmt src/*.re"  }, 
```

Enter fullscreen mode Exit fullscreen mode

# 创建第一个组件

移除`src`文件夹中的示例组件。

删除`src/index.html`的内容，替换为:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.5/css/bulma.min.css"
    />
    Reason Form
  </head>
  <body>
    <div id="root"></div>
    <script src="Index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这增加了[布尔玛](https://bulma.io/)和一个带`id`“根”的`div`，我们将在那里安装 React 应用程序。

HTML 从`Index.js`加载 JavaScript 这就是 webpack 的配置方式。想了解更多就看一下`webpack.config.js`。

修改`scr/Index.re` :

```
ReactDOMRe.renderToElementWithId(<App />, "root"); 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/App.re`文件:

```
[@react.component]
let make = () =>
  <div className="App"> {"Hello World" |> ReasonReact.string} </div>; 
```

Enter fullscreen mode Exit fullscreen mode

奇怪的语法是 ReasonReact 写 JSX 的方式。

装饰器和`make`函数创建一个 React 组件。
在`div`里面，你必须告诉 Reason 你正在处理一个字符串。

它看起来确实不漂亮。

# 运行原因反应

转到终端:

```
$ yarn run start 
```

Enter fullscreen mode Exit fullscreen mode

转到`http://localhost:3000`你应该会看到“Hello World”。

### 进一步阅读

*   [原因反应](https://reasonml.github.io/reason-react/en/)