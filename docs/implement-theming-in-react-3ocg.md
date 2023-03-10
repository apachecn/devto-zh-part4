# 在 React 中实现主题化

> 原文：<https://dev.to/kiani0x01/implement-theming-in-react-3ocg>

当我在 react 中开始 web 开发时，我决定在我的 webapp 中添加主题化功能，即通过单击来切换网站中每个组件的主题。互联网上有很多指南，但我认为它们对初学者来说很难，所以这里有一个简单的方法在你的 webapp 中添加主题。

## 你需要知道什么？

通过这种方式，你会学到一些概念。为了快速理解，看一下这些概念(即 React - React 上下文 API 中的 useContext 和 UseState 挂钩)。如果你已经知道了这些概念，那么你就可以开始了，而且你会发现这篇文章非常直截了当。

## 现在转到编码部分

我已经用 [create-react-app](https://create-react-app.dev/) 创建了我的项目。首先，我们必须用
定义一个全球背景

```
export const ThemeContext = React.createContext({
  theme: {
    type: 'light',
    primary: '#00bfa5',
    text: '#black'
  },
  setTheme: () => {}
}) 
```

当然，当你需要使用上下文的值时，你必须先导入它，否则你会得到一个未定义的错误。现在我们名为 ThemeContext 的上下文是全局定义的，但是为了能够使用它的值，我们必须用带有初始值的 ThemeContext.provider 包装组件。之后，我们可以在子组件中使用上下文的值。

```
 return (
    <ThemeContext.Provider value={state}>
      {props.children}
    </ThemeContext.Provider>
  ) 
```

现在你应该在想我到底在哪里定义了这个{state}变量。耐心点，这有点棘手，我已经把这个状态变成了一个对象，它可以存储多个值，也可以像 const 一样存储方法。我这样做是因为我将通过访问这个状态对象来更新上下文的值。如果这听起来让你困惑，看看下面的代码，希望你能理解它。

```
 export const ThemeContextProvider = props => {
  const theme = {
    light: {
      type: 'light',
      primary: '#0277bd',
      text: 'black'
    },
    dark:{
      type: 'dark',
      primary: '#212121',
      text: 'white'
    },
  }

  const setTheme = type => {
    setState({ ...state, theme: type === 'dark' ? theme.light : theme.dark })
  }

  const initState = {
    theme: theme.light,
    setTheme: setTheme
  }

  const [state, setState] = useState(initState)

  return (
    <ThemeContext.Provider value={state}>
      {props.children}
    </ThemeContext.Provider>
  )
} 
```

因为我必须更新状态对象的值，所以我使用了一个 useState 钩子。对于那些不知道这个钩子的人，我想说每当我们需要更新功能组件中组件的状态时，我们必须使用这个钩子。虽然也有一个 useReducer 挂钩，但我会在另一篇文章中介绍。现在，您应该把它看作是更新组件状态的唯一方法。

## 切换主题组件

我已经创建了这个组件来从我们的应用程序中的任何地方切换我的网站的主题，但你需要记住的一件事是，你应该用 ThemeProvider 组件包装你的所有组件，因为当我们切换主题时，我们调用一个 setState 方法来更新上下文的值。

```
import React, { useContext } from 'react'
import { ThemeContext } from './ThemeProvider'

function ToggleTheme() {
  const state = useContext(ThemeContext)

  const btn = {
    color: state.theme.textOnSecondary,
    marginTop: '20px',
    marginBottom: '20px',
    background: state.theme.secondary
  }

  return (
    <button
      variant='contained'
      onClick={() => {
        state.setTheme(state.theme.type)
      }}
      style={btn}
    >
      Toggle Theme
    </button>
  )
}

export default ToggleTheme 
```

你应该已经注意到我还没有设置任何组件的颜色。现在，随着上下文值的变化来改变组件的颜色，我们必须从我们的上下文值中设置颜色值，就像这样

```
import React, { useContext } from 'react'
import { ThemeContext } from './ThemeProvider'

export default function Footer() {
  const state = useContext(ThemeContext)

  const footer = {
    backgroundColor: state.theme.primary,
    color: state.theme.text,
    width: '100%',
    height: '50px',
  }

  return (
   <div style={footer}>
     This is a Footer
   </div>
  )
} 
```

最后，这是我的 App.js 文件的样子:

```
import React from 'react'
import { ThemeContextProvider } from './components/ThemeProvider'
import AppBar from './components/AppBar'
import Footer from './components/Footer'
import ToggleTheme from './components/ToggleTheme'

function App() {

 return (
    <ThemeContextProvider>
      <AppBar />
      <ToggleTheme />
      <Footer />
    </ThemeContextProvider>
  )
}

export default App 
```

我已经写了一篇关于主题化的更深入的文章，你也可以看看。这里的是那个的链接。[这个](https://github.com/mateenkiani/react-tutorials/tree/master/set-up-light-and-dark-theme-react)是这篇文章到我的 github 资源库的链接。如果你有任何问题，请在评论区告诉我。

另外，别忘了查看我的[网站](https://milddev.com)上的其他教程。编码快乐！