# JavaScript 101: var 还是 let 还是 const？

> 原文：<https://dev.to/rdlauer/javascript-101-var-or-let-or-const-5epj>

最近我花了一些空闲时间[将一些“旧的”NativeScript 项目升级到 6.0](https://www.nativescript.org/blog/nativescript-6.0-application-migration) 。这个过程非常顺利，但是我意识到在给变量赋值时，我对`var`、`let`和`const`的使用并不一致。

这让我想到:*我甚至不能 100%确定什么时候应该使用哪种变量赋值方法！*我的意思是，`var`和`let` *似乎可以互换，对吗？而且它仍然是 JavaScript，所以看起来我可以随时更改任何我想要的值和数据类型。所以也许没有太大的区别？还是我把这一切都想错了？经典抢！🤦*

原来*和*在`var`、`let`和`const`之间有一些相当显著的区别。所以，如果你像我一样是一个永远不会懂的 JavaScript 开发人员(或者，嘿，也许你才刚刚开始！)，以下是我了解到的情况:

## 德高望重的`var`

在 JavaScript 中，变量在创建时用`undefined`的值初始化。所以如果你曾经写过这样的东西:

```
var foo;
console.log(foo); 
```

Enter fullscreen mode Exit fullscreen mode

...它会在你的控制台中返回`undefined`。有道理，对吧？

但是如果我先给变量赋值，世界上的一切都是正确的:

```
var foo = "yo";
console.log(foo); 
```

Enter fullscreen mode Exit fullscreen mode

...您会在控制台中看到字符串`"yo"`，因为现在`foo`是一个值为“yo”的字符串。

由于是 JavaScript，用`var`初始化的变量不仅可以改变它们的值，还可以改变它们的数据类型。所以，是的，这是有效的语法:

```
var foo = "yo";
foo = 123;
console.log(foo); 
```

Enter fullscreen mode Exit fullscreen mode

...得到的输出是控制台中的`123`。

很简单，对吧？现在让我们来看看`var`的一个很大的不同，那就是它的*范围*。

## 功能范围 vs 块范围

变量的*范围*让编译器知道变量(和函数)在你的应用程序中的什么地方是可访问的。在`var`的例子中，变量被“限定”在创建它的函数范围内，并且(字面上)只能在该函数内部访问。

```
function Hello() {
    var foo = "poo";
}

Hello();

console.log(foo); // THIS NO WORK 😭 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我将`console.log(foo);`行移到`Hello()`函数中，一切都会变得很酷。

因此，在我们进入`let`时，让我们继续这个范围的概念:

## `let`

> 任何勇于承认自己知道 NKOTB 代表什么的人都会得到加分。提示:🧑‍🤝‍🧑🎤

实际上，`let`与`var`相同。除了！(你知道我会这么说。)而不是被*函数作用域*(见上文)，`let`被*块作用域*。这意味着用`let`创建的变量在创建它的“块”中是可用的。什么是“块”？它实际上是花括号内的任何东西(比如函数赋值、`for`循环、`if`语句等)。

如果你从另一种语言学习 JavaScript，`let`可能比`var`更有意义。让我给你举个例子:

```
function Hello() {
    for (var i = 0; i <= 10; i++) {
        var foo = i;
    }

    console.log(foo); // 10
}

Hello(); 
```

Enter fullscreen mode Exit fullscreen mode

...相对于我们使用`let`:

```
function Hello() {
    for (let i = 0; i <= 10; i++) {
        let foo = i;
    }

    console.log(foo); // 😭
}

Hello(); 
```

Enter fullscreen mode Exit fullscreen mode

因为在第一个例子中，`i`的作用域是**整个函数**，所以在`for`循环之外引用它完全没问题。

然而，在第二个例子中，`i`是`for`循环的*块作用域*，这意味着它在循环之外不可用。

## 这件`const`事情

乍一看，您可能认为`const`是一种分配不可变(不可改变)变量的方法。它们是现在和永远都不会改变的“常数”。**故事结束！** 🛑

`const`恰好与`let`几乎相同。主要的区别在于，一旦你使用`const`给一个值赋值，你就不能重新给它赋值。这很酷，因为它让`const`变成了字面上的*常量*。

好吧，这不完全正确。

> 我要在这里打断你。如果你想深入了解`const`以及它为什么不像你认为的那样不可改变，你应该读一读 Brian Rinaldi 的文章，[被 JavaScript 的 const？我也是！](https://dev.to/remotesynth/confused-by-javascript-s-const-me-too-4dfo)。

通过改变一个对象的*属性*，你实际上并没有重新赋值(即使它是用`const`声明的)。这意味着该语法完全有效:

```
const me = {
    name: "Rob";
}

me.name = "Rob Lauer"; 
```

Enter fullscreen mode Exit fullscreen mode

# 只是 JavaScript 东西

## 那么我该用哪个呢？？？

我听到了，实际上我还没有给你任何指导。在许多场景中，`var`、`let`和`const`似乎是可以互换的。但我的看法是:

1.  我尽量从来不用`var`。除了你自己打字的肌肉记忆之外，没有真正的理由使用它。
2.  一直用`let`(是的，我甚至用它来做常数)。
3.  如果你想使用`const`，要知道它并没有提供比`let`更多的优势。

事实上，如果你真的在创建常量，我建议你适当地命名变量。类似于:

```
const __IAMACONSTANT = "indeed i am a constant"; 
```

Enter fullscreen mode Exit fullscreen mode

**总之:**

`var`是*函数作用域的*，这意味着你只能访问创建它的函数内部的值。

`let`是*块作用域的*，这意味着您只能访问创建它的块(`{}`)内的值。

`const`也是*块范围的*，但是与`let`和`var`不同，它不能被重新分配(嗯，除了上面提到的一些例外！).