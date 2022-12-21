# 谁害怕那只大坏狼？

> 原文：<https://dev.to/johnboy5358/whose-afraid-of-the-that-big-bad-wolf-2gep>

欢迎来到幻想世界，这个帖子是关于大灰狼，单子。更确切地说，是关于一个叫做也许的单子。还有其他类型的单子；例如，为处理计算机程序中的不同情况而开发的要么和 IO 单子。

为什么是大灰狼？嗯，单子可能有点吓人；这不是，你怎么说呢，在 JavaScript 中的习惯用法。

所以这个单词 monad，如果你在网上搜索的话，是和这样的短语联系在一起的:

> "一旦你最终理解了单子，你就失去了向别人解释单子的能力."

和...

> "幺半群只是内函子范畴中的幺半群，有什么问题？"

事实证明，这些关于单子的评论当然是笑话，用来取笑人们向他人解释单子的困难。甚至在你开始之前，你就会有一种感觉，你会和单子过不去。

因此，如果您正在冒险进入抽象数据类型(ADT)的幻想世界，那么我希望我可以给您一些关于单子概念的想法，而不需要深入范畴理论，或者告诉您在理解单子之前必须学习 Haskell。

在这篇文章中，我希望，对于新手来说(让我们面对现实吧，我们在某些方面都是新手)，我可以向你展示在你目前的 JavaScript 知识和你的代码中使用的 Maybe monad 之间的“桥梁”。唯一的先决条件是你熟悉 JS 数组方法，主要是。map()和 JS Promise 对象；此外，我假设您已经使用 es2016 函数表达式使用了函数合成和 currying。

## 那么什么是单子

单子是在上下文中包装一个值的对象。让我们用 JS 程序员应该熟悉的术语来解释一下。

这里有一个类比；我们知道，数组是元素的集合，就像这样:
[0，1，2，3，4，5]

这是一个只有一个元素的数组。

```
 const  M  = [2] 
```

m 是一个包装值；它有一个背景。上下文是数组。数组提供了大量的方法。

```
 //  Stay with me I just need to set up some functions to work with...
const log = console.log
const sq = n => n * n

// add - a curried version of the add function.
const add = a => b => a + b

// a new function made by partially applying the add function.
const add10 = add(10)

log(typeof add10)
// => function

// OK let's continue...
// we can "map over" this wrapped value ie. apply a function to the value
// to transform it to another value.

const result = M
    .map(add10)
    .map(sq)

log(result)
// => [ 144 ]

// [144], this result may not be what you want, it is still wrapped.
// So in JS, the way to get at the value is .pop()

log(result.pop())
// => 144 
```

所以有能力。给我们的地图？它为我们提供了一种方法，通过对值应用函数来转换值，然后重复地做同样的事情。这不是功能组合本身，而是价值转换。

顺便说一下，带有. map 方法的对象被称为仿函数。所以数组是一个函子。还有，单子是函子；还有更多。在这个阶段，如果你仍然不太清楚我在说什么，那么[这个](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)可能会有所帮助，给你一个正在发生的事情的图像视图。

那么在上面的代码中到底发生了什么呢？这些是步骤:

*   通过从数组上下文中取出值 2 并对该值应用函数(add10)来映射包装的值([2])

*   然后，函数应用程序(12)的结果被包装回数组上下文中

*   下一个。map 以同样的方式处理 unwrap 12，应用函数 sq，值现在是 144，将它包装回数组。

让我们创建一个函数，使用与上面类似的方法，但是对一个字符串值进行操作。

```
 const  exclaim  =  s  =>  s  +  '!'
const  exclaimWorld  =  val  =>
    // .of lifts the value into the Array
    Array.of(val)
      .map(s  =>  s.concat(' World'))
      .map(exclaim)
      .pop()

// ...and call it.

log(exclaimWorld('Dave\'s'))

// => Dave's World! 
```

嗯，这一切都很好，但如果价值不存在呢？然后呢？

```
 const  o  = {
  val:  "Hello",
  nil:  null,
  udef:  undefined
}

log(exclaimWorld(o.val))
// => Hello World!

// - OK, good it worked because the value passed in is a value.

// Now watch carefully...

log(exclaimWorld(o.udef))
// => TypeError: Cannot read property 'concat' of undefined. 
```

嘣！我们的程序已经死了，现在我们也许需要它！我们不能做 undefined . concat(value)；这就是问题所在。

是的，我们可能需要，但首先，让我们再看一个类比；这次使用 JS 程序员应该知道的对象，Promise 对象。

我称之为 pMaybe，以区别它是由一个承诺派生出来的。请注意，这只是出于训练目的，以显示一个 Maybe 与 JS 中另一个常见对象的相似性。我们已经看到数组给了我们一个可能的性质；的能力。映射一个值，根据需要对该值应用任何函数。也许是一个函子。但是不能针对未定义/无效的情况提供保护。然而，应许允许我们遵循两条路径中的一条，这两条路径通常被称为解决和拒绝，但在这里我将它们重命名为公正和虚无。是的，我稍微滥用了承诺的正常用例，但是它有望帮助我们理解。

```
 const  pMaybe  =  val  =>
  new  Promise(
    (Just, Nothing) => {
      if (val  ===  undefined  ||  val  ===  null) Nothing(val)
      Just(val)
})

const pExclaimWorld = s =>
  pMaybe(s)
    .then(s  =>  s.concat(' World'))
    .then(exclaim)
    .then(s  =>  console.log(s))
    .catch((e) =>  console.log(`You passed in ${e} this is a likely cause of error!`))

pExclaimWorld(o.val)
// => Hello World!

pExclaimWorld('Brian\'s')
// => Brian's World!

pExclaimWorld(o.udef)
// => You passed in undefined this is a likely cause of error!

// to make our code more composable we need some curry! Here's a curry function

const  curry  =  f  => (...args) =>
  args.length >=  f.length
    ?  f(...args)
    :  curry(f.bind(undefined, ...args))

// a prop function
const  prop  =  curry((k, o) =>  o[k])

// Another example

const  add10ToAge  =  personO  =>
  pMaybe(personO)
    .then(prop('age'))
    .then(add(10))
    .then(result  =>  console.log(result))
    .catch(e  =>  console.log(`Nothing , check your input`))

add10ToAge({ name:  'Dinah', age:  14 })

// => 24

// Note: The results from the above will appear later in the output
// because of the effect of the Promise. 
```

现在你看到这一次我们的代码没有出错。代码中未定义的潜在威胁通过。catch()和一个未定义的或 null 被友好的响应处理。但是这有一个严重的限制，即未定义/空检查只发生在最初将值提升到 pMaybe 中时。如果由于以下任何原因生成了 null 或 undefined。那么调用它不会产生什么...这可不好。要查看这一过程，请将“age”属性更改为“ag”，这不是传入对象的属性。它现在将返回(NaN)。我们真正想要的是什么也没有，表明代码由于未定义而失败。

到现在为止，我希望你已经了解了“可能单子”的概念。我试图把它和一个 JS 编码者应该已经知道的东西联系起来。但是，如果你仍然不习惯，不要担心，还会有更多的例子。

现在我们知道也许是为了解决什么问题；价值的缺失(即未定义或空，这是 JS 代码中错误的常见原因)。我们还发现，a 可能具有数组的属性(映射的能力)和承诺的分支能力，但它们都有缺点。所以，让我们努力吧。让我们用 JavaScript 做一个实际的可能，这就是...

## 也许是为了 JavaScript 中更安全的代码

```
 const  Maybe = (function () {
  const  Just  =  function (x) { this.x =  x; };
  Just.prototype.map  =  function (fn) { return  Maybe.of(fn(this.x)) };
  Just.prototype.chain  =  function (fn) { return  fn(this.x) };
  Just.prototype.toString  =  function () { return  `Just(${this.x})` };

  const  Nothing  =  function () {};
  Nothing.map  = () =>  Nothing;
  Nothing.chain  = () =>  Nothing;
  Nothing.toString  = () =>  'Nothing';

  return {
    of: (x) =>  x  ===  null || x  ===  undefined  ? Nothing : new Just(x),
    lift: (fn) => (...args) =>  Maybe.of(fn(...args)),
    Just,
    Nothing
  };
})();

// Thanks to Edd Mann
// https://eddmann.com/posts/maybe-in-javascript/ 
```

所以，在不到 20 行的代码中，a 可能在普通的 JS 中被暴露了。仔细研究；这是一件美好的事情！这可能仍然不是一个符合幻想的世界，但是，这是使我们的代码更加健壮和可组合的一个步骤。稍后，(也许在另一篇文章中)，我们可能会交换使用民间故事，如果你认为这可能会进一步帮助你。

好的，它是如何工作的？

您可以看到，它有一个 Just 函数用于处理快乐路径(值的存在)，还有一个 Nothing 函数用于处理值不存在的情况。当“可能”沿着快乐的路径传递时，映射会将提供的函数应用于传递给“可能”的值；而当在空路径上映射时，没有这样的函数应用发生。这是防止因未定义/空值而出错的地方。最后，Maybe 返回一个对象，以便用户可以与之交互。

另外请注意每次我们。我们通过做 Maybe.of 来重新包装结果，这确保了未定义/空检查继续发生。

*   。of()是 usd，用于将一个值提升到 Maybe 中，并启动空/未定义的检查。
*   。lift()用于将一个函数及其参数提升到 Maybe 中。

不如一行一行的解释，看看怎么用它来解决上面 exclaim 函数的问题。我们来做一个 safe exault 函数。

```
 const  safeExclaimWorld  = val =>
  Maybe.of(val)
    .map(s  =>  s.concat(' World'))
    .map(exclaim)
    .chain(x  =>  x)
    .toString()

log(safeExclaimWorld('Bob\'s')
// => Bob World!

log(safeExclaimWorld(o.udef)
// => Nothing 
```

注意:这一次我们没有得到类型错误。那就好！也许证明了它的存在。

请注意，exclaim 和 safeExclaim 函数是多么相似。特别是，您应该注意以下几点:

*   如你所知，一个价值被提升到可能使用。的()；您可以将此比作 Array.of()

*   值转换是使用。map()；以类似于阵列的方式。地图()

*   现在我们来看这条线。chain(x = > x)；这使我们的价值脱离了可能，类似于。数组版本的 pop()。

顺便提一下，匿名函数 x => x 通常被称为身份函数(const identity = x => x)。

。链条拉平了可能性，并弹出了价值。

*   。chain()需要传递一个函数才能工作。在上述情况下，我们不想转换值，因此使用了 identity。

## 更多例子

这里有一个例子来说明另一种可能的方法

数组方法。find()将查找数组中第一个出现的元素，或者返回 undefined。假设我们有一组熊，想要以安全的方式找到特定的熊。

```
 const bears = ['Black', 'Grizzly', 'Kodiac', 'Polar', 'Spectacled', 'Sloth']
const find = curry((f, xs) =>  Array.prototype.find.call(xs, f))

// .lift() - lifts a function and its arguments into a Maybe

// safeFind : Array -> Any -> Any|Object

const safeFind = curry((xs, val) =>
  Maybe.lift(find)(el => el === val, xs)
    .chain(x  =>  x))

console.log(safeFind(bears, 'Kodiac'))
// => Kodiac

console.log(safeFind(bears)('Polar'))
// => Polar

console.log(safeFind(bears)('Big') )
// => a Nothing object 
```

另一个例子演示了 safeFind 可以处理其他类型的值。

```
const  integers  = [34, 72, 56, 82, 94, 27, 11, 45, 42, 77, 90, 55]

const  finder  =  curry((coll, x) =>  safeFind(coll, x))
const  intFinder = finder(integers)

console.log(intFinder(42)
// => 42

console.log(intFinder(94))
// => 94

console.log(intFinder(423))
// => a nothing object 
```

在这一点上，我鼓励你复制这段代码，用它来玩一玩，并创造你自己的例子。这样做对你理解的帮助远远超过仅仅阅读这篇文章。

下面是这篇文章的代码 [runkit](https://runkit.com/johnsw/whose-afraid-of-the-that-big-bad-wolf)

谢谢，也许我会带更多回来！

如果这有助于你理解“也许”,那么请留下你的评论。同样，如果我给你造成了困惑，那么你的反馈对进一步的编辑也很有价值。