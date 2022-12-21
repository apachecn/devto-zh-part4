# 我为什么要钩？

> 原文：<https://dev.to/avnsh/why-should-i-hook-2d55>

[![](img/520990c224bbff8efde2e6b31305f9cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--faoqhKbj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oiunpjzir5yp63xq5bx5.jpg)

自从引入 React 挂钩已经有一段时间了。组成事物是一种全新的原语，这种新的原语试图终结许多反模式(为了好)。

从组成的角度来看，React hooks 是 React 到目前为止最大的[变化](https://dev.to/avnsh/composition-patterns-in-real-world-react-4npi)，考虑到过去几年出现的模式，这种变化早就应该出现了。在这篇文章中，我试图涵盖一些我过去在代码中遇到的问题，当用一个钩子代替它们时，情况变得好得多。

## 使用生命周期方法管理副作用

一个常见的模式是在一个生命周期方法中执行副作用。这可能导致命令式和声明式代码的混合，慢慢地变得很难跟踪和调试。有了 useEffect hook(以及其他工具),管理这种关注点分离变得容易多了。

```
class Example extends Component {
    state = {
        data: null
    };
    componentDidMount() {
        axios.get('some/remote/data').then((res) => {
            this.setState({data: res})
        });
    }
    render() {
        // render DOM using data
    }
} 
```

使用钩子我们可以分离出副作用

```
function useRemoteData() {
    const [data, setData] = useState(null);
    useEffect(() => {
        axios.get('some/remote/data').then((res) => {
            setData(res)
        });
    }, []);
    return data;
} 
```

我们的`Example`组件就只剩下这个简单的函数了！事实证明，类组件真的很难读懂。好吧，当我们可以用简单的函数完成所有这些时，谁还需要它们呢。

```
function Example() {
    const data = useRemoteData();
    //render DOM using data
} 
```

让我们在我们的食谱中加入更多的副作用。在使用类的原始示例中，我们现在需要侦听一个 PubSub 事件，并使用事件数据来查询 API。

```
class Example extends Component {
    state = {
        data: null,
        query: ''
    };
    componentDidMount() {
        this.loadData();
        PubSub.addListener('event', this.handleEvent);
    }
    componentDidUpdate(prevProps, prevState) {
        if (prevState.query !== this.state.query) {
            this.loadData();
        }
    }
    componentWillUnmount() {
        PubSub.removeListener(this.handleEvent);
    }
    loadData = () => {
        axios.get(`some/remote/data?${this.state.query}`).then((res) => {
            this.setState({data: res})
        });
    }
    handleEvent = query => {
        this.setState({query})
    }
    render() {
        // render DOM using data
    }
} 
```

我们的钩子现在变成了

```
function useRemoteData(q) {
    const [data, setData] = useState(null);
    useEffect(() => {
        axios.get('some/remote/data').then((res) => {
            setData(res)
        });
    }, [q]);
    return [data]
}

function usePubSub() {
    const [query, setQuery] = useState('');
    useEffect(() => {
        function handleEvent(q) {
            setQuery(q);
        }
        PubSub.addListener('event', handleEvent);
        return () => {
            PubSub.removeListener(handleEvent);
        }
    }, []);
    return query;
} 
```

我们的`Example`组件仍然是一个普通的函数。

```
function Example() {
    const query = usePubSub();
    const data = useRemoteData(query);
    //render DOM using data
} 
```

所以干净又分开。许多组件现在可以从这些挂钩中受益。正如你可以很容易地看到，只有 2 个副作用，它开始变得复杂，使用我们的基于类的组件。

## 但是高阶组件或者渲染道具呢

有人可能会说，使用高阶组件或渲染道具模式也将解开这些复杂性。但是他们也带来了自己的一系列问题。

*   **访问组件状态** -父 HOC 不能访问包装组件的状态。
*   **命名冲突** -由于 hoc 将道具注入到包装的组件中，道具之间的命名冲突的机会出现了。即使道具有名字空间，如果同一个道具被使用了不止一次，命名冲突也必须单独处理。
*   代码解析器 -由于 hoc 有许多组成方式，静态分析代码是很困难的。
*   当一个组件上有很多道具时，追踪哪个道具来自哪个道具是很困难的。此外，在调试过程中，找出哪个组件导致了重新渲染也是一场噩梦。
*   这些模式给组件树添加了错误的层次结构，并创建了组件地狱。
*   匿名箭头函数 -渲染道具大量使用箭头函数，特别是如果你的组件树很大，它会导致大量的重新渲染，最终会影响性能。
*   **转发引用**——在理想的模板世界中，React 组件树和 DOM 之间应该有一对一的映射。因此不需要转发参考。

## 结论

所以钩子看起来像是非常有用的原语。它改变了我们思考组件和组合各种其他原语的方式。许多流行的 UI 组件库已经在其最新版本中采用了它。看看其他库和框架如何赶上将会很有趣。

不过现在，我已经非常着迷了😆

和平和[享受这首歌](https://www.youtube.com/watch?v=rPF25A-XnHE)。