# 这都是真的...函数式 JavaScript 的第一步

> 原文：<https://dev.to/johnboy5358/it-s-alltrue-firststeps-in-functional-javascript-19k3>

## 这是给谁的？

任何对初级/中级水平的函数式编程感兴趣的人(不管那是什么意思！)并将“倾听”我的沉思。

## 同 allTrue 的踢法

为什么 allTrue？好吧，allTrue 是一个结合谓词函数进行逻辑合取的函数。此外，从一个以字母“a”开头的函数开始，这可能是一系列帖子的开始，但也可能不是，这取决于你自己！

allTrue 函数是我的 Ramda 函数 allPass 版本。它使用 Array.prototype.every 方法，但该数组不包含主值，而是包含函数！我在我的 node_modules 目录中保留了这个函数来提交一个文件，我只是将这个文件称为 func.js。是的，我知道我可以使用 Ramda，但在我的 JS fp 阶段，我只是发现从 web 上收集代码片段或自己编写函数更有帮助。它似乎给我留下了更多的印象，并帮助我的学习过程。写这篇文章也是一样。

当然，你应该在你的产品代码中使用 [Ramda](https://ramdajs.com/) ,因为它已经过“战斗测试”。

这是 allTrue 的定义。

```
 const  allTrue  =  curry((preds, x) =>  every(pred  =>  pred(x), preds)) 
```

## 用法

举例是解释如何使用 allTrue 的最佳方式。

假设我们有一个从 1 到 20 的整数数组，我们只需要 5 到 15 之间的偶数，为了更有趣一点，我们需要将这些整数加倍。在我的 func.js 文件中，我有函数 gt，lt 和 isEven，这些函数是默认设置的，所以我可以写...

```
 const { allTrue, compose, filter, gt, isEven, lt, map } = require('func')
const  integers  = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]

const double = n => 2 * n
const  doubleEvensBetween5And15  =  compose(
  map(double),
  filter(allTrue([ isEven, gt(5), lt(15) ]))
) 
```

所以，你可以看到 allTrue 采用了一个列表(...JS 数组),它返回一个谓词函数，然后将该谓词函数应用于过滤函数。最后，这个过滤器的结果，即整数列表[6，8，10，12，14]被应用于 map(double)以返回[ 12，16，20，24，28 ]。

下面是函数调用。

```
 console.log(doubleEvensBetween5And15(integers))
  // => [ 12, 16, 20, 24, 28 ] 
```

访问 Runkit 的[工作示例](http://runkit.com/johnsw/using-alltrue-or-allpass-in-ramda)

## 这都要感谢库里

是的，编程世界感谢[哈斯克尔·库里](https://en.wikipedia.org/wiki/Haskell_Curry)在[组合逻辑](https://en.wikipedia.org/wiki/Combinatory_logic)中的工作，而 fp 中的这个重要概念就是以他的名字命名的。

如果你不熟悉这种 fp 实践，我鼓励你阅读埃里克·埃利奥特的这篇文章。

如果你对这篇文章感兴趣，请给它一个赞。

干杯！

另外，我希望听到你对这篇文章的任何建设性的批评。谢了。