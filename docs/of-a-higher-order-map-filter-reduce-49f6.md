# 更高阶的——映射、过滤、减少

> 原文：<https://dev.to/baweaver/of-a-higher-order-map-filter-reduce-49f6>

了解`map`、`filter`和`reduce`在 Javascript 中有很大的价值。知道它们是怎么做的吗？这带来了一种全新的价值。

[![Marigold, the Javascript lemur](img/712dcecb55a6b7c04e312c26c2c0ca52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VKRBbzJ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v0e3s6b4to4zm99snkol.png)

不过，在我们开始之前，我们需要从函数式编程中学习一些技巧。

# 工具箱

编程中的大多数概念都是建立在其他概念之上的，这也不例外。函数式编程是一种在彼此的基础上构建概念来表达其他概念的艺术，因此学习如何制作这样的东西包括学习那些基本概念是有意义的。

你会发现这些工具以后也会很方便。

## 递归

递归是大多数函数式思维的基石，是将一个大问题分解成我们知道如何解决的小问题的能力。

一个经典的例子是阶乘，一个数字后面跟着一个感叹号(`5!`)，这是一种简写，表示“从这个数字到 1 的所有数字的乘积”，或者:

```
5 * 4 * 3 * 2 * 1 
```

Enter fullscreen mode Exit fullscreen mode

这里有个窍门:`5!`可以写成上面的样子，也可以写成`5 * 4!`。随之而来的是`4!`可以表达为`4 * 3!`等等。

更一般地说，我们可以说`n!`相当于`n * (n - 1)!`，直到我们碰到`1!`，也就是`1`。因为我们不知道`n!`是什么，我们可以继续问，直到我们点击`1`知道答案是什么。这叫做基本情况，一个已知的值。

可以这样做的函数可以写成:

```
function factorial(n) {
  if (n < 2) return n;

  return n * factorial(n - 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在不会保护负数，我们只需要看到这个函数将继续请求序列中的下一个数字，直到它到达`1`。

回头看看我们最初的乘法运算，这意味着:

```
5 * (4 * (3 * (2 * (1)))) 
```

Enter fullscreen mode Exit fullscreen mode

...这些 parens 中的每一个都指示堆栈中的另一个调用，直到它到达`1`，现在我们知道我们需要乘出什么。

现在递归可以用于数学问题之外的其他事情。它也可以用来迭代列表。

## 解构

在我们进入递归迭代之前，我们需要看一下析构，但只是一点点。

为此，我们需要能够获得列表的第一项，并将其余项作为单独的值。在 Javascript 中应该是这样的:

```
const [head, ...tail] = [1, 2, 3, 4, 5];

// head: 1
// tail: [2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

允许我们收集列表中剩余的项目，并将第一个项目从列表中分离出来。这一点很重要。

## 递归迭代

让我们从我们自己的`forEach` like 函数开始，我们称之为`each` :

```
function each(list, fn) {
  const [head, ...tail] = list;

  fn(head);

  if (!tail.length) return;

  each(tail, fn);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用析构从列表中选取第一项，并将其余的存储在`tail`中。之后，我们调用给定的函数参数，其值为`head`。

如果没有更多的项目，我们就完成了，所以`return`了。如果*还有*个条目，我们希望用`tail`作为新的列表进行递归，传递相同的函数。

可以通过向`each` :
传递一个列表和函数来调用它

```
each([1, 2, 3], console.log);
// 1
// 2
// 3 
```

Enter fullscreen mode Exit fullscreen mode

知道了`forEach`是如何工作的，我们可以在它的基础上或者直接通过递归来构建任何其他函数。这给了我们足够的工具来实现上面的三个功能，所以让我们开始吧。

# 功能三

我们已经准备好了工具，所以是时候考虑实现这些高阶函数了。什么是高阶？一个将另一个函数作为参数的函数，我们已经用`each`做了一个，所以剩下的没那么糟糕。

更好的是，每一个都引入了一个新概念，让我们在未来构建更多有趣的东西！

## Map -使用函数转换一个列表

`map`是一个高阶函数，用于转换一个列表，返回一个新的列表:

```
[1, 2, 3].map(x => x * 2);
// => [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用上面的技术来实现它，它看起来会像这样:

```
function map(list, fn) {
  if (!list.length) return [];

  const [head, ...tail] = list;

  return [fn(head), ...map(tail, fn)];
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先定义一个基本情况，当列表为空时，我们只返回一个空列表。如果不是这样，我们想把列表中的`head`和`tail`分开。

一旦我们有了这些，我们就可以返回一个新的数组，其中的函数`fn`是用`head`值调用的，然后我们可以用相同的函数对`tail`调用`map`的结果进行拉平。

我们传入的函数用于转换列表中的每个元素，它的返回值是新列表中的新值，即`map`将返回的值。

试一试，我们可以看到它与本机实现做了很多相同的事情:

```
map([1, 2, 3], x => x * 2);
// => [ 2, 4, 6 ] 
```

Enter fullscreen mode Exit fullscreen mode

## 过滤-使用函数过滤列表

`filter`是一个高阶函数，用于将一个列表过滤成一个新的列表，列表中的元素匹配一个条件:

```
[1, 2, 3].filter(x => x % 2 === 0);
// => [2] 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，这个实现非常类似于 map:

```
function filter(list, fn) {
  if (!list.length) return [];

  const [head, ...tail] = list;

  return fn(head) ? [head, ...filter(tail, fn)] : filter(tail, fn);
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的区别是，我们使用函数来决定列表中的某个项目是否应该出现在新列表中。如果它返回一个真值，我们添加它并继续下去，如果不是，我们就过滤列表的其余部分并忽略它。

这种类型的函数有时被称为谓词。

试一试，我们会发现它的工作方式和它的本地版本非常相似:

```
filter([1, 2, 3], x => x % 2 === 0);
// => [2] 
```

Enter fullscreen mode Exit fullscreen mode

## Reduce——使用一个函数将一个列表缩减为一个条目

现在`reduce`，`reduce`是各种类型的乐趣，有点难以理解。它也是三个中最强大的一个，原因我们马上会谈到。

让我们先解释一下它实际上是做什么的，因为这可能有点麻烦:

```
[1, 2, 3].reduce(function (accumulator, v) {
  console.log({ accumulator, v });
  return accumulator + v;
}, 0);
// { accumulator: 0, v: 1 }
// { accumulator: 1, v: 2 }
// { accumulator: 3, v: 3 }
// => 6 
```

Enter fullscreen mode Exit fullscreen mode

> `console.log({ a, b })`是我最喜欢的调试技巧之一，用“双关语”给调试的值起个名字，看看数据是怎么流动的。

Reduce 从一个初始累加器值(`0`)开始，这个值通常是一个“空”元素。对于添加数字，`0`被认为是“空的”,因为您可以向它添加任何内容，并返回相同的数字。

对于减少的每一步，返回值成为下一个累加器。在第一步中，我们将列表的第一个值添加到初始累加器中，这将返回新的累加器`1`，依此类推。

一旦它用完了值，它就返回累加器作为新值。

那么递归实现会是什么样子呢？我们来看看:

```
function reduce(list, fn, accumulator) {
  if (!list.length) return accumulator;

  const [head, ...tail] = list;
  return reduce(tail, fn, fn(head, accumulator));
} 
```

Enter fullscreen mode Exit fullscreen mode

...就这样？这里，这个函数与`map`和`filter`函数之间唯一真正的区别是，基本情况返回这个新的`accumulator`，递归通过用列表中的`head`和当前的`accumulator`运行函数来生成一个新的`accumulator`。

如果我们调用它，我们会得到相同的结果:

```
reduce([1, 2, 3], (a, v) => a + v, 0);
// => 6 
```

Enter fullscreen mode Exit fullscreen mode

为了确保万无一失，我们在这里放一些控制台日志，因为这看起来仍然很棘手:

```
function reduce(list, fn, accumulator) {
  if (!list.length) {
    console.log({ accumulator });
    return accumulator;
  }

  const [head, ...tail] = list;

  console.log({
    head, tail, accumulator, newAccumulator: fn(head, accumulator)
  });

  return reduce(tail, fn, fn(head, accumulator));
} 
```

Enter fullscreen mode Exit fullscreen mode

...再运行一次:

```
reduce([1, 2, 3], (a, v) => a + v, 0);
// { head: 1, tail: [ 2, 3 ], accumulator: 0, newAccumulator: 1 }
// { head: 2, tail: [ 3 ], accumulator: 1, newAccumulator: 3 }
// { head: 3, tail: [], accumulator: 3, newAccumulator: 6 }
// { accumulator: 6 }
// => 6 
```

Enter fullscreen mode Exit fullscreen mode

确实非常相似。

## 减少浩荡

它是最强大的是什么？reduce 的诀窍在于它对遵循三个规则的结构起作用:

1.  它有一个空元素(像`0`)
2.  它有办法将元素组合成同一类型的东西(`Int + Int === Int`)
3.  当元素被组合时，它们可以被分组，只要它们保持相同的顺序(`a + b + c === a + (b + c)`)

所以对于可能是`+`和`0`的整数。也可能是`*`和`1`。

令人兴奋的是:更多的类是这样的:

*   带有`+`和`""`的字符串
*   带有`concat`和`[]`的数组
*   带有`Object.assign`和`{}`的对象
*   ...还有更多。

这意味着我们可以在技术上实现上述任何功能，包括用`reduce`实现`forEach`。

这也意味着我们发现了一个有趣的特性。

## 名字的力量

上面的那些规则？他们有名字:

1.  Identity / Empty -一个元素，当与另一个元素组合时，产生该元素
2.  闭合/组合——可以将一种类型的两个元素组合成另一种相同类型的元素的操作
3.  关联性/分组-只要元素保持它们的顺序，就不用分组

那些规则，组合起来应用到某个东西上，也有一个名字:[幺半群](https://en.wikipedia.org/wiki/Monoid)。

这是一种说“以一种方式”或“像一件事”或可简化事物的奇特方式。当然，还有很多，但这是一个有趣的小发现。

# 包装完毕

函数式编程是一点一点构建的，有时会出现一些模式。您已经学习了其中的一些模式，这些模式在任何语言的编程中都非常有用。稍微有点不同的想法会产生各种令人兴奋的可能性，也许在这个过程中会有一两次无休止的维基百科探索。

在未来，我可能会把我的演讲从 RubyConf，[“减少可枚举性-一个图解冒险”](https://www.youtube.com/watch?v=x3b9KlzjJNM)翻译成 Javascript，甚至像我的其他演讲一样，在这里发布一个故事书版本。谁知道呢，也许将来你会看到狐猴出现在一两个 Javascript 活动中。