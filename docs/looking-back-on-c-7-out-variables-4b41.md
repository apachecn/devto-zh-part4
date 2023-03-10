# 回顾 C# 7: Out 变量

> 原文：<https://dev.to/csmacnz/looking-back-on-c-7-out-variables-4b41>

[![Looking Back on C# 7: Out variables](img/4566ebb14f8e59bb44e657ef6b795806.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TI-DMmkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1530226406379-f84b9edd291b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

### 什么是 out 参数？

有时候你想让一个方法通过引用传回一个值。在`C`和`C++`中，这是通过将地址作为参数传入来实现的。在 C#中，我们使用`out`关键字。

```
public bool HasCount(string str, out int length)
{
    length = 0;
    if (str.Length > 0)
    {
        length = str.Length;
        return true;
    }
}

string myString = "A String";
int length;
if (HasCount(myString, out length))
{
    // Do some stuff here.
} 
```

一个方法必须初始化 out 参数值，但是调用者不必首先初始化它。调用带有 out 参数的方法时，调用方必须使用`out`关键字。这有助于提高可读性，使其工作方式一目了然。

对于那些不熟悉这个概念的人来说，更多的细节可以在这里找到。

在库中，有一些方法使用了这个，例如`Int32.TryParse`。

```
int number;
if (Int32.TryParse(numberAsString, out number))
{
    // use `number` as an integer value. 
} 
```

当您构建 C 风格的互操作时，它也很有用。但这不是你最近经常看到的东西。

### 新出变量

这种语法总感觉有点笨拙。现代的 API 建议在 out 参数上使用返回值，早期的用例倾向于与 C 库交叉兼容或者解析场景。虽然在某些地方使用它有好处，但主要是出于可读性的原因，不鼓励使用它。

然而，C# 7 引入了新的语法，允许 out 参数的内联声明。

```
if (Int32.TryParse(numberAsString, out int number))
{
    // use `number` as an integer value. 
} 
```

现在，您可以内联声明参数，而不需要空的声明行，这实际上是在浪费行数。另一个好处是你现在可以使用`var`代替显式类型。

```
if (Int32.TryParse(numberAsString, out var number))
{
    // use `number` as an integer value. 
} 
```

在 C# 7.3 中，这个`out`变量语法被扩展到包括字段初始值设定项、属性初始值设定项、构造函数初始值设定项和查询子句。基本上，你可以在任何你可以使用参数的地方使用它。

随着新的变化，可读性大大增加，在初始化之前使用值的风险已经消除，这可能是值得鼓励在那些可以受益于没有干净 API 的参数和性能的方法中再次使用的东西。

对语言的一个小而简单的改进，我将在你使用`out`参数的每个地方替换它。