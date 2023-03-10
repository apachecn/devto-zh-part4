# JavaScript 中函数管道的简单解释

> 原文：<https://dev.to/benlesh/a-simple-explanation-of-functional-pipe-in-javascript-2hbj>

有时候有人问我，为什么 RxJS 中不再有“点链”，或者 RxJS 为什么改用`pipe`。原因有很多，但这确实是需要从更高的层面而不仅仅是 RxJS 来看待的事情。

对管道函数的需求来自两个相互冲突的问题:对简单类型(如 Array、Observable、Promise 等)的广泛可用开发 API 的需求，以及对发布更小应用的需求。

## 大小问题

JavaScript 是一种非常独特的语言，它有一个大多数其他编程语言都没有的问题:通常，JavaScript 通过网络传输，解析，并在用户想要使用 JavaScript 支持的应用程序时执行。装载的 JavaScript 越多，下载和解析的时间就越长，从而降低了应用程序的响应速度。会对用户体验产生巨大影响的东西。

这意味着尽量保持 JavaScript 应用程序小是至关重要的。幸运的是，如今我们有很多很棒的工具来做这件事。我们有很多“构建时”捆绑器和优化器，它们可以做像树抖动这样的事情，以便在构建时先清除未使用的代码，这样我们就可以向用户交付尽可能少的 JavaScript。

不幸的是，如果不能静态地确定代码没有在某个地方被使用，树抖动不会删除代码。

## 提供广泛的 API

为了使类型尽可能有用，最好将一组精心设计的已知功能附加到类型上。尤其是在这种方式下，通过在该类型上从左到右进行调用，它可以被“链接”。

JavaScript 为给定类型提供广泛 API 的“内置”方式是原型扩充。这意味着您将向任何给定类型的`prototype`对象添加方法。因此，如果我们想给数组添加一个自定义的`odds`过滤器，我们可以这样做:

```
Array.prototype.odds = function() {
  return this.filter(x => x % 2 === 1)
}

Array.prototype.double = function () {
  return this.map(x => x + x);
}

Array.prototype.log = function () {
  this.forEach(x => console.log(x));
  return this;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 原型增强有问题

**变异全局变量**。你现在在操纵别人都可以触摸的东西。这意味着其他代码可能会依赖于`Array`上的这个`odds`方法启动，而不知道它实际上来自第三方。这也意味着另一段代码可能会通过它自己对`odds`的定义来践踏`odds`。有解决这个问题的方法，比如使用`Symbol`，但是仍然不理想。

**原型方法不可动摇**。Bundlers 目前不会尝试移除已经修补到原型上的未使用的方法。推理见上。捆绑者无法知道第三方是否依赖于使用该原型方法。

## 功能编程 FTW！

一旦你意识到`this`上下文实际上只是向函数传递另一个参数的一种奇特方式，你就会意识到你可以像这样重写上面的方法:

```
function odds(array) {
  return array.filter(x => x % 2 === 0);
}

function double(array) {
  return array.map(x => x + x);
}

function log(array) {
  array.forEach(x => console.log(x));
  return array;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在的问题是，你必须从右到左，而不是从左到右来读取数组中发生的事情:

```
// Yuck!
log(double(odds([1, 2, 3, 4, 5]))) 
```

Enter fullscreen mode Exit fullscreen mode

不过，这样做的好处是，如果我们不使用`double`，比方说，一个捆绑器将能够从发送给用户的最终结果中移除`double`函数，使你的应用程序更小更快。

## 从左到右可读性的管道

为了获得更好的从左到右可读性，我们可以使用一个`pipe`函数。这是一个普通的函数模式，可以用一个简单的函数来完成:

```
function pipe(...fns) {
  return (arg) => fns.reduce((prev, fn) => fn(prev), arg);
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的目的是返回一个新的高阶函数，它只接受一个参数。返回的函数*会将参数传递给函数列表中的第一个函数`fns`，然后取其结果，并将其传递给列表中的下一个函数，依此类推。*

这意味着我们现在可以从左到右编写这些东西，这样可读性更好一些:

```
pipe(odds, double, log)([1, 2, 3, 4, 5]) 
```

Enter fullscreen mode Exit fullscreen mode

您还可以创建一个助手，允许您将参数作为第一个参数提供，以使其可读性更好(如果重用性稍差的话)，如下所示:

```
function pipeWith(arg, ...fns) {
  return pipe(...fns)(arg);
}

pipeWith([1, 2, 3, 4, 5], odds, double, log); 
```

Enter fullscreen mode Exit fullscreen mode

在`pipeWith`的例子中，现在它将接受第一个参数，并将其传递给参数列表中紧随其后的函数，然后它将接受其结果并将其传递给参数列表中的下一个函数，依此类推。

### 【可管道化】带参数的函数

要创建一个可以通过管道传输但有参数的函数，只需要一个高阶函数就可以了。例如，如果我们想做一个`multiplyBy`函数来代替`double` :

```
pipeWith([1, 2, 3, 4, 5], odds, multiplyBy(2), log);

function multiplyBy(x) {
  return (array) => array.map(n => n * x);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 作文

因为它只是函数，所以您可以通过使用`pipe`来创建其他可重用的、可管道化的函数，从而简化代码并使其更具可读性！

```
const tripleTheOdds = pipe(odds, multiplyBy(3));

pipeWith([1, 2, 3, 4, 5], tripleTheOdds, log) 
```

Enter fullscreen mode Exit fullscreen mode

## 更大的 JS 生态系统和管道运营商

这与 [RxJS 操作符](https://rxjs.dev/guide/operators)通过可观察`pipe`方法使用的模式大致相同。这样做是为了解决上面原型中列出的所有问题。但是这显然适用于任何类型。

虽然增强可能是 JavaScript 中向类型添加方法的“幸运”方式，但在我看来，这有点反模式。JavaScript 需要开始更多地拥抱这种模式，理想情况下，我们可以让一个简单版本的[管道运营商提案](https://github.com/tc39/proposal-pipeline-operator)在 JavaScript 中落地。

使用管道操作符，上面的代码可能看起来像这样，但是功能是相同的，并且不需要声明`pipe`助手。

```
pipeWith([1, 2, 3, 4, 5], odds, double, log);

// becomes

[1, 2, 3, 4, 5] |> odds |> double |> log 
```

Enter fullscreen mode Exit fullscreen mode