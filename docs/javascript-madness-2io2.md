# Javascript 疯狂-完美的正方形

> 原文：<https://dev.to/bugmagnet/javascript-madness-2io2>

今天，我再次意识到 [TMTOWTDI](https://en.wikipedia.org/wiki/There%27s_more_than_one_way_to_do_it) (有不止一种方法可以做到这一点)，这对 JavaScript 和 Perl 都是如此。

感谢 dev.to 上的某个人，我发现了 [CodeWars](https://www.codewars.com) 是多么酷，并且已经花了一些快乐的时间来解决各种形。

今天他们中的一个要求我建立一个函数，根据参数是否是完美的正方形返回真或假。

我不会给你看我的解决方案。我将向你展示一个 TMTOWTDI 解，即

```
const isSquare = x => !Math.sqrt(x).toString().split(".")[1] 
```

Enter fullscreen mode Exit fullscreen mode

这是它的意思:

1.  得到自变量的平方根
2.  将其转换为字符串
3.  小数点后拆分
4.  获取拆分结果的第二个元素
5.  对表达式的结果求反(用`!`)

现在，如果平方根不是一个整数，那么第二个元素中会有一些东西，否定会转化为`false`。然而，如果平方根*是一个整数*，那么第二个元素将是`undefined`,`!`将把它转换成`true`。

由此:

```
Lychen> isSquare(81)
True
Lychen> isSquare(82)
False 
```

Enter fullscreen mode Exit fullscreen mode

也许某处有一个 TIOOWTDI(只有一个...)语言但 JavaScript 不是。