# 在 Python 中格式化大数

> 原文：<https://dev.to/marktripney/formatting-large-numbers-in-python-1kf8>

## 简介

Python 解释器可以轻松处理非常大的数字。然而，一旦数字达到一定的大小，我们人类就很难读懂它们了。例如，在撰写本文时，7723151489 是对世界人口的一个(准确无误的)估计。这个数字对你来说可读性如何？当然，只要我们只是想使用它，我们可以照原样使用它，让解释器做它的事情。但是，如果我们需要向用户显示这些信息，就像我们可能在某个时候会做的那样，我们会希望以一种更加用户友好的格式来呈现这些信息。这就是我们在这里要看的。

让我们放大一点，想象我们正在编写一个简单的程序来存储国家信息，包括人口数量…

(本文将假设 Python 的格式化字符串文字(也称为 f 字符串)的一些基本知识。一个非常简单的例子可以作为引子:

```
name = "mark"
print(f"Hello, {name.title()}. You're looking great today!") 
```

在第二行，我们给一个字符串字面值(引号内的任何东西都是字符串字面值)加上前缀“f ”,在花括号内，包含一个“替换字段”。f 字符串在运行时被求值，从上面的例子中，我们得到下面的输出:

```
>>> Hello, Mark. You're looking great today! 
```

简单！)

* * *

## 数字文字中的下划线

我们将从以下内容开始，捕捉三个国家的一些重要信息…

```
countries = {
    "scotland": {"capital": "edinburgh", "population": 5438000},
    "belgium": {"capital": "brussels", "population": 11250000},
    "germany": {"capital": "berlin", "population": 82665600},
} 
```

我们已经可以看到，人口数字是相当难以扫描的。这对 Python 解释器来说不是问题，但是，使用我们的代码应该容易被*人*阅读的优秀格言，这是我们应该解决的问题。令人高兴的是，Python 3.6 使这变得简单明了——我们可以简单地引入下划线(有关这方面的更多信息，请查看[PEP 515——数字文字中的下划线](https://www.python.org/dev/peps/pep-0515/))。

```
countries = {
    "scotland": {"capital": "edinburgh", "population": 5_438_000},
    "belgium": {"capital": "brussels", "population": 11_250_000},
    "germany": {"capital": "berlin", "population": 82_665_600},
} 
```

好多了！但是，当我们显示这些信息时会发生什么呢？如果我们运行以下…

```
for country, info in countries.items():
    print(
        f"\n{country.title()}'s capital is {info['capital'].title()}. "
        f"{country.title()} has a population of around {info['population']}."
    ) 
```

…我们收到这个:

```
Scotland's capital city is Edinburgh. Scotland has a population of around 5438000.

Belgium's capital city is Brussels. Belgium has a population of around 11250000.

Germany's capital city is Berlin. Germany has a population of around 82665600. 
```

我们用下划线引入的格式没有被保留(PEP 515 解释说，“……下划线没有语义意义，文字被解析为下划线不存在”)，我们又回到了很难理解数字的问题上。“修复”非常简单，但是在我们开始之前，我们需要了解一点关于格式规范及其伴随的格式规范迷你语言。

* * *

## 格式规范和格式规范小语种

通常情况下，Python 提供了一种非常简单的本地方法来解决我们的问题——格式化字符串。它们允许我们在替换字段中使用“字段名”、“转换”和“格式规范”标识符以各种方式操作字符串。并非所有的都是必需的，事实上，这里我们唯一需要的是格式规范。

格式规范用冒号引入。我们在这个冒号后面加上几个选项中的任何一个，每个选项赋予我们的字符串一个特定的格式质量。这些选项由[格式规范小型语言](https://docs.python.org/3/library/string.html#formatspec)描述——顾名思义，它是格式规范字段中可供我们使用的语法。例如,“符号”选项允许我们指示是否所有数字都应该以相关的符号为前缀(正或负)；是否只有负数应该有符号(默认行为)；或者负数是否有负号，而正数前面有一个前导空格。这些例子如下面的代码所示……

```
>>> '{:+g}; {:+g}'.format(100, -50)
'+100; -50' // All numbers have a relevant sign
>>> '{:-g}; {:-g}'.format(100, -50)
'100; -50'  // Only negative numbers have a sign
>>> '{: g}; {: g}'.format(100, -50)
' 100; -50' // Positive numbers have a leading space 
```

选项是详尽的，我鼓励你看一看[规格](https://docs.python.org/3.7/library/string.html#format-specification-mini-language)的全部范围。

* * *

## 解

解决我们问题的方法很简单。我们在格式规范标识符(冒号)后面加上格式规范迷你语言的另一个选项——逗号(参见[PEP 378——千位分隔符的格式说明符](https://www.python.org/dev/peps/pep-0378/))。

```
for name, info in countries.items():
    print(
        f"\n{name.title()}'s capital city is {info['capital'].title()}. "
        f"{name.title()} has a population of around "
        f"{'{:,}'.format(info['population'])}."
    ) 
```

运行这段代码会产生格式完美的信息，我们的用户可以轻松阅读……

```
Scotland's capital city is Edinburgh. Scotland has a population of around 5,438,000.

Belgium's capital city is Brussels. Belgium has a population of around 11,250,000.

Germany's capital city is Berlin. Germany has a population of around 82,665,600. 
```

(注意，我们*可以*通过在格式规范标识符后使用下划线而不是逗号来精确复制我们的代码。)