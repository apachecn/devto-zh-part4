# React 16.9 中的新特性:分析器 API、错误修复等等

> 原文：<https://dev.to/bnevilleoneill/what-s-new-in-react-16-9-profiler-api-bug-fixes-and-more-1f0e>

**作者:[内森·塞巴斯蒂安](https://blog.logrocket.com/author/nathansebhastian/)T3】✏️**

当“2018 年 JavaScript 现状”调查结果公布时， [React 以 91%的满意率和超过 16，000 名用户成为前端框架类别的赢家](https://2018.stateofjs.com/front-end-frameworks/conclusion/):

<figure>[![React Popularity In The State Of JavaScript 2018 Survey](img/58a1df46f98364684cd833648d31954f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kqdA0pkl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/react-popularity-state-js.png%3Fresize%3D730%252C396%26ssl%3D1) 

<figcaption id="caption-attachment-4978">来源于“2018 年 JavaScript 现状”调查。</figcaption>

</figure>

React 是当今最流行的构建用户界面的库，在本帖中，我们将关注 16.9 版本中添加的显著的新特性、弃用和错误修复。

## Profiler API

[React Profiler](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) 首次被添加到 React dev tools 16.5 版中。该功能测量渲染的每个组件，以找到渲染的“成本”，以便您可以更快地识别 React 应用程序中的性能瓶颈。

为了使用它，你可以打开 React 的 DevTools，运行一个记录，渲染一些 React 组件，然后停止记录。

<figure>[![React Profiler In The DevTools](img/1cd121496f2285a14367bfbf206cf8ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--joRHLU0h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/react-profiler-devtools.gif%3Fresize%3D730%252C442%26ssl%3D1)

<figcaption id="caption-attachment-4981">dev tools 中的 React Profiler。</figcaption>

</figure>

分析器很有用，但是有点麻烦。您必须启动和停止记录，然后检查记录中看起来加载时间很长的部分。这就是为什么我们现在有了`<React.Profiler>`组件，它可以用来以*编程方式*收集性能指标。

组件将需要两个属性:一个`id`属性和一个`onRender`属性。它将在树中的组件重新呈现时触发。下面是一些示例代码:

```
import React, {Profiler} from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <Profiler id="MyAwesomeApp" onRender={onRender}>
    <Router>
      <App />
    </Router>
  </Profiler>,
  document.getElementById("root")
);

function onRender(
  id,
  phase,
  actualDuration,
  baseDuration,
  startTime,
  commitTime,
  interactions
) 
{
  console.log(id)
  console.log(phase)
  console.log(actualDuration)
  console.log(baseDuration)
  console.log(startTime)
  console.log(commitTime)
  console.log(interactions)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是控制台中的结果:

[![Profiler API Results In The Console](img/7a241b97a66f8f34faf604e279d6d450.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xv4J5io3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/console-results-profiler-api-e1565806773783.png%3Fresize%3D730%252C190%26ssl%3D1)

虽然 DevTools 的 Profiler 记录了所有的 React 应用程序，但是使用`<React.Profiler>`组件，您可以隔离并只记录树中需要更多关注的部分。此外，它的*在生产构建中自动禁用*，因此您不需要在测量后移除它。

关于分析器组件的更多细节及其参数的解释，请查看官方文档[这里](https://reactjs.org/docs/profiler.html)。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 异步`act()`功能

React 引入了一个名为`act()`的测试工具来帮助开发人员创建 UI 测试。在编写 UI 测试时，有一些任务，如渲染、用户交互事件(如单击)或数据获取，可以被视为与 React 应用程序交互的“单元”。

由于 JavaScript 的异步特性，React 实际上可能会跳过一些任务并立即断言测试，而不会等到任务完成。

React 提供了一个名为`act()`的助手，它将确保在您做出任何断言之前，与这些“单元”相关的所有更新都已被处理并应用到 DOM。

但是`act()`的一个问题是它只能支持同步函数，所以如果你的`act()`中有异步函数，你就不走运了。

幸运的是，它现在已经得到了改进，您可以将 async/await 与`act()` :
一起使用

```
await act(async () => {
  // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将帮助您确保异步更新在断言之前完成。这里有一个关于如何使用`act()` 的很好的教程。

## 不安全的生命周期方法被重命名

React 团队几乎在一年前就已经宣布了这一变化。在这个版本中，不安全的生命周期方法最终被重命名为:

*   `componentWillMount` → `UNSAFE_componentWillMount`
*   `componentWillReceiveProps` → `UNSAFE_componentWillReceiveProps`
*   `componentWillUpdate` → `UNSAFE_componentWillUpdate`

别担心，不安全并不意味着你的应用更容易被黑客攻击——它只是提醒你，这些生命周期内的代码在 React 的未来版本中更有可能出现 bug。点击查看详情[。](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)

不带`UNSAFE_`前缀的方法名称在这个版本中仍然有效，但是在 React 版本 17 发布时，它们将被完全删除。如果您打开开发人员控制台，您将会收到以下警告:

[![Unsafe Method Prefix Warning In Console](img/5f63ea7f6c60ec936a113a8d6005618d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--86ToHH3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/unsafe-method-prefix-warning-console-e1565806824204.png%3Fresize%3D730%252C246%26ssl%3D1)

这些带前缀的方法名称旨在帮助具有问题模式的组件在代码审查和调试会话中脱颖而出。通常不推荐使用这些方法，所以你可以阻止你的团队更多地使用[严格模式](https://reactjs.org/docs/strict-mode.html)。

如果你的应用程序实际上使用了很多这样的方法，你可以使用这个 [codemod](https://github.com/reactjs/react-codemod) 来重构它，这比你自己手动编辑它们更快:

```
cd your_project
npx react-codemod rename-unsafe-lifecycles 
```

Enter fullscreen mode Exit fullscreen mode

它将启动一个交互式提示来选择您想要重命名的文件。

## `javascript:`按期清除网址

在`href`属性中使用`javascript:`很久以前就很流行了，但是现在，这已经是一种过时的做法，可能会在你的应用程序中打开安全漏洞。很容易意外地包含未排序的输出。比如:

```
const userProfile = {
  website: "javascript: alert('you got hacked')",
};
<a href={userProfile.website}>Profile</a> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将在 React 17 中导致一个错误，如果你想在一个链接被点击时运行一个脚本，你需要使用 React `onClick`事件处理程序和一个样式为链接的按钮元素。它既安全又易于实现:

```
awesomeFunction = () => console.log("Here is javascript: URL alternative!");
// the button below used Bootstrap styling...
<button className="btn btn-link" onClick={this.awesomeFunction}>Profile</button> 
```

Enter fullscreen mode Exit fullscreen mode

## 贬低了`FactoryComponent`的格局

这是一种相当晦涩的代码模式，由 React 团队在用 Babel 编译类流行起来之前引入。它是一个用`render`方法返回对象的组件:

```
function FactoryComponent() {
  return { render() { return <div />; } }
} 
```

Enter fullscreen mode Exit fullscreen mode

语法看起来很糟糕，因为 Babel 已经成为 React 应用程序的标准编译器，所以不再需要这种组件模式。支持它会导致 React 比需要的稍微大一点和慢一点。它将在未来的主要版本中被完全删除。

如果你的应用程序碰巧使用了这种模式，你可以使用`FactoryComponent.prototype = React.Component.prototype`来保持它的工作，但是仅仅将代码转换成一个函数或者一个类要好得多。

## 来自 useEffect 的 setState 循环记录错误

由`useEffect`中的`setState`引起的无限循环现在会记录一个错误，类似于你在类组件中调用`componentDidUpdate`中的`setState`时看到的错误。下面是一些示例代码:

```
function App() {
  const [counter, setCounter] = React.useState(0);
  React.useEffect(() => {
    setCounter(counter + 1);
  })
  return <h1>Watch out infinite loop in the console!</h1>
} 
```

Enter fullscreen mode Exit fullscreen mode

由于没有第二个参数，这种效果依赖于更新，它将永远运行，直到您关闭浏览器窗口。

## 修复`findDOMNode()`被调用时的悬念崩溃

`<React.Suspense>`组件用于延迟加载初始渲染中不需要的组件。但是从 16.7 版本开始，很多开发者在使用时开始收到错误`Invariant Violation: Unable to find node on an unmounted component`。

经过检查，开发人员弄清了这个错误是由调用`ReactDOM.findDOMNode()`内部悬念引起的。崩溃现在在这个版本中被修复了，但是如果你仍然感兴趣，这里有一个[代码沙箱](https://codesandbox.io/s/antd-reproduction-template-1r6pr)来测试这个错误。只是随机点击菜单，直到错误出现。

你可能不会直接使用`findDomNode()`，但是这个函数实际上在很多库中使用，比如[语义 UI](https://github.com/Semantic-Org/Semantic-UI-React/issues/3593#issuecomment-488595217) 和[蚂蚁设计](https://github.com/facebook/react/issues/14188#issuecomment-500478400)。所以如果你不更新你的 React 库，你可能会无意中触发这个 bug！

## npm 已经提供 React v16.9

要更新您的应用程序，只需运行:

```
npm install --save react@^16.9.0 react-dom@^16.9.0 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果你用的是纱线:

```
yarn add react@^16.9.0 react-dom@^16.9.0 
```

Enter fullscreen mode Exit fullscreen mode

黑客快乐！

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[React 16.9 中的新特性:剖析器 API、错误修复和更多](https://blog.logrocket.com/whats-new-in-react-16-9-profiler-api-bug-fixes-and-more/)首先出现在[日志火箭博客](https://blog.logrocket.com)上。