# 为什么要反应钩子？

> 原文：<https://dev.to/tylermcginnis/why-react-hooks-51lj>

*这最初发表在 [ui.dev](https://ui.dev/why-react-hooks/) 上，是我们 [React Hooks](https://ui.dev/react-hooks/) 课程的一部分。如果你喜欢这篇文章，就去看看吧。*

* * *

[https://www.youtube.com/embed/eX_L39UvZes](https://www.youtube.com/embed/eX_L39UvZes)

每当你要学习新东西时，你要做的第一件事就是问自己两个问题-

1)这个东西为什么会存在？这个东西解决了什么问题？

如果你从来没有为这两个问题找到一个令人信服的答案，当你深入细节时，你就没有足够坚实的基础。关于[反应钩子](https://tylermcginnis.com/courses/react-hooks/)，这些问题特别有趣。当 Hooks 发布时，React 是 JavaScript 生态系统中最受欢迎和喜爱的前端框架。尽管已有好评，React 团队仍然认为有必要构建和发布挂钩。迷失在各种媒体帖子和博客中思考挂钩的原因 **(1)为什么**和为了什么 **(2)利益**，尽管好评如潮，React 团队决定花费宝贵的资源来构建和发布挂钩。为了更好地理解这两个问题的答案，我们首先需要更深入地了解我们过去是如何编写 React 应用程序的。

### createClass

如果你接触 React 游戏足够长的时间，你会记得`React.createClass` API。这是我们创建 React 组件的原始方式。您用来描述组件的所有信息都将作为对象传递给`createClass`。

```
const ReposGrid = React.createClass({
  getInitialState () {
    return {
      repos: [],
      loading: true
    }
  },
  componentDidMount () {
    this.updateRepos(this.props.id)
  },
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  },
  updateRepos (id) {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  },
  render() {
    const { loading, repos } = this.state

    if (loading === true) {
      return <Loading />
    }

    return (
      <ul>
        {repos.map(({ name, handle, stars, url }) => (
          <li key={name}>
            <ul>
              <li><a href={url}>{name}</a></li>
              <li>@{handle}</li>
              <li>{stars} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-createclass-iw6s5)**

`createClass`是创建 React 组件的一种简单而有效的方法。React 最初使用`createClass` API 的原因是，当时 JavaScript 没有内置的类系统。当然，这最终改变了。在 ES6 中，JavaScript 引入了`class`关键字，并以一种本地方式在 JavaScript 中创建类。这将 React 置于一个艰难的位置。要么继续使用`createClass`对抗 JavaScript 的进步，要么服从 EcmaScript 标准的意志，拥抱类。历史已经证明，他们选择了后者。

### 做出反应。成分

> 我们发现我们的工作不是设计一个职业系统。我们只想使用惯用的 JavaScript 方式来创建类。- [反应 v0.13.0 发布](https://reactjs.org/blog/2015/01/27/react-v0.13.0-beta-1.html)

React v0.13.0 引入了`React.Component` API，允许您从(现在的)原生 JavaScript 类创建 React 组件。这是一个巨大的胜利，因为它更好地符合 EcmaScript 标准。

```
class ReposGrid extends React.Component {
  constructor (props) {
    super(props)

    this.state = {
      repos: [],
      loading: true
    }

    this.updateRepos = this.updateRepos.bind(this)
  }
  componentDidMount () {
    this.updateRepos(this.props.id)
  }
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  }
  updateRepos (id) {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  }
  render() {
    if (this.state.loading === true) {
      return <Loading />
    }

    return (
      <ul>
        {this.state.repos.map(({ name, handle, stars, url }) => (
          <li key={name}>
            <ul>
              <li><a href={url}>{name}</a></li>
              <li>@{handle}</li>
              <li>{stars} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-reactcomponent-xdhg5)T3】**

尽管朝着正确的方向迈出了明显的一步，但`React.Component`并非没有代价。

##### 构造函数

对于类组件，您可以在`constructor`方法中将组件的状态初始化为实例上的`state`属性(`this`)。然而，根据 ECMAScript 规范，如果您正在扩展一个子类(在本例中是`React.Component`，您必须首先调用`super`，然后才能使用`this`。具体来说，在使用 React 时，还必须记住将`props`传递给`super`。

```
 constructor (props) {
    super(props) // 🤮

    ...
  } 
```

Enter fullscreen mode Exit fullscreen mode

##### 自动绑定

当使用`createClass`时，React 会自动将所有方法绑定到组件的实例`this`。有了`React.Component`，情况就不一样了。很快，到处的开发者意识到他们不知道这个关键字是如何工作的。你必须记住在类的`constructor`中`.bind`方法，而不是让方法调用“刚刚工作”。如果不这样做，就会出现常见的“无法读取未定义的属性`setState`”错误。

```
 constructor (props) {
    ...

    this.updateRepos = this.updateRepos.bind(this) // 😭
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我知道你可能在想什么了。首先，这些问题相当肤浅。当然，调用`super(props)`并记住`bind`你的方法是令人讨厌的，但是这里没有什么根本性的错误。第二，这些甚至不一定是 React 的问题，因为它们是 JavaScript 类的设计方式。这两点都是正确的。然而，我们是开发者。当你一天要处理 20 多次时，即使是最表面的问题也会变得令人讨厌。对我们来说幸运的是，在从`createClass`切换到`React.Component`后不久，就产生了[类字段](https://tylermcginnis.com/javascript-private-and-public-class-fields/)提案。

##### 类字段

类字段允许您直接添加实例属性作为类的属性，而不必使用`constructor`。这对我们来说意味着，有了类字段，我们之前谈到的两个“表面”问题都将得到解决。我们不再需要使用`constructor`来设置组件的初始状态，也不再需要在`constructor`中使用`.bind`，因为我们可以在我们的方法中使用箭头函数。

```
class ReposGrid extends React.Component {
  state = {
    repos: [],
    loading: true
  }
  componentDidMount () {
    this.updateRepos(this.props.id)
  }
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  }
  updateRepos = (id) => {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  }
  render() {
    const { loading, repos } = this.state

    if (loading === true) {
      return <Loading />
    }

    return (
      <ul>
        {repos.map(({ name, handle, stars, url }) => (
          <li key={name}>
            <ul>
              <li><a href={url}>{name}</a></li>
              <li>@{handle}</li>
              <li>{stars} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-class-fields-9tbis)T3】**

所以现在我们没事了，对吧？不幸的是，没有。从`createClass`到`React.Component`的移动带来了一些折衷，但是正如我们看到的，类字段处理了这些。不幸的是，在我们看到的所有先前版本中，仍然存在一些更深刻(但很少被提及)的问题。

React 的整个思想是，通过将应用程序分解成单独的组件，然后将它们组合在一起，可以更好地管理应用程序的复杂性。这个组件模型使得 React 如此优雅。它让我们做出反应，做出反应。然而，问题不在于组件模型，而在于组件模型是如何实现的。

### 重复逻辑

从历史上看，我们构建 React 组件的方式与组件的生命周期密切相关。这种划分自然迫使我们在整个组件中散布相关的逻辑。我们可以在我们一直使用的`ReposGrid`例子中清楚地看到这一点。我们需要三种不同的方法(`componentDidMount`、`componentDidUpdate`和`updateRepos`)来完成同样的事情——让`repos`与`props.id`保持同步。

```
 componentDidMount () {
    this.updateRepos(this.props.id)
  }
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  }
  updateRepos = (id) => {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们需要一个全新的范例来处理 React 组件中的副作用。

### 共享非可视化逻辑

当您考虑 React 中的组合时，您很可能会考虑 UI 组合。这很自然，因为这是 React 擅长的。

```
view = fn(state) 
```

Enter fullscreen mode Exit fullscreen mode

实际上，构建一个应用不仅仅是 UI 层。需要组合和重用非可视化逻辑并不少见。但是，因为 React 将 UI 耦合到组件，所以这可能很困难。从历史上看，React 对此并没有一个很好的答案。

继续我们的例子，假设我们需要创建另一个同样需要`repos`状态的组件。现在，该状态和处理它的逻辑存在于`ReposGrid`组件中。我们该如何解决这个问题？嗯，最简单的方法是复制获取和处理我们的`repos`的所有逻辑，并将其粘贴到新组件中。很诱人，但是不行。更聪明的方法是创建一个[高阶组件](https://tylermcginnis.com/react-higher-order-components/)，它封装了所有的共享逻辑，并将`loading`和`repos`作为道具传递给任何需要它的组件。

```
function withRepos (Component) {
  return class WithRepos extends React.Component {
    state = {
      repos: [],
      loading: true
    }
    componentDidMount () {
      this.updateRepos(this.props.id)
    }
    componentDidUpdate (prevProps) {
      if (prevProps.id !== this.props.id) {
        this.updateRepos(this.props.id)
      }
    }
    updateRepos = (id) => {
      this.setState({ loading: true })

      fetchRepos(id)
        .then((repos) => this.setState({
          repos,
          loading: false
        }))
    }
    render () {
      return (
        <Component
          {...this.props}
          {...this.state}
        />
      )
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我们的应用程序中的任何组件需要`repos`(或`loading`)时，我们可以将它包装在我们的`withRepos` HOC 中。

```
// ReposGrid.js
function ReposGrid ({ loading, repos }) {
  ...
}

export default withRepos(ReposGrid) 
```

Enter fullscreen mode Exit fullscreen mode

```
// Profile.js
function Profile ({ loading, repos }) {
  ...
}

export default withRepos(Profile) 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-withrepos-okjsj)T3】**

这是可行的，并且在历史上(连同[渲染道具](https://tylermcginnis.com/react-render-props/))一直是共享非视觉逻辑的推荐解决方案。然而，这两种模式都有一些缺点。

首先，如果你不熟悉它们(即使你很熟悉)，你的大脑可能会不太明白其中的逻辑。通过我们的`withRepos` HOC，我们有了一个函数，它将最终呈现的组件作为第一个参数，但是返回一个新的类组件，这是我们的逻辑所在的地方。多么复杂的过程。

接下来，如果我们有不止一个正在使用的 HOC 会怎么样。你可以想象，它会很快失控。

```
export default withHover(
  withTheme(
    withAuth(
      withRepos(Profile)
    )
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

比^更糟糕的是最终得到的渲染。hoc(和类似的模式)迫使你重组和包装你的组件。这最终会导致“包装地狱”,这又一次使跟踪变得更加困难。

```
<WithHover>
  <WithTheme hovering={false}>
    <WithAuth hovering={false} theme='dark'>
      <WithRepos hovering={false} theme='dark' authed={true}>
        <Profile 
          id='JavaScript'
          loading={true} 
          repos={[]}
          authed={true}
          theme='dark'
          hovering={false}
        />
      </WithRepos>
    </WithAuth>
  <WithTheme>
</WithHover> 
```

Enter fullscreen mode Exit fullscreen mode

### 当前状态

这就是我们的现状。

*   React 很受欢迎。
*   我们为 React 组件使用类，因为这在当时是最有意义的。
*   叫超(道具)很烦。
*   没人知道“这个”是怎么运作的。
*   好了，冷静下来。我知道你知道“这”是怎么回事，但对某些人来说这是不必要的障碍。
*   通过生命周期方法组织我们的组件迫使我们在整个组件中散布相关的逻辑。
*   React 没有好的原语来共享非可视化逻辑。

现在我们需要一个新的组件 API 来解决所有这些问题，同时保持**简单**、**可组合**、**灵活**和**可扩展**。这是一项艰巨的任务，但 React 团队不知何故完成了它。

### 反应钩

从 React v0.14.0 开始，我们有两种方法来创建组件——类或函数。不同之处在于，如果我们的组件有状态或者需要使用生命周期方法，我们必须使用类。否则，如果它只是接受道具和渲染一些 UI，我们可以使用一个函数。

如果不是这样呢？如果我们不需要使用类，而是总是使用函数，会怎么样？

> 有时候，优雅的实现只是一个函数。不是方法。不是一个班。不是框架。只是一个功能。
> 
> 约翰·卡马克。Oculus VR CTO。

当然，我们需要找到一种方法来增加功能组件拥有状态和生命周期方法的能力，但是假设我们这样做了，我们会看到什么好处呢？

嗯，我们不再需要调用`super(props)`，我们不再需要担心`bind`调用我们的方法或者`this`关键字，我们也不再需要使用类字段。本质上，我们之前谈到的所有“表面”问题都会消失。

```
(ノಥ,_｣ಥ)ノ彡 React.Component 🗑

function ヾ(Ő‿Ő✿) 
```

Enter fullscreen mode Exit fullscreen mode

现在，更难的问题是。

*   状态
*   生命周期方法
*   共享非视觉逻辑

##### 状态

由于我们不再使用类或`this`，我们需要一种新的方式来添加和管理组件内部的状态。从 React v16.8.0 开始，React 通过`useState`方法为我们提供了这种新方式。

> `useState`是本课程中您将看到的许多“挂钩”中的第一个。让这篇文章的其余部分作为一个软介绍。在未来的章节中，我们将更深入地研究`useState`以及其他钩子。

`useState`接受单个参数，状态的初始值。它返回的是一个数组，第一项是状态，第二项是更新状态的函数。

```
const loadingTuple = React.useState(true)
const loading = loadingTuple[0]
const setLoading = loadingTuple[1]

...

loading // true
setLoading(false)
loading // false 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，单独抓取数组中的每一项并不是最佳的开发体验。这只是为了演示`useState`如何返回一个数组。通常，您会使用数组析构来获取一行中的值。

```
// const loadingTuple = React.useState(true)
// const loading = loadingTuple[0]
// const setLoading = loadingTuple[1]

const [ loading, setLoading ] = React.useState(true) // 👌 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们用新发现的关于`useState`钩子的知识来更新我们的`ReposGrid`组件。

```
function ReposGrid ({ id }) {
  const [ repos, setRepos ] = React.useState([])
  const [ loading, setLoading ] = React.useState(true)

  if (loading === true) {
    return <Loading />
  }

  return (
    <ul>
      {repos.map(({ name, handle, stars, url }) => (
        <li key={name}>
          <ul>
            <li><a href={url}>{name}</a></li>
            <li>@{handle}</li>
            <li>{stars} stars</li>
          </ul>
        </li>
      ))}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-usestate-ddhc4)T3】**

*   State ✅
*   生命周期方法
*   共享非视觉逻辑

##### 生命周期方法

这里有件事可能会让你难过(或者开心？).当使用 React 钩子时，我希望你把你所知道的关于传统的 React 生命周期方法以及那种思维方式的一切都忘掉。我们已经看到了按照组件的生命周期来思考的问题——“这种[生命周期]划分自然迫使我们在整个组件中散布相关的逻辑。”相反，从**同步**的角度来考虑。

回想一下您使用生命周期事件的任何时候。无论是设置组件的初始状态、获取数据、更新 DOM，还是任何事情——最终目标总是同步。通常，在 React land 之外同步一些东西(API 请求、DOM 等)。)与 React land(组件状态)内部的某个东西，反之亦然。

当我们考虑同步而不是生命周期事件时，它允许我们将相关的逻辑片段组合在一起。为此，React 给了我们另一个名为`useEffect`的钩子。

定义，`useEffect`让你在函数组件中执行副作用。它有两个参数，一个函数和一个可选数组。函数定义要运行的副作用，而(可选)数组定义何时“重新同步”(或重新运行)效果。

```
React.useEffect(() => {
  document.title = `Hello, ${username}`
}, [username]) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，传递给`useEffect`的函数将在`username`改变时运行。因此，将文档的标题与`Hello, ${username}`决定的内容同步。

现在，我们如何在代码中使用`useEffect`钩子来同步`repos`和我们的`fetchRepos` API 请求呢？

```
function ReposGrid ({ id }) {
  const [ repos, setRepos ] = React.useState([])
  const [ loading, setLoading ] = React.useState(true)

  React.useEffect(() => {
    setLoading(true)

    fetchRepos(id)
      .then((repos) => {
        setRepos(repos)
        setLoading(false)
      })
  }, [id])

  if (loading === true) {
    return <Loading />
  }

  return (
    <ul>
      {repos.map(({ name, handle, stars, url }) => (
        <li key={name}>
          <ul>
            <li><a href={url}>{name}</a></li>
            <li>@{handle}</li>
            <li>{stars} stars</li>
          </ul>
        </li>
      ))}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-useeffect-0m35p)T3】**

很圆滑，对吧？我们已经成功地摆脱了`React.Component`、`constructor`、`super`、`this`，更重要的是，我们不再让我们的效果逻辑分散(和重复)在整个组件中。

*   State ✅
*   生命周期方法✅
*   共享非视觉逻辑

##### 共享非可视逻辑

前面我们提到 React 没有很好地解决共享非可视化逻辑的原因是因为“React 将 UI 耦合到一个组件”。这导致过于复杂的模式，如[高阶组件](https://tylermcginnis.com/react-higher-order-components/)或[渲染道具](https://tylermcginnis.com/react-render-props/)。现在你可能已经猜到了，Hooks 对此也有一个答案。然而，很可能不是你想的那样。没有用于共享非可视逻辑的内置挂钩，相反，您可以创建自己的自定义挂钩，与任何 UI 分离。

我们可以通过创建自己的定制`useRepos`钩子来看到这一点。这个钩子将接受我们想要获取的回购的一个`id`，并且(遵循类似的 API)将返回一个数组，其中第一项是`loading`状态，第二项是`repos`状态。

```
function useRepos (id) {
  const [ repos, setRepos ] = React.useState([])
  const [ loading, setLoading ] = React.useState(true)

  React.useEffect(() => {
    setLoading(true)

    fetchRepos(id)
      .then((repos) => {
        setRepos(repos)
        setLoading(false)
      })
  }, [id])

  return [ loading, repos ]
} 
```

Enter fullscreen mode Exit fullscreen mode

好的一点是，任何与获取我们的`repos`相关的逻辑都可以在这个定制钩子中抽象出来。现在，不管我们在哪个组件中，即使它是非可视化逻辑，只要我们需要关于`repos`的数据，我们就可以使用我们的`useRepos`定制钩子。

```
function ReposGrid ({ id }) {
  const [ loading, repos ] = useRepos(id)

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
function Profile ({ user }) {
  const [ loading, repos ] = useRepos(user.id)

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

**[💻玩代码。](https://codesandbox.io/s/why-react-hooks-custom-hooks-t4rqv)T3】**

*   State ✅
*   生命周期方法✅
*   共享非可视化逻辑✅

* * *

钩子的市场宣传是，你可以在函数组件中使用状态。实际上，钩子远不止这些。它们是关于改进的代码重用、组合和更好的默认值。我们还需要介绍更多的钩子，但是现在你知道了它们为什么存在，我们有了一个坚实的基础。

* * *

这篇文章最初发表在 TylerMcGinnis.com，是我们 T2 反应钩子课程的一部分。如果你喜欢这篇文章，看看吧。