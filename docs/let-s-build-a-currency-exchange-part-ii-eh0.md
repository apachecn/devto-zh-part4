# 让我们建立一个货币交易所第二部分

> 原文：<https://dev.to/marlonanthony/let-s-build-a-currency-exchange-part-ii-eh0>

在本教程的这一半，我们将重点放在前端。这个项目的代码在我的 [GitHub](https://github.com/marlonanthony/forex) 上。你可以在这里找到这篇文章的前半部分。我们将把我们前端的代码存储在一个名为`client`的文件夹中。在根级别创建`client`，cd 到这个文件夹，并在终端运行下面的命令:

```
npx create-react-app . 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`npx`，这样我们就不必全局安装 create-react-app。在您的终端中运行下面的命令，让我们得到我们的依赖项:

```
npm i @apollo/react-hooks apollo-cache-inmemory apollo-client apollo-link-http graphql-tag react-chartjs-2 chart.js react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

带着我们的附属品，让我们来一次大扫除吧。删除`logo.svg`、`serviceWorker.js`、`App.test.js`和`App.css`。现在从`index.js`和`App.js`移除他们的导入(和所有那些奇怪的分号)。然后，调整`index.js`，使其类似于下面的代码:

```
// index.js

import React from 'react'
import ReactDOM from 'react-dom'
import { BrowserRouter } from 'react-router-dom'
import { ApolloClient } from 'apollo-client'
import { ApolloProvider } from '@apollo/react-hooks'
import { InMemoryCache } from 'apollo-cache-inmemory' 
import { HttpLink } from 'apollo-link-http'

import App from './App'
import './index.css'

const cache = new InMemoryCache() 
const client = new ApolloClient({
  cache,
  link: new HttpLink({
    uri: 'http://localhost:4000/graphql',
    credentials: 'include' 
  })
})

ReactDOM.render(
  <ApolloProvider client={client}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </ApolloProvider>, document.getElementById('root')
) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们处理我们的进口。然后我们创建一个新的`InMemoryCache`实例，并将其添加到我们的`ApolloClient`配置对象中。我们使用`HttpLink`来访问我们的 GraphQL API，正如我们在 [GraphQL 操场](http://localhost:4000/graphql)中所做的那样，我们添加了`credentials: 'include'`来确保我们的 cookie 随每个请求一起发送。

在我们的渲染函数中，我们用 React 路由器的`BrowserRouter`来包装一切。`react-router`将`BrowserRouter`描述为，“一个使用 HTML5 历史 API 来保持你的 UI 与 URL 同步的路由器。”

我们传递`ApolloClient`的新实例`ApolloProvider`，以便稍后我们可以`consume`它(类似于 React `Context` API)。当我写这篇文章的时候`@apollo/react-hooks`正是一天大的好时机。这是一个缩小版的`react-apollo`，它不提供渲染道具功能，但是将包的大小减少了 50%！

打开`App.js`并添加以下代码:

```
// App.js

import React from 'react'
import { Route } from 'react-router-dom'

import Landing from './pages/Landing'

const App = () => <Route exact path='/' component={ Landing } />

export default App 
```

Enter fullscreen mode Exit fullscreen mode

React Router 的`Route`组件允许我们定义一个 routes `path`，并将该路径分配给一个要渲染的组件。在我们的例子中，这个组件是`Landing`。在`src`文件夹中创建一个`pages`文件夹。在`pages`中创建一个新文件，并将其命名为`Landing.js`。插入以下代码:

```
// Landing.js

import React from 'react'

const Landing = () => <div>Hello world!</div>

export default Landing 
```

Enter fullscreen mode Exit fullscreen mode

我们再一次展示了我们对传统的尊重，并召集了我们最庄严的“你好，世界”乐队！没有什么比一句精心打扮的“你好，世界”更让我着迷的了

在`src`文件夹中，创建另一个文件夹，并将其命名为`graphql`。在这个文件夹中创建两个子文件夹:`mutations`和`queries`。在`queries`中创建一个新文件，命名为`currencyPairInfo.js`。

添加以下代码:

```
// currencyPairInfo.js

import gql from 'graphql-tag'

export const CURRENCY_PAIR_INFO = gql`
  query CurrencyPairInfo($fc: String, $tc: String) {
    currencyPairInfo(tc: $tc, fc: $fc) {
      fromCurrency 
      fromCurrencyName
      toCurrency
      toCurrencyName
      exchangeRate
      lastRefreshed
      timeZone
      bidPrice
      askPrice
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们从`graphql-tag`导入`gql`，这样我们就可以定义我们的突变和查询。在这个文件中，我们做的事情与在 GraphQL Playground 中做的完全一样，只是我们在查询中添加了一个额外的名称(`CurrencyPairInfo`)并进一步描述了我们的模式的形状。最后，我们将这个查询存储在常量`CURRENCY_PAIR_INFO`中。

现在我们有了查询，让我们返回到 Landing.js 并使用它。

```
// Landing.js

import React, { useState } from 'react'
import { useQuery } from '@apollo/react-hooks'

import { CURRENCY_PAIR_INFO } from '../graphql/queries/currencyPairInfo'

const Landing = () => {
  const [ fc, setFc ] = useState('EUR'),
        [ tc, setTc ] = useState('USD'),
        { data, loading, error, refetch } = useQuery(CURRENCY_PAIR_INFO, {
          variables: { fc, tc }
        })

  if(loading) return <p>Loading...</p>
  if(error) return <button onClick={() => refetch()}>Retry</button>

  return data && (
    <section>
      <h3>Currency Exchange</h3>
      <div>
        <select
          value={`${fc}/${tc}`}
          onChange={e => {
            const [ fromCurrency, toCurrency ] = e.target.value.split('/')
            setFc(fromCurrency)
            setTc(toCurrency)
          }}>
          <option>EUR/USD</option>
          <option>JPY/USD</option>
          <option>GBP/USD</option>
          <option>AUD/USD</option>
          <option>USD/CHF</option>
          <option>NZD/USD</option>
          <option>USD/CAD</option>
        </select>
        <button onClick={() => refetch()}>refresh</button>
      </div>
      <div className='landing_pair_data'>
        { data.currencyPairInfo && Object.keys(data.currencyPairInfo).map(val => (
          <div key={val} className='data'>
            <p><span>{val}: </span>{ data.currencyPairInfo[val] }</p>
          </div>
        ))}
      </div>
    </section>
  )
}

export default Landing 
```

Enter fullscreen mode Exit fullscreen mode

我们从`@apollo/react-hooks`导入`useQuery`，我们在`currencyPairInfo.js`编写的查询和从`React`导入`useState`。我们将使用 React 钩子`useState`，而不是使用类组件通过`this.state`初始化状态，然后使用`setState`更新它。`useState`将初始状态作为参数，返回当前状态和更新所述状态的函数。此状态将用于收集用户输入。我们将这个输入作为查询提供给`variables`，然后`useQuery`返回响应。

世界上交易量最大的货币对被称为主要货币。它们构成了外汇市场的最大份额，约占 85%，因此它们表现出高市场流动性。主要有:欧元/美元，美元/日元，英镑/美元，澳元/美元，美元/瑞士法郎，新西兰元/美元，美元/加拿大元。这些是我们将提供给用户的货币对。

我们创建一个`select`列表，每个`option`为我们的查询提供变量。这些选项构成了专业。Apollo 提供了一个`refetch`函数，它将重新加载给定的查询。我们将这个功能放在一个按钮中，这样用户就可以获得最新的数据。注意不要过于频繁地 ping Alpha Vantage API。如果你发送太多请求，他们会很大方地给你几秒钟的超时时间。有足够的时间来思考你的傲慢。

我们的数据通过`data.currencyPairInfo`返回给我们。我们映射所述数据并将其提供给 DOM。你会注意到我们正在渲染`__typename: PairDisplay`。Apollo 客户端使用`__typename`和`id`字段来处理缓存更新。如果您查询一个不同的货币对，然后再次查询原始货币对，您会注意到先前的货币对数据可以通过`apollo-cache-inmemory`立即获得。

我不能这样盯着我们压在左边的数据。进入 index.css，在`body`中添加一个快速的`text-align: center`。

先不说这个，让我们来清理一下。在`src`中创建一个新文件夹，命名为`components`。在`components`里面创建一个`pairs`文件夹。在`pairs`中创建一个新文件`SelectList.js`并插入以下内容:

```
// SelectList.js

import React from 'react'

const SelectList = ({ fc, setFc, tc, setTc }) => (
  <select
    value={`${fc}/${tc}`}
    onChange={e => {
      const [ fromCurrency, toCurrency ] = e.target.value.split('/')
      setFc(fromCurrency)
      setTc(toCurrency)
    }}>
    <option>EUR/USD</option>
    <option>JPY/USD</option>
    <option>GBP/USD</option>
    <option>AUD/USD</option>
    <option>USD/CHF</option>
    <option>NZD/USD</option>
    <option>USD/CAD</option>
  </select>
)

export default SelectList 
```

Enter fullscreen mode Exit fullscreen mode

回到 Landing.js 用`SelectList`替换`select`，并传递必要的道具。

```
 import React, { useState } from 'react'
import { useQuery } from '@apollo/react-hooks' 
import { CURRENCY_PAIR_INFO } from '../graphql/queries/currencyPairInfo'
+import SelectList from '../components/SelectList' 
const Landing = () => {
  const [ fc, setFc ] = useState('EUR'),
        [ tc, setTc ] = useState('USD'),
        { data, loading, error, refetch } = useQuery(CURRENCY_PAIR_INFO, {
          variables: { fc, tc }
        })

  if(loading) return <p>Loading...</p>
  if(error) return <button onClick={() => refetch()}>Retry</button>

  return data && (
    <section>
      <h3>Currency Exchange</h3>
      <div>
+       <SelectList fc={fc} tc={tc} setFc={setFc} setTc={setTc} />
        <button onClick={() => refetch()}>refresh</button>
      </div>
      <div className='landing_pair_data'>
        { data.currencyPairInfo && Object.keys(data.currencyPairInfo).map(val => (
          <div key={val} className='data'>
            <p><span>{val}: </span>{ data.currencyPairInfo[val] }</p>
          </div>
        ))}
      </div>
    </section>
  )
}

export default Landing 
```

Enter fullscreen mode Exit fullscreen mode

好多了！现在我们从 Aplha Vantage API 接收数据，让我们开始导航。打开`App.js`并进行以下调整:

```
// App.js

import React from 'react'
import { Route, Switch } from 'react-router-dom'

import Landing from './pages/Landing'
import Navbar from './components/navbar/Navbar'

const App = () => (
  <main>
    <div className='navbar'><Navbar /></div>
    <Switch>
      <Route exact path='/' component={ Landing } />
    </Switch>
  </main>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

我们从`react-router-dom`导入`Switch`和一个我们将要创建的名为`Navbar`的文件。`Switch`组件呈现匹配路线`path`的第一个孩子(`Route`或`Redirect`)并显示它。

在`components`中创建一个新文件夹，命名为`navbar`。在里面创建一个名为`Navbar.js`的新文件，并插入以下内容:

```
// Navbar.js

import React from 'react'
import { NavLink } from 'react-router-dom'

import './Navbar.css'

const Navbar = () => (
  <div className='navigation'>
    <header><NavLink exact to='/'>Forex</NavLink></header>
    <ul>
      <li><NavLink exact to="/login">Login</NavLink></li>
      <li><NavLink exact to='/register'>Sign Up</NavLink></li>
      <li>Logout</li>
    </ul>
  </div>
)

export default Navbar 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章不是关于造型的。我想小心不要用样式化的组件污染代码库，这样既费时又让一些人难以理解逻辑。出于这个原因，我决定只使用两个 CSS 文件:`index.css`和`Navbar.css`。我们将使用很少的 CSS——仅够黑暗模式使用。😎

在`navbar`文件夹中创建`Navbar.css`并插入下面的代码:

```
/* Navbar.css */

.navbar { margin-bottom: 55px; }

.navigation {
  position: fixed;
  left: 0;
  top: 0;
  background: var(--secondary-color);
  width: 100vw;
  height: 55px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.navigation header a {
  text-decoration: none;
  color: var(--header-text-color);
  margin-left: 10px;
}
.navigation ul {
  display: flex;
  list-style: none;
  margin-right: 15px;
}

.navigation li {
  margin: 0 15px;
  color: var(--header-text-color);
}
.navigation li:hover {
  cursor: pointer;
  color: var(--main-color);
}

.navigation a {
  text-decoration: none;
  color: var(--header-text-color);
}
.navigation a:hover,
.navigation a:active,
.navigation a.active {
  color: var(--main-color);
} 
```

Enter fullscreen mode Exit fullscreen mode

将`index.css`调整到如下:

```
/* index.css */

/* Global */

* {
  --main-color: rgb(0,0,0);
  --secondary-color: rgb(55,131,194);
  --text-color: rgba(200,200,200, 0.6);
  --header-text-color: rgb(200,200,200);
}

body {
  font-family: Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0;
  background: var(--main-color);
  text-align: center;
  box-sizing: border-box;
}

a { text-decoration: none;  color: rgb(0,0,0); }
section { padding-top: 50px; }
span { color: var(--secondary-color); }
p { color: var(--text-color); font-size: 14px; }
p:hover { color: rgba(200,200,200, 0.4); }
h1,h2, h3, h4 { color: var(--header-text-color); }
button, select { cursor: pointer; }

/* Landing && Pair */

.landing_pair_data {
  margin: 20px 0 20px calc(50% - 170px);
  padding: 20px;
  width: 300px;
  border-radius: 20px;
  box-shadow: 1px 1px 1px 1px var(--secondary-color), 
    -1px -1px 1px 1px var(--secondary-color);
}

.data {
  border-bottom: 1px solid var(--secondary-color);
  width: 280px;
  margin-left: calc(50% - 140px);
  text-align: start;
  text-transform: capitalize;
  padding: 2px 2px 2px 0;
}

.modal {
  position: absolute;
  background: rgb(225,225,225);
  color: var(--main-color);
  width: 280px;
  left: calc(50% - 160px);
  top: 25%;
  padding: 20px;
  animation: modal .5s;
}
.modal p {
  color: var(--main-color);
}

@keyframes modal {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Account  */

.pair_divs {
  padding: 20; 
  border: 1px solid rgba(255,255,255,0.1); 
  border-radius: 5px;
  width: 400px; 
  margin: 10px auto;
}
.pair_divs p {
  text-align: start;
  padding-left: 20px;
}
.pair_divs:hover {
  border: 1px solid rgba(55,131,194, 0.3);
}

/* Chart  */

.chartData {  
  padding-top: 50px;  
  height: calc(100vh - 105px); 
}
.chartData form input,
.chartData form button {
  margin: 10px;
}

/* Login && Register */

.login input,
.register input {
  padding: 5px; 
  margin: 10px 0px; 
  width: 60%;
  max-width: 400px;
  background: var(--main-color);
  color: var(--header-text-color);
  font-size: 13px;
}

.login form,
.register form {
  display: flex; 
  justify-content: center; 
  flex-direction: column; 
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个文件代表了我们整个 CSS。保存您的文件，并查看浏览器。

[![navbar](img/1e9b20e71d107b8471a591e52a516838.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PLOEWWFQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/navbar.png%3Fraw%3Dtrue)

现在我们有了导航条，让我们创建一个注册路由。在`graphql/mutations`中创建一个名为`register.js`的新文件，并插入以下代码:

```
// graphql/mutations/register.js

import gql from 'graphql-tag'

export const REGISTERMUTATION = gql`
  mutation RegisterMutation($email: String!, $password: String!, $name: String!) {
    register(email: $email, password: $password, name: $name)  
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`components`中，创建一个新文件夹，命名为`auth`。在`auth`内部创建一个新文件，命名为`Register.js`。插入以下内容:

```
// components/auth/Register.js

import React, { useState } from 'react'
import { useMutation } from '@apollo/react-hooks'

import { REGISTERMUTATION } from '../../graphql/mutations/register'

export default function Register(props) {
  const [ email, setEmail ] = useState(''),
        [ password, setPassword ] = useState(''),
        [ name, setName ] = useState(''),
        [ register, { error } ] = useMutation(REGISTERMUTATION, {
          variables: { email, password, name }
        })

  return (
    <div className='register'>
      <form onSubmit={ async e => {
        e.preventDefault()
        await register()
        props.history.push('/login')
      }}>
        <h2>Sign Up</h2>
        <input
          required
          name='email'
          type='email'
          value={ email }
          onChange={ e => setEmail(e.target.value) }
          placeholder='Enter your email'
        />
        <input
          required
          type='password'
          value={ password }
          onChange={ e => setPassword(e.target.value) }
          placeholder='Enter your password'
        />
        <input
          required
          type='text'
          value={ name }
          onChange={ e => setName(e.target.value) }
          placeholder='Enter your name'
        />
        { error && <p>{ error.message }</p> }
        <button>SignUp</button>
      </form>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一个`form`来收集用户数据，并将其放入组件状态。`onSubmit`，我们将状态作为`variables`传递给`register`突变。由于我们将电子邮件输入`type`设置为“电子邮件”并传递了`required`属性，我们不会看到我们在后端抛出的错误。我们不比较密码，所以不会出现错误。我们创建的唯一错误是“用户已经存在”这就是为什么我不检查单个错误，而只是在所有输入下显示错误。

打开`App.js`。导入`Register.js`并创建注册组件`Route`。

```
// App.js

import React from 'react'
import { Route, Switch } from 'react-router-dom'

import Landing from './pages/Landing'
import Navbar from './components/navbar/Navbar'
import Register from './components/auth/Register'

const App = () => (
  <main>
    <div className='navbar'><Navbar /></div>
    <Switch>
      <Route exact path='/' component={ Landing } />
      <Route path='/register' component={ Register } />
    </Switch>
  </main>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

如果您导航到我们的注册组件，您将能够注册一个新用户。我们可以通过检查我们的[数据库](https://www.mongodb.com/cloud/atlas)来确认这一点。

在`graphql/mutations`中创建一个新文件，命名为`login.js`并插入以下内容:

```
// graphql/mutations/login.js

import gql from 'graphql-tag'

export const LOGINMUTATION = gql`
  mutation LoginMutation($email: String!, $password: String!) {
    login(email: $email, password: $password) {
      id
      email
      name
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`graphql/queries`中创建一个名为`me.js`的新文件，并添加以下代码:

```
// graphql/queries/me.js

import gql from 'graphql-tag'

export const MEQUERY = gql`
  query MeQuery {
    me {
      id
      email 
      name
      bankroll
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

转到`auth`文件夹，创建一个新文件，命名为`Login.js`。调整 Login.js，使其类似于下面的代码:

```
// Login.js

import React, { useState } from 'react'
import { useMutation } from '@apollo/react-hooks'

import { MEQUERY } from '../../graphql/queries/me'
import { LOGINMUTATION } from '../../graphql/mutations/login'

export default function Login(props) {
  const [ email, setEmail ] = useState(''),
        [ password, setPassword ] = useState(''),
        [ login, { error } ] = useMutation(LOGINMUTATION, {
          variables: { email, password },
          update: (cache, { data }) => {
            if(!data || !data.login) return 
            cache.reset()
            cache.writeQuery({
              query: MEQUERY,
              data: { me: data.login }
            })
          }
        })

  return (
    <div className='login'>
      <form onSubmit={ async e => {
        e.preventDefault()
        await login()
        props.history.push('/') 
      }}>
        <h2>Login</h2>
        <input
          required
          name='email'
          type='email'
          value={ email }
          onChange={ e => setEmail(e.target.value) }
          placeholder='Enter your email'
        />
        <input
          required
          type='password'
          value={ password }
          onChange={ e => setPassword(e.target.value) }
          placeholder='Enter your password'
        />
        { error && <p>{ error.message }</p> }
        <button type='submit'>Login</button>
      </form>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们让用户登录，并将他们重定向回主页。ApolloClient 为我们提供了一个`update`函数，一旦用户登录，我们可以用它来更新缓存。一旦用户成功登录，我们就`update`缓存，以便`me`查询代表当前用户。

从文档中:

> 调用 update 函数时，将 Apollo 缓存作为第一个参数。缓存有几个实用函数，比如 cache.readQuery 和 cache.writeQuery，它们允许您使用 GraphQL 对缓存进行读写查询，就像它是一个服务器一样。还有其他缓存方法，如 cache.readFragment、cache.writeFragment 和 cache.writeData，如果您有兴趣，可以在我们详细的缓存指南中了解这些方法。
> 
> 注意:update 函数接收缓存而不是客户机作为它的第一个参数。该缓存通常是 InMemoryCache 的一个实例，在创建客户端时提供给 ApolloClient 构造函数。对于 update 函数，当您调用 cache.writeQuery 时，update 会在内部调用 broadcastQueries，因此侦听更改的查询将会更新。但是，这种广播行为在 cache.writeQuery 之后会发生变化，只在 update 函数中发生。在其他任何地方，cache.writeQuery 只会写入缓存，而不会立即将更改传播到视图层。为了避免这种混淆，在写入缓存时最好使用 client.writeQuery。
> 
> update 函数的第二个参数是一个对象，它的数据属性包含您的突变结果。如果您指定一个乐观响应，您的更新函数将被调用两次:一次使用您的乐观结果，另一次使用您的实际结果。您可以使用您的变异结果通过 cache.writeQuery 更新缓存。

如果用户输入了无效的电子邮件，他们会看到 HTML 错误，而不是我们的。如果用户输入了有效但不正确的电子邮件，我们会抛出“电子邮件或密码不正确！”如果用户输入了不正确的密码，我们会抛出完全相同的错误，让坏人更难破译哪个输入是不正确的。在这种情况下，我们可能不想在错误发生的地方显示错误，以免泄露秘密。

打开 App.js 并进行以下调整:

```
// App.js

import React from 'react'
import { Route, Switch } from 'react-router-dom'

import Landing from './pages/Landing'
import Navbar from './components/navbar/Navbar'
import Register from './components/auth/Register'
import Login from './components/auth/Login'

const App = () => (
  <Switch>
    <Route path='/login' component={ Login } />
    <Route path='/' render={() => (
      <main>
        <div className='navbar'><Navbar /></div>
        <Route exact path='/' component={ Landing } />
        <Route path='/register' component={ Register } />
      </main>
    )} />
  </Switch>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在用户登录之前清空了缓存，并且 navbar 利用`me`查询进行授权，所以我们将把登录组件放在 navbar 之外。

我们现在可以登录一个用户，并将用户的会话 id 保存在一个 cookie 中。如果你打开你的 DevTools，在 Application 文件夹下，在 Cookies 标签里面，你会看到我们的 cookie。

如果我们不在导航条上同时显示登录和注销，这可能是最好的。像这样调整 Navbar.js】

```
// Navbar.js

import React from 'react'
import { NavLink, Redirect } from 'react-router-dom'
import { useQuery } from '@apollo/react-hooks'

import { MEQUERY } from '../../graphql/queries/me'
import './Navbar.css'

const Navbar = () => {
  const { data, loading, error } = useQuery(MEQUERY)

  if(loading) return <p>Loading....</p>
  if(error) return <Redirect to='/login' />
  if(!data) return <p>This is unfortunate</p>

  return (
    <div className='navigation'>
      <header><NavLink exact to='/'>Forex</NavLink></header>
      { !data.me ? (
        <ul>
          <li><NavLink exact to='/login'>Login</NavLink></li>
          <li><NavLink exact to='/register'>SignUp</NavLink></li>
        </ul> ) 
      : (
        <ul>
          <li>Logout</li>
        </ul>
      )}
    </div>
  )
}

export default Navbar 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`me`查询出错的情况下，我们用`Redirect`用户登录。如果用户返回，我们显示注销，`else`登录/注册。目前注销不是很有用。我们从变异开始。在`graphql/mutations`中创建一个名为`logout.js`的新文件，并插入下面的代码。

```
// graphql/mutations/logout.js

import gql from 'graphql-tag'

export const LOGOUT_MUTATION = gql`
  mutation Logout {
    logout
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`components/auth`中创建`Logout.js`，并插入以下内容:

```
// components/auth/Logout.js

import React from 'react' 
import { useMutation } from '@apollo/react-hooks'
import { withRouter } from 'react-router-dom'

import { MEQUERY } from '../../graphql/queries/me'
import { LOGOUT_MUTATION } from '../../graphql/mutations/logout'

const Logout = props => {
  const [logout] = useMutation(LOGOUT_MUTATION, {
    update: cache => {
      cache.writeQuery({
        query: MEQUERY,
        data: { me: null }
      })
      props.history.push('/')
    }
  })

  return <div onClick={() => logout()}>Logout</div>
}

export default withRouter(Logout) 
```

Enter fullscreen mode Exit fullscreen mode

当用户点击`Logout`时，会发生三件事:

*   `logout`突变破坏了`req`对象上的会话。

*   我们更新缓存，使`me`查询返回`null`。

*   我们将用户重定向到主页。

如果一个组件没有被 React Router 渲染(作为`component`道具传递给`Route`)，那么我们就不能访问`history.push`。React 路由器的 HOC `withRouter`让我们可以通过 props 访问历史对象。我们利用`props.history.push('/')`将用户导航回主页。在导出文件时，不要忘记用`withRouter`将注销组件包装起来。

将`Logout.js`导入`Navbar.js`并用我们的新组件替换`<li><Logout></li>`。调整后，您将注销！

我们现在可以专注于允许用户打开多头/空头头寸。打开`Landing.js`并进行以下调整:

```
// Landing.js

import React, { useState } from 'react'
import { useQuery } from '@apollo/react-hooks'

import { MEQUERY } from '../graphql/queries/me'
import { CURRENCY_PAIR_INFO } from '../graphql/queries/currencyPairInfo'
import SelectList from '../components/pairs/SelectList'
import OpenLongPosition from '../components/positions/OpenLongPosition'

const Landing = () => {
  const [ fc, setFc ] = useState('EUR'),
        [ tc, setTc ] = useState('USD'),
        [ askPrice, setAskPrice ] = useState(0),
        [ bidPrice, setBidPrice ] = useState(0),
        [ showModal, setShowModal ] = useState(false),
        user = useQuery(MEQUERY),
        { data, loading, error, refetch } = useQuery(CURRENCY_PAIR_INFO, {
          variables: { fc, tc }
        })

  if(loading) return <p>Loading...</p>
  if(error) return <button onClick={() => refetch()}>Retry</button>

  return data && (
    <section>
      <h2>Currency Exchange</h2>
      { user.data.me && <p>Available Balance { user.data.me.bankroll.toLocaleString()}.00</p> }
      <div>
        <SelectList fc={fc} tc={tc} setFc={setFc} setTc={setTc} />
        <button onClick={() => refetch()}>Refresh</button>
        { user.data.me && (
          <OpenLongPosition
            fc={fc}
            tc={tc}
            pairData={data}
            askPrice={askPrice}
            setAskPrice={setAskPrice}
            showModal={showModal}
            setShowModal={setShowModal}
        />)}
        <button>Sell</button>
      </div>
      <div className='landing_pair_data'>
        { data.currencyPairInfo && Object.keys(data.currencyPairInfo).map(val => (
          <div key={val} className='data'>
            <p><span>{val}: </span>{ data.currencyPairInfo[val] }</p>
          </div>
        ))}
      </div>
    </section>
  )
}

export default Landing 
```

Enter fullscreen mode Exit fullscreen mode

我们导入`MEQUERY`和一个我们需要创建的名为`OpenLongPosition`的文件。我们集成了`useState`来存储/更新`askPrice`、`bidPrice`，并切换一个模态。有了用户后，我们显示他们的资金(可用资金)。如果用户改变货币对或刷新数据，我们相应地改变`askPrice`和`bidPrice`的状态。最后，如果找到一个用户，我们显示一个“购买”按钮(`OpenLongPosition`)。

在`graphql/mutations`中创建一个新文件，命名为`openPosition.js`，并添加以下代码:

```
// openPosition.js

import gql from 'graphql-tag'

export const OPENPOSITION = gql`
  mutation OpenPosition(
    $pair: String!, 
    $lotSize: Int!, 
    $openedAt: Float!, 
    $position: String!
  ) {
    openPosition(
      pair: $pair, 
      lotSize: $lotSize, 
      openedAt: $openedAt, 
      position: $position
    ) {
      success
      message
      pair {
        id
        user
        position
        pair
        lotSize
        openedAt
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`graphql/queries`中创建一个名为`getPairs.js`的新文件，并插入以下代码:

```
// graphql/queries/getPairs.js

import gql from 'graphql-tag'

export const GETPAIRS = gql`
  query GetPairs {
    getPairs {
      id
      user
      pair
      lotSize
      openedAt
      closedAt
      pipDif
      profitLoss
      open
      position
      createdAt
      updatedAt
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`components`中创建一个新文件夹，命名为`positions`。在里面创建一个名为`OpenLongPosition.js`的新文件，并添加以下代码:

```
// OpenLongPosition.js

import React from 'react'
import { Link } from 'react-router-dom'
import { useMutation } from '@apollo/react-hooks'

import { OPENPOSITION } from '../../graphql/mutations/openPosition'
import { MEQUERY } from '../../graphql/queries/me'
import { GETPAIRS } from '../../graphql/queries/getPairs'

const OpenLongPosition = ({
  fc, 
  tc, 
  pairData,
  askPrice,
  setAskPrice,
  showModal,
  setShowModal
}) => {

  const [ openPosition, { data, loading, error }] = useMutation(OPENPOSITION, {
    variables: {
      pair: `${fc}/${tc}`,
      lotSize: 100000,
      openedAt: askPrice,
      position: 'long'
    },
    update: cache => {
      const user = cache.readQuery({ query: MEQUERY })
      user.me.bankroll -= 100000
      cache.writeQuery({
        query: MEQUERY,
        data: { me: user.me }
      })
    },
    refetchQueries: [{ query: GETPAIRS }]
  })

  if(loading) return <p>Loading...</p>
  if(error) return <p>{ error.message }</p>

  return openPosition && (
    <>
      <button onClick={ async () => {
        await setAskPrice(+pairData.currencyPairInfo.askPrice)
        alert('Are you sure you want to buy?')
        await openPosition()
        setShowModal(true)
      }}>
        Buy
      </button>
      { data && data.openPosition.message && showModal && (
        <div className='modal'>
          <button onClick={() => setShowModal(false)}>x</button>
          <p>{ data.openPosition.message }</p>
          <p>Currency Pair: { data.openPosition.pair.pair }</p>
          <p>Lot Size: { data.openPosition.pair.lotSize.toLocaleString() }.00</p>
          <p>Opened At: { data.openPosition.pair.openedAt }</p>
          <p>Position: { data.openPosition.pair.position }</p>
          <Link to={{ pathname: '/account', state: { data } }}>
            <button>Details</button>
          </Link>
        </div>
      )}
    </>
  )
}

export default OpenLongPosition 
```

Enter fullscreen mode Exit fullscreen mode

我们将突变传递给所需的变量。一旦用户点击“购买”按钮，我们通常希望显示一些数据，并允许他们确认购买。这里我们只是使用了一个`alert`。然后向用户显示一个描述他们交易的模态和一个`details`按钮，将他们重定向到我们仍然需要创建的页面— `Account`。打开 MongoDB Atlas，您将看到新创建的职位。

Apollo 为我们提供了许多在突变后更新缓存的方法。我们已经在这个项目中实现了其中的一些。在这个组件中，我们利用`refetchQueries`来更新我们的配对。让我们看看文件:

> refetchQueries 是更新缓存最简单的方法。使用 refetchQueries，您可以指定一个或多个要在变异完成后运行的查询，以便重新提取可能受变异影响的存储部分。

我们已经看到了变异钩子接受的一些选项。查看[文档](https://www.apollographql.com/docs/react/essentials/mutations/)中的完整列表。

在我们开始创建`Account`组件之前，让我们允许用户打开一个`short`位置。打开`components/positions`，新建一个名为`OpenShortPosition.js`的文件，添加以下代码:

```
// components/positions/OpenShortPosition.js

import React from 'react'
import { Link } from 'react-router-dom'
import { useMutation } from '@apollo/react-hooks'

import { OPENPOSITION } from '../../graphql/mutations/openPosition'
import { MEQUERY } from '../../graphql/queries/me'
import { GETPAIRS } from '../../graphql/queries/getPairs'

const OpenShortPosition = ({
  fc,
  tc,
  pairData,
  bidPrice,
  setBidPrice,
  showModal,
  setShowModal
}) => {
  const [ openPosition, { data, loading, error }] = useMutation(OPENPOSITION, {
    variables: {
      pair: `${fc}/${tc}`, 
      lotSize: 100000, 
      openedAt: bidPrice, 
      position: 'short' 
    },
    update: cache => {
      const user = cache.readQuery({ query: MEQUERY })
      user.me.bankroll -= 100000
      cache.writeQuery({
        query: MEQUERY,
        data: { me: user.me }
      })
    },
    refetchQueries: [{ query: GETPAIRS }]
  })

  if(loading) return <p>Loading...</p>
  if(error) return <p>{ error.message }</p>

  return openPosition && (
    <>
      <button onClick={ async () => {
        await setBidPrice(+pairData.currencyPairInfo.bidPrice)
        alert('Are you sure you want to sell short?')
        await openPosition()
        setShowModal(true) 
      }}>
        Sell
      </button> 
      { data && data.openPosition.message && showModal && ( 
        <div className='modal'>
          <button onClick={() => setShowModal(false)}>x</button>
          <p>{ data && data.openPosition.message }</p>
          <p>Currency Pair: { data.openPosition.pair.pair }</p>
          <p>Lot Size: { data.openPosition.pair.lotSize.toLocaleString() }.00</p>
          <p>Opened At: { data.openPosition.pair.openedAt }</p>
          <p>Position: { data.openPosition.pair.position }</p>
          <Link to={{ pathname: '/account', state: { data } }}>
            <button>Details</button>
          </Link>
        </div>
      )}
    </>
  )
}

export default OpenShortPosition 
```

Enter fullscreen mode Exit fullscreen mode

这里我们做了与在`OpenLongPosition`中完全相同的事情，除了我们传递`bidPrice`而不是`askPrice`和`position: short`而不是`position: long`作为参数。

回到 Landing.js，用我们新创建的`OpenShortPosition`组件替换“Sell”按钮。

```
// Landing.js

import OpenShortPosition from '../components/positions/OpenShortPosition'

{ user.data.me && (
  <OpenShortPosition
    fc={fc}
    tc={tc}
    pairData={data}
    bidPrice={bidPrice}
    setBidPrice={setBidPrice}
    showModal={showModal}
    setShowModal={setShowModal}
/>)} 
```

Enter fullscreen mode Exit fullscreen mode

有了它，我们的用户就可以卖空了。我们仍然需要创建我们的`Account`组件。我们开始吧！在`pages`文件夹中创建`Account.js`并添加下面的代码:

```
// Account.js

import React, { useState } from 'react'
import { useQuery } from '@apollo/react-hooks'
import { Link, Redirect } from 'react-router-dom'

import { GETPAIRS } from '../graphql/queries/getPairs'
import { MEQUERY } from '../graphql/queries/me'

const Account = props => {
  const [ open, setOpen ] = useState(true),
        user = useQuery(MEQUERY),
        { data, loading, error } = useQuery(GETPAIRS)

  if(user.error) return <Redirect to='/login' />
  if(!user.data || !user.data.me) return <p>A man has no name.</p>
  if(loading) return <p>Loading...</p>
  if(!data) return <p>Nothing to show!</p>
  if(error) return <p>{ error.message }</p>

  return (
    <section>
      <h2>{ user.me.name }</h2>
      <div>
        <p><span>Available Balance: </span>{ user.me.bankroll.toLocaleString() }.00</p> 
      </div>
      <br />
      { props.location.state &&  (
        <div>
          <h3>New Position</h3>
          <div className='pair_divs'>
            <p><span>Pair: </span>{ props.location.state.data.openPosition.pair.pair }</p>
            <p><span>Lot Size: </span>{ props.location.state.data.openPosition.pair.lotSize.toLocaleString() }.00</p>
            <p><span>Pip Dif: </span>{ props.location.state.data.openPosition.pair.openedAt }</p>
            <p><span>Position: </span>{ props.location.state.data.openPosition.pair.position }</p>
          </div>
        </div>
      )}
      <br />
      <h3>Currency Pairs</h3>
      <button onClick={() => setOpen(true)}>open</button>
      <button onClick={() => setOpen(false)}>closed</button>
      <div>
      { data.getPairs && data.getPairs.map(pair => pair.open && open && (
        <div className='pair_divs' key={pair.id}>
          <Link to={{ pathname: '/pair', state: { pair, me: user.me } }}>
            { pair.pair && <p><span>Currency Pair: </span>{ pair.pair }</p> }
            { pair.lotSize && <p><span>Lot Size: </span>{ pair.lotSize.toLocaleString() }.00</p> }
            { pair.position && <p><span>Position: </span>{ pair.position }</p> }
            { pair.openedAt && <p><span>Opened At: </span>{ pair.openedAt.toFixed(4) }</p> }
            { pair.createdAt && <p><span>Created At: </span>{ new Date(+pair.createdAt).toLocaleString() }</p> }
            { pair.updatedAt && <p><span>Updated At: </span>{ new Date(+pair.updatedAt).toLocaleString() }</p> }
          </Link>
        </div>
      ))}
      { data.getPairs && data.getPairs.map(pair => !pair.open && !open && (
        <div className='pair_divs' key={ pair.id }>
          <div>
            { pair.pair && <p><span>Currency Pair: </span>{ pair.pair }</p> }
            { pair.lotSize && <p><span>Lot Size: </span>{ pair.lotSize.toLocaleString() }.00</p> }
            { pair.position && <p><span>Position: </span>{ pair.position }</p> }
            { pair.openedAt && <p><span>Opened At: </span>{ pair.openedAt.toFixed(4) }</p> }
            { pair.closedAt && <p><span>Closed At: </span>{ pair.closedAt.toFixed(4) }</p> }
            { <p><span>Pip Dif: </span>{ pair.pipDif || 0 }</p> }
            { <p><span>Profit/Loss: </span>{ pair.profitLoss.toFixed(2) || 0 }</p> }
            { pair.createdAt && <p><span>Created At: </span>{ new Date(+pair.createdAt).toLocaleString() }</p> }
            { pair.updatedAt && <p><span>Updated At: </span>{ new Date(+pair.updatedAt).toLocaleString() }</p> }
          </div>
        </div>
      ))}
      </div>
    </section>
  )
}

export default Account 
```

Enter fullscreen mode Exit fullscreen mode

React Router 的`Link`组件允许我们在将用户导航到另一个视图时传递状态。如果我们想在某些路线上渲染独特的景色，这是很方便的。我们用它来显示用户刚刚打开的新头寸——如果有的话。您可以在这里发挥创意，但我们会保持简单，只显示一些关于新职位的数据。

在新位置(如果有)下，我们显示所有用户的位置。默认情况下显示开放位置，但我们提供了一个按钮在开放和关闭之间切换。如果头寸未结，用户可以点击货币对。这将把他们导航到`/pair`(我们需要创建它)并提供进一步的选项。这个组件有点冗长。我们一会儿会重构。

让我们将`Account.js`导入 App.js 并创建它的`Route`。

```
// App.js

import React from 'react'
import { Route, Switch } from 'react-router-dom'

import Landing from './pages/Landing'
import Navbar from './components/navbar/Navbar'
import Register from './components/auth/Register'
import Login from './components/auth/Login'
import Account from './pages/Account'

const App = () => (
  <Switch>
    <Route path='/login' component={ Login } />
    <Route path='/' render={() => (
      <main>
        <div className='navbar'><Navbar /></div>
        <Route exact path='/' component={ Landing } />
        <Route path='/register' component={ Register } />
        <Route path='/account' component={ Account } />
      </main>
    )} />
  </Switch>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

我们还希望当用户登录时可以从导航栏访问`Account`。

```
// Navbar.js

return (
  <ul>
    <li><NavLink to='/account'>Account</NavLink></li>
    <li><Logout /></li>
  </ul>
) 
```

Enter fullscreen mode Exit fullscreen mode

当从导航栏导航到`/account`时，您会注意到没有显示“新位置”。酷！现在让我们重构`Account.js`并添加一些功能。在`components/pairs`中创建一个名为`NewPosition.js`的新文件。从`Account.js`中剪切以下代码，并将其插入到您新创建的文件中。

```
// components/pairs/NewPosition.js

import React from 'react'

export default function NewPosition({ state }) {
  return (
    <div>
      <h3>New Position</h3>
      <div className='pair_divs' style={{ textAlign: 'center' }}>
        <p><span>Pair: </span>{ state.data.openPosition.pair.pair }</p>
        <p><span>Lot Size: </span>{ state.data.openPosition.pair.lotSize.toLocaleString() }.00</p>
        <p><span>Pip Dif: </span>{ state.data.openPosition.pair.openedAt }</p>
        <p><span>Position: </span>{ state.data.openPosition.pair.position }</p>
      </div>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在同一个文件夹中创建一个新文件，命名为`Pairs.js`。从`Account.js`中剪切以下代码，并将其添加到该文件中。

```
// components/pairs/Pairs.js

import React from 'react'
import { Link } from 'react-router-dom'

const Pairs = ({ data, open, user }) => (
  <div>
    { data.getPairs && data.getPairs.map(pair => pair.open && open && (
      <div className='pair_divs' key={ pair.id }>
        <Link to={{ pathname: '/pair', state: { pair, me: user.data.me } }}>
          { pair.pair && <p><span>Currency Pair: </span>{ pair.pair }</p> }
          { pair.lotSize && <p><span>Lot Size: </span>{ pair.lotSize.toLocaleString() }.00</p> }
          { pair.position && <p><span>Position: </span>{ pair.position }</p> }
          { pair.openedAt && <p><span>Opened At: </span>{ pair.openedAt.toFixed(4) }</p> }
          { pair.createdAt && <p><span>Created At: </span>{ new Date(+pair.createdAt).toLocaleString() }</p> }
          { pair.updatedAt && <p><span>Updated At: </span>{ new Date(+pair.updatedAt).toLocaleString() }</p> }
        </Link>
      </div>
    ))}
    { data.getPairs && data.getPairs.map(pair => !pair.open && !open && (
      <div className='pair_divs' key={ pair.id }>
        <div>
          { pair.pair && <p><span>Currency Pair: </span>{ pair.pair }</p> }
          { pair.lotSize && <p><span>Lot Size: </span>{ pair.lotSize.toLocaleString() }.00</p> }
          { pair.position && <p><span>Position: </span>{ pair.position }</p> }
          { pair.openedAt && <p><span>Opened At: </span>{ pair.openedAt.toFixed(4) }</p> }
          { pair.closedAt && <p><span>Closed At: </span>{ pair.closedAt.toFixed(4) }</p> }
          { <p><span>Pip Dif: </span>{ pair.pipDif || 0 }</p> }
          { <p><span>Profit/Loss: </span>{ pair.profitLoss.toFixed(2) || 0 }</p> }
          { pair.createdAt && <p><span>Created At: </span>{ new Date(+pair.createdAt).toLocaleString() }</p> }
          { pair.updatedAt && <p><span>Updated At: </span>{ new Date(+pair.updatedAt).toLocaleString() }</p> }
        </div>
      </div>
    ))}
  </div>
)

export default Pairs 
```

Enter fullscreen mode Exit fullscreen mode

好吧。我们应该在处理`Account.js`时实现一个`addFunds`按钮。在`graphql/mutations`中创建一个名为`addFunds.js`的新文件，并插入以下内容:

```
// graphql/mutations/addFunds.js

import gql from 'graphql-tag'

export const ADDFUNDS = gql`
  mutation ($amount: Int!) {
    addFunds(amount: $amount) {
      success
      message
      bankroll
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`components/pairs`文件夹中创建一个名为`AddFunds.js`的新文件，并添加以下代码:

```
// components/pairs/AddFunds.js

import React, { useState } from 'react'
import { useMutation } from '@apollo/react-hooks'

import { ADDFUNDS } from '../../graphql/mutations/addFunds'

export default function AddFunds() {
  const [ showModal, setShowModal ] = useState(false),
        [ addFunds, { data, loading, error } ] = useMutation(ADDFUNDS, {
          variables: { amount: 1000000 }
        })

  if(loading) return <p>Loading...</p>
  if(error) return <p>{ error.message }</p>

  return addFunds && (
    <>
      <button onClick={ async () => {
        alert('Are you sure?')
        await addFunds()
        setShowModal(true)
      }}>Add Funds</button>
      { data && data.addFunds.message && showModal && (
        <div className='modal'>
          <button onClick={() => setShowModal(false)}>x</button>
          <p>{ data.addFunds.message }</p>
        </div>
      )}
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

通常情况下，用户对自己的存款金额有一定的发言权。也就是说，当我们在这里分发米莉的时候，谁会大惊小怪呢？

该是我们回去的时候了。

```
// Account.js

import React, { useState } from 'react'
import { useQuery } from '@apollo/react-hooks'
import { Redirect } from 'react-router-dom'

import { GETPAIRS } from '../graphql/queries/getPairs'
import { MEQUERY } from '../graphql/queries/me'
import AddFunds from '../components/pairs/AddFunds'
import Pairs from '../components/pairs/Pairs'
import NewPosition from '../components/pairs/NewPosition'

export default function Account(props) {
  const [ open, setOpen ] = useState(true),
        user = useQuery(MEQUERY),
        { data, loading, error } = useQuery(GETPAIRS)

  if(user.error) return <Redirect to='/login' />
  if(!user.data || !user.data.me) return <p>A man has no name.</p>
  if(loading) return <p>Loading...</p>
  if(!data) return (
    <section>
      <h2>{ user.data.me.name }</h2>
      <div>
        <p><span>Available Balance: </span>{ user.data.me.bankroll.toLocaleString() }.00</p>
        <AddFunds />
      </div>
    </section>
  )
  if(error) return <p>{ error.message }</p>

  return (
    <section>
      <h2>{ user.data.me.name }</h2>
      <div>
        <p><span>Available Balance: </span>{ user.data.me.bankroll.toLocaleString() }.00</p>
        <AddFunds />
      </div>
      { props.location.state && <NewPosition state={ props.location.state } /> }
      <h3>Currency Pairs</h3>
      <button onClick={() => setOpen(true)}>open</button>
      <button onClick={() => setOpen(false)}>closed</button>
      <Pairs data={ data } open={ open } user={ user } />
    </section>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们处理我们的进口。接下来，我们实现`useQuery`来找出用户。如果没有`getPair`数据，我们只显示关于用户的信息，而`AddFunds`按钮`else`显示所有数据。

我们的用户现在可以开仓并把钱存入他们的账户。让我们允许他们平仓。这又一次从突变开始。在`graphql/mutations`中创建`closePosition.js`并添加以下内容:

```
// graphql/mutations/closePosition.js

import gql from 'graphql-tag'

export const CLOSEPOSITION = gql`
  mutation ClosePosition($id: ID!, $closedAt: Float!) {
    closePosition(id: $id, closedAt: $closedAt) {
      success
      message
      pair {
        id
        user
        pair
        lotSize
        position
        openedAt
        closedAt
        pipDif
        profitLoss
        open
        createdAt
        updatedAt
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

当用户点击一个空缺职位时，他们会被导航到`/pair`。这是他们可以平仓的地方。在`pages`文件夹中，创建`Pair.js`，并对其进行调整，使其类似于下面的代码:

```
// Pair.js

import React from 'react'
import { useQuery } from '@apollo/react-hooks'

import { CURRENCY_PAIR_INFO } from '../graphql/queries/currencyPairInfo'
import ClosePosition from '../components/positions/ClosePosition'
import PairDetails from '../components/pairs/PairDetails'

export default function Pair(props) {
  const { createdAt, lotSize, openedAt, pair, position, id } = props.location.state.pair,
        { bankroll, name } = props.location.state.me,
        [ fc, tc ] = pair.split('/'),
        { data, loading, error, refetch } = useQuery(CURRENCY_PAIR_INFO, {
          variables: { fc, tc }
        })

  if(loading) return <p>Loading...</p>
  if(error) return <p>{ error.message }</p>

  const { bidPrice, lastRefreshed, askPrice } = data.currencyPairInfo,
        pipDifLong = (bidPrice - openedAt).toFixed(4),
        pipDifShort = (openedAt - askPrice).toFixed(4),
        potentialProfitLoss = position === 'long'
          ? pipDifLong * lotSize
          : pipDifShort * lotSize,
        date = new Date(lastRefreshed + ' UTC')

  return data && (
    <section>
      <div className='landing_pair_data'>
        <h3>Pair Details</h3>
        <div>
          <p>{ name } your available balance is { bankroll.toLocaleString() }.00</p> 
          <div>
            <button onClick={() => refetch()}>Refresh</button>
            <ClosePosition 
              id={id} 
              bidPrice={bidPrice} 
              askPrice={askPrice} 
              position={position} 
            />
          </div>
        </div>
        <PairDetails
          pair={pair} 
          lotSize={lotSize}
          openedAt={openedAt}
          position={position}
          createdAt={createdAt}
          askPrice={askPrice}
          bidPrice={bidPrice}
          lastRefreshed={date.toLocaleString()}
          pipDifLong={pipDifLong}
          pipDifShort={pipDifShort}
          potentialProfitLoss={potentialProfitLoss}
        />
      </div>
    </section>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了状态，我们就将查询变量传递给`currencyPairInfo`。响应提供了完成我们的`closePosition`突变所需的数据。根据`position`是做多还是做空，我们使用`askPrice`或`bidPrice`来计算自最初购买以来的价格差异。这种价格差异就是我们所说的点差(pipDif)。

正如[dailyfx.com](https://www.dailyfx.com/forex/education/trading_tips/daily_trading_lesson/2019/01/18/what-is-a-pip.html)所描述的:

> PIP——代表百分比点数——是外汇交易员用来定义两种货币之间最小价值变化的计量单位。这通常由第四个小数位数的移动来表示。点值的计算方法是将一个点(0.0001)乘以特定的手/合约大小。对于标准手数，这需要 100，000 单位的基础货币，对于迷你手数，这是 10，000 单位。例如，以欧元/美元为例，标准合约中一点的变动等于 10 美元(0.0001 x 100，000)。

每个货币对都有自己的相对关系，所以我们只需比较`openedAt`价格和`closedAt`价格就可以计算盈亏。我们通过首先计算出`position`是长还是短来计算`pipDif`。如果头寸很长，我们从`bidPrice`减去`openedAt`价格。相反，如果头寸不足，我们从`openedAt`价格中减去`askPrice`。这将提供我们的`pipDif`。一旦我们有了价格差异，我们就用它乘以`lotSize`。

一旦直观演示，您可以看到这是多么容易计算。对于标准批次(100，000 个单位)，每个点(通常是第四个小数位)移动相当于 10 个货币单位的利润/损失。

[![pipdif](img/4549f354446353e0acc4fb6a55419a4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BfdnMR4k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/pipdif.png%3Fraw%3Dtrue)

对于小批量(10，000 个单位)，我们做同样的事情，但是每个 pip 移动相当于 1 个货币单位的利润/损失。

[![pipdif minilot](img/b5c001e3ee9dd484000b278ae5be7089.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bm9anZqA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/pipdif2.png%3Fraw%3Dtrue)

理解这一点很重要，我们不是在将一种货币兑换成另一种货币。我们只是在赌哪种货币相对于另一种货币更值钱。为了清楚起见，如果你想买入(或做多)欧元对美元，你应该卖出欧元/美元或买入美元/欧元。相反，要做多美元兑欧元，你可以买入欧元/美元或卖出美元/欧元。展期(利息)和保证金超出了本教程的范围，因此我们将专门关注 pipDif。

我们需要创建`ClosePosition`和`PairDetails`。在`components/positions`中，创建`ClosePosition.js`并添加以下内容:

```
// components/positions/ClosePosition.js

import React, { useState } from 'react'
import { useQuery, useMutation } from '@apollo/react-hooks'
import { Link } from 'react-router-dom'

import { CLOSEPOSITION } from '../../graphql/mutations/closePosition'
import { MEQUERY } from '../../graphql/queries/me'
import { GETPAIRS } from '../../graphql/queries/getPairs'

export default function ClosePosition({ id, bidPrice, askPrice, position }) {
  const [ showModal, setShowModal ] = useState(false),
        { refetch  } = useQuery(MEQUERY),
        [ closePosition, { data, loading, error } ] = useMutation(CLOSEPOSITION, {
          variables: position === 'long'
            ? { id, closedAt: +bidPrice } 
            : { id, closedAt: +askPrice },
          refetchQueries: [{ query: GETPAIRS }]
        })

  if(loading) return <p>Loading...</p>
  if(error) return <p>{ error.message }</p>

  return closePosition && (
    <>
      <button onClick={ async () => {
        alert(`Are you sure you want to close your ${
          position === 'long' ? 'long' : 'short' } position?`) 
        await closePosition()
        setShowModal(true)
        refetch()
      }}>
        { position === 'long' ? 'Sell' : 'Buy' }
      </button> 

      { data && data.closePosition.message && showModal && ( 
        <div className='modal'>
          <button onClick={() => setShowModal(false)}>x</button>
          <p>{ data.closePosition.message }</p>
          <Link to='/account'><button>Account</button></Link>
        </div>
      )}
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这个文件所做的就是解密`position`是长还是短，并为`closePosition`突变提供适当的变量(配对`id`和`bidPrice/askPrice`)。`closePosition`响应消息将通过模态显示。

我们使用`useQuery`钩子来访问`me`查询`refetch`函数。我们将`refetch`方法添加到按钮中，这样在`closePosition`变异运行后，`refetch`将刷新用户数据。如果我们在这里不使用`refetchQueries`，那么在我们的突变运行之后`open`对将会是最新的，但是`closed`对不会。

在组件文件夹中创建`PairDetails.js`并添加下面的代码:

```
// components/PairDetails.js

import React from 'react'

const PairDetails = ({
  pair,
  lotSize,
  openedAt,
  position,
  createdAt,
  askPrice,
  bidPrice,
  lastRefreshed,
  pipDifLong,
  pipDifShort,
  potentialProfitLoss
}) => (
  <div>
    <p><span>Currency Pair: </span>{pair}</p>
    <p><span>Lot Size: </span>{lotSize.toLocaleString()}.00</p>
    <p><span>Opened At: </span>{(+openedAt).toFixed(4)}</p>
    <p><span>Position: </span>{position}</p>
    <p><span>Created At: </span>{new Date(+createdAt).toLocaleString()}</p>
    { position === 'long' 
      ? (
        <>
          <br />
          <p><span>Current Bid Price: </span>{(+bidPrice).toFixed(4)}</p>
          <p><span>Last Refreshed: </span>{lastRefreshed}</p>
          <p><span>Current Pip Difference: </span>{pipDifLong}</p>
          <p><span>Potential PL: </span>
            {potentialProfitLoss.toLocaleString()}.00
          </p>
        </> ) 
      : (
        <>
          <br />
          <p><span>Current Ask Price: </span>{(+askPrice).toFixed(4)}</p>
          <p><span>Last Refreshed: </span>{lastRefreshed}</p>
          <p><span>Current Pip Difference: </span>{pipDifShort}</p>
          <p><span>Potential PL: </span>
            {potentialProfitLoss.toLocaleString()}.00
          </p>
        </>
      )
    }
  </div>
)

export default PairDetails 
```

Enter fullscreen mode Exit fullscreen mode

我们显示未结头寸数据。我们还显示当前的`askPrice` / `bidPrice`和平仓将提供的`potentialProfitLoss`。

将 Pair.js 导入 App.js 并创建其`Route`。

```
// App.js

import React from 'react'
import { Route, Switch } from 'react-router-dom'

import Landing from './pages/Landing'
import Navbar from './components/navbar/Navbar'
import Register from './components/auth/Register'
import Login from './components/auth/Login'
import Account from './pages/Account'
import Pair from './pages/Pair'

const App = () => (
  <Switch>
    <Route path='/login' component={ Login } />
    <Route path='/' render={() => (
      <main>
        <div className='navbar'><Navbar /></div>
        <Route exact path='/' component={ Landing } />
        <Route path='/register' component={ Register } />
        <Route path='/account' component={ Account } />
        <Route path='/pair' component={ Pair } />
      </main>
    )} />
  </Switch>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

如果您因开设新职位而导航至/account，您应该会看到以下内容:

[![account](img/dfab5fb2aa8b4c50359a146052238170.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fDo-PpVP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/account.png%3Fraw%3Dtrue)

点击打开的一对，仔细看看浏览器。

[![pair details](img/076c9ae997d50ff8742939ab1d158e82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcTLJJYU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/pair_details.png%3Fraw%3Dtrue)

这样用户就可以平仓了。我们最好不要躺在功劳簿上。是时候实施我们的图表了！我们将从查询开始。在`graphql/queries`中创建一个新文件，并将其命名为`monthlyTimeSeries.js`。插入以下内容:

```
// graphql/queries/monthlyTimeSeries.js

import gql from 'graphql-tag' 

export const MONTHLYTIMESERIES = gql`
  query MonthlyTimeSeries($fc: String, $tc: String) {
    monthlyTimeSeries(fc: $fc, tc: $tc) {
      timesArray
      valuesArray
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在`pages`文件夹中创建一个名为`Chart.js`的新文件，并添加以下代码:

```
// Chart.js

import React, { useState } from 'react'
import { Line } from 'react-chartjs-2'
import { useQuery } from '@apollo/react-hooks'

import { MONTHLYTIMESERIES } from '../graphql/queries/monthlyTimeSeries'

export default function Chart() {
  const [ fc, setFc ] = useState('EUR'),
        [ tc, setTc ] = useState('USD'), 
        [ fromCurrency, setFromCurrency ] = useState('EUR'), 
        [ toCurrency, setToCurrency ] = useState('USD'),
        { data, error, loading, refetch } = useQuery(MONTHLYTIMESERIES, {
          variables: { fc, tc }
        })

  if(loading) return <p>loading...</p>
  if(error) return <button onClick={() => {
    refetch({ fc: 'EUR', tc: 'USD' })
    window.location.href = '/chart'
  }}>retry</button>

  const labels = data && data.monthlyTimeSeries.timesArray,
        chartData = data && data.monthlyTimeSeries.valuesArray

  return (
    <div className='chartData'>
      <form onSubmit={e => {
        e.preventDefault()
        setFc(fromCurrency)
        setTc(toCurrency) 
      }}>
        <input 
          name='fromCurrency'
          value={fromCurrency}
          placeholder='From Currency'
          onChange={e => setFromCurrency(e.target.value.toUpperCase())}
        />
        <input 
          name='toCurrency'
          value={toCurrency}
          placeholder='To Currency'
          onChange={e => setToCurrency(e.target.value.toUpperCase())}
        />
        <button>submit</button>
      </form>
      <Line data={{
        labels,
        datasets: [
          {
            label: `${fc}/${tc} Time Series FX (Monthly)`,
            fill: true,
            lineTension: 0.1,
            backgroundColor: 'rgb(55, 131, 194)',
            borderColor: 'white',
            borderCapStyle: 'butt',
            borderDash: [],
            borderDashOffset: 0.0,
            borderJoinStyle: 'miter',
            pointBorderColor: 'white',
            pointBackgroundColor: '#fff',
            pointBorderWidth: 1,
            pointHoverRadius: 5,
            pointHoverBackgroundColor: 'white',
            pointHoverBorderColor: 'rgba(220,220,220,1)',
            pointHoverBorderWidth: 2,
            pointRadius: 1,
            pointHitRadius: 10,
            data: chartData
          }
        ]
      }} />
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用我们的`monthlyTimeSeries`查询来获取图表数据。我们提供了几个输入，以便用户可以选择他们想要调查的货币对。如果用户输入了错误的密码对，我们会显示一个刷新按钮。`refetch`函数接受与其相关的`query`的参数。`onClick`我们使用该功能再次显示欧元/美元。我们向`Line`组件提供从查询中得到的`react-chartjs-2`的两个数组:`labels`和`chartData`。最后，我们添加一些样式并返回我们的图表。

我们需要将`Chart.js`导入到`App.js`中，并在`Navbar.js`中给它一个`path`。先说`App.js` :

```
// App.js

import React from 'react'
import { Route, Switch } from 'react-router-dom'

import Landing from './pages/Landing'
import Navbar from './components/navbar/Navbar'
import Register from './components/auth/Register'
import Login from './components/auth/Login'
import Account from './pages/Account'
import Pair from './pages/Pair'
import Chart from './pages/Chart'

const App = () => (
  <Switch>
    <Route path='/login' component={ Login } />
    <Route path='/' render={() => (
      <main>
        <div className='navbar'><Navbar /></div>
        <Route exact path='/' component={ Landing } />
        <Route path='/register' component={ Register } />
        <Route path='/account' component={ Account } />
        <Route path='/pair' component={ Pair } />
        <Route path='/chart' component={ Chart } />
      </main>
    )} />
  </Switch>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

Navbar.js:

```
// Navbar.js

import React from 'react'
import { NavLink, Redirect } from 'react-router-dom'
import { useQuery } from '@apollo/react-hooks'

import { MEQUERY } from '../../graphql/queries/me'
import Logout from '../auth/Logout'
import './Navbar.css'

const Navbar = () => {
  const { data, loading, error } = useQuery(MEQUERY)

  if(loading) return <p>Loading....</p>
  if(error) return <Redirect to='/login' />
  if(!data) return <p>This is unfortunate</p>

  return (
    <div className='navigation'>
      <header><NavLink exact to='/'>Forex</NavLink></header>
      { !data.me ? (
        <ul>
          <li><NavLink exact to='/login'>Login</NavLink></li>
          <li><NavLink exact to='/register'>SignUp</NavLink></li>
        </ul> ) 
      : (
        <ul>
          <li><NavLink to='/chart'>Chart</NavLink></li>
          <li><NavLink to='/account'>Account</NavLink></li>
          <li><Logout /></li>
        </ul>
      )}
    </div>
  )
}

export default Navbar 
```

Enter fullscreen mode Exit fullscreen mode

一旦你保存你的文件，我们的应用程序将完成，应该类似于下面的视频:

[![forex demo](img/8c5cd09827e8e2d34d1ecde61f150cef.png)](https://youtu.be/mbx9sLVk5UQ)

你会注意到图表反应充分，在旧的眼镜上也没有那么糟糕。

看哪！我们创建了一个货币交易所，并希望在这个过程中学到一些东西。我知道我做到了。

* * *

伸手:[Twitter](https://twitter.com/marlonanthony10)|[Medium](https://medium.com/@marlonanthony)|[GitHub](https://github.com/marlonanthony)