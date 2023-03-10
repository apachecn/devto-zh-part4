# 第一原理的函子-用 JS 解释

> 原文：<https://dev.to/snird/functors-from-first-principle-37lh>

在这篇文章中，我将解释函子是什么，以及为什么它们以不同于我所学的方式有用。当我试图学习函子概念时，我通常会得到其中一种解释:

*   它是你可以映射的东西(但是映射到底是什么意思？)
*   就像你可以映射的列表一样(那么，映射只是遍历元素吗？)

那些对我来说不是很有用。我最终阅读了非常非常长的解释，并阅读了许多行真实世界的代码，以了解所有的问题和真正的含义。我会尽可能快地解释这些概念，以免耽误你的时间。

我将从一般概念和一些 JavaScript 演示开始，以使尽可能容易理解，但我将在这里结束对 Haskell 的介绍，因为有些事情只有 Haskell 才能做，这对您的理解很重要。

## 列表是函子——你可以映射到它们上面

让我们从一个轶事的例子开始，并从那里展开。许多语言中的列表都是函子，因为你可以映射它们。

```
const myList = [1,2,3,4,5]
const addTwo = (num) => num + 2
myList.map(addTwo) // [3,4,5,6,7] 
```

### 地图是将一个功能应用到一个结构上

所以我们在上面看到的，是一个列表的基本映射函数，我们将`addTwo`函数应用于列表中的每一项。
但是映射更通用一点的定义是:

```
Applying a function over *or* around some structure while keeping the structure intact. 
```

在列表中，结构就是列表本身。我们应用一个函数，`addTwo`，它不知道列表的存在，*对列表结构中的每一项，*。

## 明白了。结构映射。什么是结构？

当你只有`list`的例子时，首先理解结构的概念有点困难。
但这正是函子这个重要的一般概念发挥作用的地方。
把游戏中的“结构”想象成一个盒子，里面装着某种价值。映射时，将函数应用于该框内的潜在值，同时保持该框不变。

让我们停留在 javascript 领域，一个不完美，但仍然足够好的例子将是承诺。承诺是保存潜在价值的盒子，你可以在不改变承诺盒子的情况下对其应用一个函数。

```
let promise1 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve('foo');
  }, 300);
});

// `then` is the equivalent of `map` in our example.
promise1.then(console.log);

console.log(promise1); // Will print out [object Promise] 
```

这里发生了什么？
我们有一个承诺对象，是一个在未来价值评估中持有的盒子。
`then`方法在承诺结构中对值*应用一个函数，同时保持承诺对象本身不变。
`then`方法相当于我们世界中的`map`。*

## Ok。明白了。但是那看起来很薄，它还有什么用呢？

我支持你。在我们继续之前，您需要更多的例子来解释这种抽象的价值。这是我需要脱离经典 JS 领域的地方，但不要太远。
下面介绍一下 Maybe Monad。别担心，单子部分与我们的解释无关。只是可能的部分。

也许单子是一种以安全的方式处理可能存在也可能不存在的值的方法。不是有`null`或`undefined`值，而是利用函子方法优雅地处理`Nothing`的情况。

基本上，Maybe 是一个可选值的框。假设我们有一个实现，它可能是这样的:

```
let maybeName1 = Maybe("Snir")
let maybeName2 = Maybe(undefined)

console.log(maybeName1)
// [status: "Just", value: "Snir"]

console.log(maybeName2)
// [status: "Nothing"] 
```

现在，这个`Maybe`结构是一个盒子，它实现了一个`map`函数，成为一个函子！让我们来看一个潜在的`map`实现:

```
// (This is not how `this` really works, assume `this` here is the
// currently referred Maybe object itself. Just for ease of read)
Maybe.map = (fun) => {
  if (this.status === "Nothing") {
    return;
  }
  return fun(this.value)
} 
```

因此，使我们能够编码这个:

```
let maybeName1 = Maybe("Snir")
let maybeName2 = Maybe(undefined)
let maybes = [maybeName1, maybeName2]

maybes.map((maybeObj) => {
  maybeObj.map((name) => console.log(`Hi ${name}!`)
}
// Will logs: "Hi Snir!" 
```

正如您所看到的，Maybe 是一个安全保存值的框，而我们可以使用 map 将函数应用于这些值(不需要担心检查“空”值，因为 Maybe 实现会处理这些)。

在函数式编程中，这些盒子到处都是。而且，我可以说，在 Haskell 这样的具有进化类型系统的语言中，它们甚至更优雅。

## 还有更！最后一个奇怪的例子是函数。

所以我们知道结构是盒子。列表是许多值的盒子，可能是可选值的安全评估的盒子。但是功能也是盒子。代码执行数据框。我们也可以将函数映射到函数上！

这就是它变得有点奇怪的地方，但在我看来这是最重要的例子，因为它扩展了你对什么是“结构”的看法。

遗憾的是，这也是我们不得不离开 JavaScript 自然领域的地方，因为 JS 并没有将函数作为实现函子的完美盒子。

这里重要的一点是函数的自然局部应用，它存在于 Haskell、Scala 和许多其他函数式自然语言中。

例如，在 Haskell 中，每个函数总是只有一个参数。那么我们如何传递多个参数呢？Haskell 只是自动将它从第一个参数创建的函数应用到带有第二个参数的函数中。

这创造了一个世界，其中部分应用的(或以其他名义使用的)功能是语言的第一类特征。

看看那个:

```
-- This function takes 2 arguments: name, and address
printPerson name address = putStrLn (name ++ address)

-- Let's apply it with 2 arguments to get a print:
printPerson "Snir" " Tel Aviv"
-- This will print "Snir Tel Aviv"

-- We can also just partially apply, and store in another name:
printShani = printPerson "Shani"
-- This will not print anything yet. It just returns a partially applied function

printShani " New York"
-- This will print "Shani New York" 
```

有 javascript 库可以让这变得“更”自然，比如 Ramdajs 和它的朋友们。从现在开始，我将用一个自然支持这一点的“虚构的”JS 来演示，只是为了让那些对它感到舒服的人，可以和 haskell 一起更容易地理解语法。

让我们看看 JS:
中函数的一些假想映射实现

```
// NOTE: this is in an imaginary JS where partial application is natural. // This will not work in real life, it just for understanding the concept.
// And as with the example before, this is not really how `this` works.
// Just assume it refers to the context function, for readability.
Function.map = (fun) => {
  this(fun)
} 
```

这在理论上使我们能够:

```
let add10 = (n) => n + 10
let multiply2 = (n) => n * 2
let addThenMultiply = add10.map(multiply2)

addThenMultiply(1) // Will result in 22 
```

这也是一个函子。
函数是存储计算信息的结构，对其进行映射会改变存储的值——也就是计算信息，从“加 10”变为“加 10 然后乘以 2 ”,而不会改变结构本身，这就是函数的概念。

如果你明白了这一点，那么可以很有把握地说，你已经得到了函子的一般概念。

为了简洁和正确(不是玩弄想象中的 JS ), haskell 中也是如此:

```
-- The (->) is what represents functions in haskell.
-- This is an instance implementation of Functors to (->) - functions.
-- We implement the `fmap` here, which is just the `map` function.
instance Functor ((->) r) where  
  fmap f g = (\x -> f (g x))

-- Intentionally avoid point-free style, for easier read.
add10 n = n+10
multiply2 n = n*2

addThenMultiply = add10 `fmap` multiply2
addThenMultiply 1 
```

## 拘谨！让我们正式定义函子。

很少。你已经走了这么远了。让我们用函子的正式定义来总结一下。

一个仿函数必须实现`map`函数，这样它就可以接受一个从类型`a`到类型`b`的函数，以及一个值为类型 a 的仿函数，并返回类型 b 的仿函数。

```
-- Formal haskell type definition
(a -> b) -> f a -> f b 
```

这是什么意思？让我们从简单的开始。我们有一个列表`[1,2,3]`和一个函数`addOne = (n) => n + 1`
，然后列表就是函子，它保存类型`Number`的值。Th 函数是从`Number`到`Number`的函数。所以我们应该再次产生一个相同类型(数字)的函子(列表)。
T5】

现在说我们有一个从数字到另一种类型的函数:
`strNum => (n) => "num: " + n`
然后，通过规则，它将是一个函数，从`Number`到`String`将`Number`的一个函子(list)转换为`String`的函子。
`[1,2,3] -> ["num: 1", "num: 2", "num: 3"]`。

### 规则

对于函子的预期工作，在函数的任何一般用法中，我们需要保持 2 个基本规则:

1.  函子必须保持同一性态射这基本上意味着，如果我为`map`传递一个空函数，函子应该保持不变。基本上，`map`本身不应该改变任何东西，没有提供的函数。

```
let noop = (n) => n
[1,2,3].map(noop)
// Must return `[1,2,3]` again. 
```

1.  函子保持态射的复合这意味着，用复合函数映射应该给出与用分离函数映射相同的结果。

```
let addTwo = (n) => n + 2
let MultiplyTwo = (n) => n * 2
let addThenMultiply = (n) => MultiplyTwo(addTwo(n))

[1,2,3].map(addTwo).map(MultiplyTwo) // [6,8,10]

// Must be equivalent to the use of the composition morphism:
[1,2,3].map(addThenMultiply) // [6,8,10] 
```

就是这样！

页（page 的缩写）关于函子还有更多要说的，函子的更多变体也要涵盖。
这应该会让你很好地理解这个概念，并且很容易进入更高级的主题。