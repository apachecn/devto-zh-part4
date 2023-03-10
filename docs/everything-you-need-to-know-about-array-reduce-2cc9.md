# 关于 Array#reduce 您需要知道的一切

> 原文：<https://dev.to/worsnupd/everything-you-need-to-know-about-array-reduce-2cc9>

*交叉发布自[我网站的博客](https://www.danielworsnup.com/blog/everything-you-need-to-know-about-array-reduce/)。*

`Array#reduce`或`Array.prototype.reduce`(从现在开始简称为`reduce`)，是 JavaScript 标准库中我最喜欢的函数。虽然这需要一些时间来适应，但是 100%的努力是值得的。一旦完全掌握了`reduce`的力量，它将使您能够以一种声明性的、可读的方式重新定义复杂的逻辑。

这篇文章分为两个主要部分:1)解释什么是`reduce`以及它是如何工作的，2)演示一些你以前可能没有考虑过的有趣的 reduce 应用。如果你是有`reduce`的老手，讲解部分就复习一下。请随意跳到[演示部分](#what-are-some-applications-of-reduce)。

## 什么是减少？

简单地说，`reduce`是一个函数，它让你*将一个数组的值*减少到一个值。这个值，我们称之为*缩减值*，可以是你想要的任何类型。您经常会发现自己需要将一个数组简化为众多 JavaScript 基本类型中的一种，比如 object、number、boolean，甚至是另一个数组(我们将在后面看到一些这样的例子！)，视情况而定。然而，您并不局限于简化为基本类型。减少的值可以是您想要的任何类型，比如“T1”、“T2”，或者由您的项目定义的任何自定义类型。

在 JavaScript 中，在`Array`原型(`Array.prototype.reduce`)上定义了一个`reduce`函数，这意味着您可以在任何数组实例上调用它:

```
const myArray = [1, 2, 3];
const reducedValue = myArray.reduce(/* args */); 
```

### 如何减少工作？

你调用的数组`reduce`描述了*你想要减少什么*，传递给`reduce`的参数描述了*你想要如何从数组中构建减少的值。`reduce` 的 [MDN 文档在详述`reduce`的输入和输出方面做得很好。去看看吧！我将在这里做一个高层次的概述。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)*

#### 参数

1.  `reducer`功能。不要将它与 Redux 中使用的状态管理缩减器函数相混淆。虽然概念相似，但它们并不相同。
2.  reduce 循环的初始值。

#### 减速器功能

当您在数组上调用`reduce`时，`reduce`将一次迭代数组中的一个元素，为每个元素调用一次 reducer 函数。当`reduce`调用您的 reducer 函数时，它会传入以下四个参数:

1.  蓄电池
2.  电流元件
3.  当前索引
4.  源阵列

现在不要太担心最后两个参数。实际上，我很少发现自己需要使用它们。

*累加器*(有时称为*收集器*)是表示对数组中的每个元素调用 reducer 函数的结果的值，直到但不包括当前元素。这实际上是“到目前为止降低的价值”。这是减速器功能的本质:

> reducer 函数描述了如何将累加器(到目前为止减少的值)与数组的当前元素相结合，以产生用于下一次迭代的累加器。

初始值(`reduce`的第二个参数)充当第一次调用 reducer 函数的累加器，最后一次调用 reducer 函数返回的值是最终从`reduce`调用返回的最终缩减值。

#### 案例分析:`sum`函数

我们都熟悉`sum`函数。让我们来看一个简单的实现:

```
function sum(numbers) {
  let sumSoFar = 0;

  for (const number of numbers) {
    sumSoFar += number;
  }

  return sumSoFar;
} 
```

关于`sum`函数可能不明显的是，它实际上只是`reduce`的一个特例。`sumSoFar`变量作为累加器:

```
function sum(numbers) {
  let accumulator = 0;

  for (const number of numbers) {
    accumulator += number;
  }

  return accumulator;
} 
```

`for`循环的主体描述了如何将当前元素(`number`)与当前累加器结合起来，为下一次迭代产生下一个累加器。这听起来应该很耳熟吧！用`reduce`，这是减速器的工作功能:

```
function sum(numbers) {
  let accumulator = 0;

  for (const number of numbers) {
    accumulator = reducer(accumulator, number);
  }

  return accumulator;
}

function reducer(accumulator, currentElement) {
  return accumulator + currentElement;
} 
```

请注意，我们是如何通过将计算下一个累加器的逻辑转移到 reducer 函数中来创建抽象层的。在这一点上，我们非常接近真正的`reduce`实现。让我们通过重命名一些东西并允许 reducer 函数和初始值被传入来完成它:

```
function reduce(array, reducer, initialValue) {
  let accumulator = initialValue;

  for (const currentElement of array) {
    accumulator = reducer(accumulator, currentElement);
  }

  return accumulator;
} 
```

支持 reducer 函数的最后两个参数(数组索引和数组本身)很简单。为了跟踪当前的数组索引，我们可以切换到标准的`for`循环，而不是`for...of` :

```
function reduce(array, reducer, initialValue) {
  let accumulator = initialValue;

  for (let i = 0; i < array.length; ++i) {
    accumulator = reducer(accumulator, array[i], i, array);
  }

  return accumulator;
} 
```

最后但同样重要的是，使用 native `reduce`我们不需要传入数组，因为我们在数组上调用`reduce` *。为了便于说明，它看起来像下面这样，但是请记住，我们不会在生产中运行这段代码。通常没有一个好的理由来重写本地 JavaScript 函数的行为:* 

```
Array.prototype.reduce = function(reducer, initialValue) {
  let accumulator = initialValue;

  for (let i = 0; i < this.length; ++i) {
    accumulator = reducer(accumulator, this[i], i, this);
  }

  return accumulator;
} 
```

注意，当函数在`Array.prototype`上定义时，我们可以将数组本身称为`this`。

## reduce 有哪些应用？

我们来看看一些野外的 reduce 函数的例子！

接下来的一些例子展示了在`Array.prototype`上定义的函数。请注意，我并不打算推荐这样的代码在生产中运行。这些例子旨在演示如何实现一些本地的`Array.prototype`方法*。实际上，我们总是希望使用现有的本机实现，而不是用我们自己的实现覆盖它们。*

 *### `sum`功能

我们已经看到了一个简单的 sum 函数如何被稍微修改成为实际的`reduce`函数，但是让我们再来看看`sum`是如何使用 reduce:
编写的

```
function sum(numbers) {
  return numbers.reduce((accumulator, currentElement) => {
    return accumulator + currentElement;
  }, 0);
} 
```

注意初始值`0`，以及 reducer 函数如何简单地将当前元素添加到累加器中以产生下一个累加器。通过利用`reduce`,我们解锁了一种极其声明性的方式来编写这个 sum 循环。

虽然`accumulator`和`currentElement`是在`reduce`循环的上下文中使用的合理的变量名，但是你会发现实际上通常有更好的名字，更适合正在编写的代码的上下文。例如，在`sum`函数的情况下，`sumSoFar`和`number`这两个名字传达了更多的间接含义，可能对其他人(甚至是你)更有帮助！)在代码审查期间或将来阅读代码:

```
function sum(numbers) {
  return numbers.reduce((sumSoFar, number) => {
    return sumSoFar + number;
  }, 0);
} 
```

### `map`功能

`map`功能是一个非常有用的功能，应该挂在你的工具带上，方便快捷地使用。如果不是，去[看看 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 上的`Array.prototype.map`。

> 对数组的每个元素调用一个函数(“映射”函数，由您定义),并返回一个包含每个函数调用结果的新数组。

这里有一个`map`的例子:

```
function addOneToEach(numbers) {
  return numbers.map((number) => number + 1);
}

addOneToEach([1, 2, 3]) // [2, 3, 4] 
```

关于`map`最可能没有意识到的是，它实际上只是`reduce`的一个特例！不像`sum`，我们把一个数组简化为一个数字，而`map`我们把一个数组简化为另一个数组。因此，我们将空数组作为初始值传递。下面是它的样子:

```
Array.prototype.map = function(mapperFn) {
  return this.reduce((accumulator, currentElement) => {
    const mappedCurrentElement = mapperFn(currentElement);

    return [...accumulator, mappedCurrentElement];
  }, []);
} 
```

注意，reducer 函数需要做的唯一事情是通过传入的 mapper 函数运行当前元素，然后将它添加到累加器的末尾，累加器被初始化为一个空数组。

随着输入数组大小的增长，上面的`map`实现会有严重的性能问题。这是因为 reducer 函数在每次迭代中创建一个新的数组，然后在最后追加新映射的当前值之前，*将累加器的元素复制到*数组中。如果你做相关的数学计算，你会发现这种方法的时间复杂度(假设映射函数的时间复杂度是常数)大约为 O(n <sup>2</sup> )。

这是不好的，所以让我们来解决它！我们没有理由不在每次迭代中创建一个新的数组，而是在整个归约过程中使用同一个数组。在每次迭代中，我们可以将映射的当前元素推送到数组中，并在下一次迭代中返回:

```
Array.prototype.map = function(mapper) {
  return this.reduce((accumulator, currentElement) => {
    const mappedCurrentElement = mapper(currentElement);

    accumulator.push(mappedCurrentElement);

    return accumulator;
  }, []);
} 
```

这种方法有两个好处:

*   我们已经将时间复杂度提高到线性(或 O(n))时间，并且
*   作为初始值传入的数组与最终返回的数组相同。

### `filter`功能

这又是一个要熟悉的！如果你不是，请前往 MDN 查看。

> `filter`对数组的每个元素调用一个函数(“过滤”函数，由您定义),并返回一个新数组，该数组包含过滤函数返回了一个[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)的原始数组的所有元素。

下面是一个“过滤器”的例子:

```
function removeUndefined(array) {
  return array.filter((x) => x !== undefined);
}

removeUndefined([1, true, undefined, 'hi']); // [1, true, 'hi'] 
```

可能不太明显的是，`filter`也只是`reduce`的一个特例！它使用 reduce 循环的实现与`map`非常相似。唯一的区别是`map`的 reducer 函数*无条件地*将映射的元素追加到累加器中，而`filter`的 reducer 函数*有条件地*将原始元素追加到累加器中，这取决于用该元素调用 filter 函数的结果。下面是它的样子:

```
Array.prototype.filter = function(filterFn) {
  return this.reduce((accumulator, currentElement) => {
    if (filterFn(currentElement)) {
      accumulator.push(currentElement);
    }
    return accumulator;
  }, []);
} 
```

酷！

### `some`功能

不要与我们已经讨论过的`sum`函数混淆。与`map`和`filter`相比，`some`功能不太为人所知，但是它有使用案例，绝对值得在您的工具箱中扮演一个小角色。如果你是第一次来`some`的话，去看看。

> `some`对数组中的每个元素调用一个函数(“测试”函数，由您定义),并返回一个布尔值，表明测试函数是否为数组中的任何元素返回一个真值。只要一遇到测试函数返回一个[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)的元素，它就通过终止数组迭代来“短路”尽可能多的计算。

这里有一个`some`的例子:

```
function gotMilk(array) {
 return array.some((x) => x === 'milk');
}

gotMilk(['juice', 'water']); // false
gotMilk(['juice', 'milk', 'water']); // true 
```

你可能已经猜到了这将走向何方...是的— `some`其实只是`reduce`的一个特例。不像`sum`(我们减少到一个数字)和`map`和`filter`(我们减少到一个数组)，用`some`我们减少到一个布尔值。布尔累加器指示到目前为止数组中是否有任何值从测试函数返回 true。因此，我们将累加器初始化为`false`，一旦它翻转为`true`，我们就停止调用数组其余部分的测试函数:

```
Array.prototype.some = function(testFn) {
 return this.reduce((accumulator, currentElement) => {
   if (accumulator) { return accumulator; }
   return testFn(currentElement);
 }, false);
} 
```

`some`的`reduce`实现比本机实现的性能稍差。一旦遇到真值，本机实现就停止迭代，而`reduce`实现只是停止调用测试函数，但不会停止迭代。我们*可以*通过在达到真值时抛出 reducer 函数的异常来解决这个问题，在外面捕捉异常，然后返回`true`。然而，这违背了使用`reduce`的初衷。

展示使用`reduce`的`some`的实现的原因是为了说明`some`函数的*思想*是`reduce`函数的一个特例，即使使用`reduce`不能容易地编写`some`的一个性能实现。

### 还有这些！

与`some`类似，下面的`Array.prototype`方法都是`reduce`的特例，可以用简单的减速器函数实现:

*   `every`
*   `find`
*   `findIndex`
*   `indexOf`
*   `flat`

正如我们在`some`中看到的，这些函数中有一些能够提前终止数组迭代，因此不能使用`reduce`高效地实现。尽管如此，观察它们都是我们想要将数组缩减为单个值的特定情况是有价值的。

## 那又怎样？

`reduce`函数代表了一个简单的想法:将一个数组简化为一个值。毫不奇怪，它还拥有一个简单的实现。如此简单，事实上，我们可以通过对一个简单的`sum`函数做一些小的修改来实现它！

但是我们不应该被`reduce`在这些方面的简单所迷惑。`reduce`的强大和适用性在`Array`原型上的大量函数(如`map`、`filter`和`some`)中显而易见，这些函数只是`reduce`的特例，可以用简单的 reduce 循环来实现。这并不是建议我们*应该*使用`reduce`来代替这些更具体的功能。使用`reduce`的特例(而不是`reduce`本身)可以提高代码的可读性！相反，我指出这一点是为了展示`reduce`的威力。

力量和美丽存在于简单之中。它们不需要复杂性。相反，要尽量避免复杂！请这样想:一个简单的问题解决方案将会更容易实现。会更难不小心把 bug 写进去。对于另一个程序员来说，接手并在其上构建或改变会更容易。测试起来会更容易。不胜枚举！

用伟大的埃德格·沃克·迪克斯特拉的话说:

> 简单是一种伟大的美德，但它需要努力工作才能实现，需要教育才能欣赏。更糟糕的是:复杂性卖得更好。

并且:

> 简单是可靠的前提。

简单的解决方案比复杂的解决方案更好，这几乎是可以想象的。困难的是*提出*简单的解决方案。这是一项你将花费整个职业生涯来发展的技能，而且永远不会完美。

我现在就知道这么多了！希望你已经受到启发，不仅在你自己的代码中寻找机会，而且在你有带宽的时候追求更简单的解决方案。长远来看会有回报的！

编码快乐！

#### 喜欢这个帖子？

在 Twitter 上关注我，我在那里发关于前端的消息: [@worsnupd](https://twitter.com/worsnupd)*