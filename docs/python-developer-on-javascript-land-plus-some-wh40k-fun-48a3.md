# Javascript 土地上的 Python 开发者，加上一些 WH40k 的乐趣

> 原文：<https://dev.to/pencillr/python-developer-on-javascript-land-plus-some-wh40k-fun-48a3>

> 首先也是最重要的:如果你已经有了 [*战锤 40k*](https://en.wikipedia.org/wiki/Warhammer_40,000) 参考资料，请按独角兽，并留下评论。只是想看看在我们中间有多少皇帝的忠实仆人，:D

## 没有 javascript 就没有前端

大约一年前，在我的开发者生涯中，我意识到如果我想创建交互式网络应用程序，我真的不能避免学习 javascript。

这一发现给了我沉重的打击，就像一艘撞向月球的巡洋舰。

[![ships ramming in PC game Gothic Armada](img/b1cff1f1a94a2a549194665afa378f92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gA5tNEkN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lblsbpsdlsfsq6erery7.gif) 
好吧，也许没那么难...

我必须承认，我曾经相信 javascript 是一个古老的、[笨重的](https://charlieharvey.org.uk/page/javascript_the_weird_parts)，甚至是[危险的](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/)工具。如果这个话题在工作中出现，我的同事*(他们大多是后端和开发人员)*都在谈论这种语言在很多方面是多么有问题和愚蠢。

我当时不知道的是，他们在 JS 之前有过经验，ES6 之前有过经验，旧的 javascript 也是如此。伙计，旧的 javascript 有它的[粗糙的边缘](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)。很明显为什么我的同事会有怨恨。

因此，当我意识到我需要 JS 时，我开始寻找一种快捷方式*。我真的想学习一些奇特的框架，而不是用普通的 javascript。于是[来到**反应过来**](https://reactjs.org/) 。我天真地认为 React 将把我从 javascript 中拯救出来。*

 ****它没有。*你知道吗？这仍然是一个不错的选择！**

## 为什么要学深水游泳

我知道我可能应该说*“不，你必须从普通 javascript 开始】*，但是对我来说，从 React 开始导致了一个**加速的学习过程**！作为一名 DevOps 人员，我最初更多的是一名 python/bash 开发人员，我从未接触过*的 webdev* 世界。React 促使我学习了关于 javascript 工具链的所有知识，以及更多。我需要学习 nodeJS，graphQL，Sass，许多好的工具和新的库。它真的为我工作。现在一年过去了，我刚刚完成了我的第一个正式自由职业前端项目！

所以我决定发表一篇关于 python 开发者的 javascript 的文章！
也许我可以为那些碰巧发现自己处于我的位置的人节省一些挣扎，*挣扎为什么那个空阵不是假的*。或者类似的东西。
好吧，我们走吧！

[![primarch ship](img/47150c22ffac01373fc5c6eb9e743e35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IA4xz2_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvzb62e4cdunps7947k3.gif)

## 进入经线

我想从一个小小的**列表开始，当你从 python 开始编写 javascript 代码时，你可能会遇到一些奇怪的事情**。这些是你必须直面的事情。

#### 虚空:

Javascript 中表达虚无有两种方式:`undefined`和`null`。Python 有一个:`None`。
`Undefined`代表不存在或尚未赋值的变量或属性，而`null`代表故意没有任何值——比如说，当你明确地将某个值设置为 null 时。

#### 式狼狈:

Python 有一个很棒的类型系统。你有列表、集合、元组、字典，甚至没有提到来自 *pathlib* (路径类型)或*集合*库的`Path`这样的东西。Javascript 并不是万能的。- Python 的`list`和 JavaScript 的`Array`非常相似，但相似之处可能仅限于此。

*   由于 JavaScript 没有可变/不可变的区别，所以没有对应 Python 的`tuple`的 JavaScript 类型。
*   此外，javaScript 没有内置的哈希表支持。用花括号构建对象的 JavaScript 代码看起来非常类似于 Python 字典，但幕后发生的事情完全不同。
*   在 javascript 中，混合类型可能很危险。你永远不知道后台会发生什么隐式转换。

#### *为什么！？*

分号。分号呢？我应该使用它们吗？没有吗？酷毙了。但是等等。还有一些场景我应该什么时候？WTF？

> 好了，冷静点。实际上，javascript 并不严格要求分号。当有需要分号的地方时，一个称为自动分号插入的过程会在后台添加分号，同时对源代码进行解析。问题是它有时会误解应该做什么。

#### 插值

在 python 中，有大量的方法来进行插值、参数扩展和字符串连接。在 javascript 中，你可以使用[模板字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)，就像在`bash`中一样，但是有反勾号，我认为这很棒！

#### 布尔:

JavaScript 有`true`和`false`。Python 有`True`和`False`。

#### 礼

在 python 中，我们喜欢使用 **`snake_case`** ，而在 javascript 中，我们喜欢使用 **`camelCase`** 。

#### 变量作用域:

Python 变量具有全局和函数作用域，这取决于它们被声明的位置，并且这可以通过`global`和`nonlocal`关键字进一步复杂化。另一方面，Javascript 变量可以通过使用关键字`let`、`const`、`var`来声明。这里 let 和 const 有一个块作用域，但是 var 有函数作用域。例如，在任何函数之外定义 let 与 var 相反——不会创建全局变量。

#### *异端*:

在 python 中，使用`global`是异端。在 javascript 中使用`var`是异端。

#### 列表操作:

让我们看看一些基本的列表操作有什么不同。

| 计算机编程语言 | 射流研究… |
| --- | --- |
| `len(l)` | `l.length` |
| `l.append(item)` | `l.push(item)` |
| `l.pop()` | `l.pop()` |
| `l.pop(0)` | `l.shift()` |
| `l[start:end]` | `l.slice(start, end)` |
| `[start:end] = […]` | `l.splice(start, howMany, […])` |

## 循环往复

当我试图做一些基本的数据操作时，更有趣的问题出现了。我不能说有时这不是一场斗争。

[![spacehulk firefight](img/02e01b46e8cf8fb99fc150c9f6d262ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4RI7GP0W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/05glg8v6tq8esnd9ezxc.gif)

比如我第一次遇到这样的问题:

```
for weapon, damage in strike_map.items():
    print(
        "With battery {} dealt {} damage!".format(
            weapon, ", ".join(str(x) for x in damage)
        )
    )
# Where strike_map is:
# strike_map = {
#    "thermal lance": [6, 5, 4, 8],
#    "macrobattery": [4, 8, 9, 7]
# } 
# Prints:
# With battery thermal lance dealt 6, 5, 4, 8 damage!
# With battery macrobattery dealt 4, 8, 9, 7 damage! 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢这个例子，因为它包含了很多关于在 python 中如何流畅地管理循环的内容。让我们看看这里有什么:

*   我们有散列表`strike_map`*——在 python 中，叫做字典——*，收集星舰武器和它们造成的伤害。
*   然后我们使用`items()`方法，它返回一个给定字典的(键，值)元组对的列表。然后我们迭代它。
*   在循环中使用 string `format()`方法我们把武器和相应的伤害打印成一个字符串。
*   棘手的部分是列表理解，这是 python 和许多函数式编程语言中最好的特性之一。

```
str(x) for x in damage 
```

Enter fullscreen mode Exit fullscreen mode

这将返回损害列表中的数字列表，但是它也对每个元素执行`str()`函数来将它们字符串化，因此它们可以连接在一起成为一个单独的字符串，用逗号分隔。

**漂亮吧？**

现在让我们用 Javascript 来看看这个。我们没有散列表，所以我们的数据可以用一个对象来表示。

```
const strike_map =  {
    thermal_lance: [6, 5, 4, 8],
    macrobattery: [4, 8, 9, 7]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了对象方法，所以你可以用 [`Object.keys()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) ， [`Object.values()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values) 返回键或值。也许最接近的可能是 [`Object.entries()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) ，它返回给定对象自己的可枚举字符串键属性[键，值]对的数组。
所以这个:

```
[ [ 'thermal_lance', [ 6, 5, 4, 8 ] ],
  [ 'macrobattery', [ 4, 8, 9, 7 ] ] ] 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，我们可以有一个不那么整洁，但仍然有效的解决方案:

```
for (let [weapon, damage] of Object.entries(object1)) {
  console.log(`With battery ${weapon} dealt {damage} damage!`);
}
// prints:
// thermal_lance: 6,5,4,8
// macrobattery: 4,8,9,7 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以说我们已经完成了，但是这样我们会让 javascript 在后台进行*隐式类型转换*——这是我们不应该依赖的。现在，当我写这篇文章的时候，数组理解在 javascript 中已经被否决了，新的解决方案仍然在 T4 的实验阶段。我没有找到比使用一个内部 [`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 循环，或者数组 [`map()`](https://developer.mozilla.org/hu/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 方法更好的方法，使用 arrow 函数会更紧凑。

在这里，我*挑战*你们这些 JS 大师，向我展示你们如何以最干净的方式解决这个简单的嵌套迭代问题。

## 箭头功能

在 javascript 中，有一些常规函数，但现在并不推荐使用。

```
function name(parameter1, parameter2, parameter3) {
  // code to be executed
} 
```

Enter fullscreen mode Exit fullscreen mode

还有这些新的箭头函数。它们可以有很多种形式，所以一开始，我确实发现它们非常混乱，因为它们的语法很松散。

看几个案例:

```
// Assigning:
const exampleFunc = function() {
//...
}
// or:
const exampleFunc = () => {
//...
}
// or:
const exampleFunc = (param1, param2) => doStuff(param1, param2)
// without parentheses
const exampleFunc = param => doStuff(param)
// Implicit returning?!
const exampleFunc = () => 'test'
// add parentheses when returning an object? Ok.
const exampleFunc = () => ({ value: 'test' }) 
```

Enter fullscreen mode Exit fullscreen mode

箭头函数的另一个棘手之处是`this`范围是从执行上下文继承的。因此，箭头函数不适合作为对象方法。

所以这个不行:

```
const IronTide = {
    class: 'Tempest',
    size: 'frigate',
    wholeType: () => {
        return `${this.class} class ${this.size}`
    }
}

// THIS WILL RETURN "undefined class undefined" : 
```

Enter fullscreen mode Exit fullscreen mode

## 异步

如果你正在使用 javascript，你可能正在开发 web 应用程序，在 web 应用程序中，有时你需要运行你想在后台运行的查询和操作，而不阻止用户点击。所以显然你需要异步操作。

从 python 来说，这是第一件让我感到困难的事情。尽管 async 对我来说并不陌生，因为我实际上在 python 后端项目中有过使用 [`asyncio`](https://docs.python.org/3/library/asyncio.html) 的经验，但有时它仍然很奇怪，尤其是如果你的大脑习惯于同步世界的话。

如果你进入了 javascript 世界，你一定要仔细阅读这些东西。我保证这将是非常有益的。

## 套餐

我必须承认，包管理在 python 中可能更难。管理 [`venv`](https://docs.python.org/3/library/venv.html) ，而你的 requirements.txt 只是比用 javascript 做要繁琐一点。
在 JS 里你有很多选项，我个人用的是 [`yarn`](https://yarnpkg.com/en/) 。有了它，管理您的库并保持更新变得非常容易。

这就是全部了！我知道我们只是触及了表面，但我希望它能在这个无情的网络应用时代帮助你。我的朋友，在网络开发的深不可测的虚空中要小心。记住。*在黑暗中，跟随~~特拉~~干净的圣光码！*

[![spire interrogated](img/a6d00840734faf168dafbe1fb3a549ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G_Ec-kmT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/glr9qpeq6lpqz3cf4ox0.gif)*