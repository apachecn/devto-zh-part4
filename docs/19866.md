# 用 Javascript 构建表达性单子:简介

> 原文：<https://dev.to/rgeraldporter/building-expressive-monads-in-javascript-introduction-23b>

monad 是一种强大的设计模式，如果使用得当，可以完全改变您对 Javascript (JS)中处理值的看法。本入门教程适用于任何 JS 熟悉程度的人，甚至(也许，特别是)初学者。

对于那些已经熟悉单子的人来说，这篇介绍只涵盖了有效使用单子的基本要素，并且在必要时只涉及到单子的起源和更多的技术语言。不会试图解释范畴理论或函数式编程的深层概念。

## “单子”是什么意思？

出于这个介绍的目的，我想参考一下[字典中的定义](https://www.merriam-webster.com/dictionary/monad)，这个定义早于它在数学和编程中的使用:*一个单独的单元*。

这个定义类似于*二元组*和*三元组*——分别意味着两个或三个单元。

术语“单子”在数学和范畴理论中有不同的用法。对于编程，monad 因 Haskell 而变得流行，并被移植到各种语言中，包括 JS。它被用作包含值和控制突变的一种方式。

虽然我认为记住*“单个单元”*的定义是很好的。至少这对我有帮助。

## 单子解决什么问题？

任何时候，当你不得不试图跟踪值的变化时，任何兼容类型的单子都会有助于争论值是如何变化的。

如果你一直在努力解决`null`和`undefined`给无法处理它们的函数带来的混乱，那么`Maybe`单子可以解决这个问题。

对我来说，它有助于将改变价值的过程分解成小步骤，让我一次只考虑一部分，而不用担心价值会以意想不到的方式突变。人们可以更容易更好地关注个人功能。结果更容易预测，过程中的步骤更容易测试。

单子甚至可以处理异步的进程，但是出于介绍的目的，我们将只关注同步的情况。

## 在 Javascript 中是如何使用的？

单子最好被认为是一个值的容器:就像类似容器的类型`Array`和`Object`可以保存一组值一样，单子也做同样的事情。

你构建的每个单子就像构建一种新的类似容器的类型。正如`Array`有像`forEach`这样的方法，正如`Object`有像`keys`这样的方法，一个单子将有标准的方法，你可以根据具体情况添加方法。

如果你使用过`Array`和`Object`，你已经获得了一些对单子有用的经验。

## 最基本的单子:`Identity`

我们将从最基本的单子开始我们的第一个例子，一个`Identity`单子。

### 首先，简要说明一下单子的命名和样式约定...

在我们开始构建一个`Identity`单子之前，我想弄清楚你将在本简介中看到的命名和样式。当我第一次学习单子的时候，我很快就被名字和风格迷住了。这足以让我慢下来。

你会看到用大写字母命名的单子，通常是相当抽象的名字。

不要太在意命名；如果一个抽象的名字让你感到困惑，记得据说*“给事物命名是计算机科学中最难的事情之一”*。通常这些名称指向一个特定的已建立的设计模式，该模式可能有多个听起来很别扭的名称。

单子名将被大写——这是一个约定，我的假设是这是为了证明它们是一个特殊的类型，很像一个`class`名。

monad 方法名称也是如此，最常见的方法有许多已确定的名称。当一个被介绍时，我会提到你可能发现的用于相同方法的其他名字。我将致力于关注我认为对 monads 新手来说最有表现力的方法名称，尽管观点可能会有所不同。

### 第二，对“同一性”这个术语的一个快速注释...

还有一个简单的旁注:单子名称`Identity`是基于一个术语“`identity`”，用于一个简单返回给它的值的函数。`Identity`单子将有效地做同样的事情。这似乎是一个几乎无用的功能(它什么也不做！)但是对于给出最基本的例子来说是很棒的，函数式编程中也有一些实际的用例。

例如，如果您需要传递一个函数作为参数来潜在地改变一个值，但是希望确保该函数在某些情况下实际上没有改变该值，那么标识是一个很好的方法。

### `Identity`:代码

```
const Identity = x => ({
    emit: () => x,
    chain: f => f(x),
    map: f => Identity(f(x))
});

// example use:
const one = Identity(1); 
```

是的，就这些。写类似上面的东西，你就写了一个单子。这是三种必需的方法。

许多教程会给你一个库，只是展示如何使用单子，但我觉得实际操作的方法会使理解这个概念变得更容易。

综上所述，这个`Identity`单子，5 行，有很多事情要做。我们来分析一下。

#### `const Identity = x => ({ ... });`

最简单的部分:我们将使用`const`,因为我们不希望我们的定义发生变化。你可能知道或听说过`const`在锁定突变方面并不完美:如果你使用`const`来定义`Array`或`Object`，那么它们可能会发生后续突变。

令人欣慰的是，我们给我们的`const`分配了一个*函数表达式*，我喜欢称之为*常量函数表达式* (CFE)。比起标准的`function`定义，我更喜欢这些，因为它们防止任何人干预函数原型。

如果你经常在 JS 中查找单子库，你会发现它们是基于`function`或`class`的，这使得它们容易受到干扰。

我们要传递给`Identity`单子的值是`x`，CFE 的美妙之处在于传递给它的参数永远不会被修改或改变:它绝对是不可变的，不需要使用任何特殊的 API。

这就是为什么我喜欢单子的这种模式:在没有高级语法的几行代码中，它创建了一个绝对不可修改的值！

一旦我们将`1`作为一个值传入，没有什么可以改变`1`是传入的值。如果我们使用了一个类并将值存储在一个访问器中，不需要一些特殊的 API 用法，我们就可以像`myIdentity.__value = 2`一样改变值。

虽然我没有测试这个假设，但我认为这是内存占用最少的 JS 单子模式。

让我们开始看看核心方法。

#### 方法:`emit`

**别称:**`join``value``valueOf`

##### 代码

```
emit: () => x, 
```

##### 举例使用

```
console.log(one.emit());
// > 1 
```

这是最简单的方法，只返回包含在。最常见的名字是`join`，但是我发现在学习 Javascript 的时候，这个名字并不令人印象深刻。我喜欢用`emit`来解释它作为动词的作用:发出包含在其中的值。

不过，有一个快速的警告，`emit`除了调试之外，你没有必要依赖任何东西。事实上，在初级示例中，你根本不会看到我使用它。

#### 方法:`chain`

**别称:** `flatMap`，`bind`

##### 代码

```
chain: f => f(x), 
```

##### 举例使用

```
console.log(one.chain(a => a + 1));
// > 2 
```

下一个最简单的方法是`chain`，它旨在*将各种*链接在一起，但可以像上面演示的那样操作。

`f => f(x)`表示函数`f`被采用，并且值`x`被传递给所述函数。在这个例子中，`a => a + 1`取值，返回加 1 的值。

更典型的用法可能是:

```
one.chain(a => SomeMonad(a + 1)); 
```

其中`SomeMonad`是单子。在这个`chain`中，我们将`Identity(1)`转化为`SomeMonad(2)`。当你使用`chain`时，通常你会指出你传入的函数本身会返回一个单子(防止递归单子内单子单子内单子...)或者您希望结果是非一元的。

现在不要太担心*为什么*，因为我发现与下一个方法`map`相比，这个方法不太有用。但是在我们看`map`之前，先了解一下是很重要的。

#### 方法:`map`

**别称:** `fmap`(“功能图”)

##### 代码

```
map: f => Identity(f(x)) 
```

##### 举例使用

```
console.log(one.map(a => a + 1));
// > [not pretty: outputs monad defintion... at least until we implement .inspect() below] 
```

`map`是最重要的方法。这就是单子如此有用的原因:我们可以获取一个已建立的单子`Identity(1)`并通过一个函数生成`Identity(2)`，而不需要对我们的示例常量`one`做任何改变。

简而言之，它是一个`chain`函数，内置了将结果值重新打包成一个新的`Identity`的功能，这个新的`Identity`函数本身可以根据您想要应用于它的函数的数量，不断地使用`map`、`chain`和`emit`。

这是我在单子上最常用的方法。

我有时喜欢把它想象成银行账户分类账。必须考虑所有的值:它们从哪里开始(`.of`)，以及它们如何随时间变化(`map` & `chain`方法)。一个单子的初始值就像一个有初始存款的新银行账户，每个`map`或`chain`都是上面的一个交易。没有什么会改变初始存款的价值，但是我们有办法计算出今天账户里还剩下多少。

#### 一更法:`inspect`

您可能已经注意到，在绘制地图后在控制台输出值看起来并不美观。虽然没有严格要求 monad 正确工作，`inspect`可以通过控制台帮助我们了解 monad 中的确切内容，以及它是什么类型的 monad。

```
const Identity = (x) => ({
    chain: f => f(x),
    emit: () => x,
    map: f => Identity(f(x)),
    inspect: () => `Identity(${x})`
});

const one = Identity(1);
const two = one.map(a => a + 1);

console.log(two.inspect());
// > Identity(2) 
```

这个方法在调试中很重要，因为简单的`emit`不会给你类型`Identity`；仅仅是内含价值`2`。这在处理多个单子类型时非常重要。

#### 最后，添加构造函数

在上面所有的例子中，我一直在直接调用`Identity`。但是，通常有一个构造函数方法。在 JS 中，约定是添加一个`of`构造函数。这看起来像:

```
const one = Identity.of(1); 
```

这在几个方面有帮助。第一，`of()`是一个非常强烈的暗示，我们在和单子打交道，因为你可能在别的地方看不到它。

其次，如果你的单子对传递给它的内容有限制，它将允许你做类型检查行为。

通常我使用导入/导出来处理这个问题，如下所示:

```
const Identity = x => ({
    emit: () => x,
    chain: f => f(x),
    map: f => IdentityOf(f(x)),
    inspect: () => `Identity(${x})`
});

// you might do type-checking here
const IdentityOf = x => Identity(x);

const exportIdentity = {
    of: IdentityOf
}

// or module.exports
export {
    exportIdentity as Identity
} 
```

```
// or require()
import { Identity } from './Identity.js`; 
```

不过，我举的例子已经够多了。你该试一试了。

*试一试:[同一性单子例子 REPL](https://repl.it/@rgeraldporter/Identity-Monad-Example)T3】*

## 我们再做一个单子:`List`

`List`是类似`Array`的单子的典型名称。

我们将从我们的`Identity`开始，但将其重命名。

```
const List = x => ({
    emit: () => x,
    chain: f => f(x),
    map: f => List.of(f(x)),
    inspect: () => `List(${x})`
}); 
```

*为了这个例子的目的，我们假设`of`构造函数已经被添加到这个例子中。当实际创建一个时，我们还要在`of`构造函数中进行类型检查，以确保传递的值是一个`Array`。*

#### 添加更多方法

正如您所看到的，随着`inspect`的加入，添加新方法变得非常容易。如果你写你自己的单子，如果你有一个经常和`map`或`chain`一起使用的特殊函数，为什么不添加方法呢？

根据我的经验，有两种方法你可以添加:

1.  like:返回相同类型单子的方法
2.  like:返回不同种类的单子或非单子值的方法；它可能会也可能不会“退出”单子模式，我喜欢称之为“解开”单子值

#### 方法:`concat`

串联是来自`Array`的一个相当简单的概念:取一个数组，并将其添加到另一个数组的末尾。这似乎是一个非常有用的方法。

```
concat: a => List.of(x.concat(a)),

// e.g.

const myNumbers = List.of([1, 3, 4, 7, 10]);

myNumbers.concat([12]).inspect();
// > List(1,3,4,7,10,12); 
```

这个函数很简单:通过对包含的值和传入的值使用`Array.concat`来创建一个新的`List`。

注意这是`map`式的；它返回一个新的`List`。

#### 方法:`head`

假设我们只想知道`List`中的第一项是什么。它不是一个`Array`，所以使用像`[0]`这样的索引访问器是行不通的。

```
head: () => x[0],

// e.g.

const myNumbers = List.of([1, 3, 4, 7, 10]);

myNumbers.head()
// > 1 
```

这个方法类似于`chain`，因为它返回一个非一元的值——在这个例子中，展开值的一部分。这一个退出了单子模式，所以在使用这些方法时要注意，继续链接`map`、`emit`、`inspect`等将不起作用。

```
const myNumbers = List.of([1, 3, 4, 7, 10]);

myNumbers.head().inspect();
// > ERROR! We unwrapped from the monad at `.head()`! 
```

#### 更多方法

如果你很了解`Array`，你会知道它有很多方法。你可以用各种东西造一个`List`。

所以这里有一个很好的练习——用这个基本的`List`单子，写一些你自己的方法！

*试出来:[列举单子例子 REPL](https://repl.it/@rgeraldporter/List-Monad-Example)T3】*

## `Maybe`:最强大的单子

您可能听说过`Maybe`(也称为`Option`):这个奇怪的名字，但是非常有用和强大的单子模式。

“也许”这个名字指的是“也许有一个价值”的想法...但也许没有”。

在 JS 中，值为`undefined`和`null`可能会在错误的地方引起混乱。如果在我们当前不得不放置一个笨拙的`if (x === undefined || x === null)`语句的每一种情况下，我们可以在值的容器内处理这些情况，并且永远不暴露那些难看和麻烦的空值，那会怎么样？

### 代码

这里有很多代码。别担心，我们会处理好的。

```
const Just = (x) => ({
  chain: f => f(x),
  emit: () => x,
  map: f => MaybeOf(f(x)),
  fork: (_, g) => g(x),
  isJust: true,
  isNothing: false,
  inspect: () => `Just(${x})`,
});

const Nothing = (x) => ({
  chain: _ => Nothing(),
  emit: () => Nothing(),
  map: _ => Nothing(),
  fork: (f, _) => f(),
  isJust: false,
  isNothing: true,
  inspect: () => `Nothing`,
});

const MaybeOf = x => x === null || x === undefined || x.isNothing ? Nothing() : Just(x);

const exportMaybe = {
  of: MaybeOf
};

export { 
    exportMaybe as Maybe
} 
```

### 用例

为了举例说明这一点的用处，让我们用一个系统来读取华氏温度，然后用摄氏温度表示出来。

```
const fahrenheitToCelsius = a => (a - 32) * 0.5556;

const reading1 = 15;
const reading2 = null;

const temp1C = Maybe.of(reading1)
                    .map(fahrenheitToCelsius);

console.log(temp1C.inspect());
// > Just(-9.4444)

const temp2C = Maybe.of(reading2)
                    .map(fahrenheitToCelsius);

console.log(temp2C.inspect());
// > Nothing() 
```

现在我们有一个问题:为了让函数`fahrenheitToCelsius`工作，我们需要`a`是一个数字。既然`reading2`是`null`(也许是死温度计？)，Javascript 会将`null`强制转换为`0`，给出一个`-17.7792`的常量错误读数。

然而，由于我们已经封装在一个`Maybe`单子中，我们只有两种可能性:一个实数(`Just`，就像“只是一个值”)，和根本没有值(`Nothing`)。

### 解释

这是怎么发生的？

我们的`Maybe.of`构造函数做到了:

```
const MaybeOf = x =>
    x === null ||
    x === undefined ||
    x.isNothing ? Nothing() : Just(x); 
```

如果 monad 中封装的值不是`undefined`、`null`，或者已经是`Nothing`，那么它被保存在`Just`中。虽然`Just`这个名字对你来说可能很陌生，但它的概念几乎和`Identity`一模一样！所以你已经非常了解`Just`是如何工作的了。

`Nothing`是一个与大多数单子相当不同的单子:它不取值，你使用的每一个方法都会产生`Nothing()`。在一个`Maybe`向`Nothing`赋值之后，就没有回头路了——所有对`map`或`chain`的尝试都会导致`Nothing`，所以你不必担心函数会有意外的行为，因为它们*从来不会真正运行*。

甚至这里的`emit`返回`Nothing()`，而不是`null`或者`undefined`。这是因为我之前稍微撒了个谎，我们的`emit`方法有时候实际上不能发出一个值，尤其是如果它有`Nothing()`！

但是我们最终需要处理我们对`Nothing`做的事情...

### 方法:`fork`

这里输入上面给出的`Maybe`单子的中奖方法:`fork`。

*一个小提示:不是所有的`Maybe` monad 实现都有一个`fork`，但是以其他方式处理`Nothing`。然而，对于本教程，我们将使用它，因为我们可以！*

`fork`是这里两个地方的一个方法:在`Just`和`Nothing`

```
// Just
fork: (_, g) => g(x),

// Nothing
fork: (f, _) => f(x), 
```

马上你可能会看到一些奇怪的东西。`_`是函数式编程中经常使用的一种风格选择，用来表示我们知道哪里会有值被传递，但是我们计划不使用它。它就像占位符的反义词。

现在让我们用它来显示温度:

```
// assume a `display` function to display the temperature reading, and act like console.log

const fahrenheitToCelsius = a => (a - 32) * 0.5556;

const reading1 = 15;
const reading2 = null;

Maybe.of(reading1)
    .map(fahrenheitToCelsius)
    .fork(
        _ => display('ERR!'),
        t => display(`${t}°C`) // will read `-9.4452°C`
    );

Maybe.of(reading2)
    .map(fahrenheitToCelsius)
    .fork(
        _ => display('ERR!'), // will read `ERR!`
        t => display(`${t}°C`)
    ); 
```

注意，在这个用例中，我们甚至没有将`Maybe`的结果赋值给`const`值，因为在这个例子中我们只需要显示它。

但是如果我们确实需要这个字符串值来做别的事情...

```
const display = a => {
    console.log(a);
    return a;
};

const fahrenheitToCelsius = a => (a - 32) * 0.5556;

const reading1 = 15;

const temp3C = Maybe.of(reading1)
    .map(fahrenheitToCelsius)
    .fork(
        _ => display('ERR!'),
        t => display(`${t}°C`)
    );

console.log(temp3C)
// > "-9.4452°C" 
```

这应该足以让你开始使用`Maybe`。这是一种非常不同的思考价值观的方式，与 JS 中通常教授的方式不同，可能需要一些时间才能完全掌握。

实践对理解单子的用法很有帮助！在你的下一个小项目中，尝试添加一个`Maybe`模块(建议如下)，或者写你自己的。如果有足够的时间，您可能无法想象在没有它的情况下用 JS 编写代码！

现在，你可以试试下面的 REPL。

*试试看:[也许单子例子 REPL](https://repl.it/@rgeraldporter/Maybe-Monad-Example)T3】*

## 关于`emit`的一个说明

你可能已经注意到这里没有任何使用`emit`(又名`join`)的例子。这是因为“解开”单子是你实际上应该尽可能完全避免的事情，除非是在移除多层容器单子的时候。

这对于调试来说很棒，但是当你`emit`你需要意识到你正在离开单子的安全世界，再次进入副作用和可变性。

有许多单子库甚至没有这个方法，因为这在技术上是不必要的——毕竟，如果给它一个简单的标识函数，`chain`也能达到同样的效果。

如果可能的话，你能做的最好的事情，而不是使用一个 monad 来计算一个返回值，是构建 monad 并传递它需要做的所有函数。

不过，如果你现在不能想出如何在每种情况下做到这一点，也不要担心。刚刚开始使用单子可能是一个令人兴奋的学习经历。

## 快速回顾

这些年来对我帮助最大的是把单子想象成一个容器。那可能对你有帮助，或者回到*的字典定义单个单位*也可能有帮助。

就像银行分类账一样，单子保持它们的值不变，但是允许方法在它们上面应用函数来生成新单子，从而生成新值。

但是请注意，在网上搜索找到你可能遇到的单子问题的解决方案可能有点困难。很多文档中充斥着你可能不熟悉的技术语言。很多东西连我都不熟悉。希望随着这种强大的模式被更广泛地采用，这种情况会有所改变。

## 现在就可以使用的单子模块

我自己的模块，与本简介中演示的模块没有太大的不同，可以作为 [simple 获得——可能在 npm](https://www.npmjs.com/package/simple-maybe) 上。

## 接下来呢？

一旦你完全掌握了本简介中概述的概念，其他单子多半只是这里显示的单子类型的微小变化。

在不久的将来，我将发布一些我正在构建的其他 monad 模块，以及它们是如何使用和构建的。

我希望这篇文章的方法是容易理解的，即使对于那些 Javascript 新手也是如此，并且代码足够有表现力，不会成为学习的障碍。请随意留下改进的建议，或者分享一些帮助你更好地理解单子用法的其他方法。