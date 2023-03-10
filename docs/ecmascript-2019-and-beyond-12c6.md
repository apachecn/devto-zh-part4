# ECMAScript 2019 及以后...

> 原文：<https://dev.to/areknawo/ecmascript-2019-and-beyond-12c6>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容。**

上个月-2019 年 6 月-[ECMA-262](https://www.ecma-international.org/ecma-262/10.0/)标准第十版正式出版。这是什么意思？——嗯， **ECMAScript 2019** 来了！JavaScript 和其他衍生语言的最新和最好的规范。虽然您可能已经听说过它的一些新功能，但我们会全部回忆起来！再加上一些 ECMAScript 细节和对下一步内容的预览！

# 发布周期

虽然我们已经知道了什么是 ECMAScript，以及每年的发布周期是如何工作的，但是还有一些更有趣的事情。

毫无疑问，每一个新的 ES 规范的发布都是重要的，但是建议不要把它看作是一年一次的事情(除非你生活在 ES6 之前的时代)。当你读这篇文章时，这个规范是“活的”，正在进行中。每一次发布都只是一堆新**特性**的总结。这就是你应该如何看待它——一个功能一个功能地看。

你现在可能知道了(可能是因为以前和 Babel 一起工作的缘故)，ECMAScript 规范是由不同的**提案产生的。每个提案需要经过 5 个不同的阶段...**

 **每一个新的提议开始时仅仅是一个想法，需要首先由 [**TC39**](https://tc39.es/) 审查。该委员会由不同公司/组织(如 Google 和 Mozilla)的专家和代表组成，决定该规范的未来。当提交的创意得到正面审核后，它将进入**阶段-0** 。从现在开始，它是公开的，可以从相应的 [GitHub repo](https://github.com/tc39/proposals/blob/master/stage-0-proposals.md) 获得。随着提案在定期 [TC39 会议](https://github.com/tc39/tc39-notes)上被进一步讨论，它要么进入下一阶段，要么在此过程中被放弃。在 [**阶段-3**](https://github.com/tc39/proposals#stage-3) 的提案可以认为是非常稳定和有保证的。提案到达**第四阶段***后，就正式了。*

 *现在，所有这些过程——不同的阶段和材料——不一定与实施时间相匹配。许多浏览器供应商为了让他们的浏览器永远保持绿色并吸引开发者，经常在他们还处于第三阶段时就实现一些功能。自然，由于这些特性仍然是非标准的，许多人很可能要么使用 **Babel** 要么根本不会使用这个特性！但这仍然是事实。也就是说，一些特性甚至可能在官方规范发布后的一段时间内实现。但是，自从上次*【大】*发布 ES (ES6)之后，这样的事情就不常发生了。

# ECMAScript 2019

ES 发布周期结束后，让我们探索 ES2019 的所有新功能！我知道你以前可能已经看过这些了，但是，请让我们耐心等待一会儿，因为我们要以初学者友好的方式再做一次！

## [array . prototype . flat { Map }()](https://v8.dev/features/array-flat-flatmap)

正如我前面提到的，ES2019 的许多功能可以在官方规范下降之前出现在浏览器中。两个新的不可变[数组方法](https://areknawo.com/map-reduce-filter-my-turn/) - [`.flat()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat) 和 [`.flatMap()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap) 就是这样。

现在，我已经在前面的中介绍了这些方法[，所以如果你想了解更多，可以去看看。这里有一个小复习！](https://areknawo.com/exploring-the-hidden-potential-of-javascript-arrays/) 

```
const arr = [1, [2, [3, 4]]];

arr.flat(2); // [1, 2, 3, 4]
arr.flatMap(num => `${num}`); // ["1", "2,3,4"] 
```

Enter fullscreen mode Exit fullscreen mode

我认为这些方法的命名揭示了它们的全部内容。`.flat()`可以用来**展平**你的多维数组。默认情况下，它的深度是 1 级，但是您可以选择传递一个参数来指定深度，就像我们在上面的例子中所做的那样。

因为结合使用 [`.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 调用`.flat()`会非常有用，这也有一个特殊的方法！——`.flatMap()`。这个人首先映射数组，然后将它展平 1 层深度(这里没有配置)。当然，所有这些都伴随着性能的提高！

## [object . from entries()](https://v8.dev/features/object-fromentries)

随着 ES6 中 [`Map`对象](https://areknawo.com/rocking-js-data-structures/)的引入，以及以键-值方式用数组表示数据的需要， [`Object.fromEntries()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries) 可以证明在进行这种操作时非常有用。它可以将您的键值对(条目)的数组或映射更改为一个实际的对象——这在 ES2019 之前需要自定义代码。

```
const arr = [
    ["key1", "value1"],
    ["key2", 2],
    ["key3", {value: 3}]
];

Object.fromEntries(arr); 
// { key1: "value1", key2: 2, key3: { value: 3 } } 
```

Enter fullscreen mode Exit fullscreen mode

## string . prototype . trim start()等等...

ES5 [`.trim()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Trim) 法有 2 个新学院，甚至 4 个！当`.trim()`被**从字符串的两边删除不必要的空格**时，现在有一些新的方法来做那只形成指定的一个！

[`.trimStart()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trimStart) ，也称为`.trimLeft()`，可用于从弦的开始/左侧修剪弦...

```
const str = "     str     ";

str.trimStart(); // "str     "
str.trimLeft(); // "str     " 
```

Enter fullscreen mode Exit fullscreen mode

而 [`.trimEnd()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trimEnd) 和`.trimRight()`可以用来做同样的事情，从右侧开始。

```
const str = " str ";

str.trimEnd(); // "     str"
str.trimRight(); // "     str" 
```

Enter fullscreen mode Exit fullscreen mode

## 可选捕捉绑定

除了带给我们几个新方法之外，ES2019 还修复了其前辈的一些缺陷。第一个是要求在`try` / `catch`语句中包含错误参数，即使不使用它。

```
// then
try {
    // ...
} catch(error) {
    // ...
}

// now
try {
    // ...
} catch {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

代码写得越少越好，对吗？

## function . prototype . tostring()；

在基于 ES 的语言中，你可以`.toString()`做几乎所有的事情！它不同的叫声会解决什么是另一个故事。但是，在 ES2019 之前，当与函数一起使用时， [`.toString()`](https://developer.mozilla.org/pl/docs/Web/JavaScript/Referencje/Obiekty/Function/toString) 返回一个仅包含函数代码的字符串。现在，它还考虑到了注释、新行和空白——一切！

```
function toBeStringified() {
    // a comment

    // a comment after new line
}

toBeStringified.toString();
/*
`function toBeStringified() {
    // a comment

    // a comment after new line
}`
*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 

[`Symbol`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)——always——唯一的数据类型，主要用作对象的属性标识符——刚刚获得了一个名为`.description`的新属性。它可以用来以字符串的形式访问`Symbol`的可选传递的描述参数。

```
const mySymbol = Symbol(2);

mySymbol.toString(); // "Symbol(2)"
mySymbol.description; // "2" 
```

Enter fullscreen mode Exit fullscreen mode

## [array . prototype . sort()](https://v8.dev/features/stable-sort)

如果你以前使用过 [`.sort()`](https://developer.mozilla.org/pl/docs/Web/JavaScript/Referencje/Obiekty/Array/sort) ，你可能会知道不要依赖它默认的**排序算法**是一种推荐的做法。这是因为在以前的规范中，没有提到算法的任何**稳定性**要求。现在，在 ES2019 中，规范要求*“稳定排序”*，每个 JS 引擎都需要遵守它。这意味着他们仍然可以使用不同的算法，但不应该有任何误解。为了理解我的意思，请看下面的例子。

```
const dogs = [
  { name: "Abby",   rating: 12 },
  { name: "Bandit", rating: 13 },
  { name: "Choco",  rating: 14 },
  { name: "Daisy",  rating: 12 },
  { name: "Elmo",   rating: 12 },
  { name: "Falco",  rating: 13 },
  { name: "Ghost",  rating: 14 },
];

// Sort the dogs by `rating` in descending order.
dogs.sort((a, b) => b.rating - a.rating);
/* 
[
  { name: "Choco",  rating: 14 },
  { name: "Ghost",  rating: 14 },
  { name: "Bandit", rating: 13 },
  { name: "Falco",  rating: 13 },
  { name: "Abby",   rating: 12 },
  { name: "Daisy",  rating: 12 },
  { name: "Elmo",   rating: 12 },
]
*/ 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面的结果注释中看到的，我们的排序函数对数组进行了排序*“如预期的”*。在最初的版本中——条目是按照`name`字段的字母顺序排列的。现在先按`rating`排序，但还是按字母顺序。第二个事实有点出乎意料(排序后的项目应该尊重它们之前的位置)，但在 ES2019 之前并不能保证。从现在开始，新的浏览器将保证你的正确顺序。但要知道旧的还在那里。这就是为什么在编写排序函数时应该非常明确，而不一定要过多依赖浏览器的默认行为。

## [JSON 上的一句话...](https://v8.dev/features/well-formed-json-stringify)

JSON 和与 JSON 相关的功能也做了一点修改！

`JSON.stringify()`现在正确地逃出了 Unicode *[【孤独的代理人】](https://unicode-table.com/en/blocks/high-surrogates/)* 。考虑到您可能很少遇到与此相关的问题，这没什么大不了的。尽管如此，在编写代码时少考虑一些是一回事。给你举个例子:

```
// then
JSON.stringify('\uD800'); // "'�'"

// now
JSON.stringify('\uD800'); // "'\\ud800'" 
```

Enter fullscreen mode Exit fullscreen mode

ES2019 修复了 Unicode 字符串的另一个问题！JSON 通常被认为是 ECMAScript 的一个子集，但是直到现在才完全正确。JSON 字符串可以包含一些非转义的 Unicode 字符( *U+2028* 和 *U+2029* )，而 ES 字符串则不能。现在， [ES 支持这些字符](https://github.com/tc39/proposal-json-superset)，这个问题已经解决，JSON 真正成为 ES 的一个子集。

# 未来会怎样？

现在我们已经介绍了 ES2019 的所有内容，接下来呢？现代浏览器引擎(如 V8)不断引入新功能，即使它们还处于第三阶段！他们中的一些人已经被安排在 2020 年，其他人-没有那么多。但是，我想让你对未来有一个小小的了解，在某些情况下，现在就可以体验到！也就是说，在 ES2020 及以后，这里有 3 个最有趣的功能值得期待！

## [【动态导入()】](https://v8.dev/features/dynamic-import)

据我所知，许多开发者仍然使用代码捆绑器，即使 ES 模块得到了所有主流浏览器的官方支持。但是谁能责怪他们呢？模块确实需要更多的*【标准化】*——不是来自规范，而是来自社区。为此，需要一段时间，旧的浏览器必须消亡...

但是，所谓的动态`import()`肯定是这个*“模块化未来”*的一部分。良好的...考虑到这一功能已经被[所有主流浏览器](https://caniuse.com/#feat=es6-module-dynamic-import)实现，并处于**阶段-4** ，计划于 **ES2020** 推出，也许未来这个词有点太大胆了。

```
import("module.mjs")
    .then((module) => {
      module.default(); // export default stuff
      module.doStuff(); // other stuff
    }); 
```

Enter fullscreen mode Exit fullscreen mode

动态`import()`的主要优点是加载模块是以一种懒惰的方式完成的。通过这种方式，你可以显著提高你的应用程序的性能，只需要先加载必要的东西(用标准的`import`语句)，然后再加载其他的东西。另外，当用`async` / `await`处理时，它们在语法上几乎不明显！

```
(async () => {
    const module = await import("module.mjs")
    module.default();
    module.doStuff();
})(); 
```

Enter fullscreen mode Exit fullscreen mode

## [BigInt](https://v8.dev/features/bigint)

在 JS 中处理大量数据可能会很成问题。可能值的范围很大，但是，在某些情况下，这还不够。这就是为什么 [`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) 诞生了！

`BigInt`是一个全新的**数字原语**，它与当前可用的`Number`一起工作。它已经在基于 Chromium 的浏览器和其他一些浏览器中实现了[，但仅此而已。一旦`BigInt`超越第三阶段并在 2020 年成为官方，支持肯定会增加。](https://caniuse.com/#feat=bigint)

现在，在支持变得更好之前，我认为你需要知道的是，你将能够用 JS 以很好的性能表达一些大的数字，而不需要任何第三方库！

```
BigInt(Number.MAX_SAFE_INTEGER) + 2n; // 9007199254740993n
/* Value bigger than Number can represent
   BigInts can be created with -n suffix */ 
```

Enter fullscreen mode Exit fullscreen mode

当然，顾名思义，`BigInt`只能用来表示整数。这让我非常期待未来可能出现的 *BigDecimal* (或者类似的东西)...

## [私有类字段](https://v8.dev/features/class-fields)

可能是 es 最期待的特性之一。私有类字段是许多开发人员真正想要的。真正隐藏实现细节的能力！

私有类字段目前处于第三阶段。还不确定我们是否会在 ES2020 年看到它们。但是，尽管这可能是一个多么棒的功能，我仍然对它有些担心。首先，根据目前的提议，没有提到任何种类的**保护字段**——那些使用打字稿或其他静态类型语言的人知道我在说什么。更糟糕的是，ES 提案中私有字段的语法只是...坏 IMHO。`public`、`protected`和`private`关键字的跨语言未描述的*“标准”*被替换为散列(`#`)符号。尽管如此，我仍然很高兴这样的特性(以任何形式)来到 JS！

```
class IncreasingCounter {
    #count = 0;
    get value() {
        console.log("Getting the current value!");
        return this.#count;
    }
    increment() {
        this.#count++;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 想了解更多？

ES 规范和建议每天都在发展。浏览器和 JS 引擎也是如此。如果你想了解最新和最棒的功能，我鼓励你去看看 [v8.dev](https://v8.dev/features) 博客，在那里 **V8** 背后的人分享了关于引擎、其最新功能以及它们是如何实现的非常有见地的信息。强烈推荐！

所以，这篇文章到此为止。考虑在 Twitter 上关注我的**[**，在我的脸书页面**](http://facebook.com/areknawoblog) ，或者查看我的个人博客 获取更多精彩内容。一如既往，祝您愉快！*****