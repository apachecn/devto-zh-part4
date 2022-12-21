# Kotlin 扩展函数与带接收器的函数文字

> 原文：<https://dev.to/frevib/kotlin-extension-function-vs-function-literal-with-receiver-411d>

# Kotlin 扩展函数和带接收器的函数文字

在 Kotlin 中，可以向现有类添加一个方法(在 Kotlin 中称为成员函数)。这被称为*扩展函数*。

也有可能从函数文本内部的类中访问成员函数。这被称为带有接收者的*函数。*

# 扩展功能

假设您想要向`StringBuilder`类添加一个新的成员函数，例如`appendMonkey()`将字符串“monkey”附加到一个字符串中。因为`StringBuilder`在 Java SDK 中，我们不能修改它。在 Kotlin 中，我们可以定义一个扩展函数，用一个新的成员函数来扩展现有的类，比如`StringBuilder`。

我们将`appendMonkey()`扩展函数定义如下:

```
fun StringBuilder.appendMonkey(): StringBuilder = this.append("monkey") 
```

`this`指的是`StringBuilder`对象，我们可以省略:

```
fun StringBuilder.appendMonkey(): StringBuilder = append("monkey") 
```

完整示例:

```
import java.lang.StringBuilder

fun main() {
    val sb = StringBuilder()

    sb.append("Hello extension function ")
    sb.appendMonkey()

    println(sb.toString())
}

fun StringBuilder.appendMonkey(): StringBuilder = append("monkey") 
```

# 带接收器的函数文字

与*扩展函数*密切相关的是带有接收者的*函数。有两种类型的函数文字:*

*   希腊字母的第 11 个
*   匿名函数

实际上，你最有可能[使用 lambda 表达式，所以我们在下面的例子中使用它。](https://kotlinlang.org/docs/reference/lambdas.html#anonymous-functions)

使用扩展函数，你可以在现有的类中添加一个新的成员函数，使用带有接收者的*函数，你可以在*的 lambda 块中(在花括号`{}`中)访问现有类的成员函数*。*

让我们用接收者创建一个*函数文本，它使用`StringBuilder`类将字符串“monkey”添加到一个字符串中。* 

```
val lambdaAppendMonkey: StringBuilder.() -> StringBuilder = { this.append("monkey") } 
```

再次，`this`指的是`StringBuilder`对象，所以我们省略:

```
val lambdaAppendMonkey: StringBuilder.() -> StringBuilder = { append("monkey") } 
```

完整示例:

```
 import java.lang.StringBuilder

fun main() {
    val lambdaAppendMonkey: StringBuilder.() -> StringBuilder = { append("monkey") }

    val sb = StringBuilder()
    sb.append("Hello lambda ")

    println(lambdaAppendMonkey(sb).toString())
} 
```

在这里，在 lambda 的块内部，我们可以访问`append()`成员函数，因为我们显式地将返回类型声明为`StringBuilder.() -> StringBuilder`。这个返回类型基本上是说:

“返回一个不带参数并返回一个`StringBuilder object`、**和**的函数，让这个函数访问`StringBuilder`成员函数”。

祝你好运！