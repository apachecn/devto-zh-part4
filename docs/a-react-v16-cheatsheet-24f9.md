# 这里有一个 React v 16+cheat sheet(PDF/JPEG/自定义主题)

> 原文：<https://dev.to/jsmanifest/a-react-v16-cheatsheet-24f9>

在[媒体](https://medium.com/@jsmanifest?source=post_page-----ab2545d5a8e3----------------------)上找到我
加入我的[简讯](https://app.getresponse.com/site2/javascript-newsletter?u=zpBtw&webforms_id=SM2hz)

[带我去看备忘单](https://jsmanifest.com/react-cheatsheet)

有时，使用 React 创建一个快速界面可能需要 30 分钟。但有时也可能需要几个小时，可能受多种原因的影响。

如果您经常忘记方法、属性或它们所提供的功能的名称，仅仅为了一个 google 搜索就不得不离开您的代码编辑器，这可能会变得令人恼火。然而，输入几个字母并得到你想要的答案真的那么难吗？嗯，绝对不行。但是，如果这种情况发生不止一次，那么也许是时候在你的身上获得一个备忘单了，这样你就不必再离开你的代码编辑器了。从长远来看，在你身边放一张小抄肯定会节省你一些时间。

这里有一张你可以使用的备忘单:

[![react v16 cheat sheet](img/98b1deb926ca5fec8ed2be743327eba4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DeVZfddn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/this-react-cheatsheet-includes-all-of-v16-features/post23.cheatsheet.gif)

[带我去看备忘单](https://jsmanifest.com/react-cheatsheet)

当您查看备忘单时，请记住您可以:

1.  将备忘单生成为可下载的 PDF 或 PNG 格式，或者您可以将该页面加入书签，以后再回来查看。

2.  如果您不喜欢列的排序方式，您可以在保存备忘单之前拖动并重新排序它们。

3.  您可以在选择框中选择任何代码语法主题，以在备忘单中生成(大约有 25 个主题可供选择):

[![select box](img/a5471f4acb6f41f22c242bb1c4d73721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--useRcZ0I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/this-react-cheatsheet-includes-all-of-v16-features/select_box.jpg)

如果有人需要的话，我会把它放入公开回购中。我也是昨天才开始写的，所以它可能还不是一个完美的备忘单。

此外，我的目标是将所有这些放在一个页面中，但是信息太多了。如果有人对更换/移除哪些部件有任何建议，请随时告诉我。

关闭浏览器后，这些更改会一直保留，这样您就不必重新做任何事情。

以下是到目前为止备忘单中的完整列表(我会不断更新备忘单):

## 片段

```
// Does not support key attribute
const App = () => (
  <>
    <MyComponent />
  </>
)

// Supports key attribute
const App = () => (
  <React.Fragment key="abc123">
    <MyComponent />
  </React.Fragment>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 返回类型

```
const App = () => 'a basic string' // string
const App = () => 1234567890 // number
const App = () => true // boolean
const App = () => null // null
const App = () => <div /> // react element
const App = () => <MyComponent /> // component
const App = () => [
  // array
  'a basic string',
  1234567890,
  true,
  null,
  <div />,
  <MyComponent />,
] 
```

Enter fullscreen mode Exit fullscreen mode

## 错误边界(React v16.0)

```
class MyErrorBoundary extends React.Component {
  state = { hasError: false }
  componentDidCatch(error, info) {...}
  render() {
    if (this.state.hasError) return <SomeErrorUI />
    return this.props.children
  }
}

const App = () => (
  <MyErrorBoundary>
    <Main />
  </MyErrorBoundary>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 静态方法

```
// Returning object = New props require state update
// Returning null = New props do not require state update
class MyComponent extends React.Component {
  static getDerivedStateFromProps(props, state) {...}
  state = {...}
}

// Return value is passed as 3rd argument to componentDidUpdate
class MyComponent extends React.Component {
  static getSnapshotBeforeUpdate(prevProps, prevState) {...}
}

// Listening to context from a class component
import SomeContext from '../SomeContext'
class MyCompmonent extends React.Component {
  static contextType = SomeContext
  componentDidMount() { console.log(this.context) }
}

// Enables rendering fallback UI before render completes
class MyComponent extends React.Component {
  state getDerivedStateFromError() {...}
  state = { error: null }
  componentDidCatch(error, info) {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 组件状态

```
// Class component state
class MyComponent extends React.Component {
  state = { loaded: false }
  componentDidMount = () => this.setState({ loaded: true })
  render() {
    if (!this.state.loaded) return null
    return <div {...this.props} />
  }
}

// Function component state (useState/useReducer)
const MyComponent = (props) => {
  // With useState
  const [loaded, setLoaded] = React.useState(false)
  // With useReducer
  const [state, dispatch] = React.useReducer(reducer, initialState)
  if (!loaded) return null
  React.useEffect(() => void setLoaded(true))
  return <div {...props} /> 
```

Enter fullscreen mode Exit fullscreen mode

## 渲染组件

```
// Ways to render Card
const Card = (props) => <div {...props} />

const App = ({ items = [] }) => {
  const renderCard = (props) => <Card {...props} />
  return items.map(renderCard)
  // or return items.map((props) => renderCard(props))
}

const App = (props) => <Card {...props} />

class App extends React.Component {
  render() {
    return <Card {...this.props} />
  }
}

const MyComp = ({ component: Component }) => <Component />
const App = () => <MyComp component={Card} /> 
```

Enter fullscreen mode Exit fullscreen mode

## 默认道具

```
// Function component
const MyComponent = (props) => <div {...props} />
MyComponent.defaultProps = { fruit: 'apple' }

// Class component
class MyComponent extends React.Component {
  static defaultProps = { fruit: 'apple' }
  render() {
    return <div {...this.props} />
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他反应出口

```
// createContext (React v16.3)
const WeatherContext = React.createContext({ day: 3 })
const App = ({ children }) => {
  const [weather, setWeather] = React.useState(null)
  const [error, setError] = React.useState(null)
  React.useEffect(() => {
    api.getWeather(...)
      .then(setWeather)
      .catch(setError)
  }, [])
  const contextValue = { weather, error }
  return (
    <WeatherContext.Provider value={contextValue}>
      {children}
    </WeatherContext.Provider>
  )
}
const SomeChild = () => {
  const { weather } = React.useContext(WeatherContext)
  console.log(weather)
  return null
}

// createRef (Obtain a reference to a react node) (React v16.3)
const App = () => {
  const ref = React.createRef()
  React.useEffect(() => { console.log(ref.current) }, [])
  return <div ref={ref} />
}

// forwardRef (Pass the ref down to a child) (React v16.3)
const Remote = React.forwardRef((props, ref) => (
  <div ref={ref} {...props} />
))
const App = () => {
  const ref = React.createRef()
  return <Remote ref={ref} />
}

// memo (Optimize your components to avoid wasteful renders) (React v16.6)
const App = () => {...}
const propsAreEqual = (props, nextProps) => {
  return props.id === nextProps.id
} // Does not re-render if id is the same
export default React.memo(App, propsAreEqual) 
```

Enter fullscreen mode Exit fullscreen mode

## 导入

```
// default export
const App = (props) => <div {...props} />
export default App
import App from './App'

// named export
export const App = (props) => <div {...props} />
import { App } from './App' 
```

Enter fullscreen mode Exit fullscreen mode

## 指针事件(React v16.4)

```
onPointerUp           onPointerDown
onPointerMove         onPointerCancel
onGotPointerCapture   onLostPointerCapture
onPointerEnter        onPointerLeave
onPointerOver         onPointerOut

const App = () => {
  const onPointerDown = (e) => console.log(e.pointerId)
  return <div onPointerDown={onPointerDown} />
} 
```

Enter fullscreen mode Exit fullscreen mode

## React 悬疑/懒惰(React v16.6)

```
// lazy -> Dynamic import. Reduces bundle size
// + Code splitting
const MyComponent = React.lazy(() => import('./MyComponent))
const App = () => <MyComponent />

// Suspend rendering while components are waiting for something
// + Code splitting
import LoadingSpinner from '../LoadingSpinner' const App = () => (
  <React.Suspense fallback={<LoadingSpinner />}>
    <MyComponent />
  </React.Suspense>
) 
```

Enter fullscreen mode Exit fullscreen mode

## React Profiler(React v 16.9)

```
const App = () => (
  <React.StrictMode>
    <div>
      <MyComponent />
      <OtherComponent />
    </div>
  </React.StrictMode>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 同步/异步`act`测试实用程序(React v16.9)

```
import { act } from 'react-dom/test-utils'
import MyComponent from './MyComponent'
const container = document.createElement('div')

// Synchronous
it('renders and adds new item to array', () => {
  act(() => {
    ReactDOM.render(<MyComponent />, container)
  })
  const btn = container.querySelector('button')
  expect(btn.textContent).toBe('one item')
  act(() => {
    button.dispatchEvent(new MouseEvent('click', { bubbles: true }))
  })
  expect(btn.textContent).toBe('two items')
})

// Asynchronous
it('does stuff', async () => {
  await act(async () => {
    // code
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

[带我去看备忘单](https://jsmanifest.com/react-cheatsheet)

## 结论

本帖到此结束！我希望你发现这是有用的，并期待在未来更多！

在[媒体](https://medium.com/@jsmanifest?source=post_page-----ab2545d5a8e3----------------------)上找到我
加入我的[简讯](https://app.getresponse.com/site2/javascript-newsletter?u=zpBtw&webforms_id=SM2hz)