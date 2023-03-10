# Nautil:一个基于 React 的响应迅速、高效、灵活的 JavaScript 框架，用于构建跨平台应用程序。

> 原文：<https://dev.to/frustigor/nautil-a-responsive-efficient-and-flexible-javascript-framework-based-on-react-for-building-cross-platform-applications-563h>

我在 React 工作了大约 3 年，发现当我开始一个新项目时，很难决定选择哪个提议。纠结了 redux，react-router，redux-thunk，redux-saga，甚至重赛，发现真的是一堆乱七八糟的库可以挑。

这是因为脸书离开时，React 只是一个用户界面库，而不是一个框架。我们应该找到一种方法来制作一个不仅包含 UI 组件，还包含数据管理、导航甚至国际化的应用程序。有一个储存库收集了学习 React 的路线图，看完之后你会心情低落。我写了一个叫做 Nautil 的 Javascript 框架，它是基于 React 的。在本文中，我将为您介绍 Nautil。

[github.com/tangshuang/nautil](https://github.com/tangshuang/nautil)

Nautil 基于 React，所以不需要学习语法。对于很多开发者来说，这是很熟悉的。您不需要更改 React 组件，只需要学习 Nautil 导出的 api 并更改应用程序级代码结构。

为什么我们需要创建一个新的框架？

**脸书把数据和路由器组织作为一个练习留给读者。**

这可能不是脸书的错，他们的初衷是像 Jquery 做的那样，建立一个 UI 驱动库。然而，开发人员喜欢 React，他们把它作为应用程序的基础。所以在最后，React 团队不得不发布他们那些糟糕的提议，比如 flux。我不拒绝 flux 和 redux，但我真的觉得它们很复杂。

什么是可怕的？开发者把 redux 当做标准。许多追随者认为应用程序应该必须使用 redux 来组织数据。所以，最终，redux 在一个生态中成长，即 redux-thunk，redux-saga，reselect…

Nautil 提供了一种组织状态、数据和导航的新方法。我们不遵循 redux，我们认为每样东西都应该足够容易使用和理解。现在让我展示一个案例:

```
import { Component, Store, Observer } from 'nautil'

const store = new Store({
  name: 'tomy',
  age: 10,
})

class App extends Component {
  render() {
    return (
      <Observer subscribe={dispatch => store.watch('*', dispatch)} dispatch={this.update}>
        <div>{store.state.name} is {store.state.age} years old.</div>
      </Observer>
    )
  }
}

function grow() {
  store.state.age ++
}

function setState(key, value) {
  store.set(key, value)
}

function update(data) {
  return store.update(data)
} 
```

前面的代码展示了简单存储用法的一个例子。在 Nautil 中，您不需要创建大量 redux reducers 和动作，只需使用 store 创建一个 Store 实例，并将其传递给一个 Observer 组件，该组件可以在存储数据发生变化时触发一些功能。存储是一个可观察的数据容器，因此无论何时存储中的数据发生变化，您都可以知道并响应这些变化。

如您所见，状态管理非常简单，没有任何无用的 reducers 逻辑。

如果你能接受之前的商店理念，我希望你会喜欢 Nautil 在里面给出的东西:

*   基于 react 的用户界面渲染
*   路由器/导航
*   具有可观察存储库的状态管理
*   用可观察数据库进行数据管理和请求
*   数据类型检查器
*   使用 react-dom 和 react-native 的跨平台开发建议
*   使用 i18next 和日期/数字/货币地区格式化程序进行国际化

如果你在 React 中纠结过异步动作，你可能会爱上 Nautil，因为在 Nautil 中，没有异步动作，所有方法都是同步的。这得益于观察者模式。如果你注意，你会发现 Nautil 的存储、数据存储和导航都是可观察的。使用 Observer/ObservableProvider 组件，有时使用 Prepare 组件，我们不需要担心异步操作。

最后，我想谈谈跨平台特性。我们将组件嵌入其中，如部分、图像、按钮等等。这些组件可以在 PC、移动甚至本地开发中很好地工作。依靠 react-native，我们可以轻松地用相同的代码构建一个原生应用。是的，你不需要再写代码了。只需将代码放入 create-react-native-app，导入 nautil/native 而不是 nautil/dom。

鹦鹉螺是反应，鹦鹉螺不仅仅是反应。

Nautil 是 React，它并没有改变 React 任何特性，尽管它提供了一些方便的属性。您可以在 Nautil 中使用旧的 react 组件，也可以在其他 React 应用程序中使用 Nautil 组件。几乎没有需要修改的代码。Nautil 只在一个包中提供状态管理、数据管理、导航管理和国际化，并提供构建跨平台应用程序的能力。那是鹦鹉螺的全部。

我知道，我没有做颠覆性的事情。但是，我不公平的去做一些颠覆性的事情。Nautil 直到现在还没有完成。借助开源的力量。我希望，如果你对这个项目感兴趣，你可以加入我。