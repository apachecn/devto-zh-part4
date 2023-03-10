# Javascript 中的前瞻

> 原文：<https://dev.to/savagepixie/lookaheads-in-javascript-5d4j>

今天我面临一个非常有趣的练习。我必须创建一个函数，以一个字符串作为它的参数，并返回相同的字符串，但用 spinal tap case 编写(这显然与[烤肉串案](https://en.m.wiktionary.org/wiki/kebab_case)相同或非常相似，就像这样)。在此之前，没有什么可害怕的，我知道我可以把它带走。当我不得不将一根像`"ThisIsAString"`这样的绳子转变成丝锥时，挑战就来了。我的第一个想法是*我究竟如何将一个字符串除以它的大写字母**并同时保持大写字母**？*

我的问题的答案是前瞻。不久前我已经了解了它们，但并没有真正理解它们。所以这个练习是学习如何使用它们的绝佳机会。

练习的第一部分非常简单。所以我就放在这里，不多说了。这将创建一个数组来分隔有空格或下划线的地方。然后将它再次连接成一个字符串，用连字符分隔每个元素，然后将其全部转换为小写。很简单。

```
const toSpinal = str => str.split(/\s|_/).join('-').toLowerCase() 
```

正如您已经注意到的，当您使用`split()`将一个字符串转换成一个数组时，它也会删除标记元素之间边界的字符。所以在我们的例子中，字符串`"I Like_Potatoes"`会变成`"i-like-potatoes"`。

### 那么，这些前瞻者呢？

做完这个冗长的介绍，让我们回到手头的问题。到目前为止，我的函数对任何不是`"ILikePotatoes"`的东西都很有效。但是为了恰当地处理这样的字符串，我知道我不能简单地使用像`[A-Z]`这样的正则表达式，因为那样也会删除大写字母。所以我会得到类似于`"--ike-otatoes"`的东西。

这就是前视头派上用场的地方。Lookaheads 允许我们检查一个表达式 A 后面是否跟有另一个表达式 B，只有 B 跟在(或者不跟在)它后面，它才会返回 A，但是它会让 B 不匹配，不动。我们用`(?=)`写一个前瞻(或者如果我们想让表达式**而不是**跟在前瞻后面，就用`(?!)`)。

```
const regEx = /[A-Z](?=\W)/g
const str = "A. Potato, B- Onion, C: All of the above"
str.match(regEx) // returns A, B, C 
```

lookahead `(?=\W)`告诉程序在前面的表达式之后寻找任何非单词字符。因此，所有大写字母是**而不是**后跟一个非单词字符，将不会返回一个匹配。但是请注意，`match()`只返回三个大写字母 A、B 和 C，而不是它们后面的字符。

如果我们想让字符串返回一个大写字母的匹配，这些字母是**而不是**后面跟一个非单词字符，我们可以简单地使用一个负的 lookahead ( `(?!)` ):

```
const regEx = /[A-Z](?!\W)/g
const str = "A. Potato, B- Onion, C: All of the above"
str.match(regEx) // returns P, O, A 
```

如果我们写一个带有前瞻的正则表达式，但是没有前一个表达式的**，它将匹配所有与前瞻匹配的，但是返回空值。当我们想把弦`"ILikePotatoes"`变成脊椎敲击盒:
时，我们可以利用这一点**

```
const toSpinal = str => str.split(/\s|_|(?=[A-Z])/).join('-').toLowerCase() //Note that the lookahead comes after a | so it isn't attached to the previous expression

console.log(toSpinal("I like potatoes")) //i-like-potatoes
console.log(toSpinal("ILikeCarrots")) //i-like-carrots
console.log(toSpinal("ThisIsA_really We1rdString")) //this-is-a-really-we1rd-string 
```

### 结论

Lookaheads 允许我们根据后面的内容来定义一个表达式是否匹配。因此，只有当表达式本身和前瞻匹配时，它才会匹配表达式。但它将只返回表达式，而不进行前瞻。