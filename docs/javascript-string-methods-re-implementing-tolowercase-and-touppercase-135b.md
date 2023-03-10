# Javascript 字符串方法:重新实现 toLowerCase 和 toUpperCase💪🏾

> 原文：<https://dev.to/thechinedu/javascript-string-methods-re-implementing-tolowercase-and-touppercase-135b>

JavaScript 中的`toLowerCase`和`toUpperCase`方法是字符串方法，
转换一个给定的字符串，根据调用的方法，使它的所有字符或者小写或者大写。在这篇文章中，我将讨论
如何实现这两个方法的功能，而不直接使用该语言提供的
方法。我们开始吧！

每个字符串字符都可以表示为一个在`0`和`65535`之间的唯一整数。
对于任何特定的字符串字符，映射到字符
的整数代表该字符的 UTF-16 编码。JavaScript 提供了一种简单的
方法，通过 charCodeAt
方法获得任何字符串字符的字符代码。charCodeAt 接受一个表示索引的参数，我们
希望返回该索引的字符代码。这一切都很好，但是我们如何从整数表示中得到一个
字符呢？Javascript 通过静态的`String.fromCharCode`方法让我们了解了这些；`String.fromCharCode`获取一个
字符代码列表，并给出给定
字符代码的相关字符串表示。

我们现在拥有了重新实现`toLowerCase`和
方法所需的一切。我们的重点是转换英文字母表中
字母的大小写。

为了将英文字母表中一个字母的大小写转换成另一个字母的大小写，
我们需要做的是根据我们想要的功能，给字符代码加上或减去 32。我们使用 32 的原因不是因为给
加 10 会得出生命意义的答案，不，我们使用 32 的原因是
，因为在两种
情况下，一个字母的字符代码之间的绝对差值是 32。稍微解释一下——英文字母表中小写字母
的字符代码是 97 到 122 之间的整数，大写字母
的字符代码是 65 到 90 之间的整数。如果我们在两个范围的
相同索引处取一个整数，并求绝对差值，我们将得到 32。
去吧，试试看！

所有这些都结束了，让我们看看一些代码，好吗？

```
const convertCase = (str, charCase = "lowercase") => {
  if (charCase !== "lowercase" || charCase !== "uppercase") charCase = "lowercase";

  let res = "";
  let actions = {
    lowercase: char => {
      const res = String.fromCharCode(char.charCodeAt() + 32);
      return char.match(/[A-Z]/) ? res : char;
    },
    uppercase: char => {
      const res = String.fromCharCode(char.charCodeAt() - 32);
      char.match(/[a-z]/) ? res : char;
    }
  };

  for (let i = 0; i < str.length; i += 1) {
    let char = str[i];
    res += actions[charCase](char);
  }

  return res;
}

const toLowerCase = str => convertCase(str, "lowercase");
const toUpperCase = str => convertCase(str, "uppercase");

console.log(toLowerCase("HELLO WORLD")); // hello world
console.log(toUpperCase("hello world")); // HELLO WORLD 
```

Enter fullscreen mode Exit fullscreen mode

我们的`toLowerCase`函数和`toUpperCase`函数都调用了传入相关参数的`convertCase`
函数。

让我们浏览一下`convertCase`函数。该函数有两个参数，
第一个是我们要转换大小写的字符串，第二个参数
是一个表示我们要转换成哪种大小写的字符串。在
函数的最顶端，我们添加了一个 guard 子句，以确保第二个参数是我们定义的有效的
值。

接下来我们用一个变量声明来存储 case
转换的结果，没有什么特别的。actions 对象存储了两个函数，根据传递给第二个参数的值，
将调用这两个函数——如果没有传递参数或者提供了无效的值，那么
将默认为小写。
actions 对象中的 lowercase 方法使用一个正则表达式
检查一个字符是否是大写的，如果是，它将它转换成
小写——大写函数做相反的事情。

我们遍历提供的字符串中的字符，调用 actions 对象中相关的
函数，并将值追加到结果变量中。

最后，我们返回转换案例的结果！

*原载于[我的个人网站](https://chinedudaniel.com)*