# JavaScript 迭代器和可迭代对象

> 原文：<https://dev.to/appsignal/javascript-iterators-and-iterables-1bak>

欢迎来到我们关于 JavaScript 的第一篇深度文章！在 AppSignal，我们正准备推出全新的前端监控解决方案，我们对此感到非常兴奋，希望您也能如此。

在过去的几年里，我看着 JavaScript 从一种有点奇怪的东西——一种公认不完美，但经常被误解的浏览器脚本语言——发展成为一种强大而富有表现力的语言，可以在多种环境中部署，在当今的计算环境中几乎无处不在。

AppSignal.com 的这个角落的目的是更详细地探索这种语言，并揭示 JavaScript 的优秀之处。就像我们的姐妹博客 [Ruby Magic](https://blog.appsignal.com/category/ruby-magic.html) 和 [Elixir Alchemy](https://blog.appsignal.com/category/elixir-alchemy.html) 一样，我们将深入研究语言特性、模式和框架，并分享一些其他的 JavaScript 见解。

我们开始吧！但首先，我们来谈谈 Ruby。

## 在链表和 Rubyists 上

在《T2》之前版本的 Ruby Magic 中，Jeff 探索了 Ruby 的`Enumerator`对象和`Enumerable`模块。杰夫是这样描述的:

> 枚举是指遍历对象。在 Ruby 中，当一个对象描述了一组条目和一个遍历每个条目的方法时，我们称之为可枚举的。

好吧，听起来很有用！我已经知道你想要这个的很多原因了。在前面提到的文章中，Jeff 使用`Enumerable`实现了一个[链表](https://en.wikipedia.org/wiki/Linked_list)——一种常见的、几乎永恒的数据结构类型，它是数据元素的集合，其中每个元素指向下一个元素。列表中的每个元素都有两个值，名为 head 和 tail。头部保存元素的值，尾部是到列表其余部分的链接。

通过确保链表响应`#each`方法，并包含`Enumerable`模块，就有可能在 Ruby 中实现这种数据结构，而无需编写一大堆代码。这让我想到——我想知道 JavaScript 是否能做类似的事情？

答案是:可以！但是，这不会是一篇 JavaScript 博文，除非我告诉你，当然，这里的情况有点不同。今天，我们将向您介绍 JavaScript 与 Ruby 的`Enumerable`类的近亲 *[可迭代](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)* ，以及我们如何利用它来编写我们自己的`LinkedList`类。

你们中的一些人可能从来没有实现过链表。毫无疑问，你们中的许多人在工作面试中已经实施了一个。也许你，[像 React 团队](https://medium.com/react-in-depth/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-67f1014d0eb7)一样，已经在用它们做你的代码库中不平凡的事情了。我们今天要实现的例子几乎是 Jeff 的 Ruby `LinkedList`类到 JavaScript 的精确移植，由于实现的简单性，我真的很喜欢它。也许，与“全脂肪”实现相比，更容易理解这里发生的事情。

它没有抓住所有的边缘情况，也没有提供您可能期望的许多类方法，但是应该有助于说明这个想法。我警告你:如果我发现你在生产中使用这些代码，你将被送进编程地狱，在那里，再多的随机组合键也不能帮你退出 Vim。

好吧，我们开始吧。

## 那么，什么是迭代器呢？

JavaScript 中的 iterable 是一个对象，它通过自身或其原型链中的任何对象上的方法来定义自定义迭代行为。您可能已经非常熟悉一些可迭代的内置 JavaScript 类型，主要是`Array`、`Map`、`Set`和`String`。用普通的编程术语来说，我们说这些类型可以被“循环”；给定一个类似于`for`循环的结构，我们可以从 iterable 中按顺序提取每个值，并对其进行处理。

JavaScript 提供了`for...of`循环来迭代一个通用的 iterable:

```
for (let value of iterable) { 
  console.log(value); 
} 
```

你也可以 *[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)* 一个 iterable 来获得它的值的子集作为命名变量。在以下示例中，`a === 'a'`和`b === 'b'` :

```
const [a, b] = new Set(['a', 'b', 'c']); 
```

Iterables 甚至可以扩展成一个数组文字，将 iterable 转换成一个线性数组，并允许您调用类似。map()或。对返回值进行过滤:

```
[...iterable].map(el => console.log(el)); 
```

那么是什么使得一个对象是可迭代的呢？这是事情开始变得更高级的地方。

## `@@iterator` -无形的财产

为了成为 iterable，必须在对象本身上实现一个特殊的函数- `@@iterator`。现在，对你们中的许多人来说，你们幸福地不知道这个财产曾经存在是可以原谅的。调用`iterable.@@iterator`无法访问。它不会出现在`for`循环中，也不会出现在对 iterable 调用`Object.keys`时。通常，`console.log`甚至不会透露这种属性。那么，它在哪里？

与其他编程语言不同，JavaScript 没有[(还没有)](https://github.com/tc39/proposal-private-methods)关于对象的私有方法或私有字段的概念，但是我们可以通过使用一种称为[符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)的特殊 JavaScript 类型来引用对象的属性，使其成为“伪私有的”。`@@iterator`属性是这样实现的:`@@iterator`属性的值只能用一个`Symbol`键引用，这个键被定义为`Symbol`类型本身的常量:`Symbol.iterator`。

访问它是这样工作的:

```
class LinkedList {
  // ...
  [Symbol.iterator]() {}
}

// ...or using an object literal
const LinkedList = {};
LinkedList[Symbol.iterator] = function () {}; 
```

在给定的`class`或对象上，如果键是`Symbol.iterator`，值必须是一个函数。在迭代器的经典同步实现中，该函数返回一个对象(称为 *iterable* )，该对象实现了一个名为`next()`的函数作为属性。让我们进一步扩展一下我们的例子，看看这是什么样子:

```
class LinkedList {
  // ...
  [Symbol.iterator]() {
    return {
      next() {
        return {
          value: "a value",
          done: false
        }
      }
    }
  }
} 
```

神圣的嵌套语句！我们已经设法在我们闪亮的新代码库中建立了一个小金字塔，但是我们已经成功地实现了一个*迭代器*，它返回一个*可迭代的*。iterable 本身返回一个具有两个属性的对象:`value`和`done`。不出所料，`value`是迭代器的当前值，`done`是一个布尔值，如果值序列已经结束，它将与迭代器通信。如果`done === true`，那么`value`属性可以被发射。

现在我们对迭代器和可迭代对象的工作原理有了更多的了解，让我们看看如何应用这些知识来构建一个`LinkedList`。

## 大厦`LinkedList`

让我们从将 Jeff 的 Ruby 类移植到 JavaScript 开始，去掉用于创建`Enumerable` :
的`#each`方法

```
class LinkedList {
  constructor(head = null, ...rest) {
    this.head = head;

    if (rest[0] instanceof LinkedList) {
      this.tail = rest[0];
    }
    // roughly equivalent to `rest.any?` in ruby
    else if (rest.some(el => el)) {
      this.tail = new LinkedList(...rest);
    }
    else {
      this.tail = null;
    }
  }

  add(item) {
    return new LinkedList(item, this);
  }
} 
```

到目前为止，一切顺利。使用上面的例子，我们已经可以创建一个新的`LinkedList`，并向`LinkedList`的头部添加新的项目，使用 *[rest](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) 和 [spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)* 操作符(`...`)来创建尾部。作为构造函数的第一个参数，我们允许任何使用我们的`LinkedList`类的人传递一个`head`作为链表的顶部，并且`constructor`中的 *rest* 操作符能够转换任何不是`head`的剩余参数，并将它们转换成一个数组。`else if`语句从这个数组创建一个新的`LinkedList`，并继续这样做，直到`rest`中的最后一项属于一个`LinkedList`的`head`。

现在，我们需要实现从`LinkedList`中检索条目的逻辑，但是我已经看到了一个问题。如果我们使用上面概述的技术实现一个迭代器和后续的 iterable，那么我们已经大大偏离了 Jeff 最初的设计。还有很多代码要写，我们需要以某种方式维护状态，因为我们需要通过将`done`设置为`true`来告诉 iterable 我们的序列已经完成。当然有可能，但是我觉得我们可以想出更优雅的。

进入发电机功能。

## 发电机功能

我们设置为`Symbol.iterator`的值也可以是一个[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)，这是 ECMAScript 2015 中引入的一种新型函数。最容易想到的生成器函数是一个可以随意退出和返回的函数，可以选择使用关键字`yield`返回值。使用[闭包](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch5.md)的能力，我们可以跨多个`yield`和重入来维护函数的状态。重要的是，生成器函数具有与 iterable 相同的接口，这意味着可以用与我们自己实现 iterable 相同的方式检索值。

让我们实现一个迭代器，使用生成器函数
从`LinkedList`中获取所有的值

```
class LinkedList {
  // ...implementation

  *[Symbol.iterator]() {
    yield this.head;
    let next = this.tail;

    while (next !== null) {
      yield next.head;
      next = next.tail;
    }
  }
} 
```

## 全面实现

所以，当一切都结束了，这就是我们的结局:

```
class LinkedList {
  constructor(head = null, ...rest) {
    this.head = head;

    if (rest[0] instanceof LinkedList) {
      this.tail = rest[0];
    }
    // roughly equivalent to `rest.any?` in ruby
    else if (rest.some(el => el)) {
      this.tail = new LinkedList(...rest);
    }
    else {
      this.tail = null;
    }
  }

  add(item) {
    return new LinkedList(item, this);
  }

  *[Symbol.iterator]() {
    yield this.head;
    let next = this.tail;

    while (next !== null) {
      yield next.head;
      next = next.tail;
    }
  }
} 
```

然后我们可以像这样使用新的`LinkedList`类:

```
const ll = new LinkedList(0, 1, 1, 2, 3, 5, 8, 13);

for (let value of ll) { 
  console.log(value); // output: 0, 1, 1, 2, 3, 5, 8, 13
}

const [a, b] = ll; // a = 0, b = 1

[...ll].map((num) => console.log(num)); // output: 0, 1, 1, 2, 3, 5, 8, 13 
```

## 就是这样！

第一次运行该函数时，我们`yield`当前的磁头。然后，只要有一个尾部要读，我们就把列表项的头放在尾部。一旦我们做到了这一点，迭代器就是隐式的`done`。在七行代码中，我们实现了迭代器。厉害！

让我们知道你对这个博客的想法，或者你想让我们在 Twitter 上写什么 JavaScript 奇迹