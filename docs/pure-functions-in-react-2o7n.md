# React 中的纯函数

> 原文：<https://dev.to/keevcodes/pure-functions-in-react-2o7n>

# 动机

最近纯函数越来越多，主要是因为 React 的流行和它对纯组件的使用。当我第一次开始学习 React 时，我知道纯组件总是在给定相同输入的情况下返回相同的输出，但我并不真正理解纯组件的来源或它们的真正价值。当然，我听说过“纯组件使测试更容易”,我会在采访中或与其他开发人员交谈时说出这一推理，但我并不真正理解为什么。

“React 会让你成为一名更好的程序员”在我早期学习 React 的过程中被多次提及。这种情绪驱使我学习框架，但是直到我开始学习更多关于函数式编程和 React 与它的组合关系，这种说法才变得更加清晰。

React 与函数式编程共享的许多部分之一是纯函数的思想；反应箱中的组件。

# 什么是纯函数？

纯函数接受一个输入值(一个参数或自变量),并根据该输入产生一个输出值，如此而已。他们只做一件事，但做得很好。应该是，每当你给一个纯函数相同的输入，它每次都会返回相同的输出。

```
const myPureFunction = number => return number * 4 
```

Enter fullscreen mode Exit fullscreen mode

如果你在我们的互联网上搜索纯函数的解释，你可能会遇到与上面类似的代码片段，这是有充分理由的。函数需要满足几个要求才能是纯的。

## 纯函数必须

*   不含副作用
*   当给定*相同输入*时，返回*相同输出*。

虽然它们是很好的例子，但它们不能解释整个故事。例如...

```
const isThisPure = number => { 
  console.log(number); 
  return number * 4
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数与我们的`myPureFunction`几乎相同，但这次我们在其中加入了一个漂亮的小`console.log()`，也许只是为了检查我们接收到了什么。虽然 console.log()不会严重影响我们的代码库，但它仍然被认为是这个函数中的一个*副作用*。

# 副作用？不纯:纯

副作用是任何超出我们功能范围的变异(例如，我们浏览器的控制台)，*影响*我们应用程序的其他部分。让我们看另一个例子。

```
let globalNumber = 4;

const multiply = (x) => {
  return globalNumber *= x 
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们正在更新一个变量(globalNumber ),它是在我们的`multiplyByThree`函数之外定义的。如果我们随后想要通过另一个函数访问我们的`globalNumber`变量，但是那个函数期望我们的全局变量是一个特定的值(例如一个用户 id ),我们可以看到这将如何在管道中引起问题。

React 中的纯组件应该照着做，永远不要操纵其他组件可能依赖的全局状态。纯组件应该接收道具，并根据这些道具输出一个组件。

然而，副作用并不是一件坏事。它们在几乎所有的项目中都是非常必要的，尤其是那些经常基于用户交互进行更新的项目。在哪里放置和处理你的副作用是保持你的项目清晰和容易发现任何可能发生的错误的重要部分。例如，React 中的状态更改通常只留给少数几个组件，或者是应用程序中完全独立的部分。

# 给定相同的输入值时返回相同的值。

我们的 pure 函数的目标是保持代码的可预测性。为了确保这一点，纯函数应该基于相同的输入返回相同的输出；如果一个函数做了其他事情，它就不再是纯的了。

```
const multiplyNumber = (x) => {
  return x * 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们总是要接受一个数，然后得到这个数乘以 2。这个函数非常容易测试和推理。React 中的纯组件行为完全相同；他们收到一些道具，并基于这些道具返回一个组件。纯组件避免使用任何状态。

```
const HeadlineComponent = props => return <h1>{props.headline}</h1> 
```

Enter fullscreen mode Exit fullscreen mode

测试这样的组件更简单，因为如果有错误，我们只需要查看作为道具传入的内容。我们不必检查组件中的状态在哪里被更新，也不必担心`HeadlineComponent`中的某种逻辑错误地更新了我们的状态；如果我们的标题错了，我们知道那是因为道具错了。

# 结论

函数组合为 React 中的许多概念奠定了基础，包括纯组件。一旦我们理解了函数式编程背后的原理，我们就可以将我们的 React 代码库构建成一个更可预测、可测试的应用程序。我希望您已经对纯组件存在的原因以及它们如何使您成为更好的程序员有了更好的理解。感觉提供任何反馈和批评。

这篇文章是对比我优秀得多的开发人员所写的函数式编程/组合思想的一个快速粗略的审视。如果你想了解更多关于函数构成的知识，我想推荐几个我认为非常有用的资源 : [阿尔文·亚历山大](https://alvinalexander.com/scala/fp-book/benefits-of-pure-functions)，[有趣的函数](https://www.youtube.com/watch?v=cUrEedgvJSk&t=2s)， [Scotch.io](https://scotch.io/tutorials/wielding-pure-functions-in-javascript-and-function-composition)