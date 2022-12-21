# 人类可读的 JavaScript

> 原文：<https://dev.to/laurieontech/human-readable-javascript-337o>

在很长一段时间里，人类需要像机器一样“说话”，以便与机器交流。这仍然是事实，我们仍然需要从事汇编和其他低级语言工作的人。但是对我们中的许多人来说，这些复杂性都被抽象掉了。我们的工作是专注于人类可读的内容，让机器解释我们的代码。

当相同的代码可以用多种方式编写时，这一点再明显不过了。所以今天，我想少讲一些东西是如何工作的，多讲一些它是如何阅读的。这里有另一个关于函数式 JavaScript 的帖子，但是让我们假设我们讨论的是`map`。

`map`是 JavaScript 中可用于数组的函数。就当是`for each`吧。它将一个函数作为参数，并通过该函数运行数组中的每个元素。不同之处在于，它根本不改变原始数组。结果是一个新的数组。

### 举例

```
const arr = [1,2,3]
let multipliedByTwo = arr.map(el => el*2)
// multipledByTwo is [2,4,6] 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们知道地图是干什么的了。但是看看上面的代码片段。一个非常简洁的函数，将一个变量乘以 2。

让我们来看看，我们可以用不同的方式来写同样的逻辑。

## 可选括号

我们可以做的第一个可选添加是在内部函数的参数定义中添加括号。这使得这段代码看起来更像一个典型的函数定义。

```
const arr = [1,2,3]
let multipliedByTwo = arr.map((el) => el*2) 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，我们不需要它们的唯一原因是因为我们只传递一个参数。

```
const arr = [1,2,3]
let multipliedByTwo = arr.map((el, index) => el*2) 
```

Enter fullscreen mode Exit fullscreen mode

在我们传递多个参数的情况下，参数不是可选的。我们的例子是`map`，如果是`reduce`，我们会一直使用括号。

因此，让我们来盘点一下。添加圆括号会损失什么吗？我们有收获吗？我们增加了两个角色，这传达了什么信息？当我们为我们的队友和未来的自己开发代码来维护和阅读时，这些是我们需要问自己的事情。

### 花括号和`return`

我们可以更进一步，使内部函数符合官方函数语法。这样做需要花括号和`return`关键字。

```
const arr = [1,2,3]
let multipliedByTwo = arr.map((el) => { return el*2}) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在对这段代码有什么感觉？作为一个函数，它读起来更清晰。牙套和`return`增加了更多的体积吗？我们对这种变化的看法取决于返回的逻辑吗？

事实证明，如果我们的函数逻辑不止一行，这也是不可选的。

```
const arr = [1,2,3]
let multipliedByTwo = arr.map(
(el) => { 
  if(el%2 === 0) {
      return el*2
  } else {
      return el+1
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

有意思。我们对额外字符的看法会根据用例而改变吗？这对我们代码的一致性意味着什么？

### 使用单独的功能

正如我们所知道和看到的，`map`将一个函数作为参数，并将数组中的每个元素传递给它。也许我们可以或者应该在`map`之外定义我们的内部逻辑。照目前的情况看，它有点像金字塔代码。

```
const arr = [1,2,3]

const timesTwo = (el) => el*2

let multipliedByTwo = arr.map((el) => timesTwo(el)) 
```

Enter fullscreen mode Exit fullscreen mode

我们怎么看？实际上，它的字符数与原始版本几乎相同。但是我们上面的例子有更复杂的逻辑吗？

```
const arr = [1,2,3]

const timesTwoOrPlusOne = (el) => { 
  if(el%2 === 0) {
      return el*2
  } else {
      return el+1
  }
}

let multipliedByTwo = arr.map((el) => timesTwoOrPlusOne(el)) 
```

Enter fullscreen mode Exit fullscreen mode

这改变了你的看法吗？还是看起来杂乱重复？

### 只是一个函数

函数式编程是一个有趣的范例。部分是因为它允许我们编写代码。再次提醒我们`map`将函数作为参数。所以为什么不给它一个功能。

```
const arr = [1,2,3]

const timesTwo = (el) => el*2

let multipliedByTwo = arr.map(timesTwo) 
```

Enter fullscreen mode Exit fullscreen mode

是的，这是有效的。知道把它得到的元素传递给函数并使用结果。通过确定我们的`timesTwo`函数可以采取什么形式，我们可以得到更多。现在它是一个简洁的一行程序。

而且注意`map`真的很聪明。我们可以传递同一个函数，即使该函数现在同时使用元素和索引来获得返回值！

```
const arr = [1,2,3]

const timesTwoPlusIndex = (el, index) => (el*2) + index

let multipliedByTwo = arr.map(timesTwoPlusIndex) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来可读吗？`multipledByTwo`读起来当然很愉快，但是`timesTwoPlusIndex`在我们的代码库中位于什么位置呢？很难追查吗？如果有人第一次看到这个，他们知道这是一个函数吗？还是假设它是一个对象或数组变量？

> 函数是 JavaScript 中的对象，但暂时忽略这种复制。

### 我们如何确定什么是可读的

没有放之四海而皆准的语法。谁是你的观众？通晓多种语言的人还是 JavaScript 专家？谁在维护你的代码？有多少人在这个代码库中工作？所有这些都很重要。

这完全取决于用例，一致性很重要。然而，看到相同功能的所有不同表示是令人大开眼界的。所有这些例子都将被构建到相同的精简代码中。因此，作为开发人员，我们的决定是基于人类的可读性。它完全没有考虑机器的性能和功能。

我提出了很多问题，却没有多少答案。我有我自己的观点，但很想听听你的。这些当中哪一个可读性最强？有你更喜欢写的版本吗？下面来讨论一下吧！