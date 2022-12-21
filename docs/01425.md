# 开发人员会犯的 10 个错误

> 原文：<https://dev.to/bnevilleoneill/10-mistakes-react-developers-make-2mje>

**由[ogundipe Samuel](https://blog.logrocket.com/author/osamuel/)T3】✏️撰写**

在开发 React 应用程序的开发人员中，某些错误已经变得相当普遍。这些错误可能是疏忽、赶时间的压力或者缺乏 React/JavaScript 经验的结果。

在这篇文章中，我将概述开发人员在构建 React 应用程序时经常犯的 10 个错误。虽然我们在本教程中使用 React，但是我们在这里讨论的大多数技术都可以应用于其他框架。

*注意:这篇文章假设你理解并在过去使用过 React。如果你没有任何使用 React 的经验，你可以参考文档[这里](https://reactjs.org/docs/getting-started.html)开始使用。*

### 1。没有创建足够的组件

React 开发人员经常犯的一个错误是他们没有创建足够的组件。

一般来说，写应用程序有两种方式:把所有东西放在一个地方( **monolith** )，或者把所有东西分成更小的部分(**微服务**)。

按照设计，React 应用程序意味着**组件化**。考虑以下模型:

[![An example of a React dashboard.](img/b77e3102961249c31ea2c7e4fb17c119.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LjqfdgtA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.logrocket.com/wp-content/uploads/2019/09/React-dashboard-nocdn.png)

为了使用 React 正确地构建这个仪表板，我们必须将它视为一组组成页面的组件，而不是一个完整的页面本身。

这样，我们可以创建不同的组件集，当它们放在一起时，就构成了整个页面。

这种技术不仅节省了您的时间，而且在调试时也减轻了您的压力，因为您可以立即知道哪个组件与每个错误相关联。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### 2。在组件中编写逻辑

当寻找创建可重用组件的适当方法时，表示和容器组件创建模式通常是最先出现的模式之一。

表示组件与事物的外观有关，而容器组件与事物的工作方式有关。

在 React 应用程序中你会看到的一个常见错误是，表示标记和应用程序逻辑被融合到一个组件中。

这种方法的缺点是，如果不复制和粘贴，就不能轻松地重用任何组件或逻辑。

如果使用表示和创建模式，可以更容易地实现标记和逻辑的可重用性。您还可以在不影响行为的情况下更改 UI。

考虑以下组件:

这是一个 books 组件，只设计用于从 props 接收数据并显示它。这是一个表象成分。

```
const Books = props => (
  <ul>
    {props.books.map(book => (
      <li>{book}</li>
    ))}
  </ul>
) 
```

Enter fullscreen mode Exit fullscreen mode

这个 books 组件管理和存储它自己的数据，并使用上面的表示组件 books 来显示它。

```
class BooksContainer extends React.Component {
  constructor() {
    this.state = {
      books: []
    }
  }

  componentDidMount() {
    axios.get('/books').then(books =>
      this.setState({ books: books }))
    )
  }

  render() {
    return <Books books={this.state.books} />
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3。突变状态变量

突变是改变事物的能力。考虑以下状态:

```
const person = {
   name : "John",
   sex  : "Male",
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您在应用程序中创建了一个新变量，并把`person`对象赋给它，意图改变它，您可能会对结果感到惊讶:

```
const newPerson = person

newPerson.name = "Jane"
newPerson.sex  = "Female" 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试记录`person`和`newPerson`对象，您会注意到两者现在都反映了设置的最新值。

这通常可以解释不寻常的组件行为。要解决这个问题，可以使用`.slice()`方法或者`ES6 spread operator`。

然而，最好的方法是不变性。你既可以自己实现，也可以使用 React 团队推荐的 [Immutable.js](https://immutable-js.github.io/immutable-js/) 和[immutable-helper](https://github.com/kolodny/immutability-helper)。

### 4。不使用绝对路径

如果您曾经使用过包含许多组件、图像、CSS 文件和其他文件的 React 应用程序，您会同意从不同的目录导入文件是很乏味的。很多时候，我们会这样导入文件:

```
../../../importone.js
../../../importtwo.js 
```

Enter fullscreen mode Exit fullscreen mode

我们已经可以看到它并不整洁，改变文件的目录将导致导入失败。随着 [Create React App 3](https://github.com/facebook/create-react-app/releases/tag/v3.0.0) 的发布，我们现在可以使用绝对导入路径了。

为此，在根目录下创建一个`jsconfig.json`文件，包含以下内容:

```
// jsconfig.json
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以像这样导入你的文件:

```
import React from 'react';
import { LINKS } from 'helpers/constants';
import Button from 'components/Button/Button';

function App() {
  return (
    <>
      <Button>
        This is my button
      </Button>

      <a href={LINKS.ABOUT}>About Us</a>
    </>
  );
}
export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这不仅更干净，还意味着在更改文件位置后，您不需要更新代码中的路径。在这里了解更多关于 CRA V3 的信息。

### 5。没有在列表组件上使用`key`

我们经常会遇到需要呈现项目列表的情况。代码如下所示:

```
const lists = ['one', 'two', 'three'];

render() {
  return (
    <ul>
      {lists.map(listNo =>
        <li>{listNo}</li>)}
    </ul>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

对于较小的应用程序，这可能行得通。但是在处理大型列表时，当您试图修改或删除列表中的项目时，会遇到渲染问题。

React 跟踪 DOM 上的每个列表元素。没有它，它就不知道列表项中发生了什么变化。要解决这个问题，你需要像这样给所有的列表元素添加一个键:

```
<ul>
  {lists.map(listNo =>
    <li key={listNo}>{listNo}</li>)}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

*注意:用 ID 或任何唯一的属性映射一个对象数组并使用 ID 作为一个键总是一个好习惯。React 中的键应该是唯一的。即使我们的例子可以工作，这只是因为我们的示例数组中的元素是唯一的。*

### 6。不编写单元测试

这是最常见的错误之一。这一点经常被忽视，因为应用程序在技术上仍然可以在没有单元测试的情况下工作。单元测试允许您独立测试应用程序的各个部分，以确保某项功能按预期运行。

例如，您可以编写一个单元测试来检查传递给组件的属性是否在浏览器上呈现。

您可能想知道为什么要编写这么小的测试。有时你希望你的道具在编写完组件后能正确显示，但是偶尔一个冲突的 CSS 样式会阻止它显示。

通过立即指出错误(失败),编写单元测试可以节省追踪错误的时间。它们帮助您快速调试应用程序。

### 7。不使用道具类型

我经常在应用程序中看到不正确的数据类型。

例如，假设您想通过 props 将数字 2 传递给另一个组件。通常，你会看到它是这样做的:

```
<MyComponent value="2" /> 
```

Enter fullscreen mode Exit fullscreen mode

这会将值 2 作为字符串而不是数字发送给`MyComponent`。要以数字形式发送，可以这样写:

```
<MyComponent value={2}/> 
```

Enter fullscreen mode Exit fullscreen mode

通过[道具类型包](http://npmjs.com/package/prop-types)定义类型是确保你发送正确道具的最可靠方式。

属性类型用于记录传递给组件的预期属性类型。React 将根据这些定义检查传递给组件的属性，如果不匹配，将在开发中发出警告。

你可以在这里了解更多道具类型[。](https://github.com/facebook/prop-types)

### 8。不使用助手类或函数

这是我在许多 React 应用程序中看到的常见错误。

除了可重用的组件，我们的应用程序中也有可重用的功能。

该功能通常是在组件到组件的基础上硬编码的，这导致了相似组件之间的低效和不一致的行为。

所有容器组件都包含获取资源、将其保存到状态和管理错误的逻辑。

大多数情况下，这种行为在不同的容器组件中是相同的，但是如果编写不当，可能会表现得不一致。

考虑上面的例子，我们调用 API 来获取资源，设置状态，并处理错误。

如果我们将该行为提取到一个助手类或函数中，我们就可以在 API 调用、设置状态和错误处理中重用相同的逻辑。

### 9。使用 Redux 或 Flux 管理所有应用程序状态

在更大的 React 应用程序中，许多开发人员使用 Redux 或 Flux 来管理全局状态。这非常有用，尤其是当应用程序的各个部分都将受益于共享状态时。

然而，使用 Redux 或 Flux 来管理应用程序中的每个状态是不明智的。

以表单组件为例。如果我们希望在访问 check 按钮时总是检查它的状态，最好的方法是使用本地状态方法或 useState(对于钩子)来管理它，而不是使用 Redux 或 Flux。

### 10。不使用 React 和 Redux 开发工具

应用程序总会在一段时间后出现问题。调试通常是一项繁重的工作，因为大多数时候会涉及到许多组件。

使用 [React 开发工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)，您可以检查 React 元素的渲染树，这对于查看各种组件如何构建页面非常有用。

[Redux dev tools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en) 还附带了许多功能，让您可以看到已经发生的每个操作，查看这些操作导致的状态变化，并返回到特定操作发生之前。

您可以添加 React 开发工具作为开发依赖项或浏览器扩展。使用它们将节省您大量的开发时间。

### 结论

在本教程中，我们讨论了 React 开发人员在创建应用程序时会犯的一些常见错误。我们还讨论了一些方法和工具，这些方法和工具可能会使这个过程更有效，痛苦更少。

React 开发过程中常见的错误有什么小技巧吗？一定要留言。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

React 开发者犯的 10 个错误首先出现在[博客](https://blog.logrocket.com)上。