# React:使用回调挂钩简单解释

> 原文：<https://dev.to/azrulaziz/react-usecallback-hooks-simple-explanation-1c1g>

```
useCallback(function, [dependecies]) 
```

Enter fullscreen mode Exit fullscreen mode

useCallback 是 react hooks API 中引入的新特性之一。就个人而言，这个名字很容易混淆，因为回调通常指的是异步函数，即每当某个操作完成时我们调用的函数。然而，useCallback 用于不同的目的。

那么它是做什么的呢？根据 React 官方文档，它返回一个内存化的回调。更简单地说，它返回一个函数的缓存版本。基本上，这个钩子主要用于性能原因(内存方面)。它是怎么做到的？让我们看一个例子:

```
const [height, setHeight] = useState(100)
const [age, setAge] = useState(3)

const handleSetHeight = () => setHeight(height + 10)
const handleSetAge = () => setAge(age + 1) 
```

Enter fullscreen mode Exit fullscreen mode

我们设置了两个 useState 钩子&声明了两个函数来处理状态变化。这似乎很正常。这里的问题是，每次我们调用一个函数并重新呈现时，都会创建这两个函数的新实例。即使我们只调用一个函数，也会创建另一个函数的实例。想象一下，如果有更多的函数，在每次重新渲染过程中需要创建多少个实例。它是冗余的&会导致性能问题。

useCallback 有助于解决这个问题。它将缓存/记忆我们传递给它的函数。例如，让我们像这样重写上面的两个函数:

```
const handleSetHeight = useCallback(() => setHeight(height + 10), [height])
const handleSetAge = useCallback(() => setAge(age + 1), [age]) 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，每当我们调用一个函数并重新呈现时，将只为被调用的特定函数创建一个新的函数实例。没有为其他函数创建新的实例。传递给 useCallback 的第二个参数是**，dependecies 数组扮演了主要角色**。只有当数组中的任何变量值在重新呈现之间发生变化时，才会生成新的函数实例。如果没有任何变化，useCallback 将只返回函数实例的缓存版本。

基本上这就是使用回调钩子的目的。以防止不必要的操作并提高性能。