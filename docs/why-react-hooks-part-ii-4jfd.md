# 为什么使用 React 钩子(第二部分:可重用代码)

> 原文：<https://dev.to/dinhhuyams/why-react-hooks-part-ii-4jfd>

## 先决条件:关于[反应](https://reactjs.org/)的基础知识

这篇文章旨在让你了解**如何反应钩子**帮助我们分享组件之间的**共同逻辑**和**使用钩子和其他方法之间的区别**。

> 如果你还没有看过第一部分的话，看看吧

你花了两个小时在你的组件中编写一个漂亮的功能，然后仅仅一分钟后，你的老板想要**同样的东西**...但是在另一个组件中。你现在应该做什么？

> 😅简单，我会做一些复制+粘贴的东西

[![Copy-paste](img/751ecf5682fe00e762a07b9666a41e03.png "Copy paste")](https://res.cloudinary.com/practicaldev/image/fetch/s--z3GDCTt4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/26871154/64095209-da0aa600-cd66-11e9-85f9-8cea2dcf7d9b.jpg)

作为一名 React 开发人员，您有时会遇到这样的情况，您必须在组件之间**共享一些公共逻辑**。**高阶组件(hoc)**在 React 社区中以解决这类问题而闻名。然而，我发现对于初学者来说, **HOCs** 有点复杂，而 Hooks 让这个想法变得更加简单和清晰。

## 💪我们开始吧！

‍Our 今天的任务是帮助**凯里**教她的淘气双胞胎[扎克和科迪](https://www.imdb.com/title/tt0426371/)，如果他们做了很多次坏事，她会对他们大喊大叫

Zack.jsx

```
class Zack extends React.Component {

    state = {
        numberOfBadActions: 0
    }

    componentDidUpdate(prevProps, prevState) {
       if (
        prevState.numberOfBadActions !== this.state.numberOfBadActions && 
        this.state.numberOfBadActions === 3
       ) {
        console.log('Use your head!')
        this.setState({numberOfBadActions: 0})
       }
    }

    doBadAction = () => {
        this.setState(state => ({numberOfBadActions: state.numberOfBadActions + 1}))
    }

    render() {
        return (
            <div>
                <p>Number of bad actions: {this.state.numberOfBadActions}</p>
                <button onClick={this.doBadAction}>Do bad action</button>
            </div>
        )
    }
}

export default Zack 
```

Enter fullscreen mode Exit fullscreen mode

看看科迪，我也得冲他吼！😫

## 0。复制+粘贴

我刚刚复制了 **Zack.jsx** ，将文件重命名为 **Cody.jsx** ，并将组件名也改为 Cody

Cody . jsx

```
class Cody extends React.Component {

    state = {
        numberOfBadActions: 0
    }

    componentDidUpdate(prevProps, prevState) {
       if (
        prevState.numberOfBadActions !== this.state.numberOfBadActions && 
        this.state.numberOfBadActions === 3
       ) {
        console.log('Use your head!')
        this.setState({numberOfBadActions: 0})
       }
    }

    doBadAction = () => {
        this.setState(state => ({numberOfBadActions: state.numberOfBadActions + 1}))
    }

    render() {
        return (
            <div>
                <p>Number of bad actions: {this.state.numberOfBadActions}</p>
                <button onClick={this.doBadAction}>Do bad action</button>
            </div>
        )
    }
}

export default Cody 
```

Enter fullscreen mode Exit fullscreen mode

> 我很想拍拍自己的背，因为我只用了 1 秒钟就解决了这个问题😎

现在看起来不错，直到后来凯里决定改变教育她儿子的方法。在这种情况下，我们最终同时更新了 **2 的组件**，你能想象如果她也不得不对她儿子的朋友做同样的事情会怎么样吗？这很棘手，因为我们必须到处复制和粘贴代码，困难的是，如果需要更改与逻辑相关的内容，我们必须更新所有这些组件😖

## 1。高阶分量

> 高阶分量是接受一个分量并返回一个新分量的函数

在我们的例子中，您需要做的就是将所有与教学相关的东西从 React 组件中取出，并将其移入一个特设的

```
export const withMom = (WrappedComponent) => {
    return class WithMom extends React.Component {
         state = {
             numberOfBadActions: 0
         }

         componentDidUpdate(prevProps, prevState) {
            if (
              prevState.numberOfBadActions !== this.state.numberOfBadActions && 
              this.state.numberOfBadActions === 3
            ) {
              console.log('Use your head!')
              this.setState({numberOfBadActions: 0})
            }
         }

         doBadAction = () => {
            this.setState(state => ({numberOfBadActions: state.numberOfBadActions + 1}))
         }

         render() {
            return (
                <WrappedComponent 
                    numberOfBadActions={this.state.numberOfBadActions} 
                    doBadAction={this.doBadAction}
                    {...this.props}/>
                )   
         }   
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**withMom HOC** 是一个函数，它接受一个参数作为组件，并返回一个新的和增强的组件，其中包含所有与教学相关的逻辑。现在你可以使用**和 Mom HOC** 来包装组件，如下所示👇：

Zack.jsx

```
class Zack extends React.Component {
    render() {
        return (
            <div>
                <p>Number of bad actions: {this.props.numberOfBadActions}</p>
                <button onClick={this.props.doBadAction}>Do bad action</button>
            </div>
        )
    }
}

export default withMom(Zack) 
```

Enter fullscreen mode Exit fullscreen mode

Cody . jsx

```
class Cody extends React.Component {
    render() {
        return (
            <div>
                <p>Number of bad actions: {this.props.numberOfBadActions}</p>
                <button onClick={this.props.doBadAction}>Do bad action</button>
            </div>
        )
    }
}

export default withMom(Cody) 
```

Enter fullscreen mode Exit fullscreen mode

HOC 帮助你以一种更好的方式组织你的代码。在我们的例子中， **Zack 和 Cody** 组件不再关心教学逻辑，因为现在 **withMom HOC** 封装了该逻辑并将其传递给被包装的组件。令人惊奇的是，如果 Carey 想改变她的方法，我们需要做的只是修改代码中的位置。

😓不幸的是，使用 hoc 有一个很大的缺点。想象你有不止一个 HOC 可以使用，然后你开始面对控制所有传递道具和[包装地狱](https://i.redd.it/pdvkpgjq6vo11.jpg)问题
的问题

```
export default withGrandPa( 
    withDad(
        withMom(
            Cody
        )
    )
) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们的 DOM 看起来是这样的

```
<WithGrandPa>
    <WithDad>
        <WithMom>
            <Cody/>
        </WithMom>
    </WithDad>
</WithGrandPa> 
```

Enter fullscreen mode Exit fullscreen mode

> **正常人:**包装地狱
> 
> 作为知识分子的我:我的亚洲家庭清洁建筑👪

[![](img/1ac5e50d12c3f035ed27e58740e19a8a.png "Asian family")](https://res.cloudinary.com/practicaldev/image/fetch/s--N4F6tAtV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/26871154/64095235-ed1d7600-cd66-11e9-93d6-1d84f209ccca.png)

## 2。反应钩

🔥这将是一个很长的故事，和我呆在一起，我会一步一步的教你。

> 如果你还没有读过这个系列的第一部分，我建议你先读一遍，然后再回来，否则你可能会迷路

**步骤 1:** 将 Zack 组件转换为函数组件

```
const Zack = () =>  {

    const [numberOfBadActions, setNumberOfBadActions] = React.useState(0)

    React.useEffect(() => {
        if (numberOfBadActions === 3) {
            console.log('Use your head!')
            setNumberOfBadActions(0)
        }
    }, [numberOfBadActions])

    const doBadAction = () => {
        setNumberOfBadActions(numberOfBadActions => numberOfBadActions + 1)
    }

    return (
      <div>
        <p>Number of bad actions: {numberOfBadActions}</p>
        <button onClick={doBadAction}>Do bad action</button>
      </div>               
    )
}

export default Zack 
```

Enter fullscreen mode Exit fullscreen mode

**第二步:**编写自定义钩子

> 自定义钩子是一个 JavaScript 函数，它的名字以“use”开头，可以调用其他钩子。

```
 const useYourHead = (initialNumberOfBadActions) => {
    const [numberOfBadActions, setNumberOfBadActions] = React.useState(initialNumberOfBadActions)

    React.useEffect(() => {
        if (numberOfBadActions === 3) {
            console.log('Use your head!')
            setNumberOfBadActions(0)
        }
    }, [numberOfBadActions])

    const doBadAction = () => {
        setNumberOfBadActions(numberOfBadActions => numberOfBadActions + 1)
    }

    return [numberOfBadActions, doBadAction]
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们需要在两个函数之间共享逻辑时，我们将逻辑提取给第三个函数。当我们想要在 React 组件之间共享逻辑时，同样的事情也适用，因为它们是函数，而 Hook 也是函数。

我刚刚提取了我们想要共享给一个名为`useYourHead`的**定制钩子**的所有代码。我们需要考虑这个函数应该接受什么参数，它应该返回什么，因为**自定义钩子**只是一个普通的函数。在我们的例子中，`useYourHead`接受错误动作的初始数量，并返回`numberOfBadActions`和`doBadAction`

第三步:使用我们定制的挂钩

```
const Zack = () =>  {

    const [numberOfBadActions, doBadAction] = useYourHead(0)

    return (
        <div>
            <p>Number of bad actions: {numberOfBadActions}</p>
            <button onClick={doBadAction}>Do bad action</button>
        </div>                 
    )
}

export default Zack 
```

Enter fullscreen mode Exit fullscreen mode

```
const Cody = () =>  {

    const [numberOfBadActions, doBadAction] = useYourHead(0)

    return (
        <div>
            <p>Number of bad actions: {numberOfBadActions}</p>
            <button onClick={doBadAction}>Do bad action</button>
        </div>                 
    )
}

export default Cody 
```

Enter fullscreen mode Exit fullscreen mode

## 3。结论:

🚀钩子帮助我们注入**可重用的逻辑**来对组件做出反应，而不会创建 hoc。正如你所看到的，我们不必处理**包装器地狱**的问题，或者通过许多组件层传递道具的问题。👏👏👏

这里有一些很好的资源供您参考:

*   [反应 HOCs](https://www.robinwieruch.de/gentle-introduction-higher-order-components)
*   [自定义挂钩](https://reactjs.org/docs/hooks-custom.html)
*   肯特·c·多兹的现代反应工作室(第一部分)
*   肯特·c·多兹的现代反应工作室(第二部分)

## 🙏 💪感谢阅读！

*请在下面留下你的评论，让我知道你对这篇文章的看法*

### ✍️所写

胡伊特林🔥 🎩♥️ ♠️ ♦️ ♣️🤓

软件开发者|魔法爱好者

打招呼👋在

✅ [Github](https://github.com/HuyAms)

✅ [领英](https://www.linkedin.com/in/huy-trinh-dinh-253534131/)

✅ [Medium](https://medium.com/@trnhnhhuy)