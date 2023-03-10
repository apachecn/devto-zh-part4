# 函数式编程介绍- #30DaysofFP 第 1 周

> 原文：<https://dev.to/dslemay/an-intro-to-functional-programming-30daysoffp-week-1-o3i>

1.5 周前，我通过一个为期 30 天的挑战开始学习更多关于函数式编程的知识。除了 JavaScript 中的各种数组方法，我开始对函数式编程知之甚少。凯尔·辛普森(Kyle Simpson)的[Functional Light JavaScript](https://leanpub.com/fljs)一书，是对概念和实际应用的极好介绍。对于感兴趣的人，这本书也可以在 [GitHub](https://github.com/getify/Functional-Light-JS/tree/master/manuscript) 上免费阅读。

在这篇文章中，我分享了我在开始这个挑战后学到的一些函数式编程的实用概念，以及使用函数式组合重构一个代码示例。一路上有许多代码示例和功能性的好东西。**注意:**代码片段包括几个函数式编程助手函数。这些实用程序的示例实现包含在代码片段中，但也可以在诸如 [Ramda](https://ramdajs.com/) 这样的库中找到。

## 局部应用

部分应用应用函数需要操作的一些参数，并返回等待剩余参数的函数。这允许预加载一个更通用的函数，使它成为一个更具体的函数。

```
const partial = (fn, ...presetArgs) =>
  (...laterArgs) =>
    fn(...presetArgs, ...laterArgs)

const add = (x, y) => x + y
console.log(add(5, 8)); //13

const add5 = partial(add, 5)
console.log(add5(8)); // 13 
```

当我们调用 partial 时，它收集第一个参数之后的所有参数，并将它们存储在`presetArgs`参数中。然后返回一个函数，该函数等待任何额外数量的参数。一旦收到这些参数，就调用函数，将`presetArgs`和`laterArgs`传播到函数调用中。使用这种模式，我们可以创建通用函数的更具体的实现。这可以用于其他情况，比如为 fetch 调用预加载 url，同时等待查询参数或 post 数据，等等。在下面的部分中包括了一些附加的实现示例。

## 逢迎

Currying 是部分应用函数的另一种方法，但是方法不同。与上面描述的`partial`函数不同，currying 返回一次只接受一个参数的函数。这可以提供额外的控制，使功能更加具体和可重用。

在深入 currying 的具体实现之前，理解 *arity* 的概念是很重要的。Arity 指的是函数可以接受的参数数量。例如，我们上面的`add`函数接受两个参数，arity 为 2。接受三个参数的函数的 arity 为 3。有一些特定的术语来描述 1 或 2 的 arity。

*   arity 为 1 的函数是*一元函数*。
*   一个 arity 为 2 *binary* 的函数。
*   具有三个或三个以上自变量的函数被称为 n 的 *arity，即 3* 的 *arity*

Currying 接受任何给定 arity 的函数，并将它们分解成多个一元函数。比如:

```
const curry = (fn, arity = fn.length, nextCurried) =>
  (nextCurried = prevArgs =>
    nextArg => {
      const args = [...prevArgs, nextArg]
      if (args.length >= arity) {
        return fn(...args)
      }
      return nextCurried(args)
    })([])

const add = (x, y) => x + y
const curriedAdd = curry(add)

const add5 = curriedAdd(5)
console.log(add5(12)); // 17

console.log(curriedAdd(5)(12)); // 17 
```

在返回新函数之前，`curry`实用程序只接受提供给它的第一个参数。例如，`curriedAdd(5, 3)(12)`的值仍然是 17，因为 3 被丢弃了。

## 功能成分

函数式编程能够安排函数一起工作，完成更大的逻辑块。这提供了更好的代码重用，并且可以预加载更复杂函数的逻辑，等待接收它们的数据。

这些概念已经存在于 JavaScript 的一些数组方法中。例如，假设我想写一个函数，它接受一个数字数组，过滤掉所有小于 5 的数字，然后返回剩余数字的和。用数组方法编写这个函数可能看起来像这样:

```
const nums = [2, 5, 8, 12, 3]

const sum = nums
  .filter(num => num > 5)
  .reduce((acc, val) => acc + val)

console.log(sum); // 20 
```

这种方法没有错，我知道我已经以类似的方式编写了许多函数。然而，给定过滤器和添加的逻辑被锁定在这个特定的实现中。由于 filter 和 reduce 都是 accept 函数，我们可以将这个实现的逻辑重构为它们自己的外部函数:

```
const nums = [2, 5, 8, 12, 3]

const greaterThan5 = num => num > 5
const adder = (x, y) => x + y

const sum = nums.filter(greaterThan5).reduce(adder)
console.log(sum); // 20 
```

这个代码片段在逻辑上与前面的实现相同。主要的区别在于，我们提取了函数的逻辑，以便它们可以在代码库的其他部分重用。也可以认为计算`sum`的调用点更清晰。过滤和减少现在关注的是正在发生的事情，而不是如何实现的。这个函数现在更多的是声明性的，而不是命令性的。如果你对这些概念不熟悉，我强烈推荐阅读泰勒·麦金尼斯的《T2》一书。

这种方法仍然保持了 filter 和 reduce 在一个数组上的链接。使用前面的部分实用程序，我们可以进一步抽象这种链接，以预加载一个等待数据数组的过滤函数。

```
const nums = [2, 5, 8, 12, 3]

const partial = (fn, ...presetArgs) =>
  (...laterArgs) =>
    fn(...presetArgs, ...laterArgs)
const filter = (fn, arr) => arr.filter(fn)
const reduce = (fn, arr) => arr.reduce(fn)

const greaterThan5 = num => num > 5
const adder = (x, y) => x + y
const filterGreaterThan5 = partial(filter, greaterThan5)
const reduceAdder = partial(reduce, adder)

const sum = reduceAdder(filterGreaterThan5(nums))
console.log(sum); // 20 
```

我们正在继续向函数式编程和组合方法迈进。在这里，我们为 filter 和 reduce 创建了实用程序助手。两者都接受一个函数作为第一个参数，接受要操作的数组作为第二个参数。使用我们之前定义的`partial`函数，我们可以用之前实现中的滤波器和加法器预加载这些函数。这里的`filterGreaterThan5`是一个函数，它等待一个数组，然后用我们的`greaterThan5`函数过滤这个数组。类似地，`reduceAdder`是一个等待数组的函数，然后用我们的`adder`函数减少数组。

在这个例子中，用来计算 sum 的函数可能看起来很奇怪。我知道一开始是为了我。不像英语，像这样的嵌套函数是从里到外读出来的。换句话说，这可以理解为:

1.  `filterGreaterThan5`被赋予一个`nums`参数并执行。
2.  `filterGreaterThan5`(另一个数组)的输出作为输入传递给`reduceAdder`
3.  `reduceAdder`在此输入下运行。该函数的输出被分配给常量`sum`。

这种方法使用更多的函数，但是每个函数的范围都更小。当我们构建我们的应用程序时，我们会创建更适合我们用例的函数。这种方法的一个主要好处是，每个函数现在都可以在应用程序的其他地方使用，而无需重写逻辑。对于我们的例子，重构的最后一个障碍和要点是清理嵌套的函数调用。这个特殊的例子只有两层嵌套的调用，但是你可以想象这可能会变得单调乏味。谢天谢地，有一个`compose`函数实用程序可以帮助我们。

Compose 是一个函数，它接受其他函数作为它的参数，并以编程方式将它们包装在彼此内部。最终结果是一个等待数据的函数。这允许我们不像上面的例子那样手工包装我们的函数。

```
const nums = [2, 5, 8, 12, 3]

// Utility functions
const partial = (fn, ...presetArgs) =>
  (...laterArgs) =>
    fn(...presetArgs, ...laterArgs)
const compose = (...fns) =>
  result =>
    [...fns].reverse().reduce((result, fn) =>
      fn(result),
    result)
const filter = (fn, arr) => arr.filter(fn)
const reduce = (fn, arr) => arr.reduce(fn)

const greaterThan5 = num => num > 5
const adder = (x, y) => x + y
const filterGreaterThan5 = partial(filter, greaterThan5)
const reduceAdder = partial(reduce, adder)

const addNumsGreaterThan5 = compose(reduceAdder, filterGreaterThan5)
const sum = addNumsGreaterThan5(nums)
console.log(sum); // 20 
```

我们的`addNumsGreaterThan5`函数现在正在等待一组数字。接收到它后，将运行 filter 和 reduce 函数，返回总和。现在，我们可以在应用程序中用任何数字数组调用这个函数。此外，这种组合结构允许我们将功能性乐高积木换成另一个积木来改变实现。例如，我们可以将过滤函数切换为过滤小于 5 的数字，而不是大于 5 的数字。

## 下一步

函数式编程旨在使代码可读性更强，更容易推理。如果函数有直接的输入和输出，开发人员就不需要跟踪函数调用堆栈来查看应用程序状态的哪些部分被管理。从心理上分析运行该函数的效果变得更加容易。这在很大程度上取决于函数的纯度和管理副作用，这将是下周的讨论内容。组合更小的函数使我们的代码更具声明性，关注正在发生的事情而不是实现细节。

我 30 天函数式编程的第一周是学习新材料的疯狂旅程。您可以在 [Twitter](https://twitter.com/search?q=%2330DaysOfFP+from%3adslemay+since%3a2019-08-01) 上加入学习讨论。