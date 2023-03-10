# TIL: String .原型. replace 支持替换模式

> 原文：<https://dev.to/stefanjudis/til-string-prototype-replace-supports-replacement-patterns-4j3o>

今天我偶然看到[彼得·克纳](https://twitter.com/sir_pepe)写的一篇博文(虽然是德语的),并了解到一些非常惊人的事情。

这篇文章描述了方法`String.prototype.replace`不太为人所知的行为。使用这种方法通常非常简单。如果需要，它支持正则表达式，但在大多数情况下，它定义了一个匹配字符串和一个替换字符串。

至少我是这么想的...🙈

原来第二个参数是一个字符串(它也可以是一个函数),包括特定的字符序列，如`$&`或`$'`。

```
const msg = 'This is a great message';

msg.replace('great', 'wonderful'); 
// "This is a wonderful message"
//
// -> 'great' is replaced by 'wonderful'

msg.replace('great', '$&-$&');
// "This is a great-great message"
// '$&' represents the matched substring
//
// -> 'great' is replaced by 'great-great'

msg.replace('great', '$`');
// "This is a This is a  message"
// '$`' represents the string before the matched string
//
// -> 'great' is replaced by 'This is a '

msg.replace('great', `$'`)
// "This is a  message message"
// `$'` represents the string after the matched string
//
// -> 'great' is replaced by ' message' 
```

Enter fullscreen mode Exit fullscreen mode

天哪...，这种行为会导致很难发现错误！

如果你想了解更多，看看 MDN 上的[`replace`文档。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace#Specifying_a_string_as_a_parameter)