# 在 React 中构建一个令人惊叹的 Breadcrumb 组件

> 原文：<https://dev.to/jsmanifest/build-a-stunning-breadcrumb-component-in-react-with-plain-css-3phh>

你有没有用个人设备浏览过一个网站，发现自己想知道当时你在网站的哪个部分，却想不出在哪里？别担心，你不是一个人。

如果有，你认为有多少用户有类似的经历？

这是一个常见的场景，web 开发社区中的开发人员~~将~~考虑在内，以改善他们 web 应用程序中的用户体验。

现在，如果你和我一样， *breadcrumbs* 是一个非常尴尬的术语。

如果你是第一次尝试，让我来帮你理清你现在脑海中对*面包屑*的潜在混淆概念。

看看 dictionary.com 的定义，我们有两个官方的术语描述:

1.  *一块干的或软的面包屑*(我们都在谈论用这些美味的[膳食纤维来源保持健康](https://www.fob.uk.com/nutrition-and-health/)来帮助我们作为网站开发人员长期保持高效吗？)
2.  网站或基于网络的应用程序的当前页面上的一系列文本链接，通常位于顶部，显示页面在内容层次结构或浏览历史中的位置，并提供方便的导航工具。

你可能已经猜到了，这是第二点:)

像 craigslist 这样的网站在其应用程序中使用面包屑组件来描述用户的位置，在某些方面，它令人惊讶地改善了他们的用户体验！

您可能已经开发了一个如下所示的导航栏:

[![0](img/cfee04ac24e179e0733ffb756fda43ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ragEXD9r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/0.JPG)

这并没有错，但是如果你这样做了，并且想知道你可以在这个非常尴尬的空白空间中放置什么其他东西，面包屑组件实际上可以很好地完成这项工作:)

在本教程结束时，您应该能够学习如何创建一个面包屑组件，如下所示:

[![final result](img/e6941d536079ba81b550ceca8c4c8744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZMuWLvY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/final-result.gif)

或者这个:

[![12](img/5cfb18b2200c6f941339cb5a35c0e58e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xIFBPx7E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/12.gif)

甚至是这个:

[![13](img/889e75eeab4e7d70c5d414952f7c9a4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y9Pcu4_r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/13.gif)

值得一提的是，您将创建一个 breadcrumb *组件*，它将*允许*您在接口中插入该组件时*附加*breadcrumb 的*逻辑*实现——然而，*实现 bread crumb 的逻辑*稍微复杂一些。如果你想看如何实现这个逻辑的源代码，你可以在这里看一个例子[。](https://github.com/icd2k3/react-router-breadcrumbs-hoc/blob/master/src/index.js)

现在，让我们开始吧！

页（page 的缩写）s:如果你有一个中等帐户，[关注我](https://medium.com/@jsmanifest),这样你一定会得到我的最新更新。我太好了，所以你会喜欢我的。

在本教程中，我们将使用 create-react-app 快速生成一个 react 项目。

(如果你想从 github 获得一份库的副本，点击[这里](https://github.com/jsmanifest/breadcrumb))。

继续使用下面的命令创建一个项目。在本教程中，我将我们的项目称为 *breadcrumb* 。

```
npx create-react-app breadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，现在进入目录:

```
cd breadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

在主条目`src/index.js`中，我们将对其进行一点清理，这样我们可以只关注组件:

> src/index.js

```
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import './styles.css'
import * as serviceWorker from './serviceWorker'

ReactDOM.render(<App />, document.getElementById('root'))

serviceWorker.unregister() 
```

Enter fullscreen mode Exit fullscreen mode

现在创建`src/App.js`:

> src/App.js

```
import React from 'react'

const App = () => <div />

export default App 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用`App.js`到*来呈现面包屑组件*。我们还将使用它来定义一些示例路线，以便稍后可以看到实际操作中的面包屑。

我们将继续创建一个`src/pages`目录，并通过为它们创建每个文件来添加示例路线:

```
// src/pages/home.js
import React from 'react'

const Home = ({ children }) => (
  <div>
    <h2>Home</h2>
    <div>{children}</div>
  </div>
)

export default Home

// src/pages/dashboard.js
import React from 'react'

const Dashboard = ({ children }) => (
  <div>
    <h2>Dashboard</h2>
    <div>{children}</div>
  </div>
)

export default Dashboard

// src/pages/contact.js
import React from 'react'

const Contact = ({ children }) => (
  <div>
    <h2>Contact</h2>
    <div>{children}</div>
  </div>
)

export default Contact

// src/pages/about.js
import React from 'react'

const About = ({ children }) => (
  <div>
    <h2>About</h2>
    <div>{children}</div>
  </div>
)

export default About

// src/pages/blog.js
import React from 'react'

const Blog = ({ children }) => (
  <div>
    <h2>Blog</h2>
    <div>{children}</div>
  </div>
)

export default Blog 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将它们导入到我们的`App.js`组件中，并在*路由器*中呈现它们，这样我们的 breadcrumb 组件就可以对它们进行操作。您可以使用任何路由库，但是对于本教程，我将使用 [@reach/router](https://reach.tech/router) :

> src/App.js

```
import React from 'react'
import { Router } from '@reach/router'
import Home from './pages/home'
import Dashboard from './pages/dashboard'
import Contact from './pages/contact'
import About from './pages/about'
import Blog from './pages/blog'

const App = () => (
  <div className='app'>
    <Router>
      <Home path='/' />
      <Dashboard path='/dashboard' />
      <Contact path='/contact' />
      <About path='/about' />
      <Blog path='/blog' />
    </Router>
  </div>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

以下是我的开始 *css 样式*:

> src/styles.css

```
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, 'Courier New',
    monospace;
}

.app {
  padding: 12px;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将继续创建 *Breadcrumb* 组件，这样我们就可以呈现内部每个路线路径的 Breadcrumb 项目:

> src/Breadcrumb.js

```
import React from 'react'

const Breadcrumb = ({ children }) => {
  return <div>{children}</div>
}

export default Breadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以从`App.js`导入它，并在它的渲染方法中渲染它。

此外，我已经将一些路线定义为一个数组，这样我就可以在 *breadcrumb 组件*中呈现它们。(注意:在真实的场景中，您可以提供子路由，这些子路由会随着浏览器位置在层次结构中的深入而呈现——但是我们在这里只使用顶级路由，只是为了展示 breadcrumb 接口的实际实现)

> src/App.js

```
import React from 'react'
import { Link, Router } from '@reach/router'
import Home from './pages/home'
import Dashboard from './pages/dashboard'
import Contact from './pages/contact'
import About from './pages/about'
import Blog from './pages/blog'
import Breadcrumb from './Breadcrumb'

const items = [
  { to: '/', label: 'Home' },
  { to: '/dashboard', label: 'Dashboard' },
  { to: '/contact', label: 'Contact' },
  { to: '/about', label: 'About' },
  { to: '/blog', label: 'Blog' },
]

const App = () => (
  <div className='app'>
    <Breadcrumb>
      {items.map(({ to, label }) => (
        <Link key={to} to={to}>
          {label}
        </Link>
      ))}
    </Breadcrumb>
    <Router>
      <Home path='/' />
      <Dashboard path='/dashboard' />
      <Contact path='/contact' />
      <About path='/about' />
      <Blog path='/blog' />
    </Router>
  </div>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该会看到类似这样的内容:

[![1](img/22af2ff278e3350f068120ff4759dceb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z0-pnBrz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/1.JPG)

到目前为止开始看起来像面包屑了！然而，在目前的状态下，这个*可能*在 90 年代已经足够了。因此，我们必须不断思考如何更好地改善这一点。

我们可以从在每个面包屑项目周围提供一个*包装器*开始。此外，由于我们将这些项目呈现为一个列表，我们可以转换曾经是一个*有序列表*的元素，这样我们在这方面会更正式一些:

> src/Breadcrumb.js

```
const Breadcrumb = (props) => {
  let children = React.Children.toArray(props.children)

  children = children.map((child, index) => (
    <BreadcrumbItem key={`breadcrumb_item${index}`}>{child}</BreadcrumbItem>
  ))

  return <ol>{children}</ol>
}

export default Breadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

对于*面包屑项目*组件，我们可以在顶部定义它:

```
const BreadcrumbItem = ({ children, ...props }) => (
  <li className='breadcrumb-item' {...props}>
    {children}
  </li>
) 
```

Enter fullscreen mode Exit fullscreen mode

现在看起来是这样的:

[![2](img/2dff8d402c3b42b15eda036d09f6131f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u7_N9v-n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/2.JPG)

因为我们将一个*有序列表*和一个*列表项目*元素列表应用到我们的 breadcrumb 组件，我们必须改变项目所面对的*方向*。我们还需要删除数字字符。

我们可以用普通的 css 来完成这些任务:

> src/styles.css

```
ol {
  list-style: none;
  display: flex;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![3](img/03bf5269cb66df8701c34d61a4717742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Swde034--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/3.JPG)

您可能已经注意到面包屑组件中发生的一件奇怪的事情是这一行:

```
let children = React.Children.toArray(props.children) 
```

Enter fullscreen mode Exit fullscreen mode

这是有充分理由的。当我们将项目呈现为 breadcrumb 组件的直接子组件时，我们是这样做的:

```
const App = () => (
  <div className='app'>
    <Breadcrumb>
      {items.map(({ to, label }) => (
        <Link key={to} to={to}>
          {label}
        </Link>
      ))}
    </Breadcrumb>
    <Router>
      <Home path='/' />
      <Dashboard path='/dashboard' />
      <Contact path='/contact' />
      <About path='/about' />
      <Blog path='/blog' />
    </Router>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

只要有多个组件，React 就将直接在其下渲染的每个组件视为一个*数组*。

当我们使用各种方法(比如 *slice* )操作这个组件“数组”时，react 每次都需要一个*键*传递给它们。当我们用*做出反应的时候。Children.toArray* 为了包装我们的孩子，react [自动将所有关键需求](https://reactjs.org/docs/react-api.html#reactchildren)分配并处理给最初的孩子，以供后续使用。*完美*！我们不必将不必要的丑陋代码应用到我们的组件代码中。谢谢你的反应:)

面包屑通常使用一个*分隔符*来分隔它们的每一个项目，以便用户更容易使用和阅读。当你应用一个分隔符(最常见的是像*/*这样的斜线)时，用户将能够分辨出哪些部分是彼此分开的。有时候——在一些屏幕上，你会发现你的单词出乎意料地彼此靠得太近，所以这有助于解决这些可见性问题。

接下来我们将继续添加一个*。将*减少到子项，以便我们可以应用逻辑将这些分隔符放置到位:

> src/Breadcrumb.js

```
import React from 'react'

const BreadcrumbItem = ({ children, ...props }) => (
  <li className='breadcrumb-item' {...props}>
    {children}
  </li>
)

const Breadcrumb = (props) => {
  let children = React.Children.toArray(props.children)

  children = children.map((child, index) => (
    <BreadcrumbItem key={`breadcrumb_item${index}`}>{child}</BreadcrumbItem>
  ))

  const lastIndex = children.length - 1

  children = children.reduce((acc, child, index) => {
    const notLast = index < lastIndex
    if (notLast) {
      acc.push(child, '/')
    } else {
      acc.push(child)
    }
    return acc
  }, [])

  return <ol>{children}</ol>
}

export default Breadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

[![4](img/692a6dff65810a42aef4d8c83bb85196.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xGEFE0Xn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/4.JPG)

(注意:我们实现了一个条件检查，这样我们就不会不必要地在面包屑的末尾添加一个斜杠)

从截图中我们可以清楚地看到，我们分离了每个面包屑项目。然而，回到我之前的评论，一些用户不能清楚地看到单词，因为单词在一些屏幕上变得非常近，现在分隔符又出现了同样的问题。

因此，我们将为分隔符应用一个包装器，并将一个 *className* prop 附加到 *li* 元素上，这样我们就可以稍微控制一下间距:

> src/Breadcrumb.js

```
const BreadcrumbSeparator = ({ children, ...props }) => (
  <li className='breadcrumb-separator' {...props}>
    {children}
  </li>
) 
```

Enter fullscreen mode Exit fullscreen mode

> src/styles.css

```
.breadcrumb-separator {
  color: #333;
  margin: auto 6px;
  user-select: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只需要用新的包装组件:
包装我们的分隔符

```
children = children.reduce((acc, child, index) => {
    const notLast = index < lastIndex
    if (notLast) {
      acc.push(
        child,
        <BreadcrumbSeparator key={`breadcrumb_sep${index}`}>
          /
        </BreadcrumbSeparator>,
      )
    } else {
      acc.push(child)
    }
    return acc
  }, []) 
```

Enter fullscreen mode Exit fullscreen mode

[![5](img/822cab451894e8eb8364e81db759179e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M55qTW13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/5.JPG)

**完美**！

然而，有时作为一名开发人员，我们可能想要切换使用哪种分隔符——比如一个*箭头*。我们可以通过允许调用者传入一个自定义分隔符作为属性来进行自定义:

> src/Breadcrumb.js

```
const Breadcrumb = ({ separator = '/', ...props }) => {
  let children = React.Children.toArray(props.children)

  children = children.map((child, index) => (
    <BreadcrumbItem key={`breadcrumb_item${index}`}>{child}</BreadcrumbItem>
  ))

  const lastIndex = children.length - 1

  children = children.reduce((acc, child, index) => {
    const notLast = index < lastIndex
    if (notLast) {
      acc.push(
        child,
        <BreadcrumbSeparator key={`breadcrumb_sep${index}`}>
          {separator}
        </BreadcrumbSeparator>,
      )
    } else {
      acc.push(child)
    }
    return acc
  }, [])

  return <ol>{children}</ol>
} 
```

Enter fullscreen mode Exit fullscreen mode

> src/App.js

```
const App = () => (
  <div className='app'>
    <Breadcrumb separator='/'>
      {items.map(({ to, label }) => (
        <Link key={to} to={to}>
          {label}
        </Link>
      ))}
    </Breadcrumb>
    <Router>
      <Home path='/' />
      <Dashboard path='/dashboard' />
      <Contact path='/contact' />
      <About path='/about' />
      <Blog path='/blog' />
    </Router>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

[![6](img/4caa348f2006fbcb51dd923c471dbc8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I9lisvu5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/6.JPG)

> src/App.js

```
const Separator = ({ children, ...props }) => (
  <span style={{ color: 'teal' }} {...props}>
    {children}
  </span>
)

const items = [
  { to: '/', label: 'Home' },
  { to: '/dashboard', label: 'Dashboard' },
  { to: '/contact', label: 'Contact' },
  { to: '/about', label: 'About' },
  { to: '/blog', label: 'Blog' },
]

const App = () => (
  <div className='app'>
    <Breadcrumb separator={<Separator>/</Separator>}>
      {items.map(({ to, label }) => (
        <Link key={to} to={to}>
          {label}
        </Link>
      ))}
    </Breadcrumb>
    <Router>
      <Home path='/' />
      <Dashboard path='/dashboard' />
      <Contact path='/contact' />
      <About path='/about' />
      <Blog path='/blog' />
    </Router>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

[![7](img/89c0014079fe763c7d216b516a29bdaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CEcZSVJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/7.gif)

当面包屑变得太长时会发生什么？

最常见的情况是，在一个大规模的应用程序中，可能有大量的路由会使面包屑比它应该有的更长。

如果我们想解决这个问题，一个已知的解决方案是使用*折叠器*。

我们将继续创建一个 collapser 组件。

继续创建`src/BreadcrumbCollapser.js`。这将用于从界面中*折叠*项，以便 breadcrumb 组件可以隐藏它的一些项:

> src/BreadcrumbCollapser.js

```
import React from 'react'
import { MdMoreHoriz } from 'react-icons/md'

const BreadcrumbCollapser = (props) => (
  <li className='breadcrumb-collapser' {...props}>
    <MdMoreHoriz />
  </li>
)

export default BreadcrumbCollapser 
```

Enter fullscreen mode Exit fullscreen mode

> src/styles.css

```
.breadcrumb-separator {
  color: #333;
  margin: auto 6px;
  user-select: none;
}

.breadcrumb-collapser {
  display: flex;
  align-items: center;
}

.breadcrumb-collapser svg {
  cursor: pointer;
  transition: all 0.2s ease-out;
  color: #000;
  width: 25px;
  height: 25px;
}
.breadcrumb-collapser svg:hover,
.breadcrumb-collapser svg:focus {
  color: #999;
  transform: scale(1.15);
}

.breadcrumb-collapser svg:active {
  color: #333;
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们引入任何形式的“折叠器”时，我们需要它知道*何时*或*何时不*折叠，让用户能够控制这种行为是增强折叠器用户体验的一个好方法。

我将创建一个名为`useBreadcrumb.js`的自定义钩子，并设置一个名为*的*状态*展开*。接口将需要这个信息，所以它是从自定义钩子返回的:

> src/useBreadcrumb.js

```
import { useState } from 'react'

const useBreadcrumb = () => {
  const [expanded, setExpanded] = useState(false)

  const open = () => setExpanded(true)

  return {
    expanded,
    open,
  }
}

export default useBreadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

在一个单独的文件中创建一个定制的 react 钩子可能有点大材小用，但是我喜欢它给我的开发流程带来的额外的组织习惯:)它完全是可选的，你可以直接在 *breadcrumb 组件*中设置状态。

接下来，我从`Breadcrumb.js`文件中导入了定制钩子和面包屑折叠器:

> src/Breadcrumb.js

```
import React from 'react'
import useBreadcrumb from './useBreadcrumb'
import BreadcrumbCollapser from './BreadcrumbCollapser' 
```

Enter fullscreen mode Exit fullscreen mode

使用自定义钩子:

```
const Breadcrumb = ({ separator, ...props }) => {
  let children = React.Children.toArray(props.children)

  const { expanded, open } = useBreadcrumb() 
```

Enter fullscreen mode Exit fullscreen mode

提供一个自定义属性可能会有所帮助，调用者可以传入这个属性来控制折叠或显示的项目数量，所以我们允许调用者传入一个自定义的*折叠*属性，我们可以用它来应用到面包屑折叠器:

```
const Breadcrumb = ({ separator, collapse = {}, ...props }) => {
  let children = React.Children.toArray(props.children)

  const { expanded, open } = useBreadcrumb()

  const { itemsBefore = 1, itemsAfter = 1, max = 4 } = collapse

  const totalItems = children.length
  const lastIndex = totalItems - 1 
```

Enter fullscreen mode Exit fullscreen mode

这将确保呼叫者有能力控制*看到什么*，如果需要的话，对其进行裁剪，使其与他们的 UI 更加兼容。

最后，将折叠器应用于渲染子对象:

```
if (!expanded || totalItems <= max) {
  children = [
    ...children.slice(0, itemsBefore),
    <BreadcrumbCollapser
      title='Expand'
      key='collapsed-seperator'
      onClick={open}
    />,
    ...children.slice(totalItems - itemsAfter, totalItems),
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是我们迄今为止的完整面包屑实现:

> src/Breadcrumb.js

```
import React from 'react'
import useBreadcrumb from './useBreadcrumb'
import BreadcrumbCollapser from './BreadcrumbCollapser'

const BreadcrumbItem = ({ children, ...props }) => (
  <li className='breadcrumb-item' {...props}>
    {children}
  </li>
)

const BreadcrumbSeparator = ({ children, ...props }) => (
  <li className='breadcrumb-separator' {...props}>
    {children}
  </li>
)

const Breadcrumb = ({ separator, collapse = {}, ...props }) => {
  let children = React.Children.toArray(props.children)

  const { expanded, open } = useBreadcrumb()

  const { itemsBefore = 1, itemsAfter = 1, max = 4 } = collapse

  const totalItems = children.length
  const lastIndex = totalItems - 1

  children = children.map((child, index) => (
    <BreadcrumbItem key={`breadcrumb_item${index}`}>{child}</BreadcrumbItem>
  ))

  children = children.reduce((acc, child, index) => {
    const notLast = index < lastIndex
    if (notLast) {
      acc.push(
        child,
        <BreadcrumbSeparator key={`breadcrumb_sep${index}`}>
          {separator}
        </BreadcrumbSeparator>,
      )
    } else {
      acc.push(child)
    }
    return acc
  }, [])

  if (!expanded || totalItems <= max) {
    children = [
      ...children.slice(0, itemsBefore),
      <BreadcrumbCollapser
        title='Expand'
        key='collapsed-seperator'
        onClick={open}
      />,
      ...children.slice(totalItems - itemsAfter, totalItems),
    ]
  }

  return <ol>{children}</ol>
}

export default Breadcrumb 
```

Enter fullscreen mode Exit fullscreen mode

[![8](img/9fe9e7bbfcbe77e80e582cdc1cef7437.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FR332Uz---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/8.gif)

这些链接需要一点点的定制，因为它们是非常基本和古老的。让我们改变一下:

```
a {
  text-decoration: none;
  font-weight: 400;
  color: #424548;
}

a:hover {
  color: #1da2b3;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![9](img/2fd997f8b8e0cbd06837917766aeb043.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m3xAWoqE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/9.gif)

如果你是一个功能型的人，而我们的组件对你来说不是很有吸引力，那么把它重构得更有吸引力一点是很好的:

> src/Breadcrumb.js

```
const toBreadcrumbItem = (child, index) => (
  <BreadcrumbItem key={`breadcrumb_item${index}`}>{child}</BreadcrumbItem>
)

const withSeparator = (lastIndex, separator) => (acc, child, index) => {
  const notLast = index < lastIndex
  if (notLast) {
    acc.push(
      child,
      <BreadcrumbSeparator key={`breadcrumb_sep${index}`}>
        {separator}
      </BreadcrumbSeparator>,
    )
  } else {
    acc.push(child)
  }
  return acc
}

const withCollapse = ({
  itemsBefore,
  itemsAfter,
  max,
  children,
  totalItems,
  open,
}) => [
  ...children.slice(0, itemsBefore),
  <BreadcrumbCollapser
    title='Expand'
    key='collapsed-seperator'
    onClick={open}
  />,
  ...children.slice(totalItems - itemsAfter, totalItems),
] 
```

Enter fullscreen mode Exit fullscreen mode

```
const Breadcrumb = ({ separator, collapse = {}, ...props }) => {
  let children = React.Children.toArray(props.children)

  const { expanded, open } = useBreadcrumb()

  const { itemsBefore = 1, itemsAfter = 1, max = 4 } = collapse

  const totalItems = children.length
  const lastIndex = totalItems - 1

  children = children
    .map(toBreadcrumbItem)
    .reduce(withSeparator(lastIndex, separator), [])

  if (!expanded || totalItems <= max) {
    children = withCollapse({
      itemsBefore,
      itemsAfter,
      max,
      children,
      totalItems,
      open,
    })
  }

  return <ol>{children}</ol>
} 
```

Enter fullscreen mode Exit fullscreen mode

[![10](img/e41a545538592069033fb7ed758182ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7DI68I_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/10.gif)

这里要记住的是，如果我们*没有*使用*做出反应。为了包装我们的孩子，我们将在控制台中得到呈现孩子列表的*键*错误，而不提供*键*给他们。*

想知道如何在两者之间渲染图标？有多种方法可以实现这一点，但其中一种方法可能是这样的:

> src/App.js

```
import React from 'react'
import { Link, Router } from '@reach/router'
import { MdHome, MdSettings, MdEmail, MdInfo, MdWeb } from 'react-icons/md'
import Home from './pages/home'
import Dashboard from './pages/dashboard'
import Contact from './pages/contact'
import About from './pages/about'
import Blog from './pages/blog'
import Breadcrumb from './Breadcrumb'

const Separator = ({ children, ...props }) => (
  <span style={{ color: 'teal' }} {...props}>
    {children}
  </span>
)

const options = {
  icons: {
    Home: MdHome,
    Dashboard: MdSettings,
    Contact: MdEmail,
    About: MdInfo,
    Blog: MdWeb,
  },
  items: [
    { to: '/', label: 'Home' },
    { to: '/dashboard', label: 'Dashboard' },
    { to: '/contact', label: 'Contact' },
    { to: '/about', label: 'About' },
    { to: '/blog', label: 'Blog' },
  ],
}

const App = () => (
  <div className='app'>
    <Breadcrumb separator={<Separator>/</Separator>}>
      {options.items.map(({ to, label }) => {
        const Icon = options.icons[label]
        return (
          <div key={to} className='some-custom-classname'>
            {Icon && <Icon />}
            <Link to={to}>{label}</Link>
          </div>
        )
      })}
    </Breadcrumb>
    <Router>
      <Home path='/' />
      <Dashboard path='/dashboard' />
      <Contact path='/contact' />
      <About path='/about' />
      <Blog path='/blog' />
    </Router>
  </div>
)

export default App 
```

Enter fullscreen mode Exit fullscreen mode

[![11](img/1c4b76f09d375787ded082ed4b3fd425.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fwsSPKbZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/11.gif)

最后，我之前提到过，在现实世界场景中，我们会在 breadcrumb 组件中呈现子路由——通过层次结构。在本教程中，我们使用了顶级路由(这并不理想，但我想展示接口是如何实现的)。

使用本教程中的策略可以获得更真实的界面:

[![final result](img/e6941d536079ba81b550ceca8c4c8744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZMuWLvY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/build-a-stunning-breadcrumb-component-in-react-with-plain-css/final-result.gif)

*漂亮！*

## 结论

本教程到此结束！如果你一直在看我以前的帖子，你可能已经意识到我在结论中没有说太多-因为一切都已经在上面说过了！:)

下次再见，期待更多高质量的帖子！跟着我上[媒](https://medium.com/@jsmanifest)。