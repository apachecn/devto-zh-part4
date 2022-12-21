# 一些 JavaScript 难题

> 原文：<https://dev.to/tchaflich/a-few-javascript-puzzlers-2p1l>

Kyle Glenn 在 Unsplash 上拍摄的封面照片。

### 早上好！🌥️，你周末过得怎么样？

我最近生病了，所以没有太多精力去做平常的工作。但是我已经整理了一些专门针对 JavaScript 的快速“谜语”。

如果你很早就得到答案，不要破坏评论中的答案，因为那些人只是在寻找提示而不是解决方案。(剧透标签什么时候？)

## # 1——非自反

如果你知道“gotchas”这种语言，这是相当简单的，但如果你以前从未见过这种行为，这确实是一个难题。

```
x === x; // true
x[+[]] === x[+[]]; // false 
```

Enter fullscreen mode Exit fullscreen mode

答案不止一个，看看你能否找到另一个挑战的完整解决方案。

提示链接:

*   [堆栈溢出](https://stackoverflow.com/questions/4170978/explain-why-this-works)
*   [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)
*   [博文](http://patriciopalladino.com/blog/2012/08/09/non-alphanumeric-javascript.html)

## #2 -倒数

这是另一个令人挠头的问题:

```
x === y; // true
1/x === 1/y; // false 
```

Enter fullscreen mode Exit fullscreen mode

求 x 和 y 的值。

资深 JS devs 在呻吟，大概已经知道答案了。但对于那些没怎么练过的人，这里有个提示:

*   [ECMAScript 规范](https://www.ecma-international.org/ecma-262/5.1/#sec-8.5)

## #3 -原型

还有一个:

```
var someone = function() {};
someone.prototype.attributes = {};

var alice = new someone();
alice.attributes.name = 'Alice';

var bob = new someone();
bob.attributes.name = 'Bob';

var eve = new someone(); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的最后，`eve.attributes.name`是什么？

您的提示是:

*   [中等](https://medium.com/launch-school/javascript-weekly-data-types-and-mutability-e41ab37f2f95)
*   [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)

* * *

JavaScript 确实是一种令人费解的语言，不是吗？哪些是你立即得到的，哪些是你需要查找的？

周末愉快，别忘了表现出色！

[![Small white dog wearing a bow-tie giving a high-five.](img/b4dfd687f67555791186fc6ac234a9cf.png)](https://i.giphy.com/media/100QWMdxQJzQC4/giphy.gif)