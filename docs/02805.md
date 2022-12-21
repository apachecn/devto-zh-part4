# React 挂钩中的状态和变量

> 原文：<https://dev.to/ghost/state-and-variable-in-react-hooks-1adl>

我是一个反应钩子的新手，我还没有发现它真正的超能力。我想问如果我做下面的示例代码，我是否会破坏什么？

```
 let somevariable= [];

const SomeComponent = () => {
  const [state, setState] = useState({});

  useEffect(()=>{
   fetch('http://insert-api/state')
      .then(response => response.json())
      .then(data => setState({ data }));
  },[])

  useEffect(()=>{
   //update `somevariable` whenever the state changes
   somevariable = state.something
  },[state])

 const clickMe = () => {
   console.log(somevariable)
   //do something to somevariable
 }

  return (
    <button onClick={clickMe}>Click Me </button>
  )
} 
```

我没有把`somevariable`放在 state 中，而是把它放在了外面，让它变得有点全局化。我这样做是因为当我将`somevariable`置于它自己的状态并在 useEffect 中设置它的值时，它会导致无限循环，但这个不会，所以这就像我的解决方法。我的很多功能都依赖于`somevariable`,这就是为什么知道这种方法是否可行对我来说很重要。

PS。我不知道在这里问这个是否合适，所以请告诉我如果不合适，我会记下来。谢了。