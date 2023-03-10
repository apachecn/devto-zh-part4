# 扩展 Array 类以计算斐波那契数列

> 原文：<https://dev.to/bugmagnet/extending-the-array-class-to-calculate-fibonacci-sequences-50a0>

今天我学会了如何做错事。好吧，好吧，不完全是这样。我做错事已经很久很久了。

以扩展本机对象为例。普遍的看法是，这是一种不好的做法，应该避免，因为有各种好的理由。显然有更好的方法来做这件事，甚至[子类化](http://perfectionkills.com/how-ecmascript-5-still-does-not-allow-to-subclass-an-array/)。

所以，今天我学习了另一种处理斐波纳契数列的方法，正如你从 RosettaCode 链接中看到的，在许多不同的语言中有许多有趣的方法，如 T2。我的方式(还)不在那个集合中。也许有人会捷足先登。

所以我用一种方法扩展了`Array`对象，生成一个`n`长度的数组，用可选的代码块初始化每个成员。我想到了这个:

```
Array.prototype.times = function (a, c) {
    if (a)
        for (var b = 0; b < a; b++)
            this[b] = "function" === typeof c ? c(b, this) : b;
    return this
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后我开始思考测试它的方法，经历了一些不同的事情，最后偶然发现了斐波那契数列，它可以这样生成:

```
var fibonacci = [].times(81, function (i, array) {
    return (i < 2)
     ? i
     : array[i - 2] + array[i - 1];
});
print(JSON.stringify(fibonacci)); 
```

Enter fullscreen mode Exit fullscreen mode

产生斐波那契-0 到斐波那契-80。之后，JavaScript 失去了精确性，这个系列也就崩溃了。

不知何故，我不认为 MDN 会将`.times`添加到它的[数组对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)方法列表中，但我真的希望他们有类似的东西。与此同时，在我的小小互联网死水里，我将使用它。

底线:不要这样做。这是不好的做法。这可能会让你受到团队领导的斥责。它甚至可能加速世界末日。