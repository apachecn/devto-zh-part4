# JavaScript 控制台 API 和方法

> 原文：<https://dev.to/cole_ruche/javascript-console-api-and-methods-3emf>

*最初发表于我的[博客](https://coleruche.com)。*

作为一名 JavaScript 工程师，我使用`console.log()`方法的次数比[在《权力的游戏》第七季](https://winteriscoming.net/2017/10/26/30-minutes-or-less-watch-all-174373-deaths-in-game-of-thrones-seasons-1-7/)中被杀的人数还要多——嗯，可能没那么多。

重点是，就像许多其他(新手)JS 编码员一样，我非常清楚`log()`是 JavaScript `console`对象的唯一方法。

> 是的，它是一个物体。
> 您可以通过打开浏览器控制台并运行`typeof(console)`来验证这一点，您应该会得到返回的“object”。

既然我们已经证明了它是一个对象，就像所有其他对象一样，除了`log()`之外，它还有许多其他方法。

“为什么知道这些其他方法如此重要？”，你可能会问。好吧，虽然你可能只是继续使用`log`方法来调试你的代码，学习和使用其他方法有助于更好的表示和更容易的调试。还有*哎！*，为什么不多学点东西来帮助我们对抗我们共同的敌人——虫子。此外，你不知道下一个面试你的人袖子里有什么。

我们开始吧，好吗？

希望你的浏览器控制台还在运行，如果没有再次打开它，在我们完成之前不要关闭它，因为我们会偶尔回到他们身边。

要在控制台上查看其他方法，试着运行`console.log(console)`——讽刺！下面的数据应该返回给我们:

```
console {debug: ƒ, error: ƒ, info: ƒ, log: ƒ, warn: ƒ, …}
assert: ƒ assert()
clear: ƒ clear()
context: ƒ context()
count: ƒ count()
countReset: ƒ countReset()
debug: ƒ debug()
dir: ƒ dir()
dirxml: ƒ dirxml()
error: ƒ error()
group: ƒ group()
groupCollapsed: ƒ groupCollapsed()
groupEnd: ƒ groupEnd()
info: ƒ info()
log: ƒ log()
memory: (...)
profile: ƒ profile()
profileEnd: ƒ profileEnd()
table: ƒ table()
time: ƒ time()
timeEnd: ƒ timeEnd()
timeLog: ƒ timeLog()
timeStamp: ƒ timeStamp()
trace: ƒ trace()
warn: ƒ warn()
Symbol(Symbol.toStringTag): "Object"
get memory: ƒ ()
set memory: ƒ ()
__proto__: Object 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们如此多的方法，比我们(确切地说，是我)在控制台上所知道的要多得多。像其他众多的列表一样，我们将挑选出最重要的。

### 控制台. assert()

使用`assert()`方法的最简单的情况是，当我们想只在控制台上显示一些东西，并且只有当传递给方法的断言为假时。如果断言通过，什么都不会发生，或者如果您使用的是浏览器控制台，您会得到一个`undefined`。要看到这一点，如果你的控制台没有打开，请打开它(PUYCINO——这不是真的),然后运行下面的代码:

```
var x = 21;
console.assert(x % 2 === 0, 'oops! x is not divisible by 2');
// or alternatively

var errMsg = 'oops! x is not divisible by 2';
console.assert(x % 2 === 0, errMsg); 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到一个断言错误，错误消息被打印到控制台。尝试将`x`更改为 20 或任何其他偶数，并再次运行断言代码，这一次什么都不会发生。

### console.clear()

这个方法很简单。运行`console.clear()`只是清空控制台，显示`Console was cleared`消息(好像我们看不到它已经被清空)。每当你觉得你的控制台堵塞了，你需要空间的时候，运行这个代码。

### console.dir()和 console.dirxml()

这个方法帮助我们将传递给它的任何有效 JavaScript 对象的所有属性(方法)打印到控制台。还记得我们说过——并且证明了——`console`是一个物体。现在让我们把它作为`console.dir()`方法中的一个参数。运行代码`console.dir(console)`，一个熟悉的输出会显示出来。您还可以尝试使用`console.dir(window)`来查看原生窗口对象的属性。总有一天会派上用场的，你会看到的！

`dirxml`与`dir`几乎相似，差异非常小且不显著。

### 控制台.错误()

这会将内容显示为错误—红色突出显示、浅红色背景和旁边的红色错误(x)符号。让你知道正在显示的是一个错误的所有功能。试试跑`console.error('This is a typical error')`看看我的意思。

这种情况的用例是当您预期代码中有错误时。例如，在返回承诺的 API 调用的. catch 块期间。

### console.group()，console.groupCollapsed()和 console.groupEnd()

这些方法用于将代码块或类似的*组合在一起，无论你想在控制台上显示什么。*
`group()`表示小组的开始。它接受可选的`label`作为参数。这个标签就是这个群体的标签。

`groupEnd()`标志着一个团体的结束。

`groupCollapsed()`与`group()`类似，但是当`group()`中的所有项目自动全部列出时，`groupCollapsed()`以折叠方式显示它们，您必须手动点击旁边的“下拉”列表才能全部列出。让我们来看看这一点。PUYCINO 并粘贴以下内容:

```
console.group('My fav tech tools')
// Here, 'my fav tech tools' is the label for my group
console.log('React')
console.log('Twitter Bootstrap')
console.log('Django')
console.log('Django REST')
console.log('Axios')
console.groupEnd() //ends the group 
```

Enter fullscreen mode Exit fullscreen mode

组也可以嵌套到另一个组中。让我们看看这个和`groupCollapsed()`是如何工作的:

```
console.groupCollapsed('My fav languages and tools')
console.group('JavaScript') //nests in JavaScript group
console.log('React')
console.log('Redux')
console.log('Twitter Bootstrap')
console.groupEnd() //exits nested group for JavaScript
console.groupCollapsed('Python') //nests in a collapsed Python group
console.log('Django')
console.log('Django REST')
console.groupEnd() //exits nested group for Python
console.groupEnd() //exits all groups 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，首先显示的组是折叠的，您必须手动展开它们。接下来，你可以看到我们嵌套在另外两个组中:JavaScript 和 Python。

> 如上所述，始终记得用`groupEnd()`退出每个嵌套组。

### console.log()

我想我们对此都很熟悉。所以没必要浪费时间。它基本上只是把一些东西打印到控制台上，没有任何级别的警告或危险。

### console.table()

这会以表格格式显示数据。它接受一个必须是数组或对象的强制`data`——传入一个字符串不起作用——和一个可选的`columns`作为参数。
让我们来看看这是怎么回事。再次，普伊奇诺(嘿，到目前为止，没有必要再包括这一点)。粘贴以下内容:

```
var nations = ['Nigeria', 'USA', 'Canada', 'Algeria'];
console.table(nations) 
```

Enter fullscreen mode Exit fullscreen mode

这将把数据以表格的形式打印出来，其中有`(index)`和`value`列。使用数组，`(index)`列会自动填充实例的索引。若要指定应该将什么用作表的索引，请改为传入对象。这里，`(index)`列将由对象的`keys`填充，而值将由对象的`values`填充。试试下面:

```
var nationsCurrency = {USA: 'dollars', Nigeria: 'naira', France: 'franc'}
console.table(nationsCurrency) 
```

Enter fullscreen mode Exit fullscreen mode

### 控制台. time()和控制台. timeEnd()

启动一个计时器，您可以用它来跟踪一项操作需要多长时间。它接受一个可选的`label`作为参数。用相同的`label`调用 t `imeEnd()`结束计时器，并输出自从`time()`被调用以及`time()`和`timeEnd()`之间的代码被执行以来已经过去的时间(毫秒)。

```
console.time('test')
let mult = (2000*4444);
mult * 222;
console.timeEnd('test') 
```

Enter fullscreen mode Exit fullscreen mode

最好的用例是比较哪两个相似的函数或逻辑更快。例如，下面的代码比较了`for`和`while`循环的执行速度。

```
console.time("test for loop");
for (i = 0; i < 100000; i++) {
  console.log(i)
}
console.timeEnd("test for loop");

console.time("test while loop");
while (i < 1000000) {
  i++
}
console.timeEnd("test while loop"); 
```

Enter fullscreen mode Exit fullscreen mode

通过运行上面的代码，我们可以有效地看到`for`循环比`while`快。

### console.warn()

向浏览器控制台输出警告消息。它以浅黄色背景显示数据，旁边有一个警告图标。试试看:

```
console.warn("GOT is hugely graphical and full of violent. Watch at your own discretion.") 
```

Enter fullscreen mode Exit fullscreen mode

我们已经完成了重要的方法。希望到现在为止，您在调试会话期间已经有了更少的`console.log()`行。

也可能不是，不管怎样谢谢你走到这一步。

***Valar Morghulis!***