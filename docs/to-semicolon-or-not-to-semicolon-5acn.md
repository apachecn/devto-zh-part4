# 要分号，还是不要分号；

> 原文：<https://dev.to/emnudge/to-semicolon-or-not-to-semicolon-5acn>

“要分号，还是不要分号”——莎士比亚什么的

每隔一段时间，争论就会出现，然后逐渐平息。我记得我第一次了解到在 JS 中不需要分号。我很困惑。

在那之前，我的整个 JS 职业生涯(打个比方——我还没有得到一份 JS 工作)都相信分号对于 JS 代码的正确运行至关重要。然后我不得不问“那我们为什么要这么做？”

为什么我认识的每个人，看到的每个例子都用分号？如果它们不是必需的，为什么还要使用它们呢？

我们先来讨论一下 ASI——一开始就允许我们忽略它们的系统。

# 自动插入分号(ASI)

自动分号插入，通常缩写为 ASI，是解析器为我们插入分号的过程。它让我们对分号的使用变得“可选”，但它主要是基于三条规则来使用分号的(摘自【Axel Rauschmayer 博士的演讲 JS ):

1.  当下一行以“非法令牌”开始时
2.  由右大括号括起来
3.  在文件的末尾

#1 将是这里更重要的一个。

需要注意的是，不是*字面上的*加个分号。它只是让引擎像它那样工作。ASI 让引擎知道一个语句/行何时“结束”。

由于 ASI 添加了我们需要的分号，下面的代码将正常工作。

```
let myVar = 3
myVar++
console.log(myVar) // > 4 
```

Enter fullscreen mode Exit fullscreen mode

ASI 在`3`后面看到一个换行符，并且知道`let myVar = 3 myVar++`是非法的，所以它在它们之间放置了一个分号。到下一个换行符时，它知道`myVar++ console.log(myVar)`也是非法的，并放置了一个分号。

我们看到 ASI 不只是检查新行，它还检查是否有新行**和**如果它们之间没有分号，那么下一个标记将是“非法”的。如果他们之间没有分号，*不是非法的呢？* 

```
let myVar = 1 +
3
console.log(myVar) // > 4 
```

Enter fullscreen mode Exit fullscreen mode

因为 ASI 看到有一个换行符，所以它检查`let myVar = 1 + 3`是否非法。不是，所以不加分号。因此，这段代码可以完美地工作。

您可能还见过其他一些不受 ASI 方法链接影响的代码。

```
const newArr = oldArr
                 .map(mapFunc1)
                 .reduce(reduceFunc)
                 .split('  ')
                 .map(mapFunc2) 
```

Enter fullscreen mode Exit fullscreen mode

有人可能会认为换行符会使 ASI 添加分号，从而打断我们的链接，但事实并非如此。

ASI 检查非法标记，如果没有分号，就会发现它是完全有效的。因此，它不去管它。有些人可能会问[2，4，2]。map(x => x * 2)(包含所有这些空格)实际上是有效的。确实是！我们可以保留这些空间，它会运行得很好。

现在我们已经知道了它是如何工作的，让我们来讨论一下我们认为它会以一种方式工作，但实际上却把我们搞得一团糟的情况。

# ASI -意外缺少插入

有些情况下，ASI 不会添加分号，因为后面的标记是合法的。

```
console.log(4) // > 4
['foo', 'bar'].forEach(el => console.log(el)) // > Uncaught TypeError: Cannot read property 'bar' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

我们可能认为这段代码会记录`4`，然后记录`'foo'`和`'bar'`，但是我们得到的是一个错误。这是因为 ASI 认为`console.log(4)['foo', 'bar']`完全有效。

ASI 认为我们正在调用一个返回对象的函数，然后我们试图获取这个对象的`bar`属性。由于[逗号操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator)，所以`'foo', 'bar'`转换成仅仅是`bar`(酷，我知道)。

在这种情况下，ASI 真的不知道我们不想要那个语法。我们不得不在代码中新的一行的开头加上一个分号或者不写数组文字。

JS 规范也给出了一个带有圆括号的例子，会产生类似的效果。这不是有效的 JS(足够有趣)，所以这里有一个更“实际”的例子。

```
let s1 = "Hello "
let s2 = "World"
let myStr = s1 + s2
(s1 + s2).split('').forEach(char => console.log(char))
// Uncaught TypeError: s2 is not a function 
```

Enter fullscreen mode Exit fullscreen mode

我们希望`myStr`等于`"Hello World"`，然后使用`forEach`循环记录`"Hello World"`的每个字符。相反，我们得到一个错误，告诉我们`s2`不是一个函数。

与上一个例子类似，ASI 正在检查`s1(s1 + s2)`是否是有效的语法。因为它**是**有效的语法(就好像我们正在调用一个名为 s1 的函数)，所以没有添加分号。

因为有时**会加上**分号，这也伤害了我们。

# ASI -意外插入

一些程序员更喜欢 Allman 括号风格，并将这种偏好带到了其他语言中。这意味着每个大括号都有自己的一行。这工作得很好(但是通常被 JS 程序员避免),直到你遇到 ASI 的情况。

```
// Allman brace style
function myFunc(val) 
{
  return
  {
    value: val
  }
}

console.log(myFunc(4)) // > undefined 
```

Enter fullscreen mode Exit fullscreen mode

虽然函数本身将大括号识别为函数体，但不返回对象，因为 ASI 在`return`后放置了一个分号。还需要注意的是，那个函数中没有`object`。

没错。除了一个物体，这还能是什么？带有[标签](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)的[块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block)。

我们可以在 JS 中通过用`{ }`将一些语句括起来来制作块。我们还可以使用`foo:bar`在 JS 中制作标签，其中`foo`代表任何字符串(不带引号)，而`bar`代表任何表达式或语句。

通常避免使用标签，表达式标签几乎完全没有用，但是引擎不在乎。我们写了无用的代码，但没有人受伤，所以它让它滑动。

引擎如何区分块和对象？如果左大括号位于行首或需要块的语句之后，它就是一个块。就是这样。

这意味着，即使我们让这个块看起来像一个物体，它仍然被当作一个块。

```
function myFunc(val) {
  return
  {
    value: val,
    name: 'Greg',
  }
}

// > Uncaught SyntaxError: Unexpected token : 
```

Enter fullscreen mode Exit fullscreen mode

标签后面不能有逗号。因此，这肯定不是一个标签。JS 看到一个块，然后看到这个怪异的准标签语法。它抛出一个错误，告诉我们我们的块有分号。

**然而**这是一个有趣的话题，但它仍然不能完全解释 ASI 在这种情况下的行为，因为下面的行为:

```
function myFunc1(val) {
  return
  ({
    value: val,
    name: 'Greg',
  })
}
console.log(myFunc1(4)) // > undefined

function myFunc2(val) {
  return
  4
}
console.log(myFunc2()) // > undefined 
```

Enter fullscreen mode Exit fullscreen mode

我们看到`return`忽略了规则#1。即使它在同一行上也是有效的，但还是添加了一个分号，我们将返回`undefined`。

这是因为规范中有一个关于“限制生产”的特殊规则。你可以在这里读到它[，但它本质上是规则的一个例外。](https://www.ecma-international.org/ecma-262/10.0/index.html#prod-asi-rules-ReturnStatement)

其他著名的“限制级作品”包括`continue`、`break`和`throw`。如果它们在各自的行上，不管接下来是什么，都会添加一个分号。

# 以分号？

看到 ASI 可以有多怪异之后，我们是不是应该开始加分号了？ASI 不添加分号的唯一情况(这对我们有害)似乎是我们在行首使用圆括号和方括号。

还有一些其他的小问题，但是一般来说，你不会在你的代码中写表达式。它们完全没用，而且容易养成坏的编程习惯。你不会发现一个悬空的`4`或`(13 + 4)`在它自己的线上，因为它不做任何事，也不帮助任何人。

在实际中，它们**起作用**的时候，比如 IIFE 和 array 析构语句，经常会被发现。

如果你需要用括号或方括号开始一行，考虑在它前面使用一个`;`,因为很多人已经采用了。虽然，很少会有这种必要的情况。

ASI **真正伤害我们的唯一情况是当它在不应该添加分号的地方添加分号时，比如当“受限产品”后面跟着一个表达式时。**

在这一点上，你是否经常添加分号并不重要——因为我仍然在制造问题。

大多数人倾向于采用的解决方案是使用棉绒来捕捉这些细微的错误。默认情况下，许多 linters 会强制使用分号，但是您可以禁用它。Linters 还会通知你不能到达的代码。如果你提前返回，之后又有代码，linter 会让你删除或更新它。

# 结论

如果您意识到了陷阱并养成了良好的编程习惯，那么使用分号似乎并不是一个很强的理由。在这一点上，[一些风格指南](https://github.com/standard/standard)甚至避免了它们(VueJS 默认也不包括它们)。

丹·阿布拉莫夫确实为保留分号提供了一个很好的理由，但是以我的经验来看，优点似乎大于缺点。如果你曾经不得不把一个以分号结尾的表达式用括号括起来，你可能会明白我的意思。

您可以在分号的正前方单击鼠标，也可以删除分号，然后再添加回去。如果不使用分号，您可以直接单击行尾的某个地方，然后添加一个结束部分。它很小，但是它增加了执行一个极其简单的操作的工作量。

至于我？我惊讶地使用分号。尽管偶尔会有挫败感，但我喜欢它的样子；感觉更干净。我坚信这仍然是一种风格偏好。

你怎么想呢?