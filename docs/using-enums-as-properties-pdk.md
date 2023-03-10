# 使用枚举作为属性

> 原文：<https://dev.to/bradwellsb/using-enums-as-properties-pdk>

[来源](https://wellsb.com/csharp/beginners/csharp-enums-enumerations/)

在 C#中，枚举是一种特殊的数据类型，用于限制给定变量的可能值。当值在整个程序中使用时，约束数据对于确保值的有效性很有用。

## 枚举入门

假设你正在编写一个待办事项应用程序。您可以[创建一个 Todo 类](https://wellsb.com/csharp/beginners/understanding-csharp-classes-and-methods/)来保存诸如任务描述、截止日期和状态之类的信息。为了确保整个计划的一致性，定义可能的状态非常重要。如果您的程序支持三种可能的状态，您可能会尝试使用三个整数并创建一个表来定义哪个整数对应于哪个状态。你甚至可以把这个表写成代码注释，这样程序员在审查你的代码时就能理解其中的细节。

```
// 0 - Not Started
// 1 - In Progress
// 2 - Completed 
```

虽然这种方法可行，但是如果每次都不引用表，源代码中每个数字的含义就不明显。使用枚举将产生更可读的代码，并且它将有助于确保您不会因为忘记哪个状态对应于哪个整数而在代码中意外出错。

枚举有助于将可能的值限制为您定义的在系统中有意义的值。在后台，`enum`的默认底层数据类型仍然是整数。然而，`enum`通过直接引用文本等价而不是数字值来消除任何歧义。

除了代码可读性和可管理性之外，对于每种状态，枚举比硬编码文字字符串更有效。基础整数是相对较小的数据类型，比字符串消耗更少的内存资源。

定义待办事项应用程序中可能状态的枚举可能如下所示。

```
enum Status
{
    NotStarted,
    InProgress,
    Completed
} 
```

如果您希望在其他类中引用这个枚举，不要忘记将代码放在`Main()`方法之外。

## 对类使用枚举

```
class Todo
    {
        public string Description { get; set; }
        public Status Status { get; set; }
    } 
```

在上面的类定义中，您可以看到状态是 Todo 类的属性之一。可能的值在您之前创建的`enum`中定义。当实例化 Todo 类的实例时，您可以从这些定义的值中进行选择。

```
Todo task = new Todo { Description = "Write Blog Post", Status = Status.InProgress }; 
```

为了基于 Todo 项目的状态执行一个动作，你可以使用一个`switch`语句，就像你在 [C#控制台菜单](https://wellsb.com/csharp/beginners/create-menu-csharp-console-application/)教程中所学的那样。

```
switch (task.Status)
{
    case Status.NotStarted:
        // Do something
        break;
    case Status.InProgress:
        // Do something else
        break;
    case Status.Completed:
        // Yay!
        break;
    default:
        break;
} 
```

`switch-case`构造与枚举配合得非常好，因为产生的代码很容易理解。回想一下，`switch`语句是使用多个`if-else if`语句的一个很好的替代方案。在这种情况下，您可以使用`switch-case`语句来选择只显示那些尚未完成的任务，或者根据任务的状态来改变任务的颜色。

## 底线

在本教程中，您学习了 C#中的一种特殊数据类型，称为枚举，或`enum`。枚举帮助开发人员定义给定变量的有效值。您学习了如何在项目中使用枚举，包括在类定义中将它们用作属性。最后，您了解到`switch`语句和枚举可以很好地协同工作！一如既往，如果这对你有帮助，请在评论中告诉我！

[来源](https://wellsb.com/csharp/beginners/csharp-enums-enumerations/)