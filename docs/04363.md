# 启动 Javascript:了解变量

> 原文：<https://dev.to/mindsers/start-javascript-learn-about-variables-1h2d>

[![Start Javascript: learn about variables](img/8f16e1b435acaed1c0d291ac70b4b638.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ChLQ2UgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1565945985123-4c67ab31eb8d%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

变量是编程的绝对基础。如果没有变量，我们就做不了什么。我们可以在所有的高级编程概念中找到它。我敢说，如果没有它，编程将毫无用处。

我们将涵盖初学者需要理解的变量的基础。不过，我们将为高级开发人员讨论一些有趣的问题，所以请继续阅读。

在这篇文章中，你将学到:

*   什么是变量
*   如何使用它
*   关于变量使用的一些好的实践

## 什么是变量？

变量是存储可能随时间变化的内存数据的好方法。这听起来没什么，但它让程序对人在键盘上(或任何其他设备上)做的事情做出反应。

变量有名字，所以我们可以在代码中需要的地方调用它。它也有价值。比如你可以说变量`abc`有一个值:`3`。当你在代码中写下`abc`时，计算机会用存储在内存中的数据替换它。在我们的例子中，它是`3`。

前面说过，一个变量的数据是可以随时间变化的。这意味着如果您再次运行该软件，或者甚至在同一运行过程中，`abc`可能会有值`5`。这就是“变量”这个名字的由来。这是一小段代码，它可以根据几个因素而变化。

## 如何创建变量？

在 JavaScript 中，变量的定义(当你创建变量时)是这样的:

```
// keyword name = value
var a = 2
let b = 4
const c = 6 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码创建/定义了 3 个变量`a`、`b`和`c`，它们分别具有值`2`、`4`和`6`。

这是我们对变量所做的另外两个动作的快速简化版本。变量的定义等于 1)变量的声明，说我们要创建一个并专门为它保留一些内存空间 2)变量的初始化:给变量我们刚刚第一次声明的值。

```
let a // declaration of the variable a

a = 1 // initialization of the variable a 
```

Enter fullscreen mode Exit fullscreen mode

当这两个动作分开时，**变量一被声明就存在，但是没有值，除非初始化已经完成**。然后我们说变量是未定义的，因为定义的过程没有完成。变量等于`undefined`。

## 变量的类型

我们已经看到变量有一个名字，一个值，我们可以用关键字来定义它。变量也有类型。

类型是一种重新组合相似变量并对它们采取行动的方法，而我们在编写代码时并不真正知道它们的值是什么。

例如，如果我有两个相同类型的变量“integar”，**我知道我可以添加它，即使我不知道将要存储的确切值**。

JavaScript 中有几种类型的变量。在我们已经谈到的 3 个`a`、`b`和`c`变量的例子中，每个变量都有一个数字。所以类型是`Number`。

变量可以是以下类型之一:

*   `String`:一串字符(文本)
*   `Boolean`:只能保存两个值的变量:`true`或`false`。
*   `Array`:数值表。
*   `Object`:一个物体。
*   `Number`:一个数字。它可以是整数、正数、负数、小数。
*   `Symbol`:不可更改的唯一数据。

在 JavaScript 中，我们不显式定义变量类型。变量的类型可以随时间而改变。这并不意味着变量没有类型。

**JavaScript 引擎能够根据变量的值**猜测(或“推断”)变量的类型。这个特性给了开发者很大的灵活性，但是如果他想确定使用了某种类型，他必须自己检查。

为了检查变量的类型，我们可以使用两个关键字`typeof`和`instanceof`，以及 JavaScript 的原生对象给出的几个方法。

`typeof a`将在以下选项中给出变量`a`的类型:

*   `string`
*   `number`
*   `boolean`
*   `function`
*   `object`
*   `symbol`
*   `undefined`

您可能已经注意到，列表中没有`array`。其实，`Array`是一个对象。对于任何其他对象，JavaScript 返回`object`。

使用关键字`instanceof`，您可以验证一个对象“从另一个对象继承”，或者更确切地说，如果它可以在另一个对象的原型链中找到一个对象的原型。

```
class ClassC {}
const objO = new ClassC()

objO instanceof ClassC // true 
```

Enter fullscreen mode Exit fullscreen mode

这个例子很简单，不言自明。

我谈了一点这种语言提供的方法。其中，你会发现`Array.isArray()`、`Number.isInteger()`等。这些方法考虑了要测试的值，并根据断言返回`true`或`false`。

一般来说，好的做法是当这些方法存在时优先考虑它们，而不是使用其他关键字或自制的解决方案。

## 申报

在 Javascript 中，变量的声明是由关键字`var`、`let`和`const`组成的，就像我们之前看到的那样。一开始在这三者之间做出选择可能有点棘手，但他们有不同的目标。让我们一起来看看。

### `var`关键词

这是历史性的关键词。很长一段时间，只有这个关键词，没有其他。

该关键字在其执行上下文中声明一个变量，这意味着该变量只在声明它的函数中可用。

```
// a doesn't exist

function context() {
  var a = 0

  // a exists
}

// a doesn't exist 
```

Enter fullscreen mode Exit fullscreen mode

如果变量是在全局作用域(或上下文)中创建的，意味着在所有函数之外，关键字`var`在全局对象中创建一个变量，该变量在浏览器中是`window`，在 NodeJS 服务器上是`global`。

```
typeof window.a === 'undefined' // return true
typeof window.b === 'undefined' // return true

var a = 0

typeof window.a === 'undefined' //return false
typeof window.b === 'undefined' // return true 
```

Enter fullscreen mode Exit fullscreen mode

### `let`关键词

由 JavaScript 的 ES2015 版本带来的，`let`是一个旨在解决`var`的几个问题的关键字。

<figure>

```
// a doesn't exist
// b doesn't exist

function context() {
  var a = 0
  // b exists but equals undefined

  if (true) {
      var b = 0

      // a exists
      // b exists and equals 0
  }

  // a exists
  // b exists and equals 0
}

// a doesn't exist
// b doesn't exist 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>`var` doesn't make a difference between the scope of the function and the `if` scope</figcaption>

</figure>

事实上，JavaScript 会做一些叫做提升的事情。它将读取函数的代码，看到我们想要在`if`中定义`b`变量，并将其声明移动到`context`函数的顶部，但将其初始化留在`if`中。

这种与其他编程语言非常不同的行为导致变量`b`在`if`之前存在。其值为`undefined`，直到其在`if`中初始化。之后，它的价值就是`0`。

这通常会导致异步代码出现问题，并使代码更加难以理解和调试。

为了避免这个问题，ES2015 版本的 JavaScript 引入了`let`关键字。

<figure>

```
// a doesn't exist
// b doesn't exist

function context() {
  let a = 0
  // b doesn't exist

  if (true) {
      let b = 0

      // a exists
      // b exists and equals 0
  }

  // a exists
  // b doesn't exist
}

// a doesn't exist
// b doesn't exist 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>`let` limits the existence of variables on the containing scope</figcaption>

</figure>

关键字`let`允许声明一个变量并将其限制在包含范围内。

在 JavaScript 中，作用域通常用花括号表示。这意味着每一个有花括号的代码结构都定义了一个作用域，在里面创建的变量在外面是不存在的。

**既然`let`存在，好的做法是默认使用它，而不是`var`。**如果需要的话，你随时可以把它改成`var`。

### `const`关键词

ES2015 版本的 JavaScript 也带来了`const`关键字。它的工作方式几乎与关键字`let`完全一样，但是它允许在声明变量时单独修改变量。

`const`意为“不变”。换句话说，**它是一个永远不变的变量。**你赋予一个常量的值是它在被删除之前唯一存储的值。

```
const a = 0

a = 1 // Uncaught TypeError: Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我说的是“允许在声明变量时单独修改变量”，而不是“允许在初始化变量时单独修改变量”

这是因为你不能像对待其他变量那样分两步定义变量。只有在声明变量的时候，才能初始化常量。

```
const a // Uncaught SyntaxError: Missing initializer in const declaration 
```

Enter fullscreen mode Exit fullscreen mode

一个好的做法是严格限制修改变量的可能性。这样用宁`const`而不用`let`就好了。如果需要，你可以随时把它改成`let`。

如果非要按优先级给关键词列个清单，那会是:`const` > `let` > `var`。

要小心，尽管 JavaScript 中有一个小陷阱，指针和引用并不明确存在，但它们的遗产却在那里。对象是“通过引用传递的”。**`const`关键字创建对值的不变引用。**T3】

<figure>

```
const obj = { name: "Robert" }

obj = { name: "Jean" } // Uncaught TypeError: Assignment to constant variable.

obj.name = "Jean"

console.log(obj) // { name: "Jean" } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>An object is not unchanging, its reference is.</figcaption>

</figure>

关键字`const`阻止我们修改对一个对象的引用。因此不可能重新分配包含一个对象的常量，但是不能保证该对象的属性不可修改。

如果你想防止一个对象的属性在以后被修改，使用`Object.freeze()`方法。

```
const obj = Object.freeze({ name: "Robert" })

obj = { name: "Jean" } // Uncaught TypeError: Assignment to constant variable.

obj.name = "Jean"

console.log(obj) // { name: "Robert" } 
```

Enter fullscreen mode Exit fullscreen mode

## 变量名称

在 JavaScript 中，你可以用大写或小写字母、数字和`_`来命名一个变量。名称不能以数字开头。

你会承认这条规则相当宽松。开发者的创造力不受限制。也就是说，我认为**所有的开发者都应该给自己一些规则**来命名变量。

### 案情

[大写](https://fr.wikipedia.org/wiki/Sensibilit%C3%A9_%C3%A0_la_casse)使用大写或小写字符。编程中有几种规范:`lowerCamelCase`、`UpperCamelCase`、`kebab-case`、`snake_case`、`SCREAMING_SNAKE_CASE`等。您经常会看到一个开发人员社区聚集在同一种语言周围，选择相同的规范来遵循。

例如，Python 社区非常喜欢*蛇案*，而其他一些社区更喜欢*骆驼案*。其他人甚至可以根据他们想要创建的元素来选择:*上骆驼格*用于对象，*下骆驼格*用于标量或原始变量，*尖叫蛇格*用于常量。

我不会在这里提倡，但这里有 3 个原则可以帮助你命名你的变量:

1.  如果你的语言中有标准，就使用它。
2.  一旦你找到了自己的风格，在整个项目中保持它。
3.  在整个项目中保持一致，不管是 1 个还是 30 个开发人员！

### 意为

给变量命名时，意义很重要。它根据上下文来表达变量内部的内容。有意义的变量名使得代码更容易阅读和理解，并且[限制了添加注释](https://blog.nathanaelcherrier.com/fr/les-meilleurs-commentaires-ne-secrivent-pas/) (fr)的需要。

避免使用`a`、`cpt`、`zejqdhalkja`这样的名字。过于一般化的名字或者因读者不同而有不同含义的名字(如`Processor`或`Manager`)也不太好。

使用易发音的变量名。实际上，你能读出的名字对我们人类大脑来说是比较容易的。如果我不得不引用鲍勃叔叔的[干净的代码，对我来说，这是一个关于这个主题的参考，我会引用:](http://amzn.to/2dD6zcB)

> 人类善于言辞。我们大脑的很大一部分致力于词汇的概念。根据定义，单词是可以发音的。[...所以，让你的名字更容易发音。

跳过思维模式。当你试图理解一点代码时，你不希望不得不问自己这种问题:“等等，什么是`r`？”。你已经在试图理解代码了，你不想再去理解每个变量或函数名...你必须记住所有这些！

有许多简单的建议可以应用于命名变量，我不打算一一介绍。反正我觉得我也说不全。

但是，我真的鼓励你阅读 Bob 叔叔写的[干净的代码或者至少——对于这个主题——他写的关于命名变量的 14 页。这一章叫做“有意义的名字”。](http://amzn.to/2dD6zcB)

* * *

这里你有了使用变量的基础(还有一点)。在这个问题上还有很多东西要学，但这是一个好的开始。

如果你想了解更多关于变量的知识，你应该去阅读关于 C/C++指针的文章。即使你不使用 C，这篇文章也会给你一些有用的信息，告诉你变量在类 C 语言中是如何工作的。

更深入地挖掘:

*   无处不在的语言:使用特定于工作的通用语言来命名代码的实体
*   [JavaScript 中的析构](https://blog.nathanaelcherrier.com/en/destructuring-javascript/):发现对`Array`或`Object`变量的特定操作。