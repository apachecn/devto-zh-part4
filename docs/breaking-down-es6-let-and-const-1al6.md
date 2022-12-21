# 分解 ES6: let 和 const

> 原文：<https://dev.to/torianne02/breaking-down-es6-let-and-const-1al6>

四年前的今天， [ECMA International](https://www.ecma-international.org/) 发布了 ECMAScript 的第六个主要版本。这个版本正式命名为 ECMAScript 2015，但通常被称为 **ES6** 。【丁】。丁。丁。我希望这能给你敲响警钟，打开好奇心之灯。

在找工作的时候，我注意到**很多时候，当一个公司把 JavaScript 列为工作要求时，他们会写成“JavaScript (ES6)”。从我的经验中，我了解到 ES6 原则/标准的知识被许多公司作为一项工作要求广泛追捧。因此，我写这个关于 ES6 的系列博客是为了**

1.  磨砺我自己的技能和 ES6 规则知识。
2.  帮助教授和/或加强你在这方面的技能。

## 入门

这篇博客将是这个系列的第一篇，所以我想我应该从分解 ES6 对`let`和`const`的介绍开始。在我们深入研究这些关键词之前，我想提一下 ES6 对块范围的引入。

### 封锁范围

在引入 ES6 之前，我们使用`var`来声明 JavaScript 中的所有变量。使用`var`声明的变量可能属于全局范围或局部范围。

**全局范围** -在函数外声明的变量被认为是具有*全局范围*的*全局*变量，这意味着“网页上的所有脚本和函数都可以访问它”

**局部作用域**——在函数中声明的变量被认为是具有*局部作用域*的*局部*变量，这意味着它“只能从声明它的函数中访问”。

抛开这些解释，让我们来讨论一下**阻塞范围**。使用`let`和`const`声明的变量被认为是在块范围内。这意味着变量只能在声明它的块中被访问。你可能会问，什么是街区？嗯，当你看到花括号`{}`时，通常会发现一个块。块可以在条件语句、for 和 while 循环等中。

简单来说，例如，如果在一个 **if 语句**中使用`let`或`const`声明了一个变量，那么该变量只能在该 if 语句的块范围内被访问。

*注意:如果你想更深入地了解这三个范围，我也写了这篇文章:*

[![torianne02](img/673cdf0e7c10bebf9684b43ea5f5ae6c.png)](/torianne02) [## Javascript 中的范围

### 维多利亚克劳福德 1919 年 4 月 4 日 3 分钟阅读

#javascript #codenewbie #womenintech #beginners](/torianne02/scope-in-javascript-4ang)

现在我们已经定义了块范围，让我们继续讨论我们的两个关键字，`let`和`const`。

## `let`

正如我已经说过的，`let`被认为是一个块范围变量。与`var`类似，`let`允许变量在其作用域内被重新分配。

```
function testLet() {
  let cat = “Mr. Whiskers”; 
  console.log(cat) // “Mr. Whiskers”;

  cat = “Tuna”;
  console.log(cat) // “Tuna”;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然用`let`定义的变量的行为类似于用再分配定义的`var`,但在重新声明时，它的行为并不类似于`var`。`var`变量可以在同一个作用域内重新声明，而`let`变量不允许这样做。

```
function testLet() {
  let cat = “Mr. Whiskers”; 
  console.log(cat) // “Mr. Whiskers”;

  let cat = “Tuna”; // SyntaxError: Identifier ‘cats’ has already been declared
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，变量可以在整个父块范围内的子块元素中用相同的名称声明。这是为什么呢？从技术上来说，这将被认为是另一个块作用域，在这个作用域内声明的变量不能在它之外被访问。

```
function testLet() {
  let cat = “Mr. Whiskers”;

  if (cat) {
    let cat = “Tuna”;
    console.log(cat) // “Tuna”
  }

  console.log(cat) // “Mr. Whiskers”
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:在同一个项目中，对多个变量使用相同的变量名是不常见的。

尽管如此，我们仍然可以在它们的子块元素中给变量重新赋值。这是为什么呢？这是因为从技术上讲，我们仍然在变量的块范围内，并且可以在其范围内的任何地方访问它。

```
function testLet() {
  let cat = “Mr. Whiskers”;

  if (cat) {
    cat = “Tuna”;
    console.log(cat) // “Tuna”
  }

  console.log(cat) // “Tuna”
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`let`声明的变量可以不用赋值就声明，类似于`var`的情况。这意味着变量已经声明但还没有定义，这将输出一个值`undefined`。

```
function testLet() {
  let cat;
  console.log(cat) // undefined

  cat = “Mr. Whiskers”;
  console.log(cat) // “Mr. Whiskers”
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们来谈谈变量*吊装*。当使用`var`时，变量和它们的定义一起被提升，这意味着它们浮动到作用域的顶部，并且在该作用域的任何点都是可用的。用`let`声明的变量不会以同样的方式提升。声明存在，但是值/定义不存在，所以如果您试图在变量被声明之前调用它，您将收到一个`ReferenceError`。

```
function testLet() {
  console.log(cat) // ReferenceError: cat is not defined
  let cat = “Mr. Whiskers”;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `const`

同样，使用`const`声明的变量被认为是一个块范围变量。与`let`和`var`不同，`const`不允许重新分配。使用`const`是标识符不会被重新分配的信号

```
function testConst() {
  const cat = “Mr. Whiskers”;
  console.log(cat) // “Mr. Whiskers”

  cat = “Tuna”; // TypeError: Assignment to constant variable 
} 
```

Enter fullscreen mode Exit fullscreen mode

`const`也不允许你声明一个没有初始化的变量，这与`let`和`var`都不同。

```
function testConst() {
  const cat; // SyntaxError: Missing initializer in const declaration
} 
```

Enter fullscreen mode Exit fullscreen mode

与`let`类似，`const`不能在同一个块范围内重新声明。

```
function testConst() {
  const cat = “Mr. Whiskers”;

  const cat = “Tuna”; // SyntaxError: Identifier ‘cat’ has already been declared
} 
```

Enter fullscreen mode Exit fullscreen mode

与`let`类似，`const`也可以在子块元素中声明一个同名的新变量，因为在该块范围内声明的变量在块范围之外是不可用的。

```
function testConst() {
  const cat = “Mr. Whiskers”;
  console.log(cat) // “Mr. Whiskers”

  if (cat) {
    const cat = “Tuna”;
    console.log(cat) // “Tuna”
  }

  console.log(cat) // “Mr. Whiskers”
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，虽然使用`const`声明的变量不能被重新赋值，但是如果变量的值被设置为一个对象或数组，那么对象或数组中的值可以被更新。简单地说，例如，对象中的键值可以被改变，或者数组可以被添加。

```
function testConst() {
  const cats = [“Snowball”];
  console.log(cats) // [“Snowball”]

  cats.push(“Mr. Whiskers”, “Tuna”)
  console.log(cats) // [“Snowball”, “Mr. Whiskers”, “Tuna”]
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 最后的想法

当谈到声明变量时，我采取的立场是我将不再使用`var`，因为它可能会变得有点混乱，因为你可以在相同的范围内声明相同的变量 1000 倍。这可能会导致问题并容易破坏代码。对于另一个想要为您的代码做贡献的开发人员来说，这看起来也非常混乱。

如果你不想改变变量值，使用`const`。对于计数器或 For 循环来说，`let`是最理想的选择。说到底，这都是基于个人喜好。只要记住，如果你的代码是公开的，就要让它干净，让每个人都容易遵循，而不仅仅是你。

我希望这是有帮助的，我知道到目前为止对我是有帮助的。如果你认为有什么需要修改或补充的，请随时告诉我，因为我还在学习中！！！

#### 来源

[JavaScript 中的 var vs let vs const](https://tylermcginnis.com/var-let-const/)
[JavaScript ES6+:var，let，还是 const？](https://medium.com/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75)
[ES6: Let 和 Const](https://medium.com/javascript-in-plain-english/es6-variable-definition-feature-using-let-and-const-14eb143f799e)
[Let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
[Const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
[ES6-Variables](https://www.tutorialspoint.com/es6/es6_variables.htm)
[JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)