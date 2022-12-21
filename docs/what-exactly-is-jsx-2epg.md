# JSX 到底是什么？

> 原文：<https://dev.to/jasterix/what-exactly-is-jsx-2epg>

我刚开始学 React。奇怪的是，我花了一段时间才理解 JSX，以及它在我过去几周学习的编程术语中的位置。

JSX 似乎是那些有经验的开发人员得到的东西中的一种，但却无法向别人解释清楚。但是对于我们这些对编程一无所知的新手来说，我希望这篇简短的帖子能让你明白一些事情。

#### 简称:

JSX 是 JavaScript 的一种类似 XML 的语法扩展。

#### 英文:

JSX 本质上是一种在 JavaScript 文件中编写 HTML 代码的方法。这使得创建 React 元素(或组件)变得容易

### 适用场合:

React 是一个用于快速创建用户界面的 JavaScript 库。React 完全由 JavaScript 代码构建而成。它创建了可重用代码的小部分，称为组件。

这使您可以将完整的用户界面分成独立的、可重用的部分，每个部分都保存在自己的。js 文件。

### 什么样子:

*   每个。React 应用程序中的 js 文件将有一个 render()方法，该方法返回一个 JSX 元素

```
class randomComponent extends ReactComponent {

  render() {

    return (
      <div> </div>
    )
  }
export default randomComponent 
```

Enter fullscreen mode Exit fullscreen mode

与此同时，你的 App.js 文件将包含类似这样的内容:

```
class App extends React.Component {

   render(){ 

    return(
    <div className="App">
      <randomComponent></randomComponent>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，FreeCodeCamp.org 有一个涵盖 JSX 语法的 React 优秀教程。来看看吧！