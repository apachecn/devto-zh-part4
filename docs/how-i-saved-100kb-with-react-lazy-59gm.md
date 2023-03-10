# 我如何用 React.lazy 节省 100KB

> 原文：<https://dev.to/chinchang/how-i-saved-100kb-with-react-lazy-59gm>

这篇文章是关于我如何将我的主页 JavaScript 减少了大约 100KB。

**TL；DR** :使用 [`React.lazy`](https://reactjs.org/docs/code-splitting.html#reactlazy) 和 [`React.Suspense`](https://reactjs.org/docs/code-splitting.html#suspense) 来延迟加载你的非关键依赖项

我有一个从 [create-react-app](https://github.com/facebook/create-react-app) 引导的 React 应用程序。该应用中的一个页面(只是一个 React 组件)使用了 [CodeMirror](https://codemirror.net/) (一个代码编辑器)。app 使用 [react-router](https://reacttraining.com/react-router/) 进行路由。因此，这个页面组件，就像所有其他页面一样，被导入到主`App`组件中，以提供给路由器。

**app . js**T2】

```
import Home from "./Home";
import Page2 from "./Page2";

function App() {
  <Router>
    <Route path="/" component={Home} />
    <Route path="/page2" component={Page2} />
  </Router>; } 
```

**第 2 页. js**

```
import CodeMirror from "react-codemirror";

function App() {
  return (
    <div>
      <CodeMirror />
    </div>
  );
} 
```

*注意* : `react-codemirror`实际上做了一个命名的导出。但是为了简单起见，我假设一个默认的导出。

这种情况导致`Page2.js`成为渲染`App.js`的同步依赖。而`Page2.js`反过来又依赖于`react-codemirror`。于是间接的，`react-codemirror`变成了渲染`App.js`的同步依赖。这基本上意味着无论我们访问什么页面，`react-codemirror`都将在页面呈现之前被获取和解析。即使 Codemirror 在那个页面上根本没有使用！让我们解决这个问题。

## 解

解决方案非常简洁和简单。React 最近推出了一个新的 API: [`React.lazy`](https://kushagra.dev/blog/how-i-saved-100kbs-with-react-lazy/) 。还有一个随行组件叫做 [`Suspense`](https://kushagra.dev/blog/how-i-saved-100kbs-with-react-lazy/) 。下面是我们如何使用它们来解决我们的问题。

## 第一步:让导入变懒

`Page2.js`进口`react-codemirror`。理想情况下，我们希望`Page2.js`应该在页面 2 被实际访问时异步加载`react-codemirror`。

这是我们目前的`Page2.js` :

```
import CodeMirror from "react-codemirror";

function App() {
  return (
    <div>
      <CodeMirror />
    </div>
  );
} 
```

使用`React.lazy` API，我们可以让导入变得懒惰。像这样:

```
import React from "react";
const CodeMirror = React.lazy(() => import("react-codemirror"));

function App() {
  return (
    <div>
      <CodeMirror />
    </div>
  );
} 
```

这只是开箱即用！不再需要改变`CodeMirror`组件的使用方式。你现在会注意到，最初当你在主页上时，CodeMirror 没有加载。当你访问 */page2/* 时，你会看到 CodeMirror 被渲染的空白区域，这段时间 CodeMirror 是异步加载的。然后当它完成加载后，`CodeMirror`组件呈现。

当获取 CodeMirror 时，在 CodeMirror 编辑器应该出现的地方只有空白。这不是一个好体验，因为用户没有关于空白空间的任何信息。这就是`React.Suspense`组件发挥作用的地方。

## 第二步:改善留白语境

以下是我们需要做的所有事情，以使体验更好:

```
import React, { Suspense } from "react";
const CodeMirror = React.lazy(() => import("react-codemirror"));

function App() {
  return (
    <div>
      <Suspense fallback="Loading editor...">
        <CodeMirror />
      </Suspense>
    </div>
  );
} 
```

我们用一个`Suspense`标签包装异步/惰性组件，并给它一个`fallback`，它应该显示而不是空白。就是这样！

## 奖励提示

使用`React.lazy`有一个特殊的要求，你需要注意。它仅适用于具有默认[输出](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export)的组件。所以带有命名导出的组件不能用它来延迟导入。但是您可能有带有命名导出的组件，那么该怎么办呢？有个小技巧。让我们假设我们的`Page2.js`文件导出了`Page2`组件，因此它最初被导入为`import {CodeMirror} from 'react-codemirror'`。在这种情况下，我们可以对它使用`React.lazy`如下:

```
import React, { Suspense } from "react";
const CodeMirror = lazy(() =>
  import("react-codemirror").then(module => ({ default: module.CodeMirror }))
);

function App() {
  return (
    <div>
      <Suspense fallback="Loading editor...">
        <CodeMirror />
      </Suspense>
    </div>
  );
} 
```

我们在这里做的是，一旦我们导入了指定的模块，在`then`回调函数中，我们将它转换成一个看似默认的导出模块——一个在`default`键上有可用模块的对象。

边注，这个 app 是 [CSSBattle](https://cssbattle.dev) 。

那都是乡亲们！去刮掉你页面上一些不必要的字节。如果你有任何问题或意见，请在这里或在 Twitter 上问我 [@chinchang457](https://twitter.com/chinchang457) (DMs 开放)。