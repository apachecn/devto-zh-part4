# [] == ![] - WTFJS 和强制

> 原文：<https://dev.to/emnudge/wtfjs-and-coercion-454e>

WTFJS 这个词最早是由 Brian Leroux 创造的，但我第一次听到它是从 T2 的 twitter 上。这篇文章的标题来自一个 [github 的同名回购](https://github.com/denysdovhan/wtfjs)。

现在，作为前言，我已经写了这篇文章大约 3 或 4 次。一次比一次长。没有发表。

这是一个困难的话题，但我将尝试写我的最短版本，同时仍然有效地交流原则。

# 威压

关于这个话题的第一点也是最重要的一点是，强制是存在的。我们可以无意或有意地将一种类型的单位转换成另一种类型。这些通常被称为“隐式”和“显式”。

您可以通过使用函数`Number()`将一个字符串显式地转换为数字*，或者通过从另一个数字中减去它来隐式地转换为数字*。这些是代码。** 

```
7 + Number("4") // -> 11
7 - "4"         // -> 3 
```

Enter fullscreen mode Exit fullscreen mode

大多数语言都有明确的强制。在这些语言中，我们通常会把整数转换成小数。在 JS 中，这两者没有区别，所以我们关心的是将数字转换成字符串，将字符串转换成布尔值，等等。

隐式转换可能有点棘手。这肯定是有用的，但它可能会导致混乱的结果。这里有一个相当著名的例子。

```
7 + "4"   // -> "74"
7 + + "4" // -> 11 
```

Enter fullscreen mode Exit fullscreen mode

我们可能已经忘记了`+`，当一个操作符是一个字符串时，会将非字符串强制转换成一个字符串并将它们连接起来。

我们可能还打错了，多加了一个`+`。我们可能也忘记了有一个叫做一元的东西，它实际上做显式的强制。`+"4" == 4`所以我们的例子就像做`7 + 4`。

所以隐性强制是不好的？不完全是。我们一直在使用它。尤其是布尔型。这里有一个例子。

```
const myArr = [1, 2, 3, 4, 5];
if (myArr && myArr.length) {
  console.log("My arr is: " + myArr);
} 
```

Enter fullscreen mode Exit fullscreen mode

该代码输出`my arr is: 1,2,3,4,5`。在这个简短的例子中，我们使用了三次隐式强制。

我们的`if`语句首先检查`myArr`是否存在。然后它检查`myArr`里面是否有任何元素。如果是这样，它会在控制台日志中打印出`myArr`的字符串版本。

对于许多开发人员来说，这可能看起来很直观，但是我们实际上在这里做了很多强制。我们将数组本身强制转换为布尔值，然后将长度属性转换为布尔值，然后将数组本身转换为字符串！

重要的是要知道什么会导致什么。我不会在这里详细介绍所有的内容(就像我在以前的版本中所做的那样)，但是我会简单介绍一些基本的内容。

# 基础知识

JS 中最重要也是最常见的强制话题大概就是[虚伪的价值观](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)。假值是 6 个东西，当强制为布尔值时，将计算为`false`。需要注意的是，这并不意味着它们是**等于**等于`false`，只是如果我们使用`Boolean()`或隐式强制，它们将评估为`false`(这对于后面很重要)。

这些 falsy 值是`false`、`0`、空字符串(`''`)、`undefined`、`null`和`NaN`。如果我们将其中任何一个运行到`Boolean()`中，我们将得到`false`。

**编辑**:我一个朋友催我加进去`-0` ( [看这里](https://dev.to/emnudge/identifying-negative-zero-2j1o))`0n`([BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt))`document.all`。最后一个有一个[超级有趣的故事](https://stackoverflow.com/questions/10350142/why-is-document-all-falsy)，可以在规格[这里](https://tc39.es/ecma262/#sec-IsHTMLDDA-internal-slot)找到。

在我们之前的例子中，我们能够判断我们的数组是否存在，以及它是否有任何由 falsy 值引起的元素。如果我们的数组不存在，`myArr`就会是`undefined`，这就是 falsy。如果没有元素，`myArr.length`就是`0`，这是假的。如果它们*不是*假，那么它们是真的，并且表达式将评估为`true`。

数字强制可能是第二重要的。几乎没那么重要，但还是第二。

你需要知道的是，数字格式的`null`、`false`和空数组强制转换为`0`、[字符串会变成它们对应的数字](https://dev.to/emnudge/converting-strings-to-numbers-in-js-subtleties-secrets-and-slip-ups-478k)、`true`为 1，其他都是`NaN`。

字符串强制通常会如您所愿。对象强制很酷，但是无关紧要。数组在被强制时会自我字符串化。

您可以通过使用它们相应的函数来测试它们。又想知道什么是`false`的数字形式？打开开发控制台并键入`Number(false)`！

# 申请

现在我们已经触及了所有的基础，让我们应用它们来真正地把它钉进去。尽量不要读得太快。花一点时间想想这将记录到控制台的内容。

```
const foo = "4a";
const bar = Number(Boolean(Number(foo)));
const baz = Number(String(Boolean(Number(foo))));

console.log(foo, bar, baz); 
```

Enter fullscreen mode Exit fullscreen mode

答案是`4a 0 NaN`。
`foo`是`"4a"`，起初看起来它能够解析成一个数字，但是我们在这里没有使用`parseInt()`。它将要挟进入`NaN`。

`NaN`是 falsy，所以当强制转换为 boolean 时，它将是`false`。如果我们把它强制转换成一个数字，我们得到`0`，但是如果我们把它变成一个介于两者之间的字符串，我们得到`NaN`，因为`"false"`不能被强制转换成一个数字。

既然我们都明白了，让我们继续一些微妙的部分。

# 平等胁迫与规范

我从没想过要检查 JS 规范。为什么我要？ [MDN](https://developer.mozilla.org/en-US/) 工作正常。嗯，在某些情况下，规范实际上让事情变得更清楚了。

我将展示一些非常令人困惑的结果。试着花一分钟时间，弄清楚为什么他们会对自己所做的事情做出评价。这实际上将是我们的第一点 WTFJS。

```
false == ''        // -> true
false == null      // -> false
false == undefined // -> false 
```

Enter fullscreen mode Exit fullscreen mode

现在我们一起来思考一下。我们的第一反应可能是说“哦！这三个是假的！我们用的是松散的平等，所以我们在强迫！”

你会在正确的轨道上，但仍然有点偏离。宽松的平等(2 个等号)*确实强制了强制，但并不总是如我们所愿。*

 *如果我们使用 3 个等号，就不会有强制。这些都是假的。松散相等在检查相等之前，首先尝试将值强制为相同的类型。

这里的一个问题是，当与`false`比较时，我们的`null`和`undefined`表达式都评估为`false`。那两个是假的，所以被胁迫的时候*应该*等于`false`吧？让我们只举一个空的例子，试着把它分解一下。

```
false == null                   // -> false 
Boolean(false) == Boolean(null) // -> true
false == Boolean(null)          // => true 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经验证了`null`实际上是假的。所以我们没疯。这里必须发生的是，我们实际上并没有强迫他们变成布尔型。让我们试试其他类型。

```
Number(false) // -> 0
Number(null)  // -> 0
Number(false) == Number(null) // -> true
String(false) == String(null) // -> false 
```

Enter fullscreen mode Exit fullscreen mode

好，所以它不能是数字，但也不能是字符串，因为我们发现之前的`false == ''`是`true`。我漏掉了一些东西。检查[规格](https://www.ecma-international.org/ecma-262/10.0/index.html)的时间到了！

我们将需要通读标有 [**抽象等式比较**](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-abstract-equality-comparison) 的部分。他们所说的“抽象”就是我们所说的“松散”的意思。

> \1.如果类型(x)与类型(y)相同，则

我们的类型不同，所以让我们跳到描述初始状态的数字。

> \6.如果 Type(x)是 Boolean，则返回比较的结果！ToNumber(x) == y。

注意:`!`和它在 JS 中的意思不一样。它指的是不会导致[突然完成](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-completion-record-specification-type)的操作，现在与我们无关。当我们在本文中看到它时，我们可以在规范中忽略它。

因此，当我们的第一个值是布尔值时，不管第二个值是什么，使用一些名为`ToNumber()`的内置函数将**仅第一个值转换为数字**，并再次执行该算法。

这里我们可以用`ToNumber()`代替好的 ole `Number()`。这给了我们`Number(false) == null`，而不是`Number(false) == Number(null)`。这评价为`0 == null`。让我们继续阅读说明书，看看现在该做什么。

如果我们有了一个数字，讨论该怎么做的唯一选项是

> \4.如果 Type(x)是数字，Type(y)是字符串，返回比较结果 x ==！到数字(y)。

我们处理的不是字符串，而是`null`。没有一个选项能解决这个问题。让我们来看最后一个选项

> \10.返回 false

哼。这个怎么样？如果一个数字与任何不是字符串的东西进行比较(并且不是一个可以字符串化的对象)，那么它的值就是`false`。我们只是在第一步之后默认得到`false`。

现在让我们解决另外两个问题。好吧，我们会先用`ToNumber` / `Number`胁迫`false`，所以我们在做`0 == ''`，就像以前一样。然而，这一次，选项 4 实际上对我们适用。我们有一根绳子！现在基本都是`0 == Number('')`哪个是`0 == 0`那个是`true`！

哇，这澄清了很多困惑。让我们继续最后一个。
我们将布尔值“编号”,所以`false == undefined`变成了`0 == undefined`,然后由于`undefined`不是一个字符串，默认为`false`!

咻。规范澄清了一些事情，但是仍然有点奇怪。欢迎来到 WTFJS！

# WTFJS

WTFJS 本质上只是 JS 中让你说“WTF JS？!"

很多时候，它与强制或 JS 的其他一些奇怪的部分有关。不管怎样，浏览 WTF JS 的例子真的可以让你对 JS 有更好的理解。让我们来解决题目中的问题。

```
[] == ![] // -> true 
```

Enter fullscreen mode Exit fullscreen mode

好吧。某物等于它自身的对立面？那不可能是对的。我们知道`[] === []`会计算为`false`,因为对象/数组是通过引用进行比较的，但是`true`是一个奇怪的结果。我们来分析一下。

因此，首先，当我们看到`==`(抽象/松散相等)时，我们知道我们将处理强制，至少在 WTFJS 例子中是这样。

然而，在我们深入研究强制之前，我们必须首先评估每一方。我们使用布尔 NOT 操作符(`!`)，根据规范，它在将布尔值翻转到相反方向之前对非布尔值执行强制转换。

现在我们有了`[] == !Boolean([])`。由于`[]`不是 falsy，它将评估为`true`。`!`将使它成为`false`，所以我们有`[] == false`。
正如我们之前看到的，如果一个操作数是布尔型的，我们首先将其转换为数字，然后再继续。那就是`[] == Number(false)`，然后就是`[] == 0`。

好的，既然`[]`不是一个字符串，那么它的值不是 false 吗？我们得到了`true`！那不可能。

继续阅读规范中的 [**抽象等式比较**](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-abstract-equality-comparison) 部分，我们看到我们忽略了选项 9。

> \9.如果 Type(x)是 Object，Type(y)是 String、Number 或 Symbol，则返回与 Primitive(x) == y 的比较结果。

这个规则说的是我们在处理对面的字符串、数字或者[符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)的时候，把一个对象转换成一个原始值(不是布尔)。

"但是`[]`是数组，不是对象！"我听到你说。没那么快。`typeof []`实际返回`"object"`。数组属于`"object"`类型，因此遵循任何基于对象的规则。

它说要用`ToPrimitive`。我们用哪个函数来得到它？它是数字、字符串还是布尔值？回到规范！

让我们来看看标有 [**ToPrimitive**](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-toprimitive) 的部分。它告诉我们调用一个名为 [OrdinaryToPrimitive](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-ordinarytoprimitive) 的函数，这个函数需要一个`hint`。这告诉我们将它转换成哪个原语。如果没有指定，就像在我们的例子中，它使用`"number"`。

当这个函数被调用时，如果`hint`不是“string”(我们的是“number”)，它会尝试调用`.valueOf()`。如果没有返回一个原语(没有)，它就调用`.toString()`。如果它没有返回一个原语(它返回了)，就会抛出一个错误。

在我们的例子中，`[].valueOf()`返回`[]`，这不是一个原语。`[].toString()`返回`""`，其中*是*一个原始人。

哇哦。好了，现在我们有了`"" == 0`。从前面我们知道，如果我们比较一个数字和一个字符串，我们会将字符串强制转换为数字。它遵循一系列规则，但是正如我们在前面的章节中看到的，如果字符串为空，我们得到`0`。

现在。`0 == 0`。确实如此。这就是为什么`[] == ![]`是`true`。

# 结论

我们在这里学到了什么？我希望有一大堆。

JS 令人困惑，有时感觉不直观，但是它的所有特性(不是 bug)都可以在规范中看到。强制也是个乱七八糟的话题。很多强制看起来很奇怪，我们把它扔进了 [WTFJS](https://github.com/denysdovhan/wtfjs#-is-equal-) 。

我强烈建议你在看这个规范之前，去 WTFJS repo 看看。他们自己的推理在他们的例子后面，可能会跳过一串*步骤*，但不管怎样，这都会帮助你更好地理解。

我希望我鼓励了一些开发者去尝试这个规范。它真的比看起来可读性强得多。***