# ！x && (x！== false) && (x！== 0) && (x！== '') && (x！== null) && (x！==未定义)

> 原文：<https://dev.to/urishaked/x-x-false-x-0-x-x-null-x-undefined-22fn>

JavaScript 从未停止给我带来惊喜。这次有个朋友在 JavaScript 以色列社区分享了下面这个谜语:

`x`的哪个值会导致下面的语句为`true`？

```
!x && (x !== false) && (x !== 0) && (x !== '') && (x !== null) && (x !== undefined) 
```

基本上，我们希望在 JavaScript 中找到一个值为 falsy(换句话说，`!x`为真)，但不是`false`、`null`、`undefined`、零或空字符串。

花点时间想一个答案。然后，向下滚动查看我的答案和社区建议的其他答案。

。
。
。
。
。
。
。
。
。
。
。
。
。
。

## 我的回答

我的方法是让`x`每次评估一个不同的值，所以当`!x`被评估时它将是 falsy，但是将通过所有其他测试。

因此，我在`window`对象上为 x 定义了一个 getter，在每次调用时递增返回值:

```
var counter = 0;
Object.defineProperty(window, 'x', { get: () => counter++ }); 
```

由于`x`从零开始，每次读取时递增，因此原始表达式变为:

```
!0 && (1 !== false) && (2 !== 0) && (3 !== '') && (4 !== null) && (5 !== undefined) 
```

其计算结果为真！

## 更多答案

我在打答案的时候，已经有人找到了一个很简单的答案:x 应该是`NaN`。该值为 falsy，并且确实不等于表达式中的任何其他值。您也可以通过查看 ECMAScript 标准中对 toBoolean 的[定义来验证这一点。](https://www.ecma-international.org/ecma-262/6.0/#sec-toboolean)

另一个答案，真的让我很惊讶，是`document.all`。如果你好奇为什么`document.all`确实是假的，看看[这个 StackOverflow 答案](https://stackoverflow.com/a/10394873/830623)。遗留原因，厉害！

最后，这个谜语的原作者正在寻找的答案是`BigInt(0)`。如果你以前没有听说过这个，不要担心——它仍然不是 JavaScript 语言的官方部分，但很快就会成为 BigInt 提案(T2)的一部分。如果你感兴趣，MDN 有一个关于 BigInt 的很好的文档。

我喜欢这个谜语的地方在于解决方案的多样性:一个利用了 JavaScript 的动态特性，使用了`Object.defineProperty`，另一个解决方案使用了遗留特性(document.all)，而`BigInt`解决方案使用了一个还没有引入到语言中的特性(但是在 Chrome 中已经有了)。纯粹的乐趣，不是吗？