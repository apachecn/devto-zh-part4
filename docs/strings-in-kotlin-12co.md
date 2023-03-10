# 科特林琴弦

> 原文：<https://dev.to/jay_tillu/strings-in-kotlin-12co>

*   字符串由 ***字符串*** 字表示。

*   字符串是不可变的。字符串的元素是可以被索引操作访问的字符:`s[i]`。

*   可以用 for 循环迭代字符串。

*   您可以使用+运算符连接字符串。这也适用于将字符串与其他类型的值连接起来，只要表达式中的第一个元素是字符串或字符。

*   如果表达式中的第一个元素是数字或布尔值，则不能执行字符串串联操作。

```
val s = “Hello” + 1234
val s = “Hello” + 11.14
val s = “Hello” + ‘J’
val s =   ‘J’    + “Hello”
val s = “Hello” + true

val s = 1234   +  “Hello”    //Compile time error.
val s = 43.24  +  “Hello”    //Compile time error.
val s = true   +  “Hello”    //Compile time error. 
```

Enter fullscreen mode Exit fullscreen mode

*   请注意，在大多数情况下，使用字符串模板或原始字符串比字符串串联更可取。

## 字符串文字

* * *

Kotlin 有两种类型的字符串:

1.  转义字符串
2.  Raw strings

#### 转义字符串

*   **转义字符串—** 它们可能包含转义字符。转义字符串非常像 java 字符串。

```
val s = “Hello World \n” 
```

Enter fullscreen mode Exit fullscreen mode

*   转义是以传统的方式完成的，用一个反斜杠。

#### 原始字符串

*   **原始字符串—** 包含换行符和任意文本的原始字符串。
*   由三重引号(" ")分隔的原始字符串不包含转义符，并且可以包含换行符和任何其他字符:

```
val text = """ This is a Example
       Look How concise it is
       That is the magic of kotlin
""" 
```

Enter fullscreen mode Exit fullscreen mode

*   `trimMargin()`是在原始字符串中缩进的推荐方式。

```
val text = """
       | Tell me and I forgot.
       | Teach me and I remember.
       | Involve me and I learn.
       | (Benjamin Franklin)
    """.trimMargin() 
```

Enter fullscreen mode Exit fullscreen mode

*   默认情况下，`|`被用作边距前缀，但是您可以选择另一个字符并将其作为参数传递，比如`trimMargin(">")`。你也可以使用`trimIndent()`。

*   如果你需要在一个原始字符串中表示一个字面上的`$`字符(不支持反斜杠转义)，你可以使用下面的语法:

```
val price = """
${'$'}9.99
""" 
```

Enter fullscreen mode Exit fullscreen mode

伙计们，科特林的弦乐到此为止。如果我错过了什么，请随时告诉我。

在那之前，继续编码，继续爱。在另一篇文章中向您介绍。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)