# JavaScript 中的不变性

> 原文：<https://dev.to/zenika/immutability-in-javascript-2b6h>

*本文原载于媒体[此处](https://medium.zenika.com/immutability-in-javascript-7e1a19b45615)T3】*

**TLDR；** EcmaScript 提供了保持数据不变的实用程序。这些工具发现了嵌套结构的局限性。 [immutadot](https://github.com/zenika-open-source/immutadot) 是一个处理嵌套不可变结构的库。[试试看](https://immutadot.zenika.com/)。

* * *

根据[维基百科](https://en.wikipedia.org/wiki/Immutable_object)的说法，*不可变对象(不可改变的对象)是一个在被创建*后其状态不能被修改的对象。这个规则很简单，如果你想修改一个对象的一些属性，你必须在一个副本上完成。稍后我们将看到它为我们的开发带来了哪些改进和新奇的特性。

## EcmaScript

EcmaScript 提供了保持数据不变的实用程序。数组和对象 API 包含创建副本甚至防止实例被更新的方法。最近，EcmaScript 引入了新的语法来创建对象和数组的副本。

### 分配对象

我们想在对象中添加一个名称属性。

```
const lutraLutra = {
  commonNames: ['eurasian otter'],
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用`Object.assign`和一点小技巧做到。基本上，它将一个对象的所有属性复制到另一个对象中，从而改变目标对象。我们使用一个小技巧，将一个空对象作为第一个参数传递，这将创建一个新的引用。

```
const newLutraLutra = Object.assign(
  {}, 
  lutraLutra,
  { name: 'Lutra lutra' },
) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了一个新对象，它的新属性`name`和属性`commonNames`保持不变。使用这种方法，您可以同时创建/覆盖多个属性。

### Array.concat

现在让我们用一个数组来做。我们希望以不可变的方式在一个数组中添加两个新元素。

```
const commonNames = ['eurasian otter'] 
```

Enter fullscreen mode Exit fullscreen mode

与`Array.push`不同，`Array.concat`不会使我们的数组发生变异。相反，它返回一个新数组。

```
const newCommonNames = commonNames.concat(
  'european otter',
  'common otter'
) 
```

Enter fullscreen mode Exit fullscreen mode

这种方法是灵活的。它需要尽可能多的元素。它们可以是值或数组。

### 冻结

不是很熟悉。它让你使一个对象不可变！它可以防止因使用 setters 而导致的各种类型的突变(创建、修改、删除)。

```
let lutraLutra = {
  commonNames: ['eurasian otter', 'european otter', 'common otter'],
  name: 'Lutra lutra'
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在对象冻结后尝试删除`name`属性。

```
lutraLutra = Object.freeze(lutraLutra)

delete lutraLutra.name 
```

Enter fullscreen mode Exit fullscreen mode

重新分配是不必要的，因为作为参数传递的对象已经被`Object.freeze`变成不可变的。该方法有两种可用模式:

*   不应用突变的非严格模式
*   一种严格模式，如果您试图应用突变，它会抛出一个`TypeError`

小心，它不是递归的。我们的属性`commonNames`不是不可变的。

### 传播算子

扩展运算符语法在 ES2015 中引入用于数组，在 ES2018 中引入用于对象。它将给定对象的所有属性复制到新的对象文本中。

```
const newLutraLutra = {
  ...lutraLutra,
  name: 'Lutra lutra',
} 
```

Enter fullscreen mode Exit fullscreen mode

对于数组，它将数组中的所有值复制到一个新数组中。

```
const newCommonNames = [
  ...commonNames,
  'common otter',
] 
```

Enter fullscreen mode Exit fullscreen mode

它很好地替代了`assign`和`concat`，易于阅读，并在数组和对象之间创建了一个约定。可以在同一个文本中分布多个数组和对象。

## 为什么要用不变性？

您发现了如何用 JavaScript 使对象和数组不可变，但是我们还没有解释为什么现在使用不变性是如此必要。作为开发人员，我们总是在寻找一种方法来编写更易于维护和阅读的代码。一些范例，如函数式编程，正在关注这一点。

> 函数式编程的目标是让我们少思考，多写描述性代码。
> 
> ——<cite>[亚历山大](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-immutability-4e9751ca005c)孔多夫</cite>

它有一个声明式的编程方法，这意味着你关注于描述你的程序必须完成什么，而不是它应该如何完成。它赋予你的代码更多的意义，这样下一个开发者就可以更容易地理解它。函数式编程带来了有助于实现这一目标的其他概念，比如不变性。

### 有什么好处？

对你来说这听起来像一个炒作术语吗？不变性为我们每天遇到的编程问题带来了许多解决方案:

*   避免副作用
*   数据更改检测变得简单(浅层比较)
*   显式数据更改
*   记忆化
*   内存优化
*   更好的渲染性能
*   易于测试

> 与 JavaScript 世界中的大多数趋势不同，数据不变性注定会伴随我们一段时间，这是有充分理由的:首先，因为它不是一种趋势:它是一种编码方式(和用代码思考),促进了清晰性、易用性和对数据流的理解，并使代码不容易出错。
> 
> ——<cite>[李嘉图](https://hackernoon.com/data-immutability-with-vanilla-javascript-63834a65a6c9)</cite>

在过去的几年中，我们最大的挑战之一是找到一种有效的方法来检测数据的变化，以确定我们是否应该呈现我们的界面。检测原始值之间的变化很容易，但是对于对象和数组来说，这是一个完全不同的问题。您可以通过值来比较它们，但是您必须实现递归算法并处理潜在的问题，比如循环引用。另一种方法是用严格的相等操作符`===`来比较对象引用。它更有效，而且没有任何进入死亡无限循环的风险。这就是为什么现代框架会强化这个概念。

### 现代框架/库所强调的

现代前端框架和库是基于一个能显著提高性能的概念。这就是众所周知的虚拟 DOM。这项技术是根据一个简单的证据创建的:DOM 操作是昂贵的。

如前所述，前端框架和库选择使用不变性来提高它们的性能。如今，我们必须在应用程序中处理越来越多的数据，因此需要更多的标记。所以我们的浏览器需要比 10 年前处理更多的计算。DOM 操作是昂贵的，现代框架倾向于减少 DOM 更新的次数。

### 我们为什么需要实用程序库？

正如我们前面看到的，EcmaScript 中处理不变性的方法由于语法糖而变得简单，但在嵌套结构中却非常有限。随着像`redux`这样的库的出现，嵌套结构变得更加流行。

```
const animals = {
  weasels: {
    lutraLutra: {
      commonNames: ['eurasian otter'],
    },
  },
}

const newAnimals = {
  ...animals,
  weasels: {
    ...animals.weasels,
    lutraLutra: {
      ...animals.weasels.otter,
      name: 'Lutra lutra',
    },
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它变得越来越乏味，越来越难读。像覆盖数组的索引这样的简单用例是不容易实现的。

```
const lutraLutra = {
  name: 'Lutra lutra',
  commonNames: ['eurasian otter', 'european', 'common otter'],
}

const newCommonNames = [...lutraLutra.commonNames]
newCommonNames[1] = 'european otter'

const newLutraLutra = { 
  ...lutraLutra,
  commonNames: newCommonNames,
} 
```

Enter fullscreen mode Exit fullscreen mode

这些理由足以让你开始寻找一些工具，帮助你专注于真正重要的事情，你的代码的意义。这就是为什么我们创建了 [immutadot](https://immutadot.zenika.com/) ，来帮助我们保持 javascript 代码库的可读性和可维护性。[试试看](https://npm.runkit.com/immutadot)。