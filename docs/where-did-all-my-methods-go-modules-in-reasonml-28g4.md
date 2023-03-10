# 我所有的方法都去哪了？推理中的模块

> 原文：<https://dev.to/hagnerd/where-did-all-my-methods-go-modules-in-reasonml-28g4>

从 JavaScript 到 ReasonML 有很多障碍。虽然语法可能看起来很友好，类型推理也很惊人，只需要你很少的努力就能掌握它，但有很多符号，需要进行巨大的思维转变。

为了帮助其他来自非函数式语言的人，特别是 JavaScript
的人，我想开始分享一些我在学习 ReasonML 时犯的错误。

这个社区是最好的社区之一，不管你认为你的问题有多傻，总会有人帮助你，但是网上没有太多关于 ReasonML 的内容。加入[不和谐](https://discord.gg/reasonml)，不要害怕在[论坛](https://reasonml.chat)发帖。

* * *

函数式编程语言倾向于以不同于非函数式语言的方式构建代码。无论您是否认为 JavaScript 是一种面向对象的语言，它仍然有某种形式的使用原型链的继承，并且您直接在数据结构的实例上调用方法。

## JavaScript 方式

一个例子。在 JavaScript 中，你可以像这样创建一个数组文字:

```
const myFriends = ["Me", "Myself", "I"]; 
```

您可以通过在数据结构的实例上使用点符号来使用数组方法。

```
const myFriends = ["Me", "Myself", "I"];

const shoutingFriends = myFriends.map(friend => friend.toUpperCase()); 
```

### 引擎盖下

在 JavaScript 中，当您声明一个新的数组文字时，您正在创建数组伪类的一个新实例。

```
const myFriends = new Array("Me", "Myself", "I"); 
```

使用伪类在 JavaScript 中实现一个简单的数组可能是这样的:

```
function MyArray() {
  // pretend I didn't use an actual Array in my definition
  this.value = Array.from(arguments)
  this.length = arguments.length
}

MyArray.prototype.forEach = function(cb) {
  for (let i = 0; i < this.value.length; i++) {
    cb(this.value[i], i, this.value);
  }  
}

MyArray.prototype.map = function(cb) {
  let __internal = [];

  this.forEach(function(element, index, array) {
    __internal.push(cb(element, index, array));
  });

  return __internal;
}

const myFriends = new MyArray("Me", "Myself", "I");
const shoutingFriends = myFriends.map(friend => friend.toUpperCase()); 
```

> 您可能习惯于看到类而不是伪类，但是在本质上，JavaScript 类只是导致相同结果的语法糖。

值得注意的是，我们的方法对`this`(实例本身)有一个*隐式*依赖。我们没有向方法中传递数组。对`this`的*隐式*依赖允许点符号`myFriends.map...`。

我们的实例`MyArray`实际上并没有复制`forEach`或`map`，因为那样会非常低效。如果我们的程序有成千上万个数组，就没有必要将每个方法复制到数据结构的每个实例中。相反，JavaScript 有一个叫做原型的东西。

所以当我们使用`myFriends.map...`时，引擎会发出“嗯....🤔myFriends 的原型上似乎没有名为`map`的功能。或许它的原型有。”然后它检查`MyArray`，因为当我们创建一个(伪)类的实例时，JavaScript 会向我们的实例添加一些信息，这样以后它就知道从谁那里继承函数了。

然后它看到“啊啊。是啊！a 在它的原型上有一个名为`map`的函数吗，然后它使用那个`map`函数进行我们的操作。

所有这些漫谈都是为了向您展示数据结构和方法在 JavaScript 中是如何紧密耦合的。为了映射一个数组，我们使用数组的实例，并像这样将一个函数链接到它上面`[1,2,3].map(n => n + 1)`。

当你知道这是常态时，你会想，“嗯，是的，废话。不然你会怎么做？”。

* * *

## 讲道理

在函数式编程语言中，数据结构存储数据。就是这样。那么你是如何操作数据结构的呢？在 ReasonML 中，我们将操作一个类型的函数集合放入模块中。一个模块可能只知道如何操作一种类型，例如数组。

函数有一个数组的*显式*依赖。

在 ReasonML 中，`Array`模块包含了*大多数*函数，这些函数知道如何操作数组数据结构。模块有什么好处？它们是独立的。它们不必被复制，也不必创建指向其“所有者”的指针。他们也完全避免“这个/自己”或数据结构实例的概念。

它们是纯函数，接受显式参数并返回一致的结果。

### 举个例子

In ReasonML `Array.map`是一个接受两个参数的函数:

1.  一个函数，它接受数组中包含的任何类型的数据(在下面的示例中，它接受一个字符串)，并对每个元素进行转换，返回相同或不同的类型(在下面的示例中，它返回一个字符串)。
2.  我们想要操作的实际数组。

```
let myFriends = [| "Me", "Myself", "I" |];
let shoutingFriends = Array.map(friend => String.uppercase(friend), myFriends); 
```

这种对传入数组的*显式*需求可能看起来仍然是一个奇怪的选择，尤其是如果您习惯于利用 JavaScript 或其他面向对象语言中的方法链的话。

```
const myArray = [1, 2, 3, 4, 5, 6];
const res = myArray
  .map(n => n * 3)
  .filter(n => n % 2 === 0)
  .reduce((acc, n) => acc + n, 0);
// => 36 
```

然而，你可以用几种不同的方式在 ReasonML 中实现同样的事情。

(我在这里使用一个列表，而不是一个数组，原因我们将在另一篇文章中探讨，但为了我们的目的，眯着眼睛，假装它是一个数组)。

```
let myArray = [1, 2, 3, 4, 5, 6];
let res = myArray
  |> List.map(n => n * 3)
  |> List.filter(n => n mod 2 === 0)
  |> List.fold_left((acc, n) => acc + n, 0);
/* => 36 */ 
```

或者，如果您使用 Bucklescript，您可以使用 Belt。

```
let myArray = [| 1, 2, 3, 4, 5, 6 |];
let res = myArray
  -> Belt.Array.map(n => n * 3)
  -> Belt.Array.keep(n => n mod 2 === 0)
  -> Belt.Array.reduce((acc, n) => acc + n, 0);
/* => 36 */ 
```

或者是 Js。阵列模块...

```
let myArray = [| 1, 2, 3, 4, 5, 6 |];
let res = myArray
  |> Js.Array.map(n => n * 3)
  |> Js.Array.filter(n => n mod 2 === 0)
  |> Js.Array.reduce((acc, n) => acc + n, 0);
/* => 36 */ 
```

> 在这篇文章中，我不想深究`|>`和`->`操作符是什么。只要知道`|>`和`->`操作符，除了阿谀奉承之外，还可以帮助你实现同样的方法链接思想。您从一个函数中获取结果，并将其传递给下一个函数。

因此，从理论上讲，方法从在数据结构的实例中可用，转移到包含在专用模块中。这使得它们成为纯粹的函数，要求你显式地传递你想要操作的数据结构。

* * *

### 一些附加信息

如果您喜欢阅读类型签名，下面的链接包含数组和列表模块的类型定义。

[阵列模块](https://reasonml.github.io/api/Array.html)
[带阵列模块](https://bucklescript.github.io/bucklescript/api/Belt.Array.html)
[列表模块](https://reasonml.github.io/api/List.html)
[带列表模块](https://bucklescript.github.io/bucklescript/api/Belt.List.html)

* * *

### 接下来是什么？

原因人类仍在进化，所以通常有很多方法来剥一只猫的皮，当选择一种而不是另一种时，通常有很好的理由。

在以后的文章中，我将探索 ReasonML 标准库中包含的一些不同模块，以及 Belt 和它们与 JavaScript 的关系。