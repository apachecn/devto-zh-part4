# 函数式 JavaScript 的隐喻性介绍

> 原文：<https://dev.to/maxwell_dev/a-metaphorical-introduction-to-functional-javascript-3og4>

函数式 JavaScript 不是一个工具、框架、第三方插件、npm 模块或任何你可以添加的东西。函数式编程是一种编写代码的方法，函数式 JavaScript (FJS)是如何将这种方法用于 JavaScript 的。像所有的方法一样，它有优点和缺点，一个人做出的权衡，喜欢和不喜欢它的人，以及国际政府中不同的游说团体。

我坚定地支持 FJS。我已经写了几年的 JavaScript 了(写得很差或者其他的),希望我从一开始就了解了它。我发现 FJS 的好处非常值得学习，它产生了可读性更好、更灵活、更易维护的代码。我努力想弄清楚写 FJS 意味着什么，但一旦我写了，就再也回不去了。

从名字上看，你会认为函数式编程就是写很多函数。在某种程度上，这是真的，但要做到这一点，必须遵循几条规则。乍看之下和谷歌搜索之后，这些规则往往难以理解。

1.  使用纯函数
2.  不要突变状态
3.  声明性的，而不是命令性的
4.  高阶函数

在这篇文章中，我将尝试用更容易理解的方式来分解 FJS 的这些元素。无论如何，这不是一个详尽的指南，而是一个起点，让人们可以更好地了解更详细、更全面的资源来学习 FJS。

我们开始吧！

## 保持函数纯净

使用纯函数和避免状态突变可能是编写 FJS 最重要的部分。我不会从通常的定义开始，而是放纵自己，用一个想象中的晚宴来解释。

### 一个天使和一个变种人进入一个晚宴...

首先，想象一个天使。天使散发出柔和、纯白的光芒，有着闪亮的翅膀和安详的面容。它们在地面上轻轻地摆动，平稳而优雅地移动。没有一个活着的人能看见这个天使，它穿过它接触的任何东西。

假设这个天使在一个拥挤的晚宴的角落里。你告诉天使他们需要穿过房间，站在潘趣酒碗旁边。天使点点头，开始向这个地方飘去。没有人能看到或触摸到它。没有人的谈话被打扰，也没有人需要离开他们的方式。多亏了这一切，天使选择了最短的路线到达潘趣酒碗。如果晚宴上坐满了全新的客人，天使可以沿着准确的路径再做一次。

现在想象一下这个天使的几乎完全相反的一面:一个放射性突变体。这个变种人曾经是人类，但现在已经变成了某种奇形怪状的东西。他们可以有任何你想要的怪诞特征:挥舞的触手，遍布背部的眼睛，有蹼有爪的脚，带有几十年前的流行文化参考的 t 恤，或者他们拥有一家房地产公司。不管你选什么，这个变种人很吓人，不能看太久。

假设这个变种人有同样的任务:从晚宴的角落移到潘趣酒碗。你可以想象那会有多可怕。人们会尖叫着不断推开变种人。此外，它的放射性会给随机的人带来不同的突变，客人也会逃离它们。变异人需要沿着一条不可预知的路径推推搡搡才能到达那个地点。如果你在一个有不同客人的派对上重新开始这个场景，辐射会让不同的人变异，人类会以新的方式恐慌。变种人将需要采取不同的，但同样粗糙，路线到潘趣酒碗。

### 为纯函数

正如你可能已经猜到的，天使拥有一个纯函数的所有品质。

1.  **外部状态不变。天使穿过房间，没有任何人或事改变。一个纯函数完成它的工作时，函数之外的任何东西都不会改变。**
2.  **同样的输入有同样的结果。天使每次都走相同的路线到达相同的地点。一个纯函数，当给定相同的输入时，每次返回相同的结果。**

如果这个名字还不足以说明问题的话，这种突变体具有改变状态的功能的所有特性。

1.  **函数外的变量受到影响。**变异人通过惊吓聚会客人，让其他人变异来影响其他人。不纯函数有意或无意地改变了存在于它们之外的变量。
2.  **相同的输入可以有不同的结果。**变异体让随机的人变异，这将改变恐慌的类型，从而改变变异体每次采取的路径。不纯函数由于每次影响外部变量的方式而返回不同的值。

下面是一些实际的 JavaScript 来说明这一切。下面的`addNumber`函数是天使还是变种人？

```
let number = 0;

let addNumber = x => {
  number += x;
  return number;
} 
```

Enter fullscreen mode Exit fullscreen mode

`addNumber`是一个突变体，因为它改变了函数外的变量`number`。这些变化意味着我们可以用相同的参数运行这个函数两次，得到不同的结果。

```
addNumber(5) // 5
addNumber(5) // 10 (which is not 5) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要一个纯天使函数，我们应该像这样重写一个。

```
let number = 0;

let addNumbers = (x, y) => x + y; 
```

Enter fullscreen mode Exit fullscreen mode

我们没有依赖外部变量，而是将两个数字都作为变量传入。这使得函数的所有变量都在自己的范围内，相同的输入给出相同的结果。

```
addNumbers(number, 5); // 5
addNumbers(number, 5); // 5 (which is 5)! 
```

Enter fullscreen mode Exit fullscreen mode

FJS 使用纯函数，因为它们就像天使。天使是好人，变种人是坏人。别让变种人赢了。使用纯函数。

## 是陈述性的，而不是命令性的

长久以来，我很难理解声明式和命令式编程之间的区别。首先，要知道声明式和命令式编程都是有效的方法，各有利弊。函数式编程只喜欢声明性。

至于具体的，我们再来想象两个不同的存在。这次是一个南方美女和一个马童。我们让他们俩给我们拿一桶牛奶，给他们一个空桶来做这项工作。

南方美女很傲慢，不喜欢弄脏自己的手。她通过召唤她的仆人并说“我*宣布*，如果外面有一头奶牛，用这样的桶给我拿一桶牛奶来！”仆人鞠了一躬，检查了一下桶，离开了，然后拿着一桶牛奶回来了。在另一个桶里，看起来和我们给她的那个一样。南方美女接过牛奶递给我们。

马童喜欢弄脏自己的手。他处理这个任务的方式是提着桶，去牲口棚，找到一头奶牛，然后经历所有的动作去挤奶。他选择合适的奶牛，给奶牛挤奶，把牛奶装满我们的桶，然后自己带回来给我们。

两个人都给我们拿了一桶牛奶，尽管方式非常不同。这位南方美女并不参与获取牛奶的实际步骤，她只关注自己需要什么，并让她的仆人去获取。与此同时，马童专注于*如何*得到牛奶，并经历了所有的步骤。

本质上，这就是声明式编程和命令式编程的区别。声明式编程根据需要来解决问题，避免了直接的 DOM 或变量操作。这非常适合纯函数，因为它们会给你新的数据和对象以避免状态突变。与此同时，命令式编程改变 DOM 并操纵状态，但是以一种更集中的方式，如果做得正确，可以提供更好的控制。

为了更好地提醒你这一切，并提供一些代码示例，我建议你参考这篇推文！

液体错误:内部

当你不写 JavaScript 来操作 DOM 时，我通过声明新变量而不是改变现有变量来接近声明式编程**。**

例如，假设您必须编写一个函数，将数组中的所有数字加倍。命令式方法将直接操作给定的数组，并重新定义每一项。

```
const doubleArray = array => {
  for (i = 0; i < array.length; i++) {
    array[i] += array[i];
  }

  return array;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是相当于马童拿着数组，把数组中的每一项都加倍，然后给你一个数组的变异版本。声明版本看起来很不一样。

```
const doubleArray = array => array.map(item => item * 2); 
```

Enter fullscreen mode Exit fullscreen mode

这个声明性版本将这项工作交给了另一个函数，在本例中是`map`，它已经有了内置的逻辑来遍历每一项(我们稍后会谈到)。这将返回一个独立于原始数组的数组，并且第一个数组没有变异，这是一个纯函数！因此，这个功能更简单、更干净、使用更安全，也更符合 FJS 教。

南方美女只是简单地*声明*她想要一个值加倍的数组，她的仆人(`map`)返回一个不同的数组来满足她的请求。

## 使用合适的 FJS 工具

好了，隐喻够多了。让我们深入了解写作 FJS 的基本方法。首先，让我们介绍一些你在编写纯粹的命令式函数时最常用的工具。

### 箭头功能

ES6 增加了箭头函数，它们的主要好处是更短、更好的函数语法。FJS 意味着要写很多函数，所以我们不妨让它变得简单一些。

在 arrow 函数之前，一个基本的“给一个数加五”函数看起来像这样。

```
const addFive = function(number) {
  return number + 5;
} 
```

Enter fullscreen mode Exit fullscreen mode

像这样的简单函数可以不用关键字`function`或显式返回来编写。

```
const addFive = number => number + 5; 
```

Enter fullscreen mode Exit fullscreen mode

变量首先标识参数，在本例中是`number`。你也可以使用圆括号来表示没有参数，比如用`()`，或者表示多个参数，比如用`(number1, number2)`。

之后是箭头，显示为`=>`。后面的任何表达式都会自动返回，在本例中，就是`number`加 5。

更复杂的函数可以用大括号来表示多余的行，但是你会丢失隐含的`return`并且需要把它写出来。没有那么好，但仍然比第一种语法好。

```
const addFive = number => {
  // more code here
  return number + 5;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 数组原型方法

每个阵列都内置了几个强大的工具，可以满足您的大部分(如果不是全部)FJS 需求。调用它们会返回新的、修改过的数组，您可以轻松地将它们赋给新的变量。从陈述性隐喻来看，它们类似于南方美女的仆人——它们已经在那里了，为你工作，并根据你开始的东西给你新的对象。

先说一个最基本的方法，`map`。它接受数组中的每一项，通过一个函数运行它以获得一个新值，并用这个新值替换旧值。一旦对每一项都这样做了，它就返回一个新更新的数组。

这是之前的声明性代码示例的一个调整示例，但是使用了`map`来使数组值加倍。

```
[2, 4, 6].map(item => item * 2);
// [4, 8, 12] 
```

Enter fullscreen mode Exit fullscreen mode

你基本上是使用`map`来取出每个数组对象作为`item`，并说“用`item * 2`替换这个`item`”

您还可以单独编写 doubling 函数，使代码更加实用。另外，你可以将`map`返回的值赋给一个完全不同的变量。

```
const double       = (item) => item * 2,
      array        = [2, 4, 6],
      doubledArray = array.map(double);

console.log(array);        // [2, 4, 6]
console.log(doubledArray); // [4, 8, 12]
// The original array hasn't been mutated! 
```

Enter fullscreen mode Exit fullscreen mode

有许多很好的方法可以学习，涵盖它们是另一篇文章。[查看我的研究报告，快速了解不同的数组原型方法](https://maxx1128.gitbook.io/study-notebook/javascript/arraymethods)，或者在谷歌上搜索它们！

#### 奖励:链式数组原型方法

你应该知道的另一个有趣的事实是:数组方法可以链接在一起！这使您可以快速组合不同的数组变化，而不会违反 FJS 规则。

假设我们想要将每个数组值加倍，然后过滤掉小于 5 的值(`filter`是稍后要学习的另一个有用的方法)。我们只需要编写一个额外的函数，并向数组中添加另一个方法。

```
const double         = (item) => item * 2,
      higherThanFive = (item) => item > 5,
      array          = [2, 4, 6],
      doubledArray   = array.map(double).filter(higherThanFive);

console.log(array);        // [2, 4, 6]
console.log(doubledArray); // [8, 12] 
```

Enter fullscreen mode Exit fullscreen mode

最后，许多人(像我一样)在链接时经常使用不同的间距来保持可读性。下面的变量和上面的一样，但是看起来更容易理解。

```
doubledArray   = array
                  .map(double)
                  .filter(higherThanFive); 
```

Enter fullscreen mode Exit fullscreen mode

### 记减

`reduce`是我想强调的一个原型方法，因为它可以说是最强大的。就其本身而言，它可以重新创建几乎任何其他原型方法，并且可以创建更复杂和更强大的方法。Reduce 也是另一篇博文，所以[我再一次向你推荐我的学习笔记本(或谷歌)来快速了解更多关于`reduce`](https://maxx1128.gitbook.io/study-notebook/javascript/reducefunction) 的信息。只要记住这些重要的事实:

1.  Reduce 对于复杂的数据合并或操作非常有用，只要您确保另一个原型方法没有做您需要的事情。
2.  关于其他原型方法的一切，比如函数的链接和传入，都适用于 reduce。

是你最强大的 FJS 工具之一，所以好好学吧。

## 高阶函数

既然我们有了这些编写函数的新方法，接下来就是管理它们的新方法。对 FJS 来说，最好的方法之一是利用高阶函数。到目前为止，已经有几个代码示例使用了 Hof，但是对它有一个更清晰的定义有助于充分利用它。

Hof 是以其他函数为自变量的函数。记住，函数是 JavaScript 王国的一等公民，所以它们可以是:

*   保存到变量
*   传递给其他函数
*   从其他函数返回

我希望在我的职业生涯中早点学会利用霍夫。它帮助我编写了更多的抽象逻辑的函数，使程序更具可读性和可维护性。因此，进入“函数传递函数”的思维模式对 FJS 来说很好，但总体上对 JavaScript 来说也更好。

假设我需要测试许多数字是否能被一组其他数字整除。一个人的第一反应可能是把每个函数写成这样。

```
const divisibleby3 = (n) => n % 3 === 0,
      divisibleby5 = (n) => n % 5 === 0,
      divisibleby7 = (n) => n % 7 === 0;

divisibleBy3(6);  // true
divisibleBy5(14); // false
divisibleBy7(28); // false 
```

Enter fullscreen mode Exit fullscreen mode

这很有效，但是你必须一遍又一遍地重复同样的表达。使用 HOFs 的解决方案看起来像这样，并且得到相同的结果。

```
const divideBy = (x) => (y) => y % x === 0;

const divisibleBy3 = divideBy(3),
      divisibleBy5 = divideBy(5),
      divisibleBy7 = divideBy(7);

divisibleBy3(6);  // true
divisibleBy5(14); // false
divisibleBy7(28); // true 
```

Enter fullscreen mode Exit fullscreen mode

这个很复杂，我们来分解一下。

1.  `divideBy`函数接受一个参数`x`，并保存它，同时*返回另一个函数*。所以当我们调用`divideBy(3)`时，我们保存`x`作为每次返回的函数的一部分。
2.  我们可以*将这个函数保存到一个变量*，比如`divisibleBy3`。这是有意义的，因为我们已经让`3`成为每次返回的函数的一部分。
3.  由于`divideBy`返回一个函数，我们现在可以像普通函数一样调用`divisibleBy3`。它使用调用时得到的`y`变量和之前得到的`x`变量。

所有这些都是“currying”函数或**函数的例子，这些函数返回其他函数，直到它们最终给出像`divisibleBy3`一样的最终函数**。正如你所看到的，JavaScript 中的 currying 有很多 Hof 在使用的例子。如果你的函数有一些，但不是全部，它们的逻辑是相同的，那么 Currying 是很棒的。您可以用它们共有的逻辑(比较运算符)创建一个模板，并传入每个模板特定的逻辑(运算中使用的数字)。

如果你不想将一个 curried 函数的第一部分保存到一个变量中，你可以使用同时调用多个参数的快捷方式。

```
const divideBy = (x) => (y) => y % x === 0;

divideBy(3)(6);  // true
divideBy(5)(14); // false
divideBy(7)(28); // true 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，无论你在程序中使用一次还是多次，curried 函数都会对你的代码有所帮助！

curry 函数也很难理解，所以[我有另外一个注意，如果你需要的话，最好分解 curry 函数](https://maxx1128.gitbook.io/study-notebook/javascript/currying#what-is-currying)。

这是 HOFs 的另一个例子，它将一个函数作为`fn`参数。接受它的函数像引用其他变量一样引用它。

```
const performMultipleTimes = (times, x, fn) => {
  for (let i = 0; i < times; i++) {
    fn(x);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数有三个参数:

1.  重复循环的次数
2.  传递给`fn`函数的参数
3.  `fn`功能

函数在循环内部被调用，所以`performMultipleTimes`让我们多次调用一个函数，而只写一次。我们需要做的就是*将一个函数传递给另一个函数*，这可以通过*将函数存储在变量中来实现。*霍夫又开始工作了！

```
const logString = s => console.log(s);

performMultipleTimes(3, 'Greetings!', logString);
// Greetings!
// Greetings!
// Greetings! 
```

Enter fullscreen mode Exit fullscreen mode

如果你在别的地方不使用这个函数，又不想存起来以后用，也可以直接通过。使用或不使用括号语法都可以做到这一点。

```
performMultipleTimes(3, 'Greetings!', (s) => console.log(s));

performMultipleTimes(3, 'Greetings!', (s) => {
  const newString = `I am here to say '${s}'`;
  console.log(newString);
}); 
```

Enter fullscreen mode Exit fullscreen mode

理解霍夫是伟大的，但尤其是对 FJS。这种风格关注功能的强大，有效地传递功能可以增加功能的强大和模块化。

然而，这对我来说很难理解，对你来说可能也一样。因此，如果你(可以理解的)仍然有困难，这一章来自雄辩的 JavaScript 做了很好的工作来进一步分解 HOFs。

## FJS 是一种风格，不是绝对的

关于 FJS 的最后一点:它是一种存在于一个谱系中的 JavaScript 编写风格。这不是简单的“这是或不是 FJS。”你可以用 FJS 元素(比如纯函数或者声明性的)编写代码，而不需要遵循规则。每一个都是偏好，当它们放在一起时，使您的 JavaScript 更接近函数范式。

FJS 可能很难理解，你可以从我额外阅读参考的链接数量中看出这一点。但是理解这四个主题将有助于你为学习更多内容打下坚实的基础。这对我来说是正确的，因为他们让我阅读的关于 FJS 的每一篇文章都变得有条不紊。希望这篇文章能对其他想学习和使用它的人起到同样的作用。

如果你真的想更深入地了解 FJS，我推荐凯尔·辛普森的书《轻便型 JS》它深入介绍了函数式编程和 JavaScript，你可以在 Github 上免费阅读！

*封面图片由[SafeBooru.org](https://safebooru.org/index.php?page=post&s=view&id=2196900)提供。*