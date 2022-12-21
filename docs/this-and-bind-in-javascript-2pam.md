# 这和 Javascript 中的绑定

> 原文：<https://dev.to/bbarbour/this-and-bind-in-javascript-2pam>

如果你正在学习 Javascript，你无疑会在早期遇到`this`关键字。起初，它看起来很简单。这是我理解的基本定义。

关键字`this`引用函数所属的对象。

说十遍。用刀把它刻在你的手臂上。然后后悔。当你挖掘得更深一点的时候，你会发现这个关键词比第一眼看到的要微妙得多。因为用`this`函数在哪里创建并不重要。唯一重要的是函数在哪里执行。

```
const protagonist = {
  name: 'Mario',
  announce() {
    return this.name
  }
}

console.log(protagonist.announce()) // Returns Mario 
```

Enter fullscreen mode Exit fullscreen mode

根据经验，看函数调用的左边。在这个例子中我们看到了什么？`protagonist`。这就是`this`所指的。很好，你明白了大多数时候`this`是如何工作的。

现在是机器中众所周知的扳手。如果函数调用左边什么都没有呢？会发生什么？

嗯，它仍然在一个对象内，`global`对象。对象是 Javascript 放入堆栈的第一个东西，也是最后一个离开堆栈的东西。在浏览器的情况下，`global`意味着`Window`对象。因此，`this`的事情有时会有点奇怪。感觉它在做一些无意义的事情，但是如果你理解了`global`和 scope 是如何工作的，它就开始变得有条理了。如果你不确定我在说什么，我绝对建议你先阅读这两件事。

```
function antagonist() {
  return this.name
}

console.log(antagonist()) //returns undefined 
```

Enter fullscreen mode Exit fullscreen mode

这里的`this`指的是什么？这个函数不是孤立的，因为它出现在代码中，它实际上在`global`对象的“内部”。因此，它在`global`中寻找一个名为`name`的变量。因为名字没有被声明，它吐出未定义的。

再来看另一个例子:

```
const protagonist = {
  name: 'Mario',
  announce() {
    return this.name
  }
}

const ourHero = protagonist.announce

console.log(ourHero()) //returns undefined 
```

Enter fullscreen mode Exit fullscreen mode

这个有点棘手。其实类似的例子我第一次看到就很困惑。让我们把它分解一下，这样你就能明白这里发生了什么。

您创建了`protagonist`对象，并为其分配了`announce`方法。很简单。接下来，创建变量`ourHero`，它是`global`对象的一部分。你从`protagonist`分配`announce`功能给`ourHero`。它只引用了`announce`函数，而不是它所在的对象。

就好像我们做了一样:

```
function announce () {
    return this.name
}

const ourHero = announce() 
```

Enter fullscreen mode Exit fullscreen mode

那么`this`在这种情况下指的是什么呢？

左边什么都没有，对吧？所以它引用了`global`对象，正如我之前所说的。知道将`use strict`添加到你的文档或者甚至添加到一个函数的开头将会阻止这种行为也是很重要的。你将得到一个堆栈错误，而不是`undefined`，说这个名字没有被定义。如果你不想处理这种行为，这本身就很有价值。

也有办法让它工作，得到我们需要的对`protagonist`的引用。输入`bind`关键字。

把 bind 方法想象成一个套索，我们把它扔向另一个对象，把它拴起来。一旦连接完毕，`this`关键字将引用您作为参数传递给它的内容。

让我们来看看

```
const protagonist = {
  name: 'Mario',
  announce() {
    return this.name
  }
}

const ourHero = protagonist.announce.bind(protagonist)

console.log(ourHero()) //returns Maro 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧，`protagonist`被绑定，`this`引用什么。有点神奇，但它有助于避免无法按预期调用函数的问题。

我在这里举的例子并没有涵盖*到`bind`和`this`的所有事情*，但是这些都是最基本的。当你开始使用 ES6 类时，你会注意到在`constructor`中也使用了`this`来声明新的类属性，你可以使用这里的课程来帮助理解这些属性。

祝你好运，继续学习。因为，这就是我要做的。