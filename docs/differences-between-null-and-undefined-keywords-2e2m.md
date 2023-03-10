# “空”和“未定义”关键词的区别？

> 原文：<https://dev.to/nunocpnp/differences-between-null-and-undefined-keywords-2e2m>

它们都代表一个空值。

## ***差 nr 1！***

当你定义了一个变量但没有给它赋值时，它会自动放置一个占位符叫做 **undefined** 这样你就不用手动去做了，JavaScript 会帮你做的。

**Null** 表示空值或不存在的值。

**Null** 被赋值，明确表示什么都没有。而**未定义**通常意味着一个变量已经被声明但尚未定义。

```
var a;
console.log(a);
// undefined

var b = null;
console.log(b);
// null 
```

## ***差 nr 2！***

**Null** 和 **undefined** 都是<u>原语和 falsy 值</u>。然而 **null** 也是一个对象。有趣的是，这实际上是原始 JavaScript 实现中的一个错误。

```
var a;
console.log(typeof(a));
// undefined

var b = null;
console.log(typeof(b));
// object 
```

## ***差 nr 3！***

到目前为止，你可以看到， **null** 和 **undefined** 是不同的，但是有一些相似之处。因此， **null** 不严格等于 **undefined** 是有意义的。

```
console.log(null !== undefined);
// true 
```

但是，这可能会让你吃惊， **null** 大致等于**未定义的**。

```
console.log(null == undefined);
// true 
```

在 JavaScript 中，double equals 测试松散等式和预成型类型强制。这意味着我们在将两个值转换成一个公共类型后对它们进行比较。

*稍后见，了解更多提示！*