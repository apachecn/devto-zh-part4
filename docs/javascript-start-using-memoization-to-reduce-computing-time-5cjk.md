# 开始使用记忆化来减少 Javascript 中的计算时间

> 原文：<https://dev.to/iggredible/javascript-start-using-memoization-to-reduce-computing-time-5cjk>

一个经典的 CS 问题是创建[斐波那契]((https://www.mathsisfun.com/numbers/fibonacci-sequence.html))序列。其中一个解决方案是递归函数，它看起来像这样:

```
function fib(n) {
  if (n === 0 || n === 1)
    return n;
  else
    return fib(n - 1) + fib(n - 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

上面递归斐波那契函数的一个主要问题是它是一个昂贵的函数。它自称太多次了。在我那台可怜的 2015 款 Macbook air 上调用 fib(40)用了大约 30 秒(它自称为*102334155*次)，fib(45)差不多 5 分钟(自称为*1134903170*次——一个**十亿**次)。

祝你好运拨打纤维(100)。

[![tearful cry](img/1e924b34d226682d890958e9e3d5a15e.png)](https://i.giphy.com/media/qQdL532ZANbjy/giphy.gif)

我们能做些什么来缩短这样一个昂贵的函数吗？

# 进入记忆状态

[Memoization](https://en.wikipedia.org/wiki/Memoization) (与 memoring 押韵)是 CS 中的一种技术，它将以前的结果保存到缓存中，这样当用相同的参数再次调用函数时，它将从缓存中返回值并再次执行函数。这对于像斐波那契这样昂贵的函数很有用。

# 我们如何在斐波那契上使用记忆化？

我们可以使用:

```
const fib = (function() {
  const cache = {};

  function f(n) {
    let value;

    if (n in cache) {
      value = cache[n];
    } else {
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(n - 1) + f(n - 2);

        cache[n] = value;
    }

    return value;
  }

  return f;
})(); 
```

Enter fullscreen mode Exit fullscreen mode

(来源:[此处](https://www.sitepoint.com/implementing-memoization-in-javascript/)。以上所有荣誉归作者所有)。

试试上面的函数，运行 fib(40)，fib(50)，甚至 fib(100)。你会感觉到不同。

# 记忆化是如何工作的？

它将值存储在 JS 对象(`const cache = {};`)上，因此如果再次调用相同的值，它将从`cache`获取值，而不是执行函数。

假设我们要调用 fib(5)。fib(5)第一次被调用时，由于缓存为空，在缓存中找不到 5(`if (n in cache)`为 falsy)，它执行 fibonacci 逻辑(`value = f(n - 1) + f(n - 2);`)，然后将结果保存到缓存(`cache[n] = value;`)。现在我们有了一个`n = 5`的缓存——类似这样:`{5: 5}`(顺便说一下，fib(5)的值是 5)。

下次我们再次调用 fib(5)时，它会在缓存中找到(`{5: 5}`)。它不是再次执行 fib(5 ),而是简单地返回缓存查找`value = cache[n]; ... return value;`的值。因为我们的 fibonacci 是递归的，所以当我们调用 fib(5)时，它会自动用高达 5 的值填充缓存。调用 fib(5)会为 fib(4)、fib(3)等创建缓存。

另一个例子是，假设我们刚刚调用了 fib(49 ),接下来我们想调用 fib(50)。在我们调用 fib(50)之前，在我们的缓存中，我们会有这样的缓存值:

```
{
  0: 0,
  1: 1,
  2: 1,
  3: 2,
  ...
  48: 4807526976,
  49: 7778742049
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经有了从 0 到 49 的值！我们需要做的就是调用`value = f(n - 1) + f(n - 2);` - aka fib(49) + fib(48)，我们已经将它存储在缓存中了！这就是有记忆的 fib(50)与它的无记忆版本相比几乎立即返回结果的方式。

# 甜！我要记住我看到的每一个功能！

不幸的是，并不是所有事情都是可以记忆的。我们只能记忆纯函数。

要成为一个纯粹的函数，它必须:

1.  有返回值
2.  不依赖于自身参数以外的参数
3.  不改变其范围之外的值

纯函数超出了本文的范围，但是可以查看这篇关于纯函数的[短文。](https://blog.bitsrc.io/understanding-javascript-mutation-and-pure-functions-7231cc2180d3?gi=56dfa64da56e)

# 其他注释

记忆化太棒了。但是不要过度使用。决定何时使用记忆时需要考虑的一些事情:

1.  不是所有的函数都是可记忆的。只有纯函数才是。
2.  记忆有很高的开销。请记住，我们必须创建一个缓存来存储每个内存化函数的许多可能的参数。
3.  记忆最好用在昂贵的功能上。正则表达式调用和递归是我想到的其中一些。

# 真好。但是我们可能永远不会在现实生活中使用斐波那契数列。有记忆化在现实生活中应用的例子吗？

没错。VueJS 利用记忆化。`cached(fn)`是一个记忆包装。

```
function cached (fn) {
  var cache = Object.create(null);
  return (function cachedFn (str) {
    var hit = cache[str];
    return hit || (cache[str] = fn(str))
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

并且正在被多次使用:

```
const camelizeRE = /-(\w)/g
export const camelize = cached((str: string): string => {
  return str.replace(camelizeRE, (_, c) => c ? c.toUpperCase() : '')
})

export const capitalize = cached((str: string): string => {
  return str.charAt(0).toUpperCase() + str.slice(1)
})

const hyphenateRE = /\B([A-Z])/g
export const hyphenate = cached((str: string): string => {
  return str.replace(hyphenateRE, '-$1').toLowerCase()
}) 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到这些函数。(写这篇文章的时候 Vue 2.5.0。将来可能会改变，但你可以随时回到以前的版本。

黑客快乐！

# 资源

更多关于记忆的阅读:

*   [3 分钟了解 JavaScript 记忆](https://codeburst.io/understanding-memoization-in-3-minutes-2e58daf33a19)
*   [JavaScript 函数记忆](http://inlehmansterms.net/2015/03/01/javascript-memoization/)
*   [在 Javascript 中实现记忆化](https://www.sitepoint.com/implementing-memoization-in-javascript/)

关于纯函数:

*   [理解 Javascript 突变和纯函数](https://blog.bitsrc.io/understanding-javascript-mutation-and-pure-functions-7231cc2180d3)