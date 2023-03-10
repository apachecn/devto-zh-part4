# 看，看，我写了关于胡克的故事

> 原文：<https://dev.to/iris/looky-looky-i-wrote-about-hook-s-y-28o7>

## 反应钩

2018 年 React 16.8.0 推出了 React 挂钩。史蒂文·斯皮尔伯格的《铁钩》于 1991 年推出。他们有什么共同点？几乎没有，除了这个模糊的主题博客帖子！

Hooks 允许开发人员不用写类就能使用 React 特性。通过钩子，一个功能组件可以有状态！

> State 是一个 JavaScript 对象，它存储 React 组件中管理的信息。- [React Docs - State vs Props](https://reactjs.org/docs/faq-state.html)
> 
> 那么什么是钩子呢？钩子是一个特殊的函数，可以让你“钩入”React 特性。- [React Docs -什么是钩子？](https://reactjs.org/docs/hooks-state.html#whats-a-hook)

下面是一个简单的 React 类组件的例子，后面是一个使用钩子存储状态的 React 功能组件。

***类组件***

```
import React, {Component} from 'react'

class ExampleReactClassComponent extends Component {
    constructor() {
      super()
      this.state = {
         exampleState: "Bangarang"
      }

    render() {
       return ( <div>{this.state.exampleState}</div> )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

***带挂钩的功能组件***

```
import React, { useState } from 'react'

function ExampleReactFunctionalComponent () {
    const [exampleState, setExampleState] = useState("this is my state")
} 

return ( <div>{exampleState}</div> ) 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么要用钩子写组件？

看一下上面的例子——钩子使得代码更干净，更容易阅读。钩子允许 react 开发人员编写组件，而不用每次都创建一个类。通过钩子，你可以在功能组件中使用状态和其他 react 特性。现在“无状态”功能组件只有在您希望它无状态时才是无状态的。

[![Picture of Rufio from the movie "Hook"](img/fdecca402860f9a351f9d8101a47820a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--whQH77uG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ow9ady2ni85mqhf191b.jpg)
*1991 年红极一时的《钩子》中鲁菲奥的这张照片与 React Hooks 超级相关。*

你需要记住一些管理钩子的规则。我从下面的 React 文档中调出了一些，但是我建议阅读它们的完整文档以获得更多信息。

> 只调用顶层的钩子。不要在循环、条件或嵌套函数中调用钩子。仅从 React 函数组件调用挂钩。 [React Docs - Hooks 规则](https://reactjs.org/docs/hooks-rules.html)

## 使用状态

在上面的功能组件示例中，导入语句读作`import React, { useState } from 'react'`。React 钩子`useState`是你如何给一个功能组件添加状态。

`useState`返回 2 个值——状态值和一个允许你更新状态的函数。你会看到`useState`通常用析构来写，比如:

```
const [bangarang, setBangarang] = useState("Bangarang!!!!") 
```

Enter fullscreen mode Exit fullscreen mode

让我们把上面的陈述分解一下。我们将两个变量(第一个变量是您的状态，第二个是更新状态的函数)设置为使用参数调用`useState`的返回结果。变量名是任意的，所以选择你自己的名字。你用`useState`输入的参数就是你要初始化的状态。这可以是任何东西(null，“RU-FI-O”，无论什么)。

下面是一个如何使用 useState React 钩子设置和更新状态的例子。

```
import React, { useState } from 'react'

function LostBoys() {
    const [marbles, setMarbles] = useState("lost")

    return (
       <div>
           <p>Marble status: {marbles}</p>
           <button onClick={() => setMarbles("found")}>Look For Marbles</button>
       </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

这张 1991 年广受欢迎的大片《钩子》中迷失男孩 Tootles 的 Gif 图，并不是试图给这篇博客一个主题。

## 使用效果

`useEffect`在组件渲染到 DOM 后运行。对于可以异步运行的副作用来说，这是非常好的(比如 fetch 请求！).

> 如果您熟悉 React 类生命周期方法，您可以将 useEffect 挂钩看作 componentDidMount、componentDidUpdate 和 componentWillUnmount 的组合。- [React Docs - useEffect](https://reactjs.org/docs/hooks-effect.html)

接受一个数组作为它的第二个参数。该数组表示`useEffect`用来决定何时触发的内容。如果你传递一个空数组，那么`useEffect`只会在第一次渲染后触发。

```
import React, { useState, useEffect } from 'react'

function LostBoys() {
    const [marbles, setMarbles] = useState(null)

    useEffect(() => {
        fetch('gofindmarbles')  //pretend this is an API endpoint with lots of marble data
        .then(response => response.json())
        .then(marbleData => {
            setMarbles(marbleData)           
        })
    }, [])

    return (
       <div>
           <p>Marble status: {marbles}</p>
       </div>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

## 连钩多

我只讲述了钩子入门的基本要点。查看 React 文档，了解关于所有可用挂钩以及如何创建定制挂钩的信息。

*最后一点:钩子是向后兼容的——这意味着 React 应用程序可以用钩子和类组件来编写*

[![Still image of the Lost Boys](img/8c475054e084ccaa18f5ad5ae8e238bb.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--q_ZG29Lr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evf4k0zdl5n8om3axjlm.jpg) *你可能在想，“哇，这个挂钩主题的东西真的起作用了。这是有意义的，它与主题无缝编织在一起。”你是对的。*

使用的资源:

*   [反应文件](https://reactjs.org/docs/hooks-reference.html)
*   Christopher Kade 介绍 React Hooks