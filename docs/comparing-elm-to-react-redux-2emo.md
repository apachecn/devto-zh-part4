# 比较 Elm 和 React/Redux

> 原文：<https://dev.to/rametta/comparing-elm-to-react-redux-2emo>

我最近一直在探索如何在 Elm 创建 web 应用程序，发现与我过去从事的 React/Redux 项目相比，这是一股新鲜空气。

* *免责声明:我仍然认为 React/Redux 对于大型团队来说是很棒且可行的，如果做得正确的话。本文将解释我在不同公司的大型团队中工作时遇到的棘手问题，以及为什么我认为 Elm 在某些情况下是更好的选择。*

## 痛点

过了一段时间，我参与的许多 React/Redux 项目变得庞大起来，有数百个 reducers、数百个组件、史诗、thunks、重选选择器、sagas 和定制中间件的混合。热模块替换变得很慢，构建时间变得很慢，运行时性能变得很慢，审计分数变得很低，捆绑包变得很大，应用程序每次推送都会出现越来越多的运行时错误。

我知道这不是每个人的经历，如果你在开发期间执行严格规则的地方工作，那么你就不会有所有这些问题。但是很可能你也经历过一些这样的痛点。(如果你没有经历过这些痛苦，那么做得好，这是一个困难的壮举)

当我谈到发展“规则”时，我指的不是 linter 规则和更漂亮。我的意思是不要安装太多的第三方库，对你的模块进行适当的代码分割，每周或每月进行灯塔审计，看看你的团队可以改进的地方。

## 解

榆树有一个美丽的生态系统，旨在防止这些痛苦。当然，它也有自己的挣扎，但在我看来，这是值得的。

**榆树的优势:**

*   没有运行时异常
*   一切都是不可改变的
*   小捆尺寸
*   类似于 Redux 的内置事件发射器和全局状态存储
*   用于单页应用程序的内置路由器
*   内置代码格式化程序(比如更漂亮的)
*   强类型系统
*   与 JS 的简单互操作
*   惊人的编译器错误信息和快速编译时间

这些优势带来了更可靠的 webapps、更好的 DX 和更好的终端用户体验。

## 比较榆树架构 React/Redux

学习 Elm 可能看起来是一项艰巨的任务，尤其是对于所有新的语法和概念，但这正是本文旨在帮助解决的问题，并说明做出反应并没有什么不同。

下面，我用 Elm 和 React/Redux 写了同一个 app，来展示它们的相似之处。

### 状态

在 Redux 中有一个全局存储用于保存应用程序状态，Elm 有一个类似的概念叫做模型，它是一个强类型版本的存储。

还原减速器的初始状态

```
const initialState = {
  count: 0
} 
```

Enter fullscreen mode Exit fullscreen mode

Elm 初始模型和类型

```
type alias Model =
  { count : Int }

initialModel =
  { count = 0 } 
```

Enter fullscreen mode Exit fullscreen mode

Elm 中的类型别名确保了在 count 属性中除了一个数字之外不会分配任何东西。

### 动作

在 Redux 中，需要编写动作来触发一些状态变化或副作用。Elm 有非常相似的消息，但是是打字的！

重复动作

```
// action types
export const INCREMENT = 'INCREMENT'
export const DECREMENT = 'DECREMENT'

// actions
export const increase = () => ({ type: INCREMENT })
export const decrease = () => ({ type: DECREMENT }) 
```

Enter fullscreen mode Exit fullscreen mode

Elm 消息

```
type Msg = Increase | Decrease 
```

Enter fullscreen mode Exit fullscreen mode

### 减速器

对于您创建的每个 redux 动作，通常都有一个对应的 reducer。在 Elm 中，几乎是一样的，除了你必须为每个消息(动作)提供一个更新函数(reducer)。

冗余减速器

```
export function myReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 }

    case DECREMENT:
      return { count: state.count - 1 }

    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

榆树更新

```
update msg model =
  case msg of
    Increase ->
      { model | count = model.count + 1 }

    Decrease ->
      { model | count = model.count - 1 } 
```

Enter fullscreen mode Exit fullscreen mode

在 Elm 中一切都是不可变的，所以要更新一个记录(对象),您必须使用 pipe |和 new record 语法来返回一个带有更新属性的状态的新副本。

### 组件

React 中的组件创建了视图，该视图将呈现给用户查看。Elm 没有组件，只有一个用于渲染的视图功能。

反应 jsx〔t0〕

```
import React from 'react'
import { connect } from 'react-redux'
import { increase, decrease } from './reducer'

const App = ({ increase, decrease, count }) => (
  <div>
    <button type="button" onClick={increase}>+1</button>
    <div>{count}</div>
    <button type="button" onClick={decrease}>-1</button>
  </div>
)

// Connect to redux
const mapStateToProps = ({ count }) => ({ count })
const mapDispatchToProps = { increase, decrease }

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(App) 
```

Enter fullscreen mode Exit fullscreen mode

榆树查看功能

```
view model =
  div []
    [ button [ onClick Increment ] [ text "+1" ]
    , div [] [ text <| String.fromInt model.count ]
    , button [ onClick Decrement ] [ text "-1" ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

### 连接

在 React/Redux 中，组件不会自动访问 Redux 存储或操作/Redux，它们必须显式连接。使用另一个名为 react-redux 的库可以很好地完成连接。在 Elm 中，所有东西都可以自动访问存储中的所有消息类型和数据。

反应/还原

```
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import { myReducer } from './reducers'
import App from './App'

const store = createStore(myReducer)

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
) 
```

Enter fullscreen mode Exit fullscreen mode

榆树

```
main =
  Browser.sandbox
    { init = initialModel
    , update = update
    , view = view
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

所以我们创建了一个简单的计数器应用程序。总的来说，它非常简单，不需要 redux 需要的任何样板文件，并且输入了有效载荷！如果你想玩这个例子，可以在 [ellie-app](https://ellie-app.com) 上查看一下。

如果这篇文章引起了您的兴趣，并且您想了解关于 Elm 的更多信息，请查看以下资源:

*   [榆树官方指南](https://guide.elm-lang.org/)
*   [榆树编程教程](https://elmprogramming.com/)
*   [榆树语法](https://elm-lang.org/docs/syntax)
*   [Elm 语法 VS Javascript](https://elm-lang.org/docs/from-javascript)

在 twitter 上关注我！ [@rametta](https://twitter.com/rametta)