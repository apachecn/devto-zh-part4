# 深入了解 Kotlin 琴弦

> 原文：<https://dev.to/joshuamdeguzman/getting-in-depth-with-kotlin-strings-33me>

#### 简介

字符串是编程语言中的一种基本数据类型。字符串由类型`String`表示。字符串是不可变的。Kotlin 字符串或多或少类似于 Java 字符串，但是 Kotlin 有更多的 API 来处理字符串。

###### 本文将涵盖:

*   [简介](#introduction)
    *   [本文将涵盖:](#this-article-will-cover)
*   [字符串基本用法](#string-basic-usage)
    *   [申报](#declaration)
    *   [字符串串联](#string-concatenation)
    *   [多行字符串](#string-with-multiple-lines)
*   [访问字符串的字符](#accessing-characters-of-a-string)
    *   [索引访问](#index-access)
    *   [遍历一个字符串](#iterate-through-a-string)
*   [字符串的不变性](#immutability-of-a-string)
    *   [重新分配字符串值](#re-assigning-string-values)
*   [字符串属性](#string-properties)
    *   [确定字符串的长度](#determining-length-of-a-string)
*   [字符串函数](#string-functions)
    *   [比较](#compareto)
    *   [等于](#equals)
    *   [获取](#get)
    *   [toString](#tostring)
*   [贡献](#contribution)

#### 字符串的基本用法

##### 申报

```
// Explicit type declaration
var firstName : String = "Elon"

// or Implicit type declaration and will still compile
val lastName = "Musk" 
```

Enter fullscreen mode Exit fullscreen mode

此外，注意`val`变量类型的用法，这里是它如何表现

```
firstName = "Mark" // can be changed
lastName = "Zuckerberg" // cannot be changed
lastName = 12 // Error: type mismatch 
```

Enter fullscreen mode Exit fullscreen mode

`val`关键字表示变量是只读的。阅读[科特林变量](https://antonioleiva.com/variables-kotlin/)。

Kotlin 有一个空安全类型系统，在你编译你的应用程序之前，它就已经给出了可能为空值的指示。

对于一个`String`，这里是如何做到这一点

```
var firstName: String? = "Joshua"
var lastName: String = "de Guzman"

firstName = null // will compile
lastName = null // will not compile 
```

Enter fullscreen mode Exit fullscreen mode

注意到`firstName`的类型了吗？它有一个显式类型`String?`，因此它表明变量是可选的，可以包含一个`String`对象或一个`null`值。阅读[科特林为空性](https://kotlinlang.org/docs/reference/null-safety.html)。

##### 字符串串联

如代码片段所示，就像 Java 一样，将`Int`附加到
`String`将导致`String`输出

```
// Declaration
var str = "abc" + 1
println(str + "def")

// Output
$ abc1def 
```

Enter fullscreen mode Exit fullscreen mode

即使没有首先显式地将`Int`值 1 转换为`String`对象，结果输出仍然是一个`String`。

在 Kotlin 中，您还可以使用`StringBuilder`
进行连接

```
// Declaration
val a = "Hello"
val b = "World"

val sb = StringBuilder()
sb.append(a).append(b)
val str = sb.toString()

println(str)

// Output
$ HelloWorld 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用 Kotlin 字符串模板，这将在
下面的[中进一步讨论](#kotlin-string-templates)

```
// Declaration
val a = "Hello"
val b = "World"
val str = "$a $b"

println(str)

// Output
$ Hello World 
```

Enter fullscreen mode Exit fullscreen mode

##### 多行字符串

程序员可以用三引号代替双引号
来声明多行的`String`变量

```
// Declaration
var str = """
        This is line 1
        This is line 2
        This is line 3
        """
println(str)

// Output
$        This is line 1
$        This is line 2
$        This is line 3 
```

Enter fullscreen mode Exit fullscreen mode

或者用`.trimIndent()`

使用`trimIndent()`将有助于通过移除每行上多余和不必要的缩进来提供清晰的输出格式。检查下面的代码片段:

```
// Declaration
var str = """
        This is line 1
        This is line 2
        This is line 3
        """.trimIndent()
println(str)

// Output
$ This is line 1
$ This is line 2
$ This is line 3 
```

Enter fullscreen mode Exit fullscreen mode

#### 访问字符串中的字符

##### 索引访问

程序员可以使用索引访问操作符:
来访问字符串中的元素(字符)

```
// Declaration
var str = "Example"
println(str[0]) // first character of the string
println(str[str.length-1]) // last character of the string

// Output
$ E
$ e 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用扩展功能
检索字符

```
// Declaration
var str = "Example"
println(str.first()) // retrieves the first character in a string
println(str.last()) // retrieves the last character in a string

// Output
$ E 
$ e 
```

Enter fullscreen mode Exit fullscreen mode

就像从数组中访问一个元素一样，在 Kotlin 字符串中，当被访问的字符超出范围
时，你也可以有一些`IndexOutOfBoundsException`

```
// Declaration
var str = "Example"
println(str[9]) 

// Output
// Error: index out of bounds 
```

Enter fullscreen mode Exit fullscreen mode

也可以在 Kotlin 中使用`String.get()`，相当于 Java 的`String.chartAt()`

```
// Declaration
var str = "Example"
var char = str.get(6)
println(char)

// Output
$ e 
```

Enter fullscreen mode Exit fullscreen mode

##### 遍历一个字符串

`String`的元素是可以通过索引操作访问的字符:`s[i]`

```
// Declaration
var str = "Example"
for (c in str) {
    println(c)
}

// Output
$ E
$ x
$ a
$ m
$ p
$ l
$ e 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 还允许你使用迭代器的扩展函数来对一个`String`，例如`forEach`

```
// Declaration
var str = "Hello"
str.forEach { c ->
    println(c)
}

// Output
$ H
$ e
$ l
$ l
$ o 
```

Enter fullscreen mode Exit fullscreen mode

#### 字符串的不变性

就像 Java 一样，你不能改变一个`String`
的单个元素

```
// Declaration
var str = "Example"
str[2] = "b" // Error 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您希望替换`String`
中的特定字符，您可以使用一些操作，如`replace()`

```
// Declaration
var str = "Example"
str = str.replace("m", "p")
println(str)

// Output
$ Exapple 
```

Enter fullscreen mode Exit fullscreen mode

##### 重新分配字符串值

```
// Declaration
var str = "Example"
println(str)
str = "Example was changed"
println(str)

// Output
$ Example
$ Example was changed 
```

Enter fullscreen mode Exit fullscreen mode

#### 字符串属性

##### 确定一个字符串的长度

`length`返回一个字符序列的长度

```
// Declaration
var str = "Example"
println(str.length)
println(str.length - 5) // with length() function returning Int, you can do arithmetic operations just like any other Integer value

// Output
$ 7
$ 2 
```

Enter fullscreen mode Exit fullscreen mode

#### 字符串函数

这些是当前 Kotlin 版本中一些常见的`String`函数

##### 比较

将此对象与指定的对象进行比较以确定顺序。如果这个对象等于指定的另一个对象，则返回零；如果小于另一个对象，则返回负数；如果大于另一个对象，则返回正数

```
// Declaration
var str = "Example"
var str2 = "Example123"
var str3 = "Example12345"
println(str.compareTo(str2)) // str has fewer (3) characters than str2
println(str.compareTo(str3)) // str has fewer (5) characters than str3
println(str3.compareTo(str)) // str3 has more (5) characters than str
println(str.compareTo("Example")) // zero denotes that the String objects being compared are equal

// Output
$ -3
$ -5
$ 5
$ 0 
```

Enter fullscreen mode Exit fullscreen mode

##### 等于

指示一个`String`对象是否完全等于另一个`String`对象

```
// Declaration
var str = "Example"
var str2 = "Example2"
println(str.equals("Example"))
println(str2.equals("Example"))

// Output
$ true
$ false 
```

Enter fullscreen mode Exit fullscreen mode

##### 得到

返回该字符序列中指定索引处的字符

```
// Declaration
var str = "Example"
println(str.get(3))

// Output
$ m 
```

Enter fullscreen mode Exit fullscreen mode

##### 字符串

返回对象
的字符串表示

```
// Declaration
println(9.toString() + 10)
println(9 + 10)

// Output
$ "910"
$ 19 
```

Enter fullscreen mode Exit fullscreen mode

#### 贡献

这里的是我为[自由代码营](https://guide.freecodecamp.org)写的这篇文章的简化版。
[这个](https://github.com/freeCodeCamp/guide/pull/9928)是对我的文章提交请求的引用。

如有疑问或建议，欢迎评论或联系我。