# 通过构建插件的 Gatsby 构建过程之旅

> 原文：<https://dev.to/alexluong/a-journey-through-gatsby-build-process-via-building-a-plugin-4nei>

让我带你看看我构建 Gatsby 插件的旅程。希望从我的经历中，你能了解一两件关于盖茨比的事，甚至能对胡克做出反应。

## 使命

这篇文章试图解释在构建和提供 HTML 步骤中运行`gatsby develop`和`gatsby build`会发生什么。

这篇文章假设你有一些使用 Gatsby 的经验，并且知道一些特定于 Gatsby 的 API。如果我在什么地方跟不上你，请随时让我进一步解释。

## 插件

我正在构建的插件是`gatsby-plugin-firebase`。我想用 Firebase 和 Gatsby 一起构建一个 web 应用程序，但是在设置方面存在一些挑战。主要是，Firebase web SDK 是仅客户端的，这与 Gatsby 服务器端呈现过程不太合拍。

我搜了一个整合 Firebase 和 Gatsby 的方案，好像不多。在我的搜索中，我遇到了两个非常有用的资源，所以你可以查看它们以获得更好的上下文:

*   凯尔·舍夫林的博客文章:[法尔巴斯和盖茨比，终于在一起了](https://kyleshevlin.com/firebase-and-gatsby-together-at-last)
*   穆罕默德·穆哈吉尔的 [`gatsby-starter-firebase`](https://github.com/muhajirdev/gatsby-starter-firebase/)

我将要构建的插件应该允许你在`gatsby-config.js`中注册它，并初始化 Firebase，为你做好准备。

## 尝试#1

### 代码

从这两个资源中获得灵感，我构建了`gatsby-plugin-firebase`。我将快速浏览我的代码，因为这不是这篇文章的重点。我是这样做的:

*   使用`gatsby-browser.js`和`gatsby-ssr.js`，我将 Gatsby root 包装在一个 React 组件中:

```
import React from "react"
import Layout from "./src"

export const wrapRootElement = ({ element, props }) => (
  <Layout {...props}>{element}</Layout>
) 
```

Enter fullscreen mode Exit fullscreen mode

*   在`src/index.js`的`Layout`组件中，我初始化了 Firebase 并将一个`firebase`实例放入 React 上下文中:

```
import React from "react"
import FirebaseContext from "./components/FirebaseContext"

function Index({ children }) {
  const [firebase, setFirebase] = React.useState(null)

  React.useEffect(() => {
    if (!firebase && typeof window !== "undefined") {
      const app = import("firebase/app")
      const auth = import("firebase/auth")
      const database = import("firebase/database")
      const firestore = import("firebase/firestore")

      Promise.all([app, auth, database, firestore]).then(values => {
        const firebaseInstance = values[0]
        firebaseInstance.initializeApp({
          apiKey: process.env.GATSBY_FIREBASE_API_KEY,
          authDomain: process.env.GATSBY_FIREBASE_AUTH_DOMAIN,
          databaseURL: process.env.GATSBY_FIREBASE_DATABASE_URL,
          projectId: process.env.GATSBY_FIREBASE_PROJECT_ID,
          storageBucket: process.env.GATSBY_FIREBASE_STORAGE_BUCKET,
          messagingSenderId: process.env.GATSBY_FIREBASE_MESSAGING_SENDER_ID,
          appId: process.env.GATSBY_FIREBASE_APP_ID,
        })
        setFirebase(firebaseInstance)
      })
    }
  }, [])

  if (!firebase) {
    return null
  }

  return (
    <FirebaseContext.Provider value={firebase}>
      {children}
    </FirebaseContext.Provider>
  )
}

export default Index 
```

Enter fullscreen mode Exit fullscreen mode

*   用一些助手创建了`FirebaseContext`，以便轻松访问`src/index.js`内部的`firebase`:

```
import React from "react"

const FirebaseContext = React.createContext(null)

export function useFirebase() {
  const firebase = React.useContext(FirebaseContext)
  return firebase
}

export const withFirebase = Component => props => (
  <FirebaseContext.Consumer>
    {firebase => <Component {...props} firebase={firebase} />
  </FirebaseContext.Consumer>
)

export default FirebaseContext 
```

Enter fullscreen mode Exit fullscreen mode

*   在根里面，我导出了一些助手:

```
exports.FirebaseContext = require("./src/components/FirebaseContext").default
exports.useFirebase = require("./src/components/FirebaseContext").useFirebase
exports.withFirebase = require("./src/components/FirebaseContext").withFirebase 
```

Enter fullscreen mode Exit fullscreen mode

### 成功了吗？

的确如此🎉🎉。当我写了一些代码来消耗这个库并运行`gatsby develop`时，它工作得非常好。下面是一个示例组件，展示了我是如何使用它的:

```
import React from "react"
import { useFirebase } from "gatsby-plugin-firebase"

export default () => {
  const firebase = useFirebase()
  const [name, setName] = React.useState("there")

  React.useEffect(() => {
    firebase
      .database()
      .ref("/name")
      .once("value")
      .then(snapshot => setName(snapshot.val()))
  }, [firebase])

  return <div>Hi {name}</div>
} 
```

Enter fullscreen mode Exit fullscreen mode

当我试图运行`gatsby build && gatsby serve`时，问题出现了。该网站仍然成功地建立和工作，但奇怪的事情发生了。

当访问一个不使用 Firebase 的页面时，它会呈现内容，然后出现一个白色屏幕，然后再次呈现内容。

当访问使用 Firebase 的页面时，它会呈现默认值、flash、默认值，然后是 Firebase 中的值。

> 这是怎么回事？

事情是这样的，在开发阶段，Gatsby 使用 Webpack Dev 服务器，所以一切都完全在客户机上运行。在这一点上，Gatsby 基本上是一个 React 应用程序(忽略 GraphQL 部分)。因此，一切都很完美。

当运行`gatsby build`时，它在一个节点进程中为所有页面生成 HTML 文件。在 React 组件中，它没有像`componentDidMount`或`useEffect`钩子那样运行生命周期。最终，不依赖 Firebase 的页面都是一样的。因为 Firebase 是在`useEffect`中运行的，所以我写的页面只是使用默认的`name`状态，并呈现“你好”。

在为站点提供服务时，在呈现 HTML 之后，Gatsby 会将站点重新水合为 React 应用程序。在这一点上，它将初始化 Firebase，并做所有在构建步骤中没有做的事情。

当我建立`FirebaseContext`的时候，在我的`src/index.js`文件中，我有这样几行:

```
if (!firebase) {
  return null
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是白色闪光出现的原因。万恶之源。如果你用`return <div style={{ width: "100%", height: "100%", background: "red" }} />`代替`return null`，你会看到一个非常红的闪光。

## 尝试#2

如果这三条线是白色闪光的原因，也许我们可以把它们去掉，对吗？对吗？

我就是这么做的。天哪，我错了。

第一次渲染时。记得在我的`src/index.js`文件中，我将 Firebase 初始化代码包装在一个`useEffect`中。第一次渲染后火焰基地将只存在*。当删除这 3 行时，我在开发步骤中收到了`firebase is undefined`错误。*

### 解

为了解决这个错误，我可以在对它做任何事情之前简单地检查一下`firebase`是否存在。**管用**。但是我不喜欢。我不想在用户每次尝试用 Firebase 做事情时给他们的大脑增加额外的认知负荷。

此外，在 React 钩子中检查`firebase`是否存在非常简单:

```
React.useEffect(() => {
  if (!firebase) {
    return
  }
  doSomething(firebase)
}, [firebase]) 
```

Enter fullscreen mode Exit fullscreen mode

而在一个类组件中，它会更复杂一些:

```
class Component extends React.Component {
  componentDidUpdate(prevProps) {
    if (!prevProps.firebase && this.props.firebase) {
      doSomething(this.props.firebase)
    }
  }
}

export default withFirebase(Component) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，没那么糟。但还可以更好。

## 尝试#3

在寻找更好的 API 时，我只是随机想到了`useEffect`是如何工作的。既然您无论如何都必须在那个钩子中使用 Firebase，并且它接受一个函数作为它的第一个参数，那么如果我的`useFirebase`也这样工作呢？在这种情况下，参数中的函数只有在已经初始化的情况下才能接收`firebase`，这样最终用户就不必关心它了。

最终用户会知道`firebase`总是在那里，为他们做好准备。

下面是我重写的助手钩子:

```
function useFirebase(fn, dependencies = []) {
  const firebase = React.useContext(FirebaseContext)
  React.useEffect(() => {
    if (!firebase) {
      return
    }
    return fn(firebase)
  }, [firebase, ...dependencies])
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个钩子，用户可以像这样简单地编写他们的组件:

```
function Component() {
  const [name, setName] = React.useState("there")

  useFirebase(firebase => {
    firebase
      .database()
      .ref("/name")
      .once("value")
      .then(snapshot => setName(snapshot.val()))
  })

  return <div>Hi {name}</div>
} 
```

Enter fullscreen mode Exit fullscreen mode

很美，如果我自己这么说的话。

### 那课呢，老弟？

现在我对这个 API 很满意了，我试着想出一种方法来支持同样的易于使用的 API，但是对于类组件，因为它们不能使用钩子。

坦白地说，我就是想不出一个像 hook 一样直观的 API。问题是类组件与呈现方法的耦合性太强，以至于不可能像钩子所允许的那样将这一方面委托给用户。

## 结论

就这样了，伙计们。一些快速回顾:

*   `gatsby develop`运行 React 应用程序
*   `gatsby build`构建 HTML 页面
*   在呈现 HTML 后，Gatsby 会对站点进行再水合反应。生命周期方法将运行，这可能会也可能不会影响您的站点的外观，可能会导致闪烁。
*   反应钩棒极了

而如果你用 Firebase 搭配 Gatsby，可以考虑用我的插件 [`gatsby-plugin-firebase`](https://github.com/alexluong/gatsby-plugin-firebase) 也许？