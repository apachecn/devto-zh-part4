# 如何用钩子写出 90%更干净的代码🎣

> 原文：<https://dev.to/aman_singh/how-to-write-90-cleaner-code-with-hooks-1mmj>

2018 年为 React 生态系统带来了许多新功能。这些特性的增加有助于开发人员更加关注用户体验，而不是花时间编写代码逻辑。

看起来 React 正在向函数式编程范式投入更多，寻找构建更加健壮和可伸缩的 UI 的伟大工具。

在 2018 年 10 月的 [ReactConf](https://conf.reactjs.org/) 上，React 宣布了一个名为 Hooks 的提议 API，在社区中掀起了一阵风暴。开发人员开始用它们进行探索和实验，并在 [RFC](https://github.com/reactjs/rfcs/pull/68) (征求意见)中收到了很好的反馈。React 16.8.0 是第一个支持钩子的版本🎉。

这篇文章是我试图解释的:

*   为什么要引入钩子

*   我们如何为这个 API 做好准备

*   如何通过使用 React 钩子来编写 90%更干净的代码🎣

如果你只是想先感受一下这个新的 API，我已经创建了一个[演示](https://codesandbox.io/s/6ymrnqn43w)来玩。否则，让我们从目前面临的 3 个主要问题开始:

## 1。重用代码逻辑

你们都知道重用代码逻辑很难，需要相当多的经验才能理解。当我大约两年前开始学习 React 时，我曾经创建类组件来封装我所有的逻辑。当涉及到跨不同组件共享逻辑时，我将简单地创建一个外观相似的组件，它将呈现不同的 UI。但这并不好。我违反了 [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 原则，理想情况下没有重用逻辑。

### 古老的方式

慢慢地，我了解了 [HOC](https://reactjs.org/docs/higher-order-components.html) 模式，它允许我使用函数式编程来重用我的代码逻辑。HOC 只不过是一个简单的[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)，它接受另一个分量(哑的)并返回一个新的增强分量。这个增强的组件将封装您的逻辑。

> 高阶函数是以一个函数作为自变量，或者返回一个函数的函数。

```
export default function HOC(WrappedComponent){
  return class EnhancedComponent extends Component {
   /*
     Encapsulate your logic here...
   */

    // render the UI using Wrapped Component
    render(){
      return <WrappedComponent {...this.props} {...this.state} />
    }
  }

  // You have to statically create your
  // new Enchanced component before using it
  const EnhancedComponent = HOC(someDumbComponent);

  // And then use it as Normal component
  <EnhancedComponent /> 
```

然后我们进入了将函数作为道具传递的趋势，这标志着[呈现道具](https://reactjs.org/docs/render-props.html)模式的兴起。渲染道具是一个强大的模式，其中*【渲染控制器】*在你的手中。这有助于[控制反转(IoC)](https://en.wikipedia.org/wiki/Inversion_of_control) 设计原理。React 文档将其描述为一种使用 **prop** 在组件之间共享代码的技术，prop 的值是**函数**。

> 具有渲染属性的组件采用一个函数，该函数返回一个
> [反应元素](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html#elements-describe-the-tree)并调用它，而不是实现自己的渲染逻辑。

简而言之，你创建一个**类**组件来封装你的逻辑(副作用),当涉及到渲染时，这个组件只是通过传递渲染 UI 所需的数据来调用你的函数。

```
export default class RenderProps extends Component {
/*
  Encapsulate your logic here...
*/

  render(){
    // call the functional props by passing the data required to render UI
    return this.props.render(this.state);
  }
 }

// Use it to draw whatever UI you want. Control is in your hand (IoC)
<RenderProps render={data => <SomeUI {...data} /> } /> 
```

尽管这两种模式都解决了重用代码的逻辑问题，但它们留给我们一个包装器地狱的问题，如下所示:

[![alt text](img/1b084b75a8c6bcb0e305466f7fd70c09.png "wrapper hell")](https://res.cloudinary.com/practicaldev/image/fetch/s--2_wz9exF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nhk5gnheuwv6h3glopcx.png)

综上所述，我们可以看到一些与重用代码逻辑相关的问题:

*   实现起来不太直观
*   大量代码
*   包装地狱

## 2。巨型组件

组件是 React 中代码重用的基本单位。当我们必须将一个以上的行为抽象到我们的类组件中时，它往往会变得越来越大，并且变得难以维护。

> 一个类应该有且只有一个改变的理由，这意味着一个类应该只有一个任务。

通过查看下面的代码示例，我们可以得出以下结论:

```
export default class GiantComponent extends Component {
  componentDidMount(){
    //side effects
    this.makeRequest();
    document.addEventListener('...');
    this.timerId = startTimer();
    // more ...
  }

  componentdidUpdate(prevProps){
   // extra logic here
  }

  componentWillUnmount(){
    // clear all the side effects
    clearInterval(this.timerId);
    document.removeEventListener('...');
    this.cancelRequest();
  }
  render(){ return <UI />; } 
```

*   代码分布在不同的生命周期挂钩中
*   没有单一的责任
*   难以测试

## 3。上课对人类和机器来说都很难

从人的角度来看这个问题，我们都曾在试图调用子组件中的函数时出错，它说:

```
TypeError: Cannot read property 'setState' of undefined 
```

然后我们绞尽脑汁想找出原因:你忘了在构造函数中绑定它。因此，*这个*甚至在一些有经验的开发人员中仍然是困惑的话题。

> **这个**获取调用函数的对象的值

此外，您甚至需要编写大量样板代码来开始实现第一个副作用:

```
extends -> state -> componentDidMount -> componentWillUnmount -> render -> return 
```

类对机器来说也很难，原因如下:

*   缩小版不会缩小方法名
*   未使用的方法不会被去掉
*   热重装和编译器优化困难重重

* * *

我们上面讨论的三个问题并不是三个不同的问题，而是一个问题的症状，那就是 React 没有比类组件更简单的有状态原语。

* * *

随着新的 React Hooks proposal API 的出现，我们可以通过将逻辑完全抽象到组件之外来解决这个问题。简而言之，您可以将有状态逻辑与功能组件挂钩。

> React 挂钩允许您在不编写类的情况下使用状态和其他 React 特性。

让我们看看下面的代码示例:

```
import React, { useState } from 'react';

export default function MouseTracker() {

  // useState accepts initial state and you can use multiple useState call

  const [mouseX, setMouseX] = useState(25);
  const [mouseY, setMouseY] = useState(25);

  return (
    <div>
      mouseX: {mouseX}, mouseY: {mouseY}
    </div>
  );
} 
```

对 [useState](https://reactjs.org/docs/hooks-state.html) hook 的调用返回一对值:当前状态和更新它的函数。在我们的例子中，当前状态值是 *mouseX* ，设置函数是 *setMouseX* 。如果将参数传递给 useState，它将成为组件的初始状态。

现在，问题是我们在哪里调用 setMouseX。在 useState 挂钩下调用它会导致错误。这将与调用类组件的*渲染*函数内的 *this.setState* 相同。

所以，答案是 React 还提供了一个名为 [useEffect](https://reactjs.org/docs/hooks-effect.html) 的占位符钩子来执行所有的副作用。

```
import React, { useState } from 'react';

export default function MouseTracker() {

  // useState accepts initial state and you can use multiple useState call
  const [mouseX, setMouseX] = useState(25);
  const [mouseY, setMouseY] = useState(25);

  function handler(event) {
    const { clientX, clientY } = event;
    setMouseX(clientX);
    setMouseY(clientY);
  }
  useEffect(() => {
    // side effect
    window.addEventListener('mousemove', handler);

    // Every effect may return a function that cleans up after it
    return () => window.removeEventListener('mousemove', handler);
  }, []);

  return (
    <div>
      mouseX: {mouseX}, mouseY: {mouseY}
    </div>
  );
} 
```

这个效果在第一次渲染和每次更新后都会被调用。你也可以返回一个可选的函数，这个函数成为一个*清理*机制。这让我们可以将添加和删除订阅的逻辑放在一起。

useEffect 调用的第二个参数是可选数组。只有当数组中的元素值改变时，你的效果才会重新运行。请将此视为 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 的工作方式。如果您想运行一个效果并只清理一次(在挂载和卸载时)，您可以传递一个**空数组** ([])作为第二个参数。这告诉 React 你的效果不依赖于道具或状态的任何值，所以它永远不需要重新运行。这接近于我们熟悉的*组件卸载*和*组件卸载*的心理模型。如果你想深入了解 *useEffect* hook，我已经在这里写了另一篇文章[。](https://dev.to/aman_singh/why-effects-shouldn-t-lie-about-their-dependencies-1645)

但是我们的 *MouseTracker* 组件不是还保存着里面的逻辑吗？如果另一个组件也想共享*鼠标移动*行为呢？此外，再增加一个效果(例如窗口大小调整)会使管理变得有点困难，我们又回到了在类组件中看到的同样的问题。

现在，真正神奇的是你可以在你的函数组件之外创建你的定制钩子。这类似于将逻辑抽象到一个单独的模块中，并在不同的组件之间共享。让我们看看实际情况。

```
// you can write your custom hooks in this file
import { useState, useEffect } from 'react';

export function useMouseLocation() {
  const [mouseX, setMouseX] = useState(25);
  const [mouseY, setMouseY] = useState(25);

  function handler(event) {
    const { clientX, clientY } = event;
    setMouseX(clientX);
    setMouseY(clientY);
  }
  useEffect(() => {
    window.addEventListener('mousemove', handler);

    return () => window.removeEventListener('mousemove', handler);
  }, []);

  return [mouseX, mouseY];
} 
```

现在我们可以清理我们的 MouseTracker 组件代码(90%)到一个新的版本，如下所示:

```
import React from 'react';
import { useMouseLocation } from 'customHooks.js';

export default function MouseTracker() {

  // using our custom hook
 const [mouseX, mouseY] = useMouseLocation();

  return (
    <div>
      mouseX: {mouseX}, mouseY: {mouseY}
    </div>
  );
} 
```

这是一个“发现”的时刻！不是吗？

但是在静下心来为 React Hooks 唱赞歌之前，我们先来看看有哪些规则是我们应该注意的。

## 规则的钩子

*   只调用顶层的钩子
*   不能在类组件中使用钩子

解释这些规则超出了本文的范围。如果你好奇，我会推荐你阅读鲁迪·亚德利的《反应文件》和《T2》文章。

React 还发布了一个名为[ESLint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)的 ESLint 插件来执行这两条规则。您可以通过运行:
将它添加到您的项目中

```
# npm 
npm install eslint-plugin-react-hooks --save-dev

# yarn 
yarn add eslint-plugin-react-hooks --dev 
```

这篇文章是我在 2018 年 12 月 ReactSydney meetup 上的[演讲](https://twitter.com/reactsydney/status/1069498781324914689?s=19)的一部分。我希望这篇文章引起了您的兴趣，让您尝试一下 React hooks。我对 React [路线图](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html)感到非常兴奋，它看起来非常有前景，有可能改变我们目前使用 React 的方式。

你可以在这个[链接](https://codesandbox.io/s/6ymrnqn43w)找到源代码和演示。

如果你喜欢这篇文章，几个❤️肯定会让我笑😀。接下来还有更多。