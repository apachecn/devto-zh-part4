# 效果不是生命周期

> 原文：<https://dev.to/samsch_org/effects-are-not-lifecycles-551o>

你不能用 useEffect 写生命周期。

随着 React hooks 被广泛认为比使用 React 社区中的类“更好”,无论是对新用户还是对有经验的开发人员来说，有一种广泛的开发人员迁移模式来学习新工具。

大多数开发人员都带来了他们已经习惯的 React 类的概念，甚至来自非 React 框架或工具。其中一些很容易直接转换过来:如果你习惯了类状态，掌握`useState`并不十分困难，而且一旦他们掌握了钩子如何保持状态的基本概念，`useRef`对许多人来说也相当简单。

([原文发表于此](https://samsch.org/2019/07/23/effects-are-not-lifecycles/))

## 生命周期是你做事的“时间”

React 类组件作者习惯在生命周期中编写功能，生命周期不存在钩子。如果你小心的话，你可以*模仿*它们，也许使用一些`useRef`实例来引用由于闭包而改变的道具。但是模仿生命周期是一个坏主意，原因是:效果是比生命周期更高层次的抽象。

当您使用像 componentDidMount 或 componentDidUpdate 这样的生命周期时(更不用说那些在不同阶段运行的过时的生命周期了)，您必须根据*来考虑什么时候*应该发生什么。"我希望在组件挂载时加载数据.""我想在组件用新的 X 属性更新时加载数据."这个“什么时候”的想法就是程序性思维。“何时”的概念实际上并不重要，但是因为完成这些任务的工具是生命周期，所以您需要将您想要做的“什么”映射到特定生命周期的“何时”。

我是来告诉你忘记这一切的。说真的，完全忘记“何时”这个概念。你不在乎*什么时候*有事情发生。你真的不知道。你觉得你会为了这件事吗？你不知道。

## 效果是“什么”，不是“什么时候”

React 是一个严格的模型。这是它如此强大和灵活的部分原因。模型说“给定 X 状态，视图应该是 viewFunction(X)”。在很长一段时间里，我们不得不打破这种模式来处理任何不是直观输出的东西。代替“给定 X 状态，执行 effectFunction(X)”的是，当我们希望这些事情发生时，我们必须分解*，并将它们分类成生命周期方法。*

用`useEffect`，你说“给定 X 状态，做 effectFunction(x)”。现在重要的是*你的状态是什么*，以及*在这种状态下你应该做什么*。“什么时候”不再重要了。对于生命周期，您可以在 componentDidMount 中异步加载数据。你在蒙特做到了，因为你知道以前没有人这样做过。但是你真的在乎它在 mount 吗？如果数据还没有被加载，那么加载数据*不是最重要的吗？*所以我们把它归结为重要的部分:如果我们的数据还没有加载，那么加载数据。

这个概念就是`useEffect`的工作原理。我们不关心组件是否正在安装，我们只是在我们的 useEffect 中写下，如果数据还没有被加载，我们希望数据被加载。更重要的是，从高层次来看，我们通常甚至不关心它是否多次加载数据，只关心数据被加载。

## 代码是什么样子的

现在我们已经把我们想做的事情简化了。"当数据未加载时，加载数据."

天真的方法看起来是这样的:

```
const [isLoaded, setLoaded] = useState(false);
const [data, setData] = useState(null);

useEffect(() => {
  if (isLoaded === false) {
    loadData().then(data => {
      setData(data);
      setLoaded(true);
    });
  }
}); 
```

这个代码*起作用*。考虑到我们对 T2 想要什么的概念，这是最天真的方法，但它工作得非常好。

> 可以说，还有更天真的方法，但是我们在这里假设我们已经知道了*如何使用*钩子，所以我们不考虑将`useEffect()`放入条件中，因为这是一个已知的错误。

让我们将它与使用`[]`作为第二个参数来模拟`componentDidMount`的代码进行比较。

```
const [data, setData] = useState(null);

useEffect(() => {
  loadData().then(data => {
    setData(data);
    setLoaded(true);
  });
}, []); 
```

乍一看，涉及的代码较少，您可能会认为这是一件好事。但是这段代码并没有很好地描述这种情况。我们有*隐*态。它*看起来*好像`loadData()`每次都应该运行，因为没有*语义*代码说它不会。换句话说，我们没有*描述*代码实际上应该做什么。如果您删除了`[]`，那么这段代码看起来几乎相同，但就是不能正常工作(它总是加载数据，而不是只在我们需要时加载)。更重要的是，无论如何，我们很可能需要 render 中的加载状态，尽管您可以假设`null`数据意味着它没有被加载，但是您通过重载变量的含义违反了单一责任原则。

这是一个非常常见的绊脚石，人们在学习钩子时会被绊倒，因为他们试图模仿生命周期。

## 优化

现在，出于实际目的，我们*不*实际上希望`loadData`函数被调用不止一次。如果你遵循`useEffect`依赖参数(每一个外部引用)中最简单的应用，这将被自动修复:

```
const [isLoaded, setLoaded] = useState(false);
const [data, setData] = useState(null);

useEffect(() => {
  if (isLoaded === false) {
    loadData().then(data => {
      setData(data);
      setLoaded(true);
    });
  }
}, [isLoaded, loadData, setData, setLoaded]); 
```

这两个 setters 不会改变，但是它们在语义上是函数的一部分，也许以后它们会被可能改变的东西所取代。我们现在假设`loadData`不会改变(如果改变了，它只会在 `isLoaded`仍然是`false`的情况下触发新的呼叫*)。我们这里的关键依赖项是`isLoaded`。第一遍，React 自动运行效果，`isLoaded`为假，所以调用`loadData()`。如果组件在`isLoaded`仍然为假时再次渲染，deps 不会改变，因此效果不会再次运行。*

一旦`loadData()`解决，`isLoaded`被设置为真。效果再次运行，但是这一次条件为假，所以不调用`loadData()`。

重要的是，依赖参数*并没有改变*我们的功能，它只是减少了对函数不必要的调用。

## 但是那些*不应该*多次加载的东西呢！

啊，对了。也许是打了一个电话，改变了其他地方的一些东西。当需要时，它应该只被调用一次。

这意味着我们的“什么”变了。不再是“若未加载，加载数据”，而是现在:“若未加载，*且尚未加载*，加载数据。”因为我们的“什么”改变了，我们的语义代码也应该改变。

我们可以简单地添加一个`isLoading`状态，但是我们可能会发生一些令人困惑的事情，比如`isLoading`和`isLoaded`都为真！由于这些状态应该是*独占*，这意味着它们也是*相关的*。而且不止相关，它们实际上是*相同的*状态字段(数据状态)，只是值不同。

所以现在我们改变我们的州代码来反映我们新的“什么”:

```
const [dataStatus, setDataStatus] = useState('empty');
const [data, setData] = useState(null);

useEffect(() => {
  if (dataStatus === 'empty') {
    loadData().then(data => {
      setData(data);
      setDataStatus('available');
    });
    setDataStatus('loading');
  }
}); 
```

现在我们有了代码，当我们需要它时，只有*调用`loadData()`，而它还没有被加载，并且它没有使用`useEffect`的依赖参数。*

此外，我们州的不同部分都明确地包括在这里。

## 告诉我怎么办！

因此，忘记生命周期、安装、更新以及通常“何时”发生的事情。把它完全抛在脑后。

想想*你需要做什么*，以及*导致这些事情发生的状态是什么*。

在您的代码中显式地对这些状态建模，并对基于这些状态运行的效果建模。

您的代码应该*总是*工作，而不使用`useEffect`的第二个参数。如果你*需要*，第二个参数，你可能是错误地编写了你的功能。