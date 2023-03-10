# Kotlin 中的字符和布尔

> 原文：<https://dev.to/jay_tillu/char-and-boolean-in-kotlin-27ok>

## 字符类型

* * *

*   字符由 ***Char*** 关键字表示。
*   它们不能被直接视为数字，但您可以根据需要执行显式转换函数。让我们通过例子来理解这一点。

```
char a = 65  // Compile time error.
This code works fine in Java but will throw an error in kotlin. 
```

Enter fullscreen mode Exit fullscreen mode

*   字符文字用单引号括起来，如*、【V】、*、【J】、*等。*

*   在 kotlin 中，字符串由 *String* 类表示。字符串文字放在双引号内，如*【Viveki】**【Jay】*等。

*   特殊字符可以使用反斜杠进行转义。

***支持以下转义序列:***

*   \t —打印选项卡
*   \b —使用反斜杠
*   \n —换行符
*   \r —回车
*   \ '-打印单引号
*   \ "-打印双引号
*   \-打印反斜杠
*   \$ —打印美元符号
*   使用 Unicode 转义序列语法对十六进制或二进制等任何其他字符进行编码: *'\uFF004'* 。

## 布尔型

* * *

*   布尔值由 Boolean 关键字表示。
*   布尔值有两个值，不是真就是假。两者都必须是小写，并且不能表示为 0 和 1。

伙计们，这就是科特林中的 char 和 boolean。如果我错过了什么，请随时告诉我。

在那之前，继续编码，继续爱。在另一篇文章中向您介绍。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)