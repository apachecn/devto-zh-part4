# JS 代码高尔夫-如何破坏每个人的一天

> 原文：<https://dev.to/emnudge/js-code-golfing-how-to-ruin-everyone-s-day-40h3>

这篇文章的标题有点半开玩笑。代码高尔夫是一个术语，用来描述通过使用尽可能少的代码来获得尽可能少的“分数”(就像你在高尔夫中所做的那样)。

由于向后兼容性、怪癖、它是一种高级语言以及所有的强制，Javascript 是一种非常棒的代码高尔夫语言。我们将回顾一些极端的 JS 代码高尔夫示例，并解释它们如何工作以及为什么工作。

虽然极端的代码高尔夫会很快使您的代码库变得不可读，但偶尔代码高尔夫的某些方面会使一些看起来更复杂的代码变得更可读。理解代码高尔夫还可以让你更好地理解 JS 中的一些概念。

# 基础知识

在这里，我们将回顾一些代码高尔夫球技巧，我相信你已经见过了。这包括:

1.  缩短变量名。
2.  简单操作不要用中间变量。
3.  尽可能避免使用块(if、while 和 for 语句)。
4.  不要在 return 之后使用不必要的语句。
5.  尽可能使用箭头函数。

让我们给出一个代码片段，然后使用这 5 个基本规则将其缩短。

```
// 345 characters
function getNumOneOrZeroOff(baseNumber, shouldTryToGoUp, falsyOrTruthyVal) {
  const myBoolean = Boolean(falsyOrTruthyVal);
  const oneOrZero = Number(myBoolean);

  if (shouldTryToGoUp) {
    const numberPlus = baseNumber + oneOrZero;
    return numberPlus;
  } else {
    const numberPlus = baseNumber - oneOrZero;
    return numberPlus;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了这是一个非常无用的函数，它的长度也是不必要的。

让我们实施前 5 个基本规则来缩短这一点。

```
// 164 characters
const getNewNum = (num, goUp, arbitraryVal) => {
  const oneOrZero = Number(Boolean(arbitraryVal));

  if (goUp) return num + oneOrZero;
  return num - oneOrZero;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 缩短的变量名
> 删除了`const myBoolean = ;{}else{}`、空格和第 7、10 行

哇，尽管我们去掉了许多超级具体的变量名，它实际上变得更容易阅读。让我们再用 4 条基本规则来简化这个问题。

1.  偏好一元`!!`而非`Boolean()`。
2.  偏好一元`+`而非`Number()`。
3.  尽可能使用名词而不是`if`语句。
4.  将三角形缩短为两个表达式的三角形。

```
// 136 characters
const getNewNum = (num, goUp, arbitraryVal) => {
  const oneOrZero = +!!arbitraryVal;

  return num + (goUp ? oneOrZero : -oneOrZero);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 删除了`Number(Boolean())ifreturn;num`和空白
> 增加了`+!!?:`

第四点可能有点令人困惑。这意味着我们不做`return goUp ? num + oneOrZero : num - oneOrZero`，而是做`return num + (goUp ? oneOrZero : -oneOrZero)`，因为它们有一个共同的因素(`num`)。delta 是两个表达式的区别，唯一变化的是。

只有 3 个字符的差异(在我们的例子中)，但是这些东西加起来，*可以提高可读性。我们也可以删除额外的 2 个字符的 parens，但它会很难阅读。*

让我们暂时停止关心可读性。

# 通过简洁来混淆视听

重用前面的代码片段，我们可以使用更多的规则。

1.  删除不必要的空白。
2.  删除不必要的 parens。
3.  删除不必要的分号
4.  使用单字符变量
5.  偏爱`let`胜过`const`。

```
// 43 characters
let f=(a,b,c)=>{let d=+!!c;return a+(b?d:-d)} 
```

Enter fullscreen mode Exit fullscreen mode

> 缩短的变量名称
> 被删除`constconst;()`和空白
> 被添加`letlet`

在这里，所有关于代码高尔夫提高可读性的讨论都被抛到了九霄云外。几乎无法辨认。

如果我们不再关心性能会怎么样？

1.  如果节省空间，最好重新计算表达式
2.  比起用`let`、`const`或`var`定义，更喜欢创建全局变量(假设没有“严格使用”)

```
// 26 characters
f=(a,b,c)=>a+(b?+!!c:-!!c) 
```

Enter fullscreen mode Exit fullscreen mode

> 删除了`let{}returnletdd+`和空白
> 增加了`!!c`

通过将`+!!`放在我们的三元组中，我们能够移除变量赋值，从而允许单行返回。在只有一个包含 return 语句的函数体的 arrow 函数中，我们可以去掉括号。

我们也可以用`-!!`代替`-+!!`，因为一元否定是存在的。

因此，抛开几乎所有的可读性和最佳实践，我们将一个函数从 345 个字符减少到 26 个字符——不到其原始大小的 8%!哇哦。

让我们更进一步，发现一些不*为*经常使用的 JS 技巧。

# 表情评价

从某种意义上说，所有的函数和赋值都是表达式。很多时候函数会返回`undefined`，但还是*什么的*。这给了我们很大的权力来缩短我们的代码。

是时候深入了解更多片段了！

```
// empty function
const myFunc = () => {};

myFunc()       // -> undefined
!!myFunc()     // -> false
4 + +!myFunc() // -> 5

// assignments as expressions
let a = 1;
let b = 1;
let c = 1;
c += b += a += 1;

a // -> 2
b // -> 3
c // -> 4

// 2 operations at once
let i = 0;
console.log(i++); // logs 0 and now i is 1
console.log(++i); // logs 2 and now i is 2 
```

Enter fullscreen mode Exit fullscreen mode

应该注意的是，声明不返回任何东西(甚至是未定义的)，因此不是表达式。您不能记录`let a = 3`或者在表达式中的任何地方使用它(但是您可以做`let a = b = c`)。

知道了这些都是可表达的(新词)，让我们加入一个经常被遗忘的 JS 片段。以下是有效的 JS:

```
// ignore this. Just initializations.
let a = 0, b = 0, myFunc = (num) => console.log(num);

let c = (a++, b+=a, myFunc(2), a+=b, b + a); // > 2
c // -> 3 
```

Enter fullscreen mode Exit fullscreen mode

这里刚刚发生了什么？如果您使用过 C++，您可能对这种行为很熟悉。在 JS 中，我们可以在括号内写逗号分隔的表达式。表达式从左到右计算，然后返回最右边的表达式。

在我们的例子中，我们做了很多本可以在他们自己的生产线上做的事情。

这个什么时候有用？嗯，在大多数情况下，这不会很有用，因为我们可以放弃 parens，而使用分号。最有用的地方是在`while`循环、`for`循环和缩短箭头函数中。

```
// prints 0-9 inclusive
let i = 0;
while (console.log(i++), i < 10);

// prints 0-9 inclusive
for (j = 0; console.log(j++), j < 10;);

// 32 characters
a=>{r=0;a.map(n=>r+=n);return r}
// 25 characters
a=>(r=0,a.map(n=>r+=n),r)
// a reduce would do the same thing and be only 23 characters, I know 
```

Enter fullscreen mode Exit fullscreen mode

通过循环，我们甚至不需要父母；它们是可选的。我们已经创建了没有主体的全功能 for 和 while 循环。确保使用分号，这样循环就不会意外地循环它下面的某个随机语句。

应该注意的是，我们也可以省略 for 循环的一部分，只要 for 循环中包含 2 个分号。由于我们的表达式，只要我们的最后一个表达式是布尔值(或者计算/强制为 1)，parens 的最后一部分(在第二个分号之后)基本上是无用的。

我们也可以用恶`eval()`把非表情变成表情。一般建议避免，但也有一些用例，比如代码高尔夫。它总是返回最后一个表达式，所以我们可以用它来减少一个箭头函数 3 个字符。我们在这一点上节省了一些钱，但从长远来看，这可能是值得的。

```
// 43 characters
a=>{for(var o="",i=0;i<a;i++)o+=i;return o}

// 40 characters
a=>eval('for(let o="",i=0;i<a;i++)o+=i') 
```

Enter fullscreen mode Exit fullscreen mode

# 乘虚而入

JS 中有许多棘手和古怪的部分揭示了一些非常有趣的行为。我们可以利用这些行为来缩短代码。

这些技巧中更常见的是使用[位操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)将浮点数强制转换成整数。通常我们会使用类似于`Math.floor()`、`Math.ceil()`或`Math.round()`的东西，但是这些占据了太多的字符。

我们可以使用位运算符，它有截断小数的副作用，只用两个字符来执行类似于*的*行为。通过使用`~~`或`|0`,我们可以执行不做任何事情的位运算。因为它们在计算结果时被截断，所以我们得到一个没有小数的数。

```
// 31 characters
Math.floor(Math.random() * 100)

// 21 characters
~~(Math.random()*100)

// 19 characters
Math.random()*100|0 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的 2 件事:1。截断在处理负数时会得到与`Math.floor()`不同的结果。2.按位运算符的执行顺序与乘法相同。想想 PEMDAS，但是在 M 或 d 后面加一个 B。这就是为什么第二个按位例子不需要 parens，但是第一个例子需要。

另一个你可能熟悉的行为是[短路评估](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation)。它处理`&&`和`||`操作符，让我们节省了大量空间。

```
// logs thing and then returns it
const logIt = thing => (console.log(thing), thing)

logIt(0) || logIt('hey') // logs both since 0 is falsy
logIt('hey') || logIt(0) // only logs 'hey' since 'hey' is truthy

logIt('hey') && logIt(0) // logs both since 'hey' is truthy
logIt(0) && logIt('hey') // only logs 0 since 0 is falsy 
```

Enter fullscreen mode Exit fullscreen mode

根据第一个函数的返回值，它被多次用于执行一个或两个函数。如果您希望第二个函数只在函数为真时执行，请使用`&&`。如果您希望第二个函数仅在函数为 falsy 时执行，请使用`||`。

`&&`和`||`也可用于检索假值或真值。

```
const theFalsyOne = '' && 100; // it is ''
const theTruthyOne = '' || 100; // it is 100 
```

Enter fullscreen mode Exit fullscreen mode

如果两者都为真，`&&`将返回第二个值，`||`将返回第一个值。如果两者都为 falsy，`&&`将返回第一个值，`||`将返回第二个值。这种行为也是由于短路评估。

最后一个行为围绕`valueOf`。有一个有趣的问题是关于[(a = = 1&&a = = 2&&a = = 3)是否可以评估为`true`](https://codeburst.io/javascript-can-a-1-a-2-a-3-ever-evaluate-to-true-aca13ff4462d) ，答案也与`valueOf`有关。

我们可以创建在连接和数学中使用时看起来像原始值的对象。如果我们在这些情况下使用一个对象，JS 将检出它的`valueOf`属性，将它强制转换成一个原语。我们可以用它做一些很酷的东西，但是我发现最常见的用法是`Math.random()`

```
const makeEllipse = (x, y, width, height) => { 
  // do stuff
}

// 91 characters
makeEllipse(Math.random() * 50, Math.random() * 50, Math.random() * 10, Math.random() * 10)

// 60 characters
let r={valueOf:Math.random}
makeEllipse(r*50,r*50,r*10,r*10) 
```

Enter fullscreen mode Exit fullscreen mode

当您必须定义一个新对象并包含原始函数时，显然有一个折衷，但是如果您使用它的次数足够多，它有助于缩短它。

# 储蓄便士

在真正的代码高尔夫比赛中，每个字符都很重要。如果你能去掉一个字符，那就去做吧。这里有一些技术来节省字符在这里和那里。

当在两个字符串之间时，用`${}`而不是`++`连接。这将保存一个字符。

```
// 27 characters
'You are '+age+' years old'

// 26 characters
`You are ${age} years old` 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用常规函数作为[标记的模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates)，只要函数使用它要求的字符串作为字符串。这样可以节省 2 个字符。

```
// 16 characters
myArr.split('.')

// 14 characters
myArr.split`.` 
```

Enter fullscreen mode Exit fullscreen mode

这不能用于像`eval`这样的东西，因为它们不像字符串那样使用输入。您还必须尽量不包含任何`${}`，因为标记的模板文字在不同的参数中接收变量。

如果你有一个无限循环，用`for(;;i++)`代替`while(true)i++`。这节省了 4 个字符。`i++`在这种情况下，表达式或函数会在每次迭代中被调用。

最后，数字也可以用科学符号来表示。代替`1000`，你可以使用`1e3`，它将计算相同的东西。这从 1000 开始节省了一个字符，但是 ROI 随着更大的数字而快速上升。

# 结束语

代码高尔夫可以有很多乐趣。这种练习也能教会你很多关于语言的知识。

我显然不能涵盖所有的 JS 代码高尔夫技巧，但我希望我涵盖了相当大的一部分。我强烈推荐查看[这个帖子](https://codegolf.stackexchange.com/questions/37624/tips-for-golfing-in-ecmascript-6-and-above)中更多的 JS 代码高尔夫技巧。

如果你想开始打代码高尔夫，我强烈推荐去看看 https://codegolf.tk/的[和 https://www.dwitter.net/的](https://codegolf.tk/)和 T4 的【这些网站使用代码高尔夫和画布用少得可笑的字符创建漂亮的可视化效果。

迷茫？有更多的技巧分享吗？欢迎留言告诉我！