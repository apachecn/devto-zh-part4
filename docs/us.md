# C# 中的一个<us>被区分的联合</us>

> 原文：<https://dev.to/integerman/oneof-us-discriminated-unions-in-c-3mnf>

曾经希望可以对不同类型的变量进行操作——有效地根据对象类型进行切换，并根据存在的类采取不同的操作？来自函数式编程语言的有区别的联合提供了这个问题的答案。在这篇文章中，我探讨了 C#中有区别的联合的好与坏，并提供了一些关于这是否适合您的项目的想法。

[区分联合](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions)是一种方便的函数式编程，它表明某个东西是*几个不同类型对象中的*一个。例如，用户可能是未经身份验证的用户、普通用户或管理员。

虽然[受歧视的联合正在被评估以添加到 C#语言](https://github.com/dotnet/csharplang/issues/113)中，但它们目前还不可用，然而在我们对其提供官方语言支持之前，[one of](https://github.com/mcintyre321/OneOf)库为那些想要使用受歧视的联合的人提供了另一种选择。

让我告诉你它是如何工作的，以及为什么你可能想或不想使用它。

# 一个人是如何工作的

### 返回结果之一

这个例子来自我正在修补的一个业余游戏开发项目。我需要一个例程，让技术人员尝试处理一个工作项目。根据随机数的结果，他们将得到一个`WorkCompletedResult`、一个`ProgressMadeResult`或者一个`SetbackResult`。

*注意:我要求你们暂停对这些职业是否应该存在的怀疑，把这看作是*
的一个纯粹的技术演示

```
private OneOf<WorkCompletedResult, ProgressMadeResult, SetbackResult> 
        GetWorkOnItemResult(CrewContext context, WorkItem item)
{
    var roll = context.GetRandomNumber(1, 20);

    // Something went wrong. Likely a mistaken assumption or bad requirement
    if (roll == 1)
    {
        return new SetbackResult(Priority.High);
    }

    // We got a massive success or we've previously made significant progress
    if (roll >= 20 - item.ProgressMade)
    {
        return new WorkCompletedResult(item);
    }

    // Okay, not a great roll, let's call it a setback
    if (roll < 3)
    {
        return new SetbackResult(Priority.Normal);
    }

    // Just a bit of progress
    return new ProgressMadeResult(item);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们打开这个。这段代码唯一不同寻常的地方是方法的返回类型:`OneOf<WorkCompletedResult, ProgressMadeResult, SetbackResult>`。

坦率地说，这是一种相当丑陋的使用泛型的方式，说方法的结果将是不同类型的泛型参数之一。

从此，只要方法返回这些结果之一，就不需要修改代码。

### 处理结果之一

那么，现在我们有了一个返回`OneOf`结果的方法，我们能用它做什么呢？

下面的例子来自我正在构建的行为树中的一个节点(请继续关注下一篇关于行为树更多细节的文章)。这段代码关闭了对上面定义的`GetWorkOnItemResult`方法的调用结果，并以不同的方式处理每种情况。

```
private BehaviorResult<CrewContext> ProcessCrewWorkOnItem(CrewContext context, WorkItem ticket)
{
    var result = GetWorkOnItemResult(context, ticket);

    result.Switch(
        completed =>
        {
            ticket.Status = WorkItemStatus.ReadyForReview;
            AddMessage(context, $"{context.CrewMember.FullName} finishes work on {ticket.Title}");
        },
        progress =>
        {
            ticket.ProgressMade++;
            AddMessage(context, $"{context.CrewMember.FullName} made steady progress on {ticket.Title}");
        },
        setback =>
        {
            if (setback.Severity > Priority.Normal)
            {
                ticket.ProgressMade -= 3;
                AddMessage(context, $"{context.CrewMember.FullName} encountered a major setback on {ticket.Title}");
            }
            else
            {
                ticket.ProgressMade -= 1;
                AddMessage(context, $"{context.CrewMember.FullName} encountered a setback on {ticket.Title}");
            }
        }
    );

    return MatchedResult(context);
} 
```

Enter fullscreen mode Exit fullscreen mode

`.Switch`函数为每个定义的泛型类型使用一个`Action`参数，让您可以访问该对象类型的强类型实例，就像我们看到的使用`setback`参数一样。

这些参数按照泛型类型在`OneOf`类型声明中定义的顺序进行匹配。在我们的例子中，第一个参数是一个`Action<WorkCompletedResult>`，第二个是一个`Action<ProgressMadeResult>`，第三个是`Action<SetbackResult>`。这就是为什么我们可以使用`setback.ProgressMade`，一个在`SetbackResult`实例上声明的属性。

所以，`OneOf`让我们返回几个不同选项中的一个，然后给你一个方法，根据你遇到的类型来做不同的事情。

如果你需要构建你的代码，使每种情况都返回一个值，你可以使用`Match`代替`Switch`，如下:

```
private BehaviorResult<CrewContext> ProcessCrewWorkOnItem(CrewContext context, WorkItem ticket)
{
    var result = GetWorkOnItemResult(context, ticket);

    var message = result.Match(
        completed =>
        {
            ticket.Status = WorkItemStatus.ReadyForReview;
            return $"{context.CrewMember.FullName} finishes work on {ticket.Title}";
        },
        progress =>
        {
            ticket.ProgressMade++;
            return $"{context.CrewMember.FullName} made steady progress on {ticket.Title}";
        },
        setback =>
        {
            if (setback.Severity > Priority.Normal)
            {
                ticket.ProgressMade -= 3;
                return $"{context.CrewMember.FullName} encountered a major setback on {ticket.Title}";
            }
            else
            {
                ticket.ProgressMade -= 1;
                return $"{context.CrewMember.FullName} encountered a setback on {ticket.Title}";
            }
        });

    AddMessage(context, message);

    return MatchedResult(context);
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这与`Switch`几乎相同，除了`Match`使用了`Func<T, TResult>`而不是`Action<T>`。

# 薄弱环节

这是一个有趣的把戏，但是它在哪里失败了呢？

### 键入别名

与 TypeScript 和 F#等语言不同，您不能为有区别的联合定义简单的可重用别名，这意味着如果您传递相同的类型组合，您必须对返回类型和参数值使用相同的`OneOf`语法，并且您不能使用简单类型别名。

作为参考:在 TypeScript 中，被区分的联合的类型别名如下:

```
type User = GuestUser | StandardUser | Administrator; 
```

Enter fullscreen mode Exit fullscreen mode

### 代码补全

代码完成在`Switch`和`Map`函数上非常有限。最令人恼火的是不得不记住`OneOf`定义中类型的排序。

### 订单交换

如果您用不同的顺序声明两个不同的参数返回类型，它们将不能在彼此之间切换

### 只作用于特定类型

假设您想查看一个结果，并且只在返回结果为`StetbackResult`时做一些事情。您可以使用带有空参数的`Switch`，也可以使用`AsT1`和`IfT1`成员。遗憾的是，这些是它们的真实名称，因此很容易再次混淆哪种类型的顺序。

* * *

# 我的看法

所以，这是你在 C#中可以做的事情。更重要的问题是:你应该吗？

在调查了图书馆之后，我的回答是:可能不会。这是一个很酷的技巧，但是语言限制阻碍了任何图书馆的生存能力。

目前，除非您有一些非常有针对性的用法，否则我建议您避免增加`OneOf`的复杂性，要么等待官方语言支持，要么添加一个小的 F#库并从 C#或 VB 中引用它。NET 代码。

* * *

如果你有其他想法或者知道另一种在 C#中更好地使用歧视性工会的方法，请告诉我。

* * *

*马库斯·斯皮斯克在 Unsplash 上拍摄的照片*