# 使用 useContext 和 useState 挂钩作为存储

> 原文：<https://dev.to/nazmifeeroz/using-usecontext-and-usestate-hooks-as-a-store-mnm>

我建立了一个小型的公司内部应用程序(*起立机器人*)，我们记下我们的*有什么新鲜事*，如果有人需要任何帮助，以及我们当天的*结对配置*(我们练习结对编程)。在这个应用程序中，我想将 notes 数据从输入组件传递到发布模块，该模块以帖子的形式发送到我们的 Discord 频道(因此得名 *Stand Up Bot* )。

通常的做法是使用一个*状态容器*，比如 redux，来管理组件之间的数据传递，但是使用 Redux 需要对`reducers`和`actions`有深刻的理解，如果你的*小*应用只是想传递数据而不改变它，那就不一定了。

React JS 为我们提供了一个名为`createContext`的 api，我们可以轻松地从应用程序的任何组件中调用任何数据。通常，当需要在父组件的子组件中使用某个值时，我们通常会将数据作为`prop`向下传递。有时一个`prop`被传递给一个父组件的另一个子组件的子组件！这就是我们所说的*道具钻*。

在这篇文章中，我将分享我所学到的，以及我如何通过使用 useContext 钩子来解决我的问题。我喜欢使用它，希望你也会喜欢！

## 反应上下文

> 上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。
> 
> *   【React JS 官方网站(上下文)

我有 3 组数据要传递给输入组件，并将其全局存储，以便于访问。

```
const teamMembersNames = ['John', 'Mary', 'Jason', 'David']

const [sharing, setSharing] = React.useState([])
const [help, setHelp] = React.useState([])
const [pairing, setPairing] = React.useState(teamMembersNames) 
```

根据 React 官方上下文文档，我将需要使用`createContext`，并用`Context.Provider`嵌套我的主组件。

```
<StoreContext.Provider value={store}>
  <App />
</StoreContext.Provider> 
```

然后在组件上，我们用一个`Context.Consumer`标签再次嵌套组件。

```
<StoreContext.Consumer>
  {store => <InputComponent store={store} />}
</StoreContext.Consumer> 
```

## React 使用上下文挂钩

React `useContext` hooks 为我们提供了一种不用嵌套就能调用数据的优雅方式。我们来试试吧！

我们将把我们上下文提供移动到它自己的文件中。

```
// ./src/utils/store.js
import React from 'react'

export const StoreContext = React.createContext(null) 
```

在同一个上下文文件中，我们将定义一个默认函数，数据被初始化，它的子节点将提供数据。

```
// ./utils/store.js

import React from 'react'

export const StoreContext = React.createContext(null)

export default ({ children }) => {
  const teamMembersNames = ['John', 'Mary', 'Jason', 'David']

  const [sharing, setSharing] = React.useState([])
  const [help, setHelp] = React.useState([])
  const [pairing, setPairing] = React.useState(teamMembersNames)

  const store = {
    sharing: [sharing, setSharing],
    help: [help, setHelp],
    pairing: [pairing, setPairing],
  }

  return <StoreContext.Provider value={store}>{children}</StoreContext.Provider> } 
```

现在我们的上下文已经设置好了，我们可以将上下文包装到主应用程序中。在`index.js`文件中，我将用上下文包装应用程序。

```
// ./index.js

import React from 'react'
import ReactDOM from 'react-dom'

import App from './App'
import StoreProvider from './utils/store'

ReactDOM.render(
  <StoreProvider>
    <App />
  </StoreProvider>,
  document.getElementById('root')
) 
```

在任何组件中，为了获取数据，我们将使用 useContext。

```
import React from 'react'
import { StoreContext } from '../utils/store'

const SomeComponent = () => {
  // to fetch the sharing data
  const { sharing } = React.useContext(StoreContext)
} 
```

现在，我们应用程序中的组件将提供商店数据。但是为了获取数据，让我们使用`useContext`钩子代替*上下文。消费者*语法。

我已经创建了一个输入组件，它将获取用户输入并根据类型(共享、帮助或配对)设置状态

```
// ./components/input-section.js

import React from 'react'
import { StoreContext } from '../utils/store'

export default ({ type, description }) => {
  const [input, setInput] = React.useState('')
  const {
    [type]: [data, setData],
  } = React.useContext(StoreContext)

  /*
  .
  . some other handlers
  .
  */

  return (
    <div>
      <ul>
        {data.map(d => (
          <li>{d}</li>
        ))}
      </ul>
      <input
        placeholder={description}
        type="text"
        value={input}
        onChange={e => setData([e, ...data])}
      />
    </div>
  )
} 
```

我已经缩短了组件，这样我们可以看到数据是如何获取的。我们简单地调用`React.useContext(StoreContext)`，在`store.js`中传递给提供者的值就像它被传递时一样被获取。没有从 parents 组件向该组件传递任何属性！

为了进一步说明，在父组件中，我传递了存储数据的关键类型(共享、帮助、配对)。

```
// ./app.js
import React from 'react'
import InputSection from './components/input-section'

const App = () => {
  /*
  .
  . some stuffs
  .
  */

  return (
    <InputSection type="sharing" description="What are your thoughts?..." />
  )
} 
```

如您所见，我没有向子组件传递任何状态或数据属性！

希望这能帮助你更好地理解和展示使用`useContext` hook 是多么优雅！完整的应用程序，请查看我的[回购](https://github.com/nazmifeeroz/stand-up-bot)。

编码快乐！