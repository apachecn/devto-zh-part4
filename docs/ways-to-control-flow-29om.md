# 控制流量的方法...

> 原文：<https://dev.to/this_frankie/ways-to-control-flow-29om>

今天，我要谈谈如何控制你的代码流。因为我是一个 JavaScript 开发人员，所以所有的例子都是用 JavaScript 编写的。

当我开始自学开发时，我学到的第一件事是`if`和`else`。这是最简单的控制选项。它通常会震动成这样...

```
let a = 10;

if (a >= 10) {
  alert('yippee');
} else {
  alert("guess it's not quite 10");
}
/* will alert 'yippee' */ 
```

Enter fullscreen mode Exit fullscreen mode

我相信这是几个初学者/初级开发人员以前见过的。

我的第二步是条件操作符，或三元操作符。这是上面例子的简写。

```
let a = 11;

a === 10 ? alert('heck yeah!') : alert('whomp whomp');
/* will alert 'whomp whomp' */ 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个，因为它非常清晰简洁。在您只需要检查一个语句是否存在的情况下，这很有用。

但是知道你什么时候应该使用它是很重要的。一个`switch`语句总是比其他控制流方法更适合作为一个`switch`语句。例如，如果需要，您可以嵌套一个`if else`，但是如果您不小心开始嵌套三元运算符，事情将会变得非常棘手。

我学到的最后一个叫做短路评估。这可能是最简洁的流程控制。我想我曾经听到有人把它描述成一个小故障，而不是一个预期的功能。

```
let a = 10;

/* If true, evaluate next statement */
a === 10 && alert('* air horn noise *');
/* If false, evaluate next statement */
a === 10 || alert('* slowly dying air horn noise *');
/* will alert '* air horn noise *' */ 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，因为条件语句，任何测试逻辑结果的语句，返回 true 或 false(或未定义的或任何其他数量的选项，但我们现在忽略这些)。通过将`&&`放在其中一个条件之后，“计算机”认为“如果上一个条件评估为真，你需要评估下一个语句。”

这个的逆，`||`，基本会尝试相反的。如果你刚刚评估的语句是假的，你需要评估下一个语句。

对于我最近做的一些函数式编程来说，这真的很好。

```
function runIfMoreThanTen(num) {
    return num > 10 && alert('woohoo');
} 
```

Enter fullscreen mode Exit fullscreen mode

我基本上说的是，‘只要`num`大于 10，就运行警报，否则返回 false。’

所以本质上我写了这个:

```
function runIfMoreThanTen(num) {
    if (num > 10) {
        return alert('woohoo');
    }
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

哪个更好？没关系。我有过爱打代码高尔夫的同事，我自己也有涉猎，会一直用短路。我在工作的时候，我觉得用短路之类的东西没什么帮助。它需要多一点脑力，提高了你代码库的准入门槛。有用例，它在 React 中很有用(或者至少这是我看到的第一个例子)。

我希望这能让你更好地理解如何以简单明了的方式控制流量！