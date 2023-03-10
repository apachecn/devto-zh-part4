# 回顾 C# 7:模式匹配

> 原文：<https://dev.to/csmacnz/looking-back-on-c-7-pattern-matching-55ae>

[![Looking Back on C# 7: Pattern matching](img/4566ebb14f8e59bb44e657ef6b795806.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TI-DMmkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1530226406379-f84b9edd291b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

模式匹配是一项强大的功能，已经针对 C#中的各种语言结构进行了解锁。这个想法是从 switches 和`is`中提取像`case`这样的现有特性，并将它们的能力提取到类型和值的“模式匹配”概念中。这可以在将来应用到这些语言特征和其他地方。

在模式新概念之前，`case`接受一个常量作为参数，`is`将接受一个类型。

```
// Check the type of the variable "is assignable to" Shape
if(widget is Shape)
{
    switch ((widget as Shape).Type)
    {
        // When value matches the constant "Square"
        case "Square":
            // ...
            break;
        default:
            break;
    }
} 
```

随着 C# 7.0 的引入，这两种语言特性都得到了增强，可以使用新的模式匹配语法。这是一个向后兼容的变化，意味着模式类型可以是常量或类型。除了这些现有的案例，它还包括`when`子句和 var 模式。

### 是表情

本来，`is`是可以查一个类型的。这可以创建更可读的代码，但通常会留下强制转换或`as`操作符的使用。

```
if (widget is Shape)
{
    var myShape = widget as Shape;
    // var myShape = (Shape)widget;
} 
```

这已经通过模式匹配得到了改善。首先，我们有持续的检查:

```
if (widget.Type is null)
{
    // ...
}

if (widget.Type is "FOO")
{
    // ...
} 
```

除此之外，类型检查模式现在还支持创建正确类型的作用域变量，类似于新的 out 参数功能。这种语言特征统称为“表达式变量”。

```
if (widget is Shape shape)
{
    // ... use `shape`
} 
```

### 切换语句

以前的开关只能匹配常量，但有了新的模式匹配，我们可以做得更多。

```
void SwitchIt(object value)
{
    var result = 0;
    switch (value)
    {
        // We can still switch on constants, even when the types don't match
        case 0:
            break;
        // We can switch on type, and even create a scoped variable (like with `is`)
        case IEnumerable<int> childSequence:
        {
            foreach(var item in childSequence)
                result += (item > 0) ? item : 0;
            break;
        }
        // using `when`, we can do range or bounds checking
        case int n when n > 0:
            result += n;
            break;
        // We can constant check against null
        case null:
            throw new NullReferenceException("Null found in sequence");
        default:
            throw new InvalidOperationException("Unrecognized type");
    }

    return result;
} 
```

对于 switch，还有另一个有用的新模式，那就是 var 模式。“var 模式”类似于 type 模式，除了它总是匹配，但是用指定的值创建一个变量。

```
switch(shape)
{
    case Circle c:
        WriteLine($"circle with radius {c.Radius}");
        break;
    case Rectangle s when (s.Length == s.Height):
        WriteLine($"{s.Length} x {s.Height} square");
        break;
    case Rectangle r:
        WriteLine($"{r.Length} x {r.Height} rectangle");
        break;
    case var s:
        // Always matches (similar to default) but gives access to the value as `s`
        WriteLine($"This is some kind of {s.Name} shape");
        break;
} 
```

值得指出的是，你的论点的顺序现在很重要。虽然这不是一个逻辑上的突破性变化，因为声明什么顺序常数并不重要，它总是匹配正确的答案，这是一个你需要注意的概念上的变化。在开关中混合和匹配模式意味着顺序很重要，第一个匹配的模式将被执行。重复一遍，当所有情况都是常量时，这不会对结果产生任何影响，但更高级的检查会产生影响。

在 C# 7.1 中，这些模式被扩展到同样适用于一般变量。在 C# 7.0 中，您可以像预期的那样使用这些类型模式，首先将`T foo`值强制转换为`Object`(这可能会导致装箱)，然后类型检查将全部工作。在 C# 7.1 中，这种强制转换不再是必需的，并且还避免了任何装箱和拆箱操作。[文档](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/switch#type-pattern)通过一个比较两种实现的例子详细介绍了这一点，因为存在一些细微的差异，尤其是在空/默认情况下。

### 模式匹配即将推出

这个特性很有意思，因为它是新的，而且很可能没有被充分利用，也不太为人所知。你可以快乐地用 C#编程，永远不需要使用它，也不会碰到它。

但是随着 C# 8 的出现，将会有一系列新的特性使用现有的模式匹配，所以你可能会想要做好准备，因为很快你将会需要理解使用它的代码，并且可能会看到更多。

带上开关表情！