# 使用 Go、Vecty 和 WebAssembly 构建全栈 web 应用

> 原文：<https://dev.to/bnevilleoneill/building-full-stack-web-apps-with-go-vecty-and-webassembly-1hdp>

**作者:[亚伦·施莱辛格](https://blog.logrocket.com/author/aaronschlesinger/)T3】✏️**

我们中的许多人都听说过——也许还写过——全栈 web 应用程序。我们以各种不同的方式实现它们，但共同点通常是 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/javascript) 和 [Node.js](https://nodejs.org/en/) 。

今天，我们将打破传统，编写一个完整的 web 应用程序——前端和后端——而不用编写一行 JavaScript。我们会将开发者体验与 JavaScript 进行比较，但我们将只为整个项目编写 [Go](https://golang.org) 。

我们将学习如何使用 just Go 构建一个单页面链接缩短应用程序，并且我们将以展示其运行的工作代码结束。

## 先决条件

今天，我们将关注 Go，所以确保你已经在你的机器上[安装了](https://golang.org/dl/)工具。我假设你已经掌握了围棋的基本知识，但是请查看免费的[围棋之旅](https://tour.golang.org)来复习(或者学习！)如果需要的话。

我将要展示的所有 shell 命令都可以在 Mac 上运行，但也应该可以在大多数 Linux 系统上运行(包括 [WSL](https://docs.microsoft.com/en-us/windows/wsl/about) ！).

最后，确保用本文的代码克隆[存储库](https://github.com/arschles/vectyshortener)。

然后你就可以走了，让我们开始吧！

## 入门

首先，我们将让应用程序在本地运行。

来自 [Webpack](https://webpack.js.org/) 和相关技术——你可以用它们来构建一个带有 JavaScript 的 web 应用程序——构建和运行这个应用程序非常容易。有一个前端和一个后端部分(下面会详细介绍)，你可以用`go`工具编译它们，这不需要任何配置。

首先，运行后端服务器:

```
$ go run . 
```

接下来，在一个新的终端窗口中构建前端:

```
$ cd frontend
$ GOOS=js GOARCH=wasm go build -o ../public/frontend.wasm 
```

最后，在你的浏览器中进入 [https://localhost:8081](https://localhost:8081) 查看应用程序的运行情况。

## 这一切是如何运作的

像大多数网络应用一样，我们的链接缩短器有前端和后端两部分。在我们的应用程序中，后端只是一个用 Go 编写的静态服务器。所有的神奇之处都在前端目录中，所以让我们从那里开始吧！

如果你熟悉 [React](https://reactjs.org) 或 [DOM](https://www.w3.org/TR/WD-DOM/introduction.html) ，你会认识到我们将涉及的许多概念。如果没有，这个东西自然就来了。

我们正在使用一个名为 [Vecty](https://github.com/gopherjs/vecty) 的新 Go 框架来组织我们的应用程序。Vecty 强迫你把你的应用程序分解成组件，并把它们排列成一棵树。整个方案实际上类似于 HTML 和 DOM 或 React。

如果是 HTML，我们的应用程序的高级组件看起来会是这样:

*   一个`h2`作为页面的标题
*   A `form`进入缩短的环节
*   一个`div`来保存缩短的链接(这个值随着用户在上面输入链接而动态更新)
*   一个`a`保存的短链接

Vecty 组件与 React 非常相似，除了它们有更多的括号之外，它们看起来就像是 JSX 的 Go 等价物。

让我们放大一个，看看它是如何工作的。下面是`form`组件的代码:

```
elem.Form(
    elem.Input(vecty.Markup(
        event.Input(func(e *vecty.Event) {
            short := uuid.NewV4().String()[0:5]
            h.shortened = short
            vecty.Rerender(h)
        }),
    )),
) 
```

首先，第 1 行和第 2 行的`elem.Form`和`elem.Input`分别用于`<form>`和`<input>`标签。这两个函数都有一个或多个参数。每个参数都是介于 HTML 开始和结束标记之间的东西。例如，我们传递给`elem.Form`的东西在`<form>`和`</form>`之间。这是上面的代码在 HTML 中的样子:

```
<form>
    <input>
    </input>
</form> 
```

很简单，对吧？

我们没有看的最后一段代码是`event.Input`函数。这是一个事件处理器，就像在 HTML/JavaScript 中一样。这个函数接受*另一个*函数，在这里大概是一个 [`onchange`](https://www.w3schools.com/jsref/event_onchange.asp) 处理程序。正如您所料，处理程序接受的`*vecty.Event`参数与 JavaScript 事件大致相同。

缩短链接的逻辑都在这个处理程序中，而且相当简单。下面是注释彻底的代码:

```
// First, make a new UUID and take the first 5 characters of it.
// This will be our new shortcode
short := uuid.NewV4().String()[0:5]
// Next, write the shortcode to a variable. This variable is shared
// with the <div>, so when we re-render this component, the <div> will
// get updated
h.shortened = short
// Finally, re-render the component so that the <div> gets the new shortcode.
// Unlike React, there's no automatic diff functionality. We tell Vecty
// explicitly which components to re-render.
vecty.Rerender(h) 
```

## 你免费获得网页组装

由于这种组件结构，Vecty 可以扩展到大型应用程序，我们可以通过根据需要添加更多组件来扩展我们的应用程序。例如，我们可以在当前顶层的上添加一个组件*，根据 URL 动态地路由到不同的子组件。这类似于一些流行的 react-router 实现。*

最后要记住的一点是 **WASM 不是 HTML** ，它完全背离了 DOM 和 HTML 的一切。

我将上一节中的所有组件与 HTML 标记进行了比较，但它们不是！这就是维奇/ WASM 和 React 之间的巨大差异所在。我们正在将我们的 Go 代码*直接*编译成 WASM，它以不同于 DOM 的方式表示这些组件。

## 结论

最终，您会从使用 Go 和 Vecty 构建应用程序中获得一些巨大的好处:

1.  你可以考虑组件和嵌套，就像 React 和 DOM 一样
2.  你可以写尽可能多的动态逻辑，就在你的组件旁边，全部在 pure Go 中完成
3.  您可以在服务器和客户端之间共享代码，类似于编写 React 客户端和 Node.js 服务器
4.  你可以利用 WASM
    1.  如果你愿意，你也可以把你的 Vecty 代码编译成 HTML！那完全是另外一篇文章。[😉](https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f609.png)

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[用 Go、Vecty 和 WebAssembly 构建全栈 web 应用](https://blog.logrocket.com/building-full-stack-web-apps-with-go-vecty-and-webassembly/)最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。