# React 的 useEffect 和 useRef 为凡人解释

> 原文：<https://dev.to/leewarrickjr/react-s-useeffect-and-useref-explained-for-mortals-2ddk>

[![Photo of the Acropolis of Athens, Athens, Greece by Cristina Gottardi](img/78f1d06ff4823f91e8fccc228a64c6b9.png "Photo of the Acropolis of Athens, Athens, Greece by Cristina Gottardi")](https://leewarrick.com/blog/static/fdb92914fc9093dee6a2c20dad8fbd50/c35de/acropolis.jpg)

*最初发表于[leewarrick.com/blog](https://leewarrick.com/blog/react-use-effect-explained/)T3】*

如果 React docs 让你望尘莫及，或者 Dan Abramov 的[博客](https://overreacted.io/a-complete-guide-to-useeffect/)让你觉得你在读一份用古希腊文写的奥林匹斯山传下来的卷轴，你并不孤单。

有时，反应神的众神很难将他们的智慧翻译成我们其他人能理解的语言。事情通常是这样的:你在 React 上遇到了麻烦，谷歌一下你的问题，然后阅读一篇博客或 Stack Overflow 帖子，上面有一些建议，这让你感到比开始时更失落。

我已经成为这种情况的受害者很多很多次了。尤其是有了`useEffect`。在我们深入这个特殊的钩子之前，让我说我喜欢用钩子写 React，并且不想回到课堂上。这么说，`useEffect`是一大痛点。

(注:如果你想看下面的代码片段，请访问[原始帖子](https://leewarrick.com/blog/react-use-effect-explained/)。)

## 究竟什么是“效果”？

为了真正理解`useEffect`，让我们稍微回顾一下，谈谈编程和 JavaScript。

`useEffect`被命名的效应是我们亲切地称之为“副作用”。

那么什么是副作用呢？这是一段代码，它伸出手去…做一些别的事情。这是一个抽象的概念，我们来举例说明。

这里有一个没有副作用的函数:

```
function add(a, b) {
    return a + b
} 
```

函数`add`除了接受一个输入并返回一个输出之外什么也不做。它不会触及自身之外去扰乱其他任何东西！

先介绍一个副作用。

```
const resultDiv = document.getElementById('add-example')
function add(a, b) {
    const result = a + b
    resultDiv.textContent = `The Result is ${result}`
    return a + b
}
add(3, 4) 
```

```
<div id="add-example"></div> 
```

现在我们的函数扩展到自身之外来更新 DOM([文档对象模型](https://www.youtube.com/watch?v=H63dVFDuJDM)的缩写)并显示结果。这种额外的行为是一种副作用。

## 副作用在起反应

那么 React 呢？这是一个几乎只更新 DOM 的库。一个视图库，如果你愿意的话。那么你认为 React 的副作用是什么？

除了更新页面之外的任何事情。如果您没有使用 React 来更新状态或呈现 HTML，这是一个副作用。它是任何无反应的东西。

这意味着任何时候你调用一个 API，使用`setInterval` / `setTimeout`，添加一个键盘监听器，或者任何时候你弄乱了`window`对象，你就引入了副作用。

钩子很神奇，可以让你写出真正可读的、可重用的代码……除非你在处理副作用。这很不幸，因为当你仔细想想，我们作为 web 开发人员所做的大多数有趣的事情都围绕着副作用。

让我们暂时离开钩子，看看我们如何处理基于类的组件中的副作用。

### 基于类的组件和副作用

在基于类的组件中，我们会使用生命周期方法来执行副作用。例如，在`componentDidMount`上，我们会调用 API 来获取渲染数据。

*“为什么不调用构造函数中的 API？”*，你可能会问。因为 React 说我们用于渲染的东西是在“状态”中进行的，在我们的组件被加载到页面上之前，我们不能弄乱状态。如果我们试图在组件加载之前更新状态，我们会得到错误。

这里有一个典型的基于类的组件点击 [PokeAPI](https://pokeapi.co/) :

```
class Pokemon extends React.Component {
    constructor() {
        super()
        this.state = null
    }
    componentDidMount() {
        fetch('https://pokeapi.co/api/v2/pokemon/gengar/')
        .then(res => res.json())
        .then(res => {
            this.setState(res)
        })
    }
    render() {
        const pokemon = this.state
        const style = {textTransform: 'capitalize'}
        return (
            <div>
            { pokemon
                ? <>
                    <img src={pokemon.sprites.front_default}
                        alt={'Image of ' + pokemon.name}/>
                    <p style={style}>Name: {pokemon.name}</p>
                    <p style={style}>
                        Type: {pokemon.types.map(x => x.type.name).join(', ')}
                    </p>
                </>
                : 'Loading...'
            }
            </div>
        )
    }
} 
```

除了不工作的时候，这个工作很好。当我们想再次点击 API 来抓取不同的口袋妖怪时会发生什么？如果这个组件附加到我们应用程序中的某个路由，并且路由发生了变化，但是组件没有卸载/重新装载，该怎么办？如果用户转到另一个页面，而组件在 API 调用完成之前卸载了，该怎么办？

答案是添加更多的生命周期方法，如`componentDidUpdate`和`componentWillUnmount`来执行更多的 API 调用，并防止奇怪的卸载错误。所以我们添加了那些生命周期方法来处理我们所有的边缘情况。当我们完成时，我们意识到我们的大部分组件是由生命周期代码组成的。

## 这时反应过来的是钩子

React 团队意识到类 API 有点笨拙，很难推理。人们正在制作生命周期流程图，试图理解 React 的内部工作……真是一团糟。

因此，在 2018 年 10 月的 ReactConf 上，在 Sophie Alpert 概述了使用类有多糟糕之后，Dan Ambramov 上台介绍了 hooks(你可以在这里观看视频)。

钩子在功能组件中引入了状态，以及一种处理副作用的新方法。钩子用更少的代码提高了 React 代码的可重用性——这是一个巨大的胜利！

除了一个小怪癖。每次渲染，整个组件/功能都要重新运行。

让我们看一个基于钩子的组件的例子:

```
function RerenderExample() {
    const [bool, setBool] = React.useState(false)
    const randomNum = Math.random()
    return (
        <div>
            <p>This number will be different each time you click the button:</p>
            <p>{randomNum}</p>
            <button onClick={() => setBool(!bool)}>Trigger a render</button>
        </div>
    )
} 
```

我们甚至没有在渲染的 JSX 中使用`bool`,但是每次状态改变时，整个函数都会运行。每一次渲染，组件内部的所有东西都会重新运行:函数定义，变量创建/赋值，等等。

如果你在想*“如果我必须在一个组件内部做一些计算量很大的事情怎么办？这不可能是表演……”*，这是一个敏锐的观察。功能组件*中的昂贵操作*不会被执行。事实上`useCallback`和`useMemo`是这个问题的逃生出口。Kent C Dodds 对这些[进行了富有挑战性的解读，但总而言之，根据 Kent 的说法，在看到性能影响之前，你不必担心重新渲染。](https://kentcdodds.com/blog/usememo-and-usecallback)

(**注意**:如果你不熟悉 hooks，把那篇文章加入书签，等你准备好了再回来看。不到万不得已，不要担心记忆。)

像`useState`这样的钩子在引擎盖下使用了一些魔法来避免重新渲染的问题。这很好，使用`useState`似乎很简单，但是当您需要在设置状态之外做事情时怎么办？

输入`useEffect`。那些讨厌的生命周期方法一去不复返了，万岁！然而，这个钩子也有它自己的古怪之处。首先让我们检查一下语法:

```
//accepts two arguments: a function, and dependency array
useEffect(() => {
    // do stuff
    return () => {} //function to undo our stuff from above when component unmounts
}, []) //dependency array of things to watch for changes on 
```

所以你传递给`useEffect`一个包含你的副作用的回调函数来运行，然后是一组要观察的东西。如果被观察的东西发生了变化，`useEffect`会重新运行我们的回调函数。如果您需要在卸载时清除副作用，请返回包含该代码的函数。

让我们看看我们的口袋妖怪例子，有钩子和`useEffect` :

```
function Pokemon() {
    const [pokemon, setPokemon] = React.useState(null)
    React.useEffect(() => {
        fetch('https://pokeapi.co/api/v2/pokemon/gengar/')
        .then(res => res.json())
        .then(res => {
            setPokemon(res)
        })
    }, []) // empty array means nothing to watch, so run once and no more
    const style = {textTransform: 'capitalize'}
    return (
        <div>
        { pokemon
            ? <>
                <img src={pokemon.sprites.front_default}
                    alt={'Image of ' + pokemon.name}/>
                <p style={style}>Name: {pokemon.name}</p>
                <p style={style}>
                    Type: {pokemon.types.map(x => x.type.name).join(', ')}
                </p>
            </>
            : 'Loading...'
        }
        </div>
    )
} 
```

如果你盯着那个空的依赖数组，你会注意到第一个奇怪的现象。通过传递一个空数组，我们说“只做一次”。React pantheon 告诉我们，在极少数情况下，这是可以的，但大多数时候你想在那里的东西。原因是通常你想与代码中的*某个*同步，而不是只执行一次效果。例如，如果我们的口袋妖怪组件依赖于路线参数或道具，任何说“去获得一个新的口袋妖怪”而没有安装/卸载，会怎么样？

假设我们的组件依赖于一个道具`pokemonToGet`,这是一个告诉它从 API 中获取哪个口袋妖怪的参数。让我们也添加一个微小的表单进行测试:

```
function Pokemon({pokemonToGet}) {
    const [pokemon, setPokemon] = React.useState(null)
    React.useEffect(() => {
        fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonToGet}/`)
        .then(res => res.json())
        .then(res => {
            setPokemon(res)
        })
    }, [pokemonToGet]) // get a new pokemon with the pokemonToGet prop changes
    const style = {textTransform: 'capitalize'}
    return (
        <div>
        { pokemon
            ? <>
                <img src={pokemon.sprites.front_default}
                    alt={'Image of ' + pokemon.name}/>
                <p style={style}>Name: {pokemon.name}</p>
                <p style={style}>
                    Type: {pokemon.types.map(x => x.type.name).join(', ')}
                </p>
            </>
            : 'Loading...'
        }
        </div>
    )
}
function PokemonForm() {
    const [inputValue, setInputValue] = React.useState("rowlet")
    const [pokemonToGet, setPokemonToGet] = React.useState("gengar")
    function getPokemon() {
        setPokemonToGet(inputValue.trim().toLowerCase())
        setInputValue("")
    }
    return (
        <div>
            <input onChange={(e) => setInputValue(e.target.value)}
                value={inputValue} type="text"/>
            <button onClick={getPokemon}>
                Get Pokemon
            </button>
            <Pokemon pokemonToGet={pokemonToGet} />
        </div>
    )
}
render(<PokemonForm />) 
```

太好了，现在我们组件根据我们的道具变化获取了一个新的口袋妖怪。对于类，我们不得不用`componentDidUpdate`之类的东西来达到类似的效果。

瑞安·弗洛伦斯有一条很棒的[推文](https://twitter.com/ryanflorence/status/1125041041063665666?lang=en)总结了`useEffect`的用法:

> 问题不是“这个效果什么时候运行”而是“这个效果与哪个状态同步”
> 
> useEffect(fn) //所有状态
> 
> useEffect(fn，[]) //无状态
> 
> useEffect(fn，[这些，状态])

他提到的“全状态”情况很奇怪，在这种情况下，没有为依赖项传递任何值。我个人从未发现它的用途。我们知道整个组件在每个渲染中运行，所以我想知道是否有一些奇怪的情况需要你使用没有任何依赖数组的`useEffect(fn)`。在任何情况下，大部分时间你都将使用这个:`useEffect(fn, [these, states])`。

## 使用效果和先前状态:事物分崩离析的地方

到目前为止,`useEffect`的心理模型看起来足够直接:让它与你选择的某些状态变化同步。问题是当你需要在一个效果中引用那个状态，而不仅仅是知道它是否改变了。

**您无法访问`useEffect`中的当前状态。**

我们在 API 调用示例中没有看到这一点，因为我们只是覆盖了之前的状态。

我们来看一个简单的例子:

```
function Timer() {
    const [count, setCount] = React.useState(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            setCount(count + 1)
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>The count is: {count}</div>
    )
} 
```

看起来我们做了所有正确的事情(甚至清除了卸载的副作用)，但是我们的计数器没有增加。这是一个`this`问题吗？我们不能访问`setInterval`所属的`window`对象吗？

不，不。如果你在这个时间间隔上加一个`console.log`,你会看到它每秒滴答一次。

我会证明给你看:

```
function Timer() {
    const [count, setCount] = React.useState(0)
    const [randomNum, setRandomNum] = React.useState(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            setCount(count + 1)
            setRandomNum(Math.random())
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>
            <p>The count is: {count}</p>
            <p>RandomNum is {randomNum}</p>
        </div>
    )
} 
```

注意我们每秒钟都会得到一个新的随机数。我们的区间码没问题。

我们能够设置新状态，但无法访问当前状态。

这是一个“**陈旧的闭包**”。我不会深入讨论闭包，但我只想知道，由于 React/hooks 的实现，在我们的区间函数中，count 变量总是为 0。这是一个老的参考。

**2019 年 9 月 7 日更新**:正如约翰·塔克所指出的，对于上面这样的简单例子，有一个解决方案(谢谢约翰！).与基于类的组件中的`setState`非常相似，`useState`也可以接受一个回调函数，该函数接收前一个状态作为参数。[的 React 文档](https://reactjs.org/docs/hooks-reference.html#functional-updates)也注意到了这一点。

这里有一个快速修复的例子:

```
function Timer() {
    const [count, setCount] = React.useState(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            //let's pass a function instead
            //the argument is the current state
            setCount(count => count + 1)
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>The count is: {count}</div>
    )
} 
```

然而，这仍然不能解决我们所有的问题。如果你需要访问`useEffect`中的最新状态，但是*没有*更新它，你将不得不开始在`setState`回调中包装你的`useEffect`代码，然后在最后返回未改变的状态。这可能会很快变得一团糟，尤其是当您使用多个状态值时。

另一个可能的解决方案是只使用`useReducer`，因为它接收以前的状态，但是用副作用填充你的减速器看起来也很混乱，我不建议任何人完全停止使用`useState`。

无论如何，在编写钩子时，我已经多次陷入陈旧的闭包陷阱。我甚至不知道它有一个名字，直到我在 Rich Harris 的演讲中读到它，他是《T2》的幕后人物。

显然，我也不是唯一一个被钩子绊倒的人:

[![Photo of Tweet by Kent Dodds asking about hooks pitfalls and a snarky response of "stale closures"](img/519c901945cdfd737f8614c283b59e43.png "Photo of Tweet by Kent Dodds asking about hooks pitfalls and a snarky response of "stale closures"")](https://leewarrick.com/blog/static/5a5dbcc6667a5da9eeabc5cbe90a1427/ed46b/staleclosures.png)

React 甚至在他们的[文档](https://reactjs.org/docs/hooks-faq.html#why-am-i-seeing-stale-props-or-state-inside-my-function)中提到了这一点:

> 组件中的任何函数，包括事件处理程序和效果，都“看到”创建它时所在的渲染中的属性和状态

我读了这篇文章，在真正深入研究这个问题之前，它对我来说没有什么意义。我想这可能是一个有点黑眼睛的反应，所以也许他们不希望叫出来太大声。

然而，丹·阿布拉莫夫在他的博客中更好地描述了这个问题，甚至提供了一个解决方案:

> 效果总是从定义它们的渲染中“看到”道具和状态。这有助于防止错误，但在某些情况下可能会令人讨厌。对于这些情况，您可以在可变 ref 中显式维护一些值。

这很有帮助，因为它以`useRef`的形式提供了一个解决方案(谢谢 Dan！)，但它让我对如何帮助避免这个问题一无所知(主要是因为我不明白`useRef`)。

## 什么是“ref”，如何使用？

所以传统上，refs 意味着让你直接访问 html 元素。例如，假设您需要聚焦一个输入字段。您必须退出 React，使用常规 JavaScript 来调用`.focus()`。钩子实际上使这变得相当简单:

```
function InputField() {
    const inputRef = React.useRef()
    return (
        <div>
            <input type="text" ref={inputRef} />
            <button onClick={() => inputRef.current.focus()}>
                Click to Focus the input
            </button>
        </div>
    )
} 
```

太好了！当我们需要时，引用是访问本地 DOM APIs 的一个简单的出口。

…但是这如何帮助我们修复陈旧的闭包反例呢？

## 使用引用来避免陈旧的闭包

[React 文档](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)将引用与“实例变量”进行比较。我不知道那是什么意思(感谢[维基百科](https://en.wikipedia.org/wiki/Instance_variable))，所以我不觉得那有什么帮助。

我设法通过这样想来理解裁判:

参考存在于重新渲染周期之外。

把 refs 想象成一个你放在一边的变量。当您的组件重新运行时，它会愉快地跳过那个引用，直到您用`.current`在某个地方调用它。

让我们来看看计时器示例的修复:

```
function Timer() {
    const [count, setCount] = React.useState(0)
    const countRef = React.useRef(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            countRef.current = countRef.current + 1
            setCount(countRef.current)
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>The count is: {count}</div>
    )
} 
```

如果我们想在比如说 10 点停止计时器，我们可以很容易地使用 ref:
来完成

```
function Timer() {
    const [count, setCount] = React.useState(0)
    const countRef = React.useRef(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            if (countRef.current === 10)
                return clearInterval(intervalId)
            countRef.current = countRef.current + 1
            setCount(countRef.current)
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>The count is: {count}</div>
    )
} 
```

为了便于比较，下面是使用`setState`回调方法的替代方法:

```
function Timer() {
    const [count, setCount] = React.useState(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            setCount(count => {
                if (count === 10) {
                    clearInterval(intervalId)
                    return count
                }
                else return count + 1
            })
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>The count is: {count}</div>
    )
} 
```

我可以看到这很快接近回调地狱，所以如果你正在做一些更复杂的事情，我会警告不要使用回调方法。

## 国家对国家

有没有可能完全抛弃状态，只使用引用？

您可能倾向于认为可以对组件使用 refs 而不是 state，从而避开所有这些奇怪的行为。

你不能。裁判不是被动的。当你改变一个参考时，不会导致重新渲染。(记住，它们存在于重新渲染周期之外)。

这个不行:

```
function Timer() {
    const count = React.useRef(0)

    React.useEffect(() => {
        const intervalId = setInterval(() => {
            count.current = count.current + 1
            //console.log('Ref example count: ' + count.current)
        }, 1000)
        return () => clearInterval(intervalId)
    }, [])

    return (
        <div>The count is: {count.current}</div>
    )
} 
```

该组件实际上在内部向上计数，但它不会导致 HTML 更新。为此你需要`useState`。(如果你不相信我，请取消对`console.log`的注释)

这里有一个例子来说明 refs vs state:

```
function Counter() {
  const [count, setCount] = React.useState(0)
  const countRef = React.useRef(0)

  return (
    <div>
      <p>State Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment State Count
      </button>
      <p>Ref Count: {countRef.current}</p>
      <button onClick={() => countRef.current = countRef.current + 1}>
        Increment Ref Count
      </button>
    </div>
  )
}

render(<Counter/>) 
```

在通过设置状态触发重新渲染之前，您不会看到引用计数发生变化。

## 离别的思念

我喜欢 React 中的钩子和功能组件，但是我所描述的一些怪癖让我犹豫了。我不喜欢在使用 React 时被要求知道这么多关于它的内部工作原理。我认为这对那些学习 React 的人来说是一个障碍，我希望将来 React 团队能想出一些更好的方法来处理这些怪癖。

你遇到“陈旧的关闭”障碍了吗？请在评论中告诉我！

*喜欢这个帖子？[请订阅我的简讯](https://tinyletter.com/leewarrick)和[查看我的播客！](https://techjr.dev)*