# 从 React 到 React-Redux 只需几个步骤

> 原文：<https://dev.to/brightdevs/from-react-to-react-redux-in-a-few-steps-e1m>

在这篇文章中，我将重点关注连接`React`组件和`Redux`。如果你刚刚开始使用`React + Redux`或者你以前已经使用过这些，但是想让这个概念更清晰一点，那么请阅读这篇文章直到最后；)

如果你想对 Redux 中的流程有更多的了解，你可以看看我之前的[关于我如何理解 Redux 架构](https://brightinventions.pl/blog/redux-how-do-i-understand-it/)的帖子。

## 在我们介绍 Redux 之前

在我们深入研究`Redux`之前，让我们先来看看简单的`React`组件。它看起来像什么？
为了让它更清楚一点——让我们使用带接口的类型脚本来显示我们在组件中期望的`props`(输入数据)。

```
interface Props {
    title: string
    numbers: number[]
}

interface State {}

export class SimpleComponent extends React.Component<Props, State> {

  render() {
    return <div>
      <h1>{this.props.title}</h1>
      {this.props.numbers.map(number => <p>{number}</p>)}
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个组件接受两个输入参数- `title`和`numbers`。如果我们想在我们的应用程序中显示它，我们需要手动传递它们。比如:

```
<SimpleComponent title='Test' numbers={[1,2,3,4]}/> 
```

Enter fullscreen mode Exit fullscreen mode

## 介绍 Redux

我想在每个开发人员的生活中，总会有这样的时候，人们会无缘无故地想让事情变得更复杂，所以让我们向我们的例子介绍一下`Redux`。(声明:这是个玩笑)。

我们真的需要`Redux`吗？我们先来看一个没有`Redux`的 app 的例子。

```
interface Props {}
interface State {}
export class FatComponent extends React.Component<Props, State> {
    render() {
        return <div>
            <SimpleComponent title='Test' numbers={[1,2,3,4]}/>
            <SimpleComponent title='Test' numbers={[1,2,3,4]}/>
            <SimpleComponent title='Test' numbers={[1,2,3,4]}/>
            <SimpleComponent title='Test' numbers={[1,2,3,4]}/>
        </div>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`FatComponent`显示 4 个具有相同数字的`SimpleComponents`。
假设我们希望在我们所有的`SimpleComponents`中共享相同的号码。
一个好方法是将这些数据转移到一个地方(父节点)。在这种情况下，我们的`FatComponent`是一个很好的选择。

```
interface Props {}
interface State {
    numbers: number[]
}
export class FatComponent extends React.Component<Props, State> {

    constructor(props) {
        super(props)
        this.state = { numbers: [1, 2, 3, 4] }
    }

    render() {
        const { numbers } = this.state
        return <div>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
        </div>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们的`FatComponent`不能立即获得这些数据，并且需要下载，那该怎么办呢？
让我们使用`componentDidMount`中的`fetchNumbers`方法。

```
interface Props {}
interface State {
    numbers: number[]
}
export class FatComponent extends React.Component<Props, State> {

    constructor(props) {
        super(props)
        this.state = { numbers: [] } // initially we have an empty numbers array
    }

    // async await - https://javascript.info/async-await
    async componentDidMount() {
        const numbers = await fetchNumbers() // this is my imaginary function that will provide me with numbers
        this.setState({ numbers })
    }

    render() {
        const { numbers } = this.state
        return <div>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
        </div>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们有了一个知道如何获取数字并在`SimpleComponents`中显示它们的组件。太好了！
但是如果我们想要重用我们的`FatComponent`并呈现来自不同来源的数字呢？
如果我们不想每次装载组件时都获取数据，该怎么办？毕竟，我们可以一次性获取这些数据，并在将来使用它们。如果我们想使用不同的初始数组呢？为了做到这一点，我们可以向`FatComponent`添加参数，并从呈现我们的`FatComponent`的父节点传递这些参数。

```
 interface Props {
    // we moved numbers from State to Props as our FatComponent will not control the source of the numbers
    numbers: number[]
    // this is a method that FatComponent will call when it decides that it wants to refresh its numbers
    // we expect that if we call it, then FatComponent's parent will handle fetching the data and pass it to our component,
    // that's why we use "() => void" type
    refreshNumbers: () => void
}
interface State {
}
export class FatComponent extends React.Component<Props, State> {

    // async await is no longer needed here as we tell our parent to load data for us.
    componentDidMount() {
        this.props.refreshNumbers()
    }

    render() {
        const { numbers } = this.props // we no longer have numbers in state - we need to change it to props
        return <div>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
            <SimpleComponent title='Test' numbers={numbers}/>
        </div>
    }
}

interface BigBossProps {}
interface BigBossState {
    numbers: number[]
}
export class BigBossParent extends React.Component<BigBossProps, BigBossState> {

    constructor(props) {
        super(props)
        this.state = { numbers: [] }
    }

    async onFetchNumbers() {
        // if we fetched numbers before, then we won't do it again
        const hasDataOrPendingRequest = // check pending request && data existence
        if (!hasDataOrPendingRequest) {
            const numbers = await fetchNumbers() // this is my imaginary function that will provide me with numbers
            this.setState({ numbers })
        }
    }

    render() {
        return <FatComponent numbers={this.state.numbers} // we present numbers from BigBossParent's state in FatComponent
            refreshNumbers={this.onFetchNumbers.bind(this)}/> // Bind with `this` in order to use BigBossParent component as `this` in `onFetchNumbers` method
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们的`BigBossParent`中的渲染逻辑发生变化，它将有条件地渲染`FatComponent`，我们将会遇到这样的情况，即`onFetchNumbers`将被多次调用。这里的问题是我们的`BigBossParent`相当智能，所以它不会下载任何新数据，而是重用旧阵列。
不过话说回来。如果在某个时候我们决定`unmount` `BigBossParent`，那么我们将会丢失保存在那里的状态，我们将不得不再次获取它。
如果我们想避免这种情况，我们可以将状态移动到...你猜对了！另一位家长。
这就是`Redux`帮助我们的地方。`Redux`为我们提供了一种将应用程序的状态保存在一个名为`Store`的统一“父”中的方法，它将把应用程序的状态提供给我们呈现的组件。
使用`Redux`，您将能够:

*   将您的应用程序状态保存在一个地方- `Store`
*   用一种更简单的方式为你的应用程序的状态变化编写测试，因为你可以从 UI 部分中分离出来测试它。
*   使用一种统一的方式来改变这种状态(通过`Actions`和`Reducers`)，当项目增长并且您需要围绕它移动时，这就很方便了。

请记住,`Redux`不是必须的，如果您觉得不需要它，就不必在应用程序中使用它！- [你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367) 。但是让我们假设我们想要将`Redux`引入到我们的例子中，并且在这个统一的`Store`中保留数字。有许多方法可以帮助我们做到这一点。我个人喜欢的广泛使用的方法是将您的主要父节点`components`与`Store`(在我们的例子中是`BigBossParent`)连接起来，然后通过它们的`props`将所需的数据传递给它的子节点。通过这种方式，被渲染的孩子不会意识到任何`Redux`魔法，如果我们在某个时候决定放弃`Redux`，那么我们所有的“dumber”(不连接到 store)组件都不需要任何改变。

我们如何将我们的`BigBossParent`连接到存储(存放数据的`Redux`中的位置)？
首先，我们需要像指定`FatComponent`一样指定`BigBossParent`的输入道具。
就像以前一样，我们把不想控制的东西移到`BigBossProps`那里，我们希望呈现这个组件的东西会
照顾它们并给它使用。

```
 interface BigBossProps {
    numbers: number[] // numbers will be provided to BigBossParent
    refreshNumbers: () => void // BigBossProps will not fetch the data on its own (just as FatComponent)
}
interface BigBossState {}
// we do not export this method anymore
// Please remember that we COULD do it and use this component as any other React component
class BigBossParent extends React.Component<BigBossProps, BigBossState> {

    render() {
        // If FatComponent decides to refreshNumbers, our BigBossParent will pass this request to its parent.
        return <FatComponent numbers={this.props.numbers}
            refreshNumbers={this.props.refreshNumbers()}/>
    }
}

export const connectedComponent = ... // we will get to this later 
```

Enter fullscreen mode Exit fullscreen mode

但是谁来负责渲染我们的`BigBossParent`？我们将在我们的应用程序“root”中呈现它，它将连接到`Redux`。
让我们想象这里的`div`是我们应用程序的根。这里首先要呈现的是`Provider`。
提供者，`createStore`通过`react-redux`包可用，它将负责提供在其中渲染的组件，并通过
与主应用程序`store`连接。我们将能够从中获取状态，并对其应用更改(现在让我们专注于“获取”状态)。
Provider 将接收一个参数——一个将使用`reducer`创建的商店(我们现在不关注它们)。

```
 <div>
        <Provider store={createStore(reducer)}>
            <BigBossParent /> // Where are the props that we normally would pass here?
            // Why don't we do it in as before?
            // <BigBossParent numbers={} refreshNumbers={}/>
        </Provider>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

在我们进入`BigBossParent`组件之前，让我们为应用程序中的状态定义一个接口。
我的意思是，每次我们从商店(我们用`createStore(reducers)`创建的)获取状态时，我们都希望它是`ApplicationState`类型的。

```
interface ApplicationState {
    numbers: number[]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用从`react-redux`包中获得的`connect`，而不是以通常的方式将道具传递给 BigBossParent。

```
 interface BigBossProps {
    numbers: number[] // numbers will be provided to BigBossParent
    refreshNumbers: () => void // BigBossProps will not fetch the data on its own (just as FatComponent)
}
interface BigBossState {}
// we will not export the old component
class BigBossParent extends React.Component<BigBossProps, BigBossState> {

    render() {
        // If FatComponent decides to refreshNumbers, our BigBossParent will pass this request to its parent.
        return <FatComponent numbers={this.props.numbers}
            refreshNumbers={this.props.refreshNumbers()}/>
    }
}

// This method will receive the application state in a first parameter
// its job is to take the part of the application state that BigBossParent is interested in and return it
// In this method we would like to exactly match the props that BigBossParent expects, however, we will not care about
// methods. (We will not provide refreshNumbers method through mapStateToPros)
function mapStateToProps(state: ApplicationState) {
    // this method will return object has "numbers" with a value of numbers that are kept in our application state
    return {
        numbers: state.numbers
    }
}

// This method will receive dispatch method as a first parameter
// The dispatch will allow us to send actions to the store.
// (if this concept is unfamiliar to you, please take a look at Redux documentation or my previous post - http://eliaszsawicki.com/story-of-redux/ )
function mapDispatchToProps(dispatch: Redux.Dispatch) {
    return {
        refreshNumbers: () => dispatch({
            type: 'UPDATE_NUMBERS',
            payload: { numbers: [1, 2, 3, 4, 5]}
        })
    }
}
// instead we will export the component that is connected to our application store.
// this means that the props that the BigBossParent component needed will be provided via our mapping functions
// functions through mapDispatchToProps and variables through mapStateToProps
export const connectedComponent = connect(mapStateToProps, mapDispatchToProps)(BigBossParent) 
```

Enter fullscreen mode Exit fullscreen mode

我们来快速看一下`Reducer`。你记得我们已经把它作为我们的`createStore`参数传递了吗？
`Reducer`是一个接受两个参数- `state`和`action`并返回一个新的`state`的函数。

```
const DefaultState = { numbers: [] } // if we do not have a state yet (start of the app), we need to provide a default one
function reducer(state: ApplicationState = DefaultState, action: Action): ApplicationState {
    switch (action.type) {
        case 'UPDATE_NUMBERS': // This is the action type that we sent from our BigBossParent component.
            const newState = { numbers: action.payload.numbers }
            return newState
    }
    return state
} 
```

Enter fullscreen mode Exit fullscreen mode

在真正简化的情况下，我们将有一个 reducer 来处理我们的整个状态，但在更大的应用程序中，我们将有组合的 reducer，它们只将应用程序状态的一部分作为第一个参数。他们知道如何处理的部分。
`UPDATE_NUMBERS`是我们从 BigBossParent 组件发送的动作类型。我们再来看看`mapDispatchToProps`:

```
//the dispatch parameter is in fact way to call `store.dispatch()`.
function mapDispatchToProps(dispatch: Redux.Dispatch<ApplicationState>) {
    return {
        refreshNumbers: () => dispatch({
            type: 'UPDATE_NUMBERS',
            payload: { numbers: [1, 2, 3, 4, 5]}
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这张地图是做什么的？当时我们从`BigBossParent`组件调用`refreshNumbers`。事实上发生的是:

```
store.dispatch({
            type: 'UPDATE_NUMBERS',
            payload: { numbers: [1, 2, 3, 4, 5]}
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们向我们的商店发送一个操作。Store 接收动作，然后将应用程序状态和该动作传递给 reducer(在我们的例子中，这是上面提到的 reducer)。它看到动作类型与它处理的动作类型相匹配- `UPDATE_NUMBERS`并相应地创建
一个新状态。在我们的例子中，它将应用作为动作有效载荷发送的数字。完成后，新的状态被返回并应用到`store`。
这将是我们应用程序的新状态。当我们收到这个新状态时，我们的`BigBossParent`将被更新(映射函数将被再次调用)。

这就是你如何从一个`React`到`React-Redux`；)
如果你有什么意见，请在下面分享！

*本文与[我的个人博客](https://eliaszsawicki.com/)交叉发布。*