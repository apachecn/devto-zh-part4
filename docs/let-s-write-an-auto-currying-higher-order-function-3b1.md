# 让我们写:一个自动弯曲的高阶函数

> 原文：<https://dev.to/samwightt/let-s-write-an-auto-currying-higher-order-function-3b1>

我最近一直在做一个项目，涉及到编写大量回调函数，其中一些变得相当大。我决定将尽可能多的函数转移到单独的模块中，以使事情变得小而相对简单，但是我注意到它们中的许多依赖于父函数的作用域。Javascript 中的函数不能继承它们看不见的东西的范围。解决此类问题的简单方法是编写一个函数，它接受所需的范围变量，然后返回回调函数。这是因为返回的回调函数将继承父函数的范围(传递的变量)。所以大概是这样:

```
const helloCallback = (instanceArg1, instanceArg2) =>
  (callbackArg1, callbackArg2) => {
    // Both instance args and callback args are in scope here!
  }

export default helloCallback 
```

在一个单独的模块中的监听器中会被这样调用:

```
import helloCallback from './helloCallback'

pretendAPI.onMessage(helloCallback(arg1, arg2)) 
```

这是一个非常简单和优雅的解决方案！在函数式编程的世界里，我们称这样的事情为*curry*(不严格地使用它)。真正的 currying 是把所有的函数参数分割成连续的函数，就像俄罗斯洋娃娃一样。看起来是这样的:

```
const helloCallback = instanceArg1 => instanceArg2 => callbackArg1 => callbackArg2 => {
  // all the variables are in scope!
} 
```

这对于我所需要的来说并不是最理想的，所以我只是将我的函数分成了两个不同的层次。

为了好玩，我决定编写自己的函数，它可以自动处理任何函数。它的用法是这样的:

```
const curried = curry((one, two, three, four) => console.log(one, two, three, four)) 
```

并且可以通过以下方式调用:

```
curried(1)(2)(3)(4)
// Output: 1 2 3 4
curried(1, 2)(3, 4)
// Output: 1 2 3 4
curried(1, 2, 3, 4)
// Output: 1 2 3 4 
```

我说过它只有 8 行吗？看看我是怎么写的吧。

* * *

在我们能够编写这个`curry`函数之前，我们需要知道一些信息。首先，这到底是怎么回事？如果我们看看函数是如何使用的，我们可以看到`curry`在一个函数中接受，然后返回另一个函数。这里需要注意一件重要的事情:返回的函数*与我们在*中传递的不同。返回的函数要么返回我们原始函数的值，并神奇地应用所有参数，要么返回另一个函数*，在*中接受更多参数。起初，这可能不是很明显，但是在`curry`函数中有某种递归在进行，因为我们根据每个前面的函数的输入返回不同数量的函数**。**

考虑到这一点，我们可以开始编写`curry`函数的框架:

```
const curry = functionToCall => {
  const recursiveSomething = () => something => {
    if (someCondition) return functionToCall(someArgs)
    else return recursiveSomething()
  }

  return recursiveSomething()
} 
```

让我们一行一行地看。我们的`curry`函数接受一个名为`functionToCall`的参数，我们最终会调用这个参数(很棒的命名，对吗？).然后在下一行，我们定义一个返回另一个函数的递归函数。这里只使用函数名，以便我们能够根据需要递归返回函数；据我所知，在 Javascript 中不可能返回可以递归调用的匿名函数。返回的函数接受一些参数，根据`someCondition`，我们要么返回传递给它一些参数的`functionToCall`，要么返回调用`recursiveSomething`的结果，这是我们当前所在的函数。最后，我们调用`recursiveSomething`，返回我们的条件返回函数 mess。

这看起来很复杂，但是我们实际上已经写了一半的函数。剩下要做的就是填空。我们在这里试图解决的主要问题是**参数存储**:我们需要一些地方来存放我们将要接收到的所有参数，这样我们就可以一次性地将它们传递给我们的“回调函数”。最简单的方法是使用一个 [rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)，一个存储所有参数的数组，然后在调用
时，将该数组覆盖在`functionToCall`的参数上

```
const curry = functionToCall => {
  let argumentsArray = []
  const recursiveSomething = () => (...args) => {
    argumentsArray = argumentsArray.concat(args)
    if (someCondition) return functionToCall(...argumentsArray)
    else return recursiveSomething()
  }
  return recursiveSomething()
} 
```

浏览我们添加的行，我们可以看到我们添加了一个数组`argumentsArray`，它在`recursiveSomething`函数之外。这很重要，因为它不仅在根`recursiveSomething`返回函数的范围内，而且在*所有将来返回的函数*的范围内。在 return 函数中，我们添加了一个 rest 参数(允许我们的函数接受无限个参数，并将它们放入一个数组中)，然后用`argumentsArray`将它们连接起来。最后，我们使用[扩展语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)将数组中的参数应用到我们调用的`functionToCall`中。

这太好了，我们实际上真的很接近完成我们的 auto-curryer！我们只需要填写何时调用`functionToCall`，或者为我们的递归函数调用**基础用例**。当且仅当我们有了调用它所需的所有参数时，我们才调用`functionToCall`。Javascript 中的函数有一个[长度属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/length)，所以我们可以用它来检查`argumentsArray`的长度是否等于函数期望的参数个数:

```
const curry = functionToCall => {
  let argumentsArray = []
  const recursiveSomething = () => (...args) => {
    argumentsArray = argumentsArray.concat(args)
    if (argumentsArray.length === functionToCall.length) return functionToCall(...argumentsArray)
    else return recursiveSomething()
  }
  return recursiveSomething()
} 
```

就是这样！我们现在可以传递一个函数，由于递归的魔力，它会自动为我们处理所有的参数！只有八行还不错。如果你愿意，你还可以添加一些检查来支持零参数函数，并确保你正确地调用函数:

```
const curry = functionToCall => {
  if (functionToCall.length === 0) return functionToCall;
  let argumentsArray = [];
  const recursiveSomething = () => (...args) => {
    if (
      (args.length === 1 && argumentsArray.length + 1 > functionToCall.length) ||
      (argumentsArray.length === 0 && args.length > functionToCall.length) ||
      args.length + argumentsArray.length > functionToCall.length
    )
      throw new Error("Wrong number of arguments received");
    argumentsArray = argumentsArray.concat(args);
    if (argumentsArray.length === functionToCall.length) return toCall(...argumentsArray);
    return recursiveSomething();
  };
  return recursiveSomething();
}; 
```