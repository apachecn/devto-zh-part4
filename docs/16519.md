# React Next 2019:React Hooks——主要用途

> 原文：<https://dev.to/apium_hub/react-next-2019-react-hooks-the-main-uses-g>

今年 6 月 12 日， [Apiumhub](https://apiumhub.com) Frontend 团队的部分成员有机会参加在特拉维夫举行的 [React Next 2019](https://react-next.com/) 会议，该会议主要关注 React 和 React Native。在那里，我们有机会参加了非常有趣的关于 React 框架、React 代码的优化和性能改进、带有 SPA 的 SEO 以及许多其他内容的讲座。

引起我注意的演讲是 Liad Yosef 的“**Modern React-The Essentials**”，他在演讲中解释了 React 的最新消息，如 **React Hooks** 、并发模式、悬疑、懒惰。

就我个人而言，我对 React 钩子的使用非常感兴趣，以及这将如何改变 React 的发展，所以我将在本文中解释钩子的主要用途。

## React Next 2019: React 挂钩——主要用途

### **useState 钩子**

我们将从查看一个传统的 React 类开始，我们已经创建了一个由按钮组成的下拉列表，该按钮将执行状态切换来显示或隐藏我们的项目列表:

```
 export default class Dropdown extends Component {
  constructor() {
    super();
    this.state = { isDropdownvVisible: false };
    this.handleClick = this.handleClick.bind(this);

  handleClick() {
    this.setState(() => {
      return { isDropdownvVisible: !this.state.isDropdownvVisible };
    });
  }

  render() {
    const { isDropdownvVisible } = this.state;
    return (
      <div>
        <button onClick={this.handleClick}>Toogle dropdown</button>
        { isDropdownvVisible &&
            <ul>
                <li>First item</li>
                <li>Second item</li>
                <li>Third item</li>
              </ul>

      </div>
    );
  };
} 
```

沙盒

在下面的示例中，我们可以看到，当您单击按钮时，我们将执行“handleClick”函数，该函数将更改“isDropdownvVisible”状态变量的值，因此 html 列表将对此更改做出反应，并根据所述变量的值显示或隐藏列表。

虽然它工作正常，但我们可以看到我们必须编写大量代码来执行一个简单的布尔值。为了解决这个问题，React 为我们提供了使用钩子的可能性。

使用钩子允许我们使用函数，并且仍然拥有状态变量，这样就省去了使用类时的所有繁琐。

为此，我们将使用 React 的“使用状态”功能。

```
import React, { useState } from "react"; 
```

我们将如下使用导入的:

```
const [isDropdownvVisible, toogleDropdownVisibility] = useState(false); 
```

首先，我们将定义一个包含两个变量的数组:

*   Value:我们要定义的状态变量的名称。在我们的例子中，它将被称为“isDropdownVisible”。
*   ModifierFunction:将修改我们的状态的函数的名称。在我们的例子中，它将被称为“toogleDropdownVisibility”。

同样在“useState”函数中，我们可以定义变量的初始值。在我们的情况下，它将开始是假的。

使用钩子代替类重写前面的代码，看起来会像这样:

```
 export default function Dropdown() {
  const [isDropdownvVisible, toogleDropdownVisibility] = useState(false);

  function handleClick() {
    return toogleDropdownVisibility(!isDropdownvVisible)
  }

  return (
      <div>
        <button onClick={handleClick}>Toogle dropdown</button>
        { isDropdownvVisible &&
            <ul>
                <li>First item</li>
                <li>Second item</li>
                <li>Third item</li>
              </ul>

      </div>
    );
} 
```

沙盒

### 使用效果挂钩

除了允许我们拥有一个状态而不强迫我们使用类之外，钩子还为我们提供了多种可能性，其中最突出的是“useEffect”函数，它允许我们在函数中产生副作用。

使用`useEffect`我们通知 React 我们希望我们作为参数传递的函数在每次渲染中执行(默认情况下)，这节省了我们依赖生命周期方法的时间，如“componentDidUpdate”、“componentWillUnmount”等。

让我们看一个使用我们上面定义的组件的例子:

```
 import React, { useEffect, useState } from "react";

export default function Dropdown() {
  const [isDropdownvVisible, toogleDropdownVisibility] = useState(false);

  function handleClick() {
    return toogleDropdownVisibility(!isDropdownvVisible)
  }

  useEffect(() => {
    console.log(isDropdownvVisible);
  })

    return (
      <div>
        <button onClick={handleClick}>Toogle dropdown</button>
        { isDropdownvVisible &&
            <ul>
                <li>First item</li>
                <li>Second item</li>
                <li>Third item</li>
              </ul>

      </div>
    );
} 
```

运行前面的代码，我们可以看到在组件的每次渲染中，如何显示“isDropdownvVisible”变量的值。

在这种情况下，我们不会有任何问题，因为这是一个简单的数据记录，但如果我们想在值改变时发出 http 请求，会发生什么情况呢？我们必须在每次呈现时都这样做吗？

为了避免这种情况，我们可以将第二个参数传递给“useEffect”函数，该函数由一个数组构成，其值是我们想要比较的值，除非它不同，否则我们定义为“useEffect”的第一个参数的函数将不会被执行。

```
 useEffect(() => {
    console.log(isDropdownvVisible);
  }, [true]) 
```

在这种情况下，只有当它为“假”时，它才会向我们显示值。

我们已经看到了如何使用效果挂钩来封装我们的逻辑，因此我们可以创建自己的挂钩来在不同的组件之间共享功能，例如数据获取、数据解析等。

这使我们能够非常快速、干净地创建和共享我们的服务。

### **下一步反应:反应钩子结论**

作为使用钩子的结论，它为我们的组件和服务的创建提供了一个彻底的改变，因为我们已经从使用类到能够使用具有内部状态的函数，并且我们保存了整个“样板文件”来实现使用“useEffect”的 React 生命周期的所有流程，所以一旦这些改进被评论，你只需要试着使我们的代码适应钩子的使用并享受它！

如果你对这篇关于 React Next 的文章感兴趣，请订阅我们的每月简讯。

帖子[React Next 2019:React Hooks——主要用途](https://apiumhub.com/tech-blog-barcelona/react-next-react-hooks/)最早出现在 [Apiumhub](https://apiumhub.com) 上。