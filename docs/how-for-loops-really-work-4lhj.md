# For 循环是如何工作的

> 原文：<https://dev.to/emnudge/how-for-loops-really-work-4lhj>

视频更符合你的风格吗？我在这里做了一个:
[https://www.youtube.com/watch?v=lebu6I-qJrg](https://www.youtube.com/watch?v=lebu6I-qJrg)

> for 循环是如何工作的？

乍一看，这似乎是一个基本问题。这是一个迭代器。它迭代。就这么简单。
如果我们将问题限定在专门讨论`for([initialization]; [condition]; [final-expression])` for 循环会怎么样？它**真的**是如何工作的？

“什么意思？”你可能会说。“您初始化一个变量，陈述一个计算结果为布尔值的条件，并提供一个在每次迭代后执行的语句。“你说的很简单。

那么让我们提供一个片段。

# 问题

你可能已经把这个问题看作面试准备的一部分或者很酷的 JS 代码片段。

```
for (var i = 0; i < 10; i++) {
    setTimeout(() => console.log(i), 0);
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意*:你可能还会看到函数被添加到一个数组中。背后的结果和原因其实是一样的。我们只需要一个函数在循环结束后触发，该函数包含对`i`的引用。

不管我们最初怎么想，这个 for 循环输出`10` 10 次。我们期望它打印出数字 0-9，但它没有。我们可以通过使用`let`而不是`var`来解决这个问题。

至于“为什么”，解释通常会涉及到`var`吊装的使用。然而，人们经常忘记提到闭包这个方面。即便如此，人们还是忘记了`var`是**中更容易解释的**部分，for 循环在幕后做着一些甚至在 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for) 中也没有解释的事情。

我什么意思？首先，让我们提供通常的解释。让我们假设这是一个基本的面试准备问题，并像在面试中一样解释它。

我们先来过一遍`var` vs `let`。

# Var vs Let

`var`与它的后 ES5 版本`let`不同，是函数作用域的[半悬挂](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting)。这到底是什么意思？

我称`var`变量*半*被提升，因为不像[函数声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)，只有它们的声明被提升。这意味着以下两个片段是相同的:

```
var myVal = 233;

for (var i = 0; i < 5; i++) {
  var newVal = myVal + i;
  console.log(newVal)
}

function showNumPlusOne(num) {
  var plusOne = num + 1;
  alert(plusOne);
} 
```

Enter fullscreen mode Exit fullscreen mode

和

```
var myVal, i, newVal;
function showNumPlusOne(num) {
  var plusOne;
  plusOne = num + 1;
  alert(plusOne);
}

myVal = 233;

for (i = 0; i < 5; i++) {
  newVal = myVal + i;
  console.log(newVal)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用`var`定义的变量声明被“提升”到顶部。“top”将是我们的全局代码的顶部或函数的顶部(如果它是在一个函数中声明的)。还要注意声明是如何脱离 for 循环的，而不是脱离函数的。如前所述，函数声明被“完全”提升，因为它们的主体也上升了，尽管用`var`声明的变量只有它们的声明上升了。

这意味着，我们可以在声明变量之前访问用`var`声明的变量。因为它们只是被声明，并没有被赋值，所以它们的值将是`undefined`，但是我们仍然可以使用它们。`let`会阻止这个并抛出一个错误。

`let`也是块范围的，所以用`let`声明的变量如果是在 for 循环中声明的，就不能在 for 循环外访问。

举例来说，下面是`let`对`var`块范围:

```
{
  var blockVar = 4;
}

console.log(blockVar) // > 4

for (var i = 0; i < 10; i++) {
  // do stuff
}

console.log(i); // > 10 
```

Enter fullscreen mode Exit fullscreen mode

vs

```
{
  let blockVar = 4;
}

console.log(blockVar) // > Uncaught ReferenceError: blockVar is not defined

for (let i = 0; i < 10; i++) {
  // do stuff
}

console.log(i); // > Uncaught ReferenceError: i is not defined 
```

Enter fullscreen mode Exit fullscreen mode

当试图访问用`let`声明的变量时，我们会得到一个错误，但是用`var`就可以了。现在我们已经弄清楚了，让我们再次展示这个例子。

# 解释-第 1 部分

这是网上很多解释止步的地方。`let`不同于`var`，因为`var`不是块范围的；它跳出 for 循环。如果我们使用`let`，我们会很好，但是`var`使`i`等于它最后的值(10)，并且每次都被输出。

```
// i's declaration gets hoisted
var i;
for (i = 0; i < 10; i++) {
    setTimeout(() => console.log(i), 0);
}
// loop is over and setTimeout functions trigger 
```

Enter fullscreen mode Exit fullscreen mode

然而，这个答案并不令人满意。我们可能知道`var`和`let`是根本不同的，但是这仍然不能解释为什么我们给它的数字会改变。即使`var`使得`i`最终等于`10`并且`setTimeout`使得我们的函数在 for 循环结束后被调用，这个解释还是遗漏了一些东西。

从表面上看，这种解释可能会让我们相信`let`应该每次都使我们的输出成为错误。如果在这两种情况下，我们的函数都是在 for 循环之后运行的，我们可能会认为我们会输出任何等于 T1 的值。在我们的例子中，要么是`10`，要么什么都没有(一个错误)。

还有更多。肯定有。

# 关闭

你们中最精明的人可能已经猜到了。一个[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)描述了一个函数抓取它没有声明或通过参数接收的变量的情况。

对于许多人来说，这个概念可能会变得复杂，所以我将尝试略读一些更复杂和抽象的部分。相反，我将解释我们需要理解用例的什么。

```
let myVar = 4;

function showNumPlusOne() {
  console.log(myVar + 1);
}

showNumPlusOne(); // > 5

myVar = 8;

showNumPlusOne(); // > 9 
```

Enter fullscreen mode Exit fullscreen mode

这是一个结束的例子。我们的函数`showNumPlusOne()`获取外部变量`myVar`并记录其值加 1。

关于闭包需要注意的重要一点是，它们不仅仅获取变量的值，它们还获取对变量本身的引用。当我们改变上面代码片段中的值时，函数的输出也会改变。

这可以产生一些非常有趣的代码。看看这个片段:

```
let getVar;
let myVar = 4;
{
  let myVar = 8;
  getVar = () => {
    return myVar;
  }
}
console.log(getVar()); // > 8
console.log(myVar);    // > 4 
```

Enter fullscreen mode Exit fullscreen mode

*注意* : [分块提升的函数声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function#Conditionally_created_functions)在浏览器之间超级不一致，这就是我使用函数表达式的原因

我们声明了变量`myVar`两次，但是没关系，因为其中一个在另一个作用域中。该函数使用闭包的概念来获取名为`myVar`的**最近变量**。

它仍然获取实际的变量，而不仅仅是它的值，但是它使用了一个不同于我们正在使用的`myVar`。这与`this`的概念和词汇环境密切相关，我们不会在这里深入讨论。

# 解释-第二部分

所以现在一些更好的解释将包括闭包的概念。现在让我们用`var`和闭包来解释我们的例子。

```
var i;
for (i = 0; i < 10; i++) {
  // closure referencing the i variable outside of the for loop
  setTimeout(() => console.log(i), 0);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，由于我们的`var`在作用域之外，并且我们的闭包引用了文本变量`i`，我们正在记录循环结束后得到的文本变量。

用`var`声明时，`i`在 for 循环之外。如果我们在 for 循环后得到它的值，它将是`10`。这不是关于*何时*或*在哪里*运行函数。因为它引用了变量`i`，无论它在哪里被声明，它都会输出`i`。

让我们用另一个片段来说明这一点

```
var i = 0;
function myFunc() {
  console.log(i);
}

(function(){
  var i = 4;
  myFunc(); // > 0
})() 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 IIFE 为`var`创建一个作用域，因为它不是块作用域。即使我们在调用函数之前定义了`i`并给它赋值`4`，函数仍然记录 0。原因是它没有引用我们声明的`i`。它指的是一个完全不同的`i`——我们片段顶部的那个。

记住这个概念，我们看到`setTimeout`中的函数引用的`i`是不同的`i`，因为`let`是块范围的。然而，由于`var`是唯一的函数作用域，并且在我们的循环之外被提升，它是函数每次引用的同一个变量。

不幸的是，这个答案仍然不完全令人满意。你们中的一些人可能会怀疑地看着这篇文章，因为我们似乎已经变得足够复杂了。

我想让你回忆一下我在这篇文章开头说的话。

> 即便如此，人们还是忘记了`var`是**中更容易解释的**部分...

没错。现在很清楚为什么我们得到了`var`的答案，但是为什么我们没有得到`let`的相同答案？想想吧。

我们引用的`i`应该还是我们开始时的`i`。`i`与众不同的唯一方式是如果我们在每一次迭代中都使用`let`。即使这样，我们如何用我们提供给 for 循环的参数(i++)来改变它呢？我们不能在 JS 里做`let i = i++;`。然后，即使这以某种方式工作，我们没有变异，我们应该能够在我们的 for 循环中使用`const`！

这是怎么回事？

要回答这个问题，我们需要尝试模拟一个 for 循环。

# 一个 For 循环的制作

我们的第一反应可能是使用`while`循环。就这么办吧。

```
let i = 0;
while (i < 10) {
  setTimeout(() => console.log(i), 0)
  i++;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将像 for 循环一样工作，但不是在这个特定的上下文中。即使我们使用了`let`，我们仍然会得到`10` 10 次。让我们把它放在一个街区来阻止它。

```
{
  let i = 0;
  while (i < 10) {
    setTimeout(() => console.log(i), 0)
    i++;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们不能自己访问它，但是这个函数仍然引用一个公共变量`i`，所以我们得到了和使用`var`一样的错误结果。
这里一定还有别的东西。让我们尝试创建一个最内部作用域的局部变量。

```
{
  let i = 0;
  while (i < 10) {
    // copying i's value into a new variable _i
    let _i = i;
    // using _i wherever we would normally use i
    setTimeout(() => console.log(_i), 0)
    _i++;
    i = _i;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

哇...成功了！我们实际上得到的是数字 0-9！如果我们用`var`或`const`替换`let`，我们也会得到同样的结果，就好像这是一个 for 循环一样！

事实证明，这与 for 循环在引擎端发生的情况非常相似。
如果我们在[13.7.4.9 运行时语义:CreatePerIterationEnvironment](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-createperiterationenvironment)上看一下[规范](https://www.ecma-international.org/ecma-262/10.0/index.html)，我们会看到一些非常混乱的语言:

> g.对于 perIterationBindings 的每个元素 bn，do
> i. Perform！thisIterationEnvRec。creatematablebinding(bn，false)。
> 二。让 lastValue 成为？lastIterationEnvRec。GetBindingValue(bn，true)。
> 三世。执行 thisIterationEnvRec。InitializeBinding(bn，lastValue)。

这实质上意味着我们的 for-simulator 做了什么。我们的变量被绑定到每次迭代的上下文中。

现在来全面解释一下。

# 解释-第三部分

让我们再检查一遍所有的东西。

我们意外地得到了 10 倍的`10`，而不是 0-9。

1.  由于`var`的作用域和提升行为，`var`将使`i`在 for 循环之外。
2.  因为我们使用的是函数，所以包含了闭包。
3.  闭包引用文本变量，如果我们使用`var`或`let`会有所不同。
4.  因为我们在每次迭代中创建一个新的绑定上下文，如果我们使用`let`，`i`总是一个新的变量，并且等于迭代中`i`的当前值。
5.  使用`var`，由于`var`不是块范围的，我们在 for 循环中实际上没有`var`语句。因此`var`在每次迭代中并不局限于上下文。
6.  因此，`var`将使闭包在每次迭代中引用同一个变量，而`let`将迫使闭包每次引用一个完全独立的变量。

那里。这就是你的解释。这就是 for 循环的工作方式。

哦？我没有提到 for-in，for-of 和 for-await-of？

哎呀。