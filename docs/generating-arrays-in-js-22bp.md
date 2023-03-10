# 在 JS 中生成数组

> 原文：<https://dev.to/emnudge/generating-arrays-in-js-22bp>

这种情况并不经常出现，但在年轻程序员的生活中，总有一段时间他们不得不生成一组数字。良好的...不总是。一些程序员有时可能需要...嗯**我**做过一次！

一个这样的用例是在前端框架中，你需要在一个`select`菜单上显示一组数字。我个人不建议使用`select`，但是如果到了客户或老板要求你这么做的时候，说“不”就不太好了。

让我们从最简单的选项开始，然后继续研究越来越复杂的例子。

# For 循环

对于我们所有的例子，让我们试着生成数字 0-60。假设我们使用它让用户在表单中选择特定的秒或分钟。当处理这个问题时，for 循环可能是人们想到的第一个例子。

```
const arr = [];
for (let i = 0; i < 61; i++) {
  arr.push(i);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们只是简单地递增`i`并在每次递增时将`i`添加到预定义的数组中。一天结束时，我们得到一个包含 61 个元素的数组，0-60 包含在内。

这种方法很好，但在处理语句时，它不是“`functional`”。这意味着我们不能在`JSX`中内嵌它。我们必须将它放入一个函数中，并在`render`中调用它。这不一定是“坏”，只是多了一点。

# 数组功能

虽然我们可以将逗号分隔的元素传递给`Array()`，为了创建一个新的数组，我们也可以只提供一个参数。这将是一个描述要生成的数组长度的数字。这是我们需要记住的一个陷阱:

```
Array(50, 5)        // -> [50, 5]
Array(50, 5).length // -> 2

Array(50)           // -> [empty × 50]
Array(50).length    // -> 50 
```

Enter fullscreen mode Exit fullscreen mode

你可能还会注意到，我们正在创建一个长度为`50`的**空**数组。我们*没有*有 50 个元素。这和做:
是一样的

```
const arr = []
arr.length = 50; 
```

Enter fullscreen mode Exit fullscreen mode

这些被称为阵列“孔”。我们习惯于用`undefined`代替未定义的变量，但是除了空数组的长度之外，我们实际上没有改变任何东西。

现在，我们可能认为我们可以通过做以下事情来生成一个数字为 0-60 的数组:

```
Array(61).map((_, i) => i) // -> [empty × 61] 
```

Enter fullscreen mode Exit fullscreen mode

但是你错了。我们无法迭代`empty`个项目。【Axel Rauschmayer 博士更深入地讨论了这个问题[这里](https://2ality.com/2015/09/holes-arrays-es6.html)和[这里](http://speakingjs.com/es5/ch18.html#array_holes)，但是我们本质上需要用*东西*填充我们的数组，以便迭代它。
我们可以用两种方法中的一种——使用`Array.prototype.fill`或`Function.prototype.apply`。

```
Array(61).fill()             // -> [undefined x 61]
Array.apply(null, Array(61)) // -> [undefined x 61] 
```

Enter fullscreen mode Exit fullscreen mode

我推荐前者(`.fill()`)，因为它可读性更强，更容易理解。这就把我们最后的表达式变成了:

```
Array(61).fill().map((_, i) => i) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想让它变得更清晰一点呢？

# 使用 Array.from

有没有另一种被称为“类数组”数据结构的方法用得更多一点。`Array.from`可用于将任何具有`length`属性的对象转换成数组。

您可能已经看到过在类似处理 DOM 节点的上下文中使用的`Array.from`:

```
const divNodeList = document.querySelectorAll('div');
const divArr = Array.from(divNodeList);
const texts = divArr.map(el => el.textContent); 
```

Enter fullscreen mode Exit fullscreen mode

`Array.from`将遍历对象的编号属性，直到找到长度属性，并用`undefined`替换找不到的内容。我们实际上可以用 JS:
相当容易地重新创建它

```
const getArr = obj => {
  const arr = [];

  for (let i = 0; i < obj.length; i++) {
    arr.push(obj[i]);
  }

  return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个，够搞笑的，其实是`Array.from`更优化的版本。更大的区别是`Array.from`允许更多的参数，并接受一个[可迭代的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)，而不仅仅是一个类似数组的对象。我们将在下一节讨论 iterables。

那么我们如何在我们的问题中使用`Array.from`?如果我们传递给`Array.from`一个只有**和**一个`length`属性的对象，我们会在每个位置得到未定义，不像`Array()`！

```
Array.from({})                  // -> []
Array.from({ 2: 4, length: 4 }) // -> [undefined, undefined, 4, undefined]
Array.from({ length: 61 })      // -> [ undefined x 61 ]
Array.from({ length: 61 }).map((_, i) => i) // 0-60 inclusive 
```

Enter fullscreen mode Exit fullscreen mode

这里最酷的是`Array.from`接受了第二个参数——一个映射函数！这意味着我们可以在括号内移动我们的地图:

```
Array.from({ length: 61 }, (_, i) => i) 
```

Enter fullscreen mode Exit fullscreen mode

# 迭代器和可迭代对象

这应该是它自己的帖子，但是本质上我们有所谓的“迭代器”。我们在某些数据结构上循环，而不需要访问任何与索引有关的东西。数据结构本身处理下一个值是什么。

对于这篇文章来说，这个主题有点多，所以我建议查看一下 [MDN 页面](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)以获得更多信息，但是它是 JS 中非常酷的一部分，它允许[展开语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)和[...循环的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)开始工作。

迭代器函数在处理内部状态时变得有点复杂，所以我们有[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)来帮助我们创建它们。

```
function* makeIterator() {
  yield 2;
  yield 3;
  yield 'bananas';
}

[...makeIterator()] // -> [2, 3, 'bananas'] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把每个`yield`按照它们出现的顺序看作数组中的一个元素。我们使用 spread 语法并用括号将它括起来，使它变成一个数组。还要注意我们是如何需要一个`*`来区分这个函数和普通函数的。

我们也可以在生成器函数中使用循环来产生多次

```
function* makeIterator() {
  for (let i = 0; i < 4; i++) {
    yield i;
  }
}

[...makeIterator()] // -> [0, 1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

如果数据结构包含一个`@@iterator`属性，那么它们就是可迭代的。如果属性遵循迭代器协议，则该 iterable 是“格式良好的”。我们可以通过`Symbol.iterator`赋予一个对象这个属性，并且我们可以通过使用一个生成器函数来遵循这个协议。

我们也可以以其他方式遵循协议，但它们比我们在这篇文章中要经历的更多。

让我们尝试使用 iterable 来解决我们的问题！

```
const iterable = {
  [Symbol.iterator]: function*() {
    yield 2;
    yield 3;
    yield 'bananas'
  }
};

[...iterable] // -> [2, 3, 'bananas'] 
```

Enter fullscreen mode Exit fullscreen mode

我们已经从一个函数转移到一个可迭代的对象。现在让我们将收益转移到一个循环中。

```
const iterable = {
  [Symbol.iterator]: function*() {
    for (let i = 0; i < 61; i++) {
      yield i;
    }
  }
};

[...iterable] // 0-60 inclusive 
```

Enter fullscreen mode Exit fullscreen mode

因为我们有一个对象，它是一个表达式，让我们看看是否可以把它压缩成 3 行。

```
[...{*[Symbol.iterator]() {
    for (let i = 0; i < 61; i++) yield i;
}}] 
```

Enter fullscreen mode Exit fullscreen mode

不错！不是最漂亮的，但它做我们想要的。注意，我还把`Symbol.iterator]: function*()`改成了`*[Symbol.iterator]()`，因为它稍微短了一点。

还应注意，所有数组都是可迭代的。这就是它们能够与 spread 语法一起使用的方式。spread 语法也将数组孔变成了`undefined`。这意味着我们可以把我们的`Array()`例子改成:

```
[...Array(61)].map((_, i) => i) 
```

Enter fullscreen mode Exit fullscreen mode

老实说看起来更干净。我们甚至可以使用数组缓冲区，这是一个我们也不会过多讨论的概念，使用 spread 语法也可以得到相同的结果！

```
[...new Uint8Array(61)].map((_, i) => i) 
```

Enter fullscreen mode Exit fullscreen mode

# 首选项

现在我们只剩下使用哪一个了。我们有很多选择。当程序员有很多选择时，我们通常会考虑两件事:风格和性能。

对于 JS，一般来说不考虑性能基准，因为 JIT 编译器可能会在某一天优化解决方案，使其更快，而前一天它并没有更快。由于引擎优化的原因，性能基准测试也经常会产生极大的误导。

考虑到这一点，可变数组选项似乎总是最快的。将`Array()`与`.fill()`或 spread 语法结合使用似乎排在第二位，迭代器排在第三位，而`Array.from()`排在最后。

对于大多数用例来说,`Array.from`可以用一个基本函数重新创建，如果它专门用于特定的用例，它会是`Array.from`的更好形式，但是除非你一秒钟内调用它很多次，否则我不会担心。

带有 spread 语法的`Array()`选项似乎是最干净的，但是为这个问题创建自己的类似乎总是有趣得多:

```
class Range {
  constructor(min, max, step = 1) {
    this.val = min;
    this.end = max;
    this.step = step;
  }

  * [Symbol.iterator]() {
    while (this.val <= this.end) {
      yield this.val;
      this.val += this.step;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用`new Range(min, max[, step])`来生成任意范围的 iterable，并使用 spread 语法来创建数组！有点冗长，但使用起来也更有趣！

你怎么想呢?有什么风格偏好吗？