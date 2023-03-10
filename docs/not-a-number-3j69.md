# 不是数字

> 原文：<https://dev.to/atila/not-a-number-3j69>

# 查楠很棘手

首先，查起来简直不是小事。

```
NaN === NaN // false 
```

这是其中一个这样的头像。长话短说，浮点数有一个[规范](https://en.wikipedia.org/wiki/IEEE_754)，它决定了`NaN`的值永远不相等。

然后，很长一段时间我们只有`isNaN()`方法，它涵盖了上述情况，但它会给你带来一些意想不到的结果。

```
isNaN(NaN) // true
isNaN('foo') // true 
```

那是令人困惑的。为了解决这个问题，在 ES5 时代，人们通常会这样做:

```
const isReallyNaN = value => {
  const n = isNaN(value)
  return n !== n
} 
```

幸运的是，自从 ES6 出现以来，我们现在在`Number`原型中有了一个新的方法，它在所有情况下都是一致的:

```
Number.isNaN('foo') // false
Number.isNaN(NaN) // true 
```

重要的是要注意，`isNaN`和`Number.isNaN`都是可用的，注意不要混淆！💥

## 参考文献

*   [MDN 上的`isNaN`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/isNaN)
*   [MDN 上的`Number.isNaN`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN)