# React —以正确的方式访问处理程序中的自定义参数

> 原文：<https://dev.to/flexdinesh/react-access-custom-params-in-handlers-the-right-way-44mi>

***TL；你可以在处理程序中访问渲染时间值，而不必使用匿名箭头函数。提示—利用数据属性。***

React 在很多方面都很棒，它给了我们以不同方式做事的自由，减少了固执己见(或非固执己见)。

最近几天，尤其是在 hooks 发布之后，社区一直在围绕**引用相等**和渲染中匿名箭头函数如何对性能不利而争论不休。

我们不打算深入探究为什么或者如何在渲染过程中使用箭头函数影响性能(或者不影响)。以下是两条对比鲜明的推文。

> ![shawn swyx wang 🌟 profile image](img/db834a48a0edb93c01a03f5e47352b9c.png)王🌟[@ swyx](https://dev.to/swyx)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ ryanflorence](https://twitter.com/ryanflorence)[@ ConAntonakos](https://twitter.com/ConAntonakos)TFW 你意识到“但是在渲染中创建函数是一种反模式”的人群仍然是对的但是他们不知道为什么下午 23:44-2019 年 5 月 23 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1131707595880898560)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1131707595880898560)

> ![Kent C. Dodds profile image](img/94f207e09e64133620a88b3ae5533c42.png)肯特 c .多兹[@肯特多兹](https://dev.to/kentcdodds)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)请住手！内联函数不是你的瓶颈！[twitter.com/ferdaber/statu…](https://t.co/LArrkRnEVd)下午 15:48-04 2019 年 6 月佛迪布达摩[@佛达伯](https://dev.to/ferdaber)我不会撒谎，我担心 React 特有的面试测试或问题，因为有很多 FUD，有人可能会因为我内嵌我的箭头函数而从我身上扣分。[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1135936248386736128)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1135936248386736128)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1135936248386736128)

# 当

我们通常在渲染时使用箭头函数，只是为了将自定义参数传递给事件处理程序。

例如，这是一个常见的用例—

```
const MyComp = () => {
  const handleClick = (e, id) => {
    // handle click and use id to update state
  }

  // id comes from state
  return (
    <div onClick={(e) => handleClick(e, id)}>Hello World!</div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

# 如何

我们可以利用**数据属性**并从事件对象中访问值，而不是在这里使用匿名箭头函数。

```
const MyComp = () => {
  const handleClick = (e) => {
    const id = e.target.dataset.id
    // handle click and use id to update state
  }

  // id comes from state
  return (
    <div data-id={id} onClick={handleClick}>Hello World!</div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

# 优点

这种方法有很多优点—

*   如果需要，你可以很容易地记住回调。

```
const MyComp = () => {
  // id comes from state
  const handleClick = React.useCallback((e) => {
    const id = e.target.dataset.id
    // handle click and use id to update state
  }, [id])

  return (
    <div data-id={id} onClick={handleClick}>Hello World!</div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在组件渲染过程中不需要创建新的函数，这样可以节省 vdom 差分过程中的额外计算成本。

*   如果使用 **React.memo** 的子组件将这个处理程序作为道具，你就不必编写自定义的 *areEqual* 方法来规避使用 anon arrow 函数的**参照完整性**问题。

***请记住，所有数据属性都返回字符串值，即使你传入了其他原始类型。因此，在从数据集中提取时强制您的值是一个很好的做法。【T2***

```
const id = e.target.dataset.id // this will be String

const id = Number(e.target.dataset.id) // convert to type if needed 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个 codesandbox 来演示它是如何工作的——[处理程序中的自定义参数](https://codesandbox.io/s/customparamsinhandlers-9wlls?fontsize=14&hidenavigation=1&module=%2Fsrc%2FTodoList.js&view=editor)

你太棒了！祝您愉快！⚡️