# 什么是操作码？

> 原文：<https://dev.to/dotnet/what-is-an-opcode-2bch>

在我的上一篇文章中，我们提到了生成的 IL 中接口实现的区别，但是作为一个回顾，它看起来是这样的:

```
.method private final hidebysig newslot virtual
    instance void UserQuery.ICounter.Add (
        int32 count
    ) cil managed
{
    .override method instance void UserQuery/ICounter::Add(int32)
    .maxstack 8

    IL_0000: ldarg.0
    IL_0001: ldarg.0
    IL_0002: call instance int32 UserQuery/Counter::get_Count()
    IL_0007: ldarg.1
    IL_0008: add
    IL_0009: call instance void UserQuery/Counter::set_Count(int32)
    IL_000e: nop
    IL_000f: ret 
```

Enter fullscreen mode Exit fullscreen mode

创建它的代码是这样的:

```
interface ICounter {
    void Add(int count);
}

class Counter : ICounter {
    public int Count { get; set }

    public void Add(int count) => Count += count;
} 
```

Enter fullscreen mode Exit fullscreen mode

今天，我想谈谈这到底意味着什么，具体来说，是什么组成了方法体，为此我想向您介绍一下`System.Reflection.Emit`名称空间，特别是 [`OpCodes`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes?view=netframework-4.8&WT.mc_id=devto-blog-aapowell) 类。

## 在我们潜入之前

在我们深入了解今天要看的内容之前，有一些重要的背景信息需要了解，这就是方法。网络工程。。NET 语言如 C#、F#和 VB.NET(以及其他)都输出公共中间语言(CIL)(有时称为微软中间语言/MSIL)，然后由运行时如公共语言运行时(CLR)使用实时(JIT)编译器执行，以创建被执行的本机代码。

所以，不管你写的是 C#还是 F#,最终都是一样的，你可以通过反转 CIL 将 F#代码转换成 C#,但是这可能会很奇怪。这就是像 [ilspy](https://ilspy.net/) 这样的工具的工作方式。

CIL 被定义为通用语言基础设施(CLI)的一部分，该基础设施被标准化为 [ECMA-335](http://www.ecma-international.org/publications/standards/Ecma-335.htm) ，其主要实现是微软已经完成的实现。NET，但是没有什么可以阻止其他人实现他们自己的实现(除了时间……)。在有人问之前，不，我没读过 ECMA-335。我以前确实在 kindle 上看过，但我从来没有读过。 [ECMA-262](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 另一方面…😉

CIL 是一种基于堆栈的字节码，这使它非常低级，让我想起了我在大学时做的许多 x86 汇编编程，所以如果这是你的难题，那么我们就来找点乐子吧！如果你从未享受过使用汇编或基于堆栈的机器的乐趣，那么最重要的是要知道，你要把东西推到堆栈上，这样你就可以再次读取它们，而且你必须以相反的顺序读取它们，最后一个接一个地读取。

现在，进入有趣的部分！

## 操作码

操作码代表 CIL 中可由 CLR 执行的*操作*。这些主要是关于栈的工作，但幸运的是并不是所有的`PUSH`和`POP`，我们得到了一些更高级的操作，甚至我们也可以利用基本的索引器(所以它不像我学的 8086 那样顺序)。

## [T1】我们的第一个操作码](#our-first-opcode)

让我们从这一行开始:

```
IL_0000: ldarg.0 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们可以删除`:`之前的内容，因为它代表该行的标签，我们可以用它作为跳转点，但是我们现在不需要标签，所以我们将关注指令:

```
ldarg.0 
```

Enter fullscreen mode Exit fullscreen mode

英寸 NET 这表示为 [`OpCodes.Ldarg_0`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.ldarg_0?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) ，它的作用是将(当前方法的)第一个参数推送到堆栈上。还有`ldarg.1`、`ldarg.2`和`ldarg.3`用于访问方法的前 4 个参数，而`ldarg.s <int>`用于访问其余的参数。以后再看`ldarg.s`怎么用，不是为了今天。

所以如果我们像这样调用代码:

```
counter.Add(1); 
```

Enter fullscreen mode Exit fullscreen mode

在`Add` CIL 内，我们将`1`推入堆栈的第一个位置。

## 调用 CIL 函数

CIL 的下一个重要作品是:

```
call instance int32 UserQuery/Counter::get_Count() 
```

Enter fullscreen mode Exit fullscreen mode

这是一个使用 [`Call`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.call?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 操作码的方法调用。为了使用这个操作码，我们需要提供更多的信息，我们调用的方法的位置，返回类型，最后是方法引用。

但是等等，我们调用什么方法呢？我们正在调用`Counter.get_Count()`，但我们从未在 C#代码中编写过，它是作为属性访问器为我们生成的。这个方法只是包装了支持字段(它也是在我们使用一个[自动属性](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/auto-implemented-properties?WT.mc_id=devto-blog-aapowell)时为我们生成的)。

因为方法是类型的一部分，我们也是类型的一部分，所以我们使用`instance`位置，它将返回一个`int32`。由于这是一个非`void`方法调用，我们需要将返回值推送到堆栈上，这是使用 [`ldarg.1`或 in 来完成的。`Ldarg_1`网](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.ldarg_1?view=netcore-2.2&WT.mc_id=devto-blog-aapowell)。

## 添加数字

完成`ldarg.1`后，我们现在在堆栈上有两个值，`Add`的参数在索引 0 处，`Count`的当前值在索引 1 处。这意味着我们可以将这些数字加在一起，这就是`+`操作符所做的，从而产生下面的 IL:

```
add 
```

Enter fullscreen mode Exit fullscreen mode

我打赌你没有选择那就是 [`Add`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.add?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 操作码所做的！

这个 CIL 指令还会把结果值放到堆栈上，所以不需要在它后面使用一个`ldarg`操作码。

## 更新我们的属性

是时候更新我们对象的`Count`属性了，我们将再次使用`Call`操作码来做这件事:

```
call instance void UserQuery/Counter::set_Count(int32) 
```

Enter fullscreen mode Exit fullscreen mode

当我们需要传递一个参数给`set_Count`(自动生成的赋值函数)时，你可能想知道它是如何得到的，它从堆栈中得到它。当一个函数接受参数时，它会根据需要从堆栈中弹出尽可能多的参数来执行，所以你需要确保当你将数据压入堆栈时，它是以正确的顺序压入的，否则你可能会以类型不匹配结束，或者错误的值在错误的参数中。

最后，`Add`将使用 [`Ret`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.ret?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 操作码:
退出

```
nop
ret 
```

Enter fullscreen mode Exit fullscreen mode

我不确定为什么编译器会包含 [`nop`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.nop?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 操作码，它不做任何事情，因此可以省略。

## 结论

希望你还和我在一起，并且喜欢尝试理解 CIL 的操作码。我们已经分解了为这一行 C#代码生成的 8 行 CIL:

```
Count += count; 
```

Enter fullscreen mode Exit fullscreen mode

在我们的方法实现中，我们使用了[表达式体语法](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods?WT.mc_id=devto-blog-aapowell#expression-body-definitions)，而不是传统的[方法签名](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods?WT.mc_id=devto-blog-aapowell#method-signatures)。

您可能有兴趣知道的是，这两种方法类型生成的 IL 没有区别，因为它们在功能上是等价的。

这同样适用于`Count += count` vs `Count = Count + count`的使用，两者都生成相同的 CIL，因为与[加法赋值操作符](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/addition-operator?WT.mc_id=devto-blog-aapowell#addition-assignment-operator-)没有区别(在我们的例子中，有些场景并不总是如此)。

能够理解这些差异或不足是很重要的，这样我们就不会基于对代码如何执行的先入为主的信念来做出任意的代码风格决定。

我们会继续探索 CIL，所以如果你有什么特别想了解的，让我知道，我们可以做一些挖掘！