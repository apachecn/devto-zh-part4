# 使用效果的快速解释

> 原文：<https://dev.to/bdbch/a-quick-explanation-on-useeffect-2nn9>

有人问我是否可以对 React 提供的 useEffect 挂钩写一个简短的解释，我想“当然，这应该能帮助一些人！”。

如果设置正确，useEffect 可以在一个函数中表现得像`componentDidMount` `shouldComponentUpdate`和`componentWillUnmount`。在这篇文章中，我将向你展示几种复制不同生命周期行为的方法。

**记住，useEffect 使用第二个参数`dependencies`作为性能工具**

这里有一篇有趣的文章，讲述了如何在没有依赖的情况下编写钩子:

[https://dev.to/samsch_org/effects-are-not-lifecycles-551o](https://dev.to/samsch_org/effects-are-not-lifecycles-551o)

## 的例子为`componentDidMount`

首先，你可以写一个效果，当组件挂载时，这个效果只运行一次，不会再运行:

```
useEffect(() => {
  console.log('I was mounted and will not run again!')
}, []) 
```

这里重要的是作为第二个参数的空数组。useEffect 的第二个参数可用于观察属性的变化。见下文。

## 的例子为`shouldComponentUpdate`

useEffect 还可以帮助监视您的属性，这样您就可以在每次更新特定值时运行它。假设我们有一个名为“名称”的道具，我们的组件应该在每次名称道具改变时通过效果更新一些东西，你可以这样做:

```
const MyComponent = (props) => {
  useEffect(() => {
    document.title = `Page of ${props.name}`
  }, [props.name])

  return <div>My name is {props.name} </div>
} 
```

您可以看到，我们在第二个参数中将`props.name`传递给了数组。这将导致效果总是在名称改变时再次运行。

**附注**:你应该总是设置第二个参数，否则你会陷入渲染循环。

## 的例子为`componentWillUnmount`

useEffect 也可用于在组件卸载时运行代码。这对于订阅或其他侦听器(例如 Websockets)是有效的。

```
let bookSubscription = null
useEffect(() => {
  // stop the subscription if it already exists
  if (bookSubscription && bookSubscription.unsubscribe) bookSubscription.unsubscribe()

  // start a new subscription
  bookSubscription = startBookSubscription({ bookId: props.bookId })

  return () => {
    // stop the subscription when the component unmounts
    bookSubscription.unsubscribe()
  }
}, [props.bookId]) 
```

您可以看到，现在我们使用了所有可用的选项。该代码现在将

1.  安装组件时启动新的订阅
2.  当 bookId 属性更改时，用新的 bookId 更新订阅
3.  卸载组件时取消订阅。

只要组件卸载，您就可以通过在效果中返回一个函数来运行逻辑。

* * *

我希望这篇短文对你有所帮助，并有助于你的进一步发展。如果你有问题，让我知道！