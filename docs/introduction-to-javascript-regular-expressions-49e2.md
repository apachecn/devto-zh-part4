# JavaScript 正则表达式简介

> 原文：<https://dev.to/attacomsian/introduction-to-javascript-regular-expressions-49e2>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/javascript-regular-expressions)。*

* * *

正则表达式(也称为 **regex** 或 **regexp** )是定义搜索模式的字符序列。这些搜索模式通常用于**文本搜索**、**文本搜索和替换**、**数据提取**和**输入验证**操作。

就像其他编程语言一样，JavaScript 也支持正则表达式对字符串执行模式匹配和搜索替换功能。JavaScript 将正则表达式视为具有预定义属性和方法的对象。

## 语法

正则表达式可以由一个或多个元字符和文字字符组成。

```
/pattern/modifiers; 
```

例如，`/javascript/i`是一个正则表达式，其中`javascript`是一个搜索模式，`i`是一个修饰符。

## 创建正则表达式

在 JavaScript 中，可以通过两种方式创建正则表达式:使用正则表达式文字或调用`RegExp`的构造函数来初始化新对象。

```
var re1 = /java+script/g;
var re2 = new RegExp('java+script', 'g'); 
```

在文字形式中，正则表达式在脚本加载时编译。如果模式保持不变，正则表达式文字在性能方面会更好。

另一方面，使用构造函数创建的正则表达式是在运行时编译的，应该只在您确定模式是动态的时候使用。

## 测试正则表达式

你可以使用`RegExp`对象的`test()`方法来快速测试一个正则表达式。此方法搜索正则表达式和指定字符串之间的匹配项。如果找到匹配，它返回`true`，否则返回`false`。

```
var re = /java+script/gi;
re.test('javascript'); // true
re.test('JavaScript is a client side programming language'); // true
re.test('java'); // false 
```

另一种测试正则表达式的方法是使用`RegExp`对象的`exec()`方法。它在目标字符串中搜索匹配，如果找到就返回一个数组，否则返回`null`。

```
/^The/.exec('The Apple')
// ["The", index: 0, input: "The Apple", groups: undefined] 
```

## 正则表达式修饰符

修饰符(也称为标志)是特殊字符，可用于执行不区分大小写的更高级的搜索。

*   `i`执行不区分大小写的匹配
*   `g`执行全局匹配(找到第一个匹配后不停止，并找到所有可能的匹配)
*   `m`执行多行匹配
*   `u`启用 unicode 字符匹配(在 ES6 中引入)
*   `s`(也称为“dotAll”)允许`.`匹配换行符(在 ES9 中引入)

可以组合标志来执行复杂的匹配操作:

```
var re1 = /java+script/gi;
var re2 = new RegExp('java+script', 'gi'); 
```

## 正则表达式模式

正则表达式模式由简单字符(如`/javascript/`)或简单和特殊字符(如`/java*script/`)的组合组成。简单字符用于直接匹配。例如，简单模式`/bcd/`仅当字符‘BCD’以完全相同的顺序一起出现时才匹配字符串中的字符组合。

```
/bcd/g.test('Who is this bcd?') // exact match substring bcd 
```

特殊字符用于匹配比文字字符串更广泛的值。例如，要匹配一个“a”后面跟着一个或多个“b”后面跟着“d”，我们可以使用模式`/ab+d/`。“b”后面的`+`表示“前一项出现 1 次或多次。”

```
/ab+d/g.test('aabbbdccbbbd') // match substring abbbd 
```

下表提供了特殊字符的完整列表，以及可以在正则表达式中使用的示例:

### 断言

断言表明以某种方式匹配是可能的。断言包括**前瞻**、**后视**和**条件表达式**。

> `?`字符也可以用作量词。

| 特性 | 例子 | 描述 |
| --- | --- | --- |
| `x(?=y)` | `/Atta(?=shah)/` | 前瞻性断言。仅在`y`之后匹配`x`。 |
| `x(?!y)` | `/\d+(?!\.)/` | 否定前瞻断言。只有在`y`之后才匹配`x`。 |
| `(?<=y)x` | `/(?<=shah)Atta/` | 回顾性断言。仅在`y`之前匹配`x`。 |
| `(?<!y)x` | `/(?<!-)\d+/` | 消极的回顾性断言。仅当前面没有`y`时才匹配`x`。 |

在断言中，只有`x`是匹配的一部分。例如，`/Europe+(?=Germany|France)/`只有在后跟“德国”或“法国”时才匹配“欧洲”。然而，“德国”和“法国”都不是比赛结果的一部分。

```
/Europe+(?=Germany|France)/.test('EuropeGermany') // matches "EuropeGermany"
/(?<!-)\d+/.test('45.99') // matches "45" 
```

### 界限

边界表示行和词的开始和结束。

| 特性 | 例子 | 描述 |
| --- | --- | --- |
| `^` | `/^An/` | 匹配输入的开始 |
| `$` | `/App$/` | 匹配输入的结尾 |
| `\b` | `/un\b/` | 匹配单词边界 |
| `\B` | `/\Bon/` | 匹配非单词边界 |

```
/^An/.test('An Apple') // matches "An"
/App$/.test('Mobile App') // matches "App" 
/un\b/.test('Sun') // matches "un"
/\Bon/.test('Moon') // matches "on" 
```

### 分组和范围

组和范围有助于查找一系列字符。

*   `(x|y)`匹配`x`或`y`。比如`/(green|red)/`匹配《红牛》中的“红”。
*   `[abcd]`匹配任何一个括起来的字符。相当于`[a-d]`。
*   `[^abcd]`不匹配任何包含的字符。相当于`[^a-d]`。
*   `[0-9]`匹配任何一个括起来的字符(任何数字)。
*   `[^0-9]`不匹配任何包含的字符(任何非数字)。

```
/[a-z]/.exec('a2') // matches "a"
/[0-9]/.exec('a2') // matches "2"
/[a-z0-9]/.exec('$a2') // matches "a" 
```

### 人物类

字符类(也称为**元字符**)是具有特殊含义的字符，用于区分各种字符。例如，区分字母和数字。

| 特性 | 例子 | 描述 |
| --- | --- | --- |
| `.` | `/.s/` | 匹配任何单个字符，除了换行符或行终止符 |
| `\w` | `/\w/` | 匹配任何字母数字字符，包括下划线。相当于`[A-Za-z0-9_]`。 |
| `\W` | `/\W/` | 匹配任何非字母数字字符。相当于`[^A-Za-z0-9_]`。 |
| `\d` | `/\d/` | 匹配任何数字。相当于`[0-9]`。 |
| `\D` | `/\D/` | 匹配任何不是数字的字符。相当于`[^0-9]`。 |
| `\s` | `/\s/` | 匹配单个空白字符 |
| `\S` | `/\S/` | 匹配除空白以外的单个字符 |
| `\t` | `/\t/` | 匹配一个制表符 |
| `\n` | `/\n/` | 匹配换行符 |
| `\0` | `/\0/` | 匹配一个 NUL 字符 |
| `\uxxxx` | `/\uxxxx/` | 匹配 unicode 字符 |

```
/.s/.test('yes') // matches "es"
/\d/.test('3D') // matches "3"
/\w/.test('$9.99') // matches "9"
/\W/.test('45%') // matches "%" 
```

### 量词

量词定义数量并指示要匹配的字符或表达式的数量。

| 特性 | 例子 | 描述 |
| --- | --- | --- |
| `n+` | `/ab+/` | 匹配任何至少包含一个`n`的字符串 |
| `n*` | `/ab*/` | 匹配包含零个或多个`n`的任何字符串 |
| `n?` | `/ab?/` | 匹配任何包含零或一的字符串`n` |
| `n{x}` | `/a{2}/` | 精确匹配前一项`n`的`x`次出现 |
| `n{x,}` | `/a{2, }/` | 匹配前面项目`n`的至少`x`个事件 |
| `n{x,y}` | `/a{1,3}/` | 匹配前一项`n`的至少`x`次和最多`y`次出现 |

```
/ab+/.test('abbcdab') // matches "abb"
/bb*/.test('abbcdab') // matches "bb"
/b{2,}/.test('abbcdab') // matches "bb"
/a{1,3}/.test('bbcdaaab') // matches "aaa" 
```

## 正则表达式转义

如果您想使用任何特殊字符作为文字(比如搜索一个'+')，您需要通过在它们前面加一个反斜杠来转义它们。例如，要搜索' a '后面跟着'+'后面跟着' c '，您可以使用`/a\+b/`。反斜杠“转义”了“+”，使其成为文字而不是特殊字符。

```
/\d\+\d/.test('2+2') // true
/\$\d/.test('$2.49') // true 
```

## 正则表达式用法

在 JavaScript 中，正则表达式与`RegExp`的对象方法`exec()`和`test()`，以及`String`的`match()`、`replace()`、`search()`和`split()`方法一起使用。

```
var str = "JavaScript is a client-side programming language";
str.search(/client/i)
//16 (the starting position of "client")
str.replace(/client/i, 'server')
//JavaScript is a server-side programming language
str.match(/client/i)
//["client", index: 16]
str.split(/\s/i)
//["JavaScript", "is", "a", "client-side", "programming", "language"] 
```

以上只是使用正则表达式进行搜索和替换操作的几个例子。它们也用于 JavaScript:
中的输入验证和数据提取

```
// extract number from a string
'I paid $45.99'.match(/\d+\.*\d*/) //["45.99", index: 8]

// validate date in dd-mm-yyyy format
/(\d{2})-(\d{2})-(\d{2,4})/.test('23-12-89') // true 
```

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。