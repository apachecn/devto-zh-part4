# 地图上的小把戏

> 原文：<https://dev.to/jckuhl/a-trick-with-map-55hl>

# 问题

考虑我在 [r/learnjavascript](https://www.reddit.com/r/learnjavascript/) 上遇到的以下问题:为什么`[1,3,11].map(parseInt)`会导致`[1, NaN, 3]`？

# 答案

新开发人员通常不知道 map 是如何工作的，也不知道它是如何将参数提供给所提供的函数的。我们都知道它给出的第一个参数是我们正在迭代的数组中的元素。

例如:`[1,3,11].map(element => console.log(element))`给出了一个列出 1，3，11 的输出。

但事实是，map 实际上为给定的函数提供了三个参数。Map 提供了被迭代的当前元素、当前索引和被迭代的数组。

```
[1,3,11].map((element, index, array) => //... ) 
```

我们可以通过直接引用我们的 console.log 来看到这一点，而不是将其包装在一个匿名函数中:

```
[1,3,11].map(console.log)
1 0 [ 1, 3, 11 ]
3 1 [ 1, 3, 11 ]
11 2 [ 1, 3, 11 ] 
```

这是直接从节点 REPL 粘贴的。你可以看到，我们在每一行，元素，索引，和数组。

那么回到最初的问题，我们为什么会得到`[1, NaN, 3]`？

因为这三个参数正在被传入`parseInt`。我相信许多新开发人员忘记了`parseInt`实际上接受了*两个*参数，而不是忘记了`map`提供了三个参数。`parseInt`取一个数和*基数*。基数告诉`parseInt`我们使用的是哪种数字系统。0 是十进制，2 是二进制，3 是三进制，8 是八进制，16 是十六进制，以此类推。

`parseInt(11, 3)`输出`2`,因为那是它的三进制等价。

所以在`map`，`parseInt`的每次传递中，看起来像这样:

```
parseInt(1, 0);
parseInt(3, 1);
parseInt(11, 2); 
```

数组参数被忽略，因为`parseInt`只接受两个参数。提供的数字成为数组中的元素，基数来自索引。

在第一遍中，我们把 1 转换成十进制，当然，是 1。基数 0 和 10 是十进制的*，所以十进制数不会改变什么。在第二遍中，我们得到`NaN`，因为值`1`对于基数无效。在第三遍中，我们将`11`转换为`2`。`11`在二进制中是当然的，`3`。我们在 2^1 位置有一个 1，在 2^0 位置有一个 1，2^1 + 2^0 = 2 + 1 = 3。*

 *简而言之，我们得到意外值的原因是因为`map`向`parseInt`函数提供了比我们期望的更多的参数。

# 我们如何解决这个问题？

解决方案非常简单，我们不直接将对函数的引用像`parseInt`传递给`map`。而是最好先用匿名函数包装:

```
[1, 3, 11].map((element, index, array) => parseInt(element) ); 
```

> 当然，如果您不使用参数`index`或`array`，您可以省略它们，我把它们留在这里是为了说明

然后，您可以根据需要将参数传递给函数。

现在，这只对接受或可能接受多个参数的函数有必要。如果你想映射一个只有一个参数的函数，你可以直接把引用传递给它。

```
const square = x => x * x;
[1,2,3].map(square);
//  [1, 4, 9] 
```

只是要小心确保你传递给`map`的任何东西，这适用于`foreach`和许多其他的数组方法，只需要一个参数。

你可以在这里阅读更多关于`parseInt`如何在 Mozilla 开发者网络[上工作的信息。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

快乐编码。*