# IL 中的冒险:条件和循环

> 原文：<https://dev.to/dotnet/adventures-in-il-conditionals-and-loops-28m5>

上次我们探索 IL(嗯，CIL，但大多数人只知道它是 IL)时，我们通过一个非常简单的方法了解了[操作码及其含义](https://dev.to/dotnet/what-is-an-opcode-2bch)。今天我想看一下我们在编程中常用的两个语句，条件语句(`if`、`switch`)和循环语句(`for`、`foreach`等。).我们还将看看我们从上一篇文章中跳过的一些东西，即`Debug`和`Release`构建之间的区别(无论有没有编译器优化)。

## If 语句

就拿下面这个 F#函数来说:

```
let ifStatement a =
    if a = 1 then
        Console.WriteLine "one"
    else
        Console.WriteLine "not one" 
```

Enter fullscreen mode Exit fullscreen mode

*我用的是`Console.WriteLine`而不是 [F# `Core.Printf`模块](https://msdn.microsoft.com/visualfsharpdocs/conceptual/core.printf-module-%5bfsharp%5d?WT.mc_id=devto-blog-aapowell)和`printfn`，因为这样代码更简洁。通常在 F#中我会使用`printfn`。*

这将生成以下 IL:

```
IL_0000: ldarg.0
IL_0001: ldc.i4.1
IL_0002: bne.un.s IL_0006

IL_0004: br.s IL_0008

IL_0006: br.s IL_0013

IL_0008: ldstr "one"
IL_000d: call void [mscorlib]System.Console::WriteLine(string)
IL_0012: ret

IL_0013: ldstr "not one"
IL_0018: call void [mscorlib]System.Console::WriteLine(string)
IL_001d: ret 
```

Enter fullscreen mode Exit fullscreen mode

有一些熟悉的操作码，但我们也遇到了一些新的。我们输出中的前两个负责将参数值(代码中的`a`)加载到堆栈上，然后将`int32`值`1`推送到堆栈上。现在我们要学习一个新的操作码，我们还需要了解更多关于`:`(我们上次忽略了)的*左边*的部分，它被称为**指令前缀**。

IL 中的一条指令可以由两条`prefix: instruction`格式的信息组成。我们来看下面这条线:

```
IL_0002: bne.un.s IL_0006 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个前缀`IL_0002`，并且该行的指令是`bne.un.s IL_0006`，它在这里被定义为。引用`bne.un.s`的说明文件:

> 当两个无符号整数值或无序浮点值不相等时，将控制转移到目标指令(短格式)。

有意思的是，这是一个*不等于*的运算，而我们的代码是`if a = 1 then`，所以 IL 代表了我们代码所代表的*的逆运算*。为了理解为什么我们需要查看指令的其余部分，以及 IL 的其余部分，因为还有另一位信息传递给了`bne.un.s`、`IL_0006`，它表示要将控制转移到的目标指令。实际上，这意味着“如果两个值不匹配，下一行执行的是`IL_0006`”。

这是它在 IL 中的位置:

```
IL_0004: br.s IL_0008

IL_0006: br.s IL_0013 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到`IL_0006`是 `IL_0004`之后的*，所以我们跳过了`IL_0004`，本质上使用了一个 [`goto`语句](https://en.wikipedia.org/wiki/Goto)！*

*说句题外话，F#没有`goto`语句， [C#有](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/goto?WT.mc_id=devto-blog-aapowell)！*

`IL_0004`和`IL_0006`都使用 [`br.s`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.br_s?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) ，后者**再次**将控制权转移给另一条指令:

```
IL_0008: ldstr "one"
IL_000d: call void [mscorlib]System.Console::WriteLine(string)
IL_0012: ret

IL_0013: ldstr "not one"
IL_0018: call void [mscorlib]System.Console::WriteLine(string)
IL_001d: ret 
```

Enter fullscreen mode Exit fullscreen mode

这两个块非常相似，`IL_0008`是我们的`if`语句的 *truthy* 分支的开始，将一个字符串加载到堆栈上，然后在发出`ret`结束方法之前调用`Console.WriteLine`。`IL_0013`是*false*分支，使用控制转移我们跳过 truthy 块。

因此，如果我们把它分解开来，一个`if`语句就是一系列 GOTO 调用来跳过我们不想执行的块。

### If 语句中的`C#`

出于好奇，我决定用 C#创建同样的例子:

```
public void IfStatements(int a)
{
    if (a == 1)
    {
        Console.WriteLine("One");
    }
    else
    {
        Console.WriteLine("Not One");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现它会产生不同的 IL！

```
IL_0000: nop
IL_0001: ldarg.1
IL_0002: ldc.i4.1
IL_0003: ceq
IL_0005: stloc.0
IL_0006: ldloc.0
IL_0007: brfalse.s IL_0018

IL_0009: nop
IL_000a: ldstr "One"
IL_000f: call void [mscorlib]System.Console::WriteLine(string)
IL_0014: nop
IL_0015: nop
IL_0016: br.s IL_0025

IL_0018: nop
IL_0019: ldstr "Not One"
IL_001e: call void [mscorlib]System.Console::WriteLine(string)
IL_0023: nop
IL_0024: nop

IL_0025: ret 
```

Enter fullscreen mode Exit fullscreen mode

这里有意思的是，在 C#版本中它使用了 [`ceq`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.ceq?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) ，并将其与 [`brfalse.s`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.brfalse_s?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 组合在一起。`ceq`对两个值进行相等测试，如果它们相等，它将`1`推到堆栈上，否则`0`，然后`brfalse.s`将控制转移到一个指令，在我们的例子中是`IL_0018`，如果堆栈上的值为假，`null`或 0。

当 *truthy* 块跳过*false*块并落在`ret`上时，你还会注意到 [`br.s`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.br_s?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 的使用，而 F#只是内联了`ret`。

这使得 C#版本在实现同样的事情时比 F#版本更冗长一些。

### 调试 vs 发布

以上两个例子都是使用“调试模式”编译的，所以没有启用编译器优化。但这不是您要部署到生产环境中的东西(对吗？对！)所以我又一次很有兴趣去看看那里的差异。下面是经过优化编译的 F # one:

```
IL_0000: ldarg.0
IL_0001: ldc.i4.1
IL_0002: bne.un.s IL_000f

IL_0004: ldstr "one"
IL_0009: call void [mscorlib]System.Console::WriteLine(string)
IL_000e: ret

IL_000f: ldstr "not one"
IL_0014: call void [mscorlib]System.Console::WriteLine(string)
IL_0019: ret 
```

Enter fullscreen mode Exit fullscreen mode

这是 C#:

```
IL_0000: ldarg.1
IL_0001: ldc.i4.1
IL_0002: bne.un.s IL_000f

IL_0004: ldstr "One"
IL_0009: call void [mscorlib]System.Console::WriteLine(string)
IL_000e: ret

IL_000f: ldstr "Not One"
IL_0014: call void [mscorlib]System.Console::WriteLine(string)
IL_0019: ret 
```

Enter fullscreen mode Exit fullscreen mode

嗯，它们看起来很相似，不是吗，事实上，它们是一样的(我必须检查几次，是的，我是在复制不同的！).它们都类似于未优化的 F#输出，但是有一点小小的不同，就是少了几条`br.s`指令。

## 为循环

当谈到循环时，我倾向于使用最常用的`for`循环，所以我们将在今天的探索中使用它。从一个简单的 F#函数开始:

```
let forLoop() =
    for i in 0 .. 99 do
        Console.WriteLine i 
```

Enter fullscreen mode Exit fullscreen mode

这导致以下 IL:

```
IL_0000: ldc.i4.0
IL_0001: stloc.0
IL_0002: br.s IL_000e
// loop start (head: IL_000e)
    IL_0004: ldloc.0
    IL_0005: call void [mscorlib]System.Console::WriteLine(int32)
    IL_000a: ldloc.0
    IL_000b: ldc.i4.1
    IL_000c: add
    IL_000d: stloc.0

    IL_000e: ldloc.0
    IL_000f: ldc.i4.1
    IL_0010: ldc.i4.s 99
    IL_0012: add
    IL_0013: blt.s IL_0004
// end loop

IL_0015: ret 
```

Enter fullscreen mode Exit fullscreen mode

我留下了一些 IL Spy 为我生成的缩进和注释，以帮助更好地可视化 IL。

我们首先用`ldc.i4.0`将`i`的初始值推到堆栈上，然后在用`br.s`向下移动到`IL_000e`之前，用`stloc.0`确保堆栈在正确的位置。这是我们等式测试的开始，以确保我们仍然在循环范围内:

```
 IL_000e: ldloc.0
    IL_000f: ldc.i4.1
    IL_0010: ldc.i4.s 99
    IL_0012: add
    IL_0013: blt.s IL_0004 
```

Enter fullscreen mode Exit fullscreen mode

位置 0 被加载到堆栈上(这是我们的`i`值),然后在调用将`100`的值推送到堆栈上的`add`之前，分别用`ldc.i4.1`和`ldc.i4.s`将`1`和`99`的值推送到堆栈上(`ldc.i4.1`是`ldc.i4.s 1`的简写，可能会被您的运行时优化)。最后 [`blt.s`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.blt_s?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 被调用，如果堆栈位置`0`中的值小于`add`的结果，我们将把控制转移到指令列表中更高的位置，特别是转移到`IL_0004`。现在我们已经完成了这个程序块:

```
 IL_0004: ldloc.0
    IL_0005: call void [mscorlib]System.Console::WriteLine(int32)
    IL_000a: ldloc.0
    IL_000b: ldc.i4.1
    IL_000c: add
    IL_000d: stloc.0 
```

Enter fullscreen mode Exit fullscreen mode

我们从堆栈位置`0`获取值，将它传递给`Console.WriteLine`，再次获取它，将`1`添加到它，并传递到`IL_000e`，因为没有控制权转移。

我发现这个顺序很有趣，输出 IL 的顺序与我预期的相反，条件测试在指令表的**末端**，但是仔细分析后，它很有意义。如果条件测试在顶部，当循环体结束时，你必须使用一个`br.s`将控制转移回顶部，然后如果超出范围，进行控制转移测试(一个`brtrue.s`)。这将是低效的，因为你总是执行一个控制转移，然后有第二个*潜在的*控制转移，而相反，你只有一个控制转移，而且是有条件的。

### 为`C#`中的循环

我们再来看一个 C#实现:

```
public void ForLoop()
{
    for (var i = 0; i < 100; i++)
    {
        Console.WriteLine(i);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

产生以下 IL:

```
IL_0000: nop
IL_0001: ldc.i4.0
IL_0002: stloc.0
IL_0003: br.s IL_0012
// loop start (head: IL_0012)
    IL_0005: nop
    IL_0006: ldloc.0
    IL_0007: call void [mscorlib]System.Console::WriteLine(int32)
    IL_000c: nop
    IL_000d: nop
    IL_000e: ldloc.0
    IL_000f: ldc.i4.1
    IL_0010: add
    IL_0011: stloc.0

    IL_0012: ldloc.0
    IL_0013: ldc.i4.s 100
    IL_0015: clt
    IL_0017: stloc.1
    IL_0018: ldloc.1
    IL_0019: brtrue.s IL_0005
// end loop

IL_001b: ret 
```

Enter fullscreen mode Exit fullscreen mode

它与 F#版本非常相似(忽略了`nop`指令),除了等式测试是如何完成的。在我们的 F#版本中，与`if`语句一样，等式测试与使用`blt.s`的控制转移结合在一起，而 C#使用 [`clt`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.clt?view=netcore-2.2&WT.mc_id=devto-blog-aapowell) 和`brtrue.s`来比较值，然后转移控制。

另一个主要区别是，C#版本将 100 加载到堆栈上进行`clt`测试，但是 F#将 1 和 99 推入堆栈，然后将它们相加，这意味着它的相等测试更类似于`i < 1 + 99`而不是 C#的`i < 100`。

### 调试 vs 发布

是时候比较一下当我们启用编译器优化时会发生什么了，从 F#开始:

```
IL_0000: ldc.i4.0
IL_0001: stloc.0
IL_0002: br.s IL_000e
// loop start (head: IL_000e)
    IL_0004: ldloc.0
    IL_0005: call void [mscorlib]System.Console::WriteLine(int32)
    IL_000a: ldloc.0
    IL_000b: ldc.i4.1
    IL_000c: add
    IL_000d: stloc.0

    IL_000e: ldloc.0
    IL_000f: ldc.i4.s 100
    IL_0011: blt.s IL_0004
// end loop

IL_0013: ret 
```

Enter fullscreen mode Exit fullscreen mode

而现在 C#:

```
IL_0000: ldc.i4.0
IL_0001: stloc.0
IL_0002: br.s IL_000e
// loop start (head: IL_000e)
    IL_0004: ldloc.0
    IL_0005: call void [mscorlib]System.Console::WriteLine(int32)
    IL_000a: ldloc.0
    IL_000b: ldc.i4.1
    IL_000c: add
    IL_000d: stloc.0

    IL_000e: ldloc.0
    IL_000f: ldc.i4.s 100
    IL_0011: blt.s IL_0004
// end loop

IL_0013: ret 
```

Enter fullscreen mode Exit fullscreen mode

这次我们得到了相同的 IL (F#甚至使用 100，而不是做加法！)，了解这一点很好，但有趣的是，当您启用编译器优化时，输出会有多么不同。

## 结论

现在我们已经看到了 IL 的一些细节部分，一切都只是一天结束时的 GOTO 语句！🤣

理解控制是如何在 IL 中传递的有助于我们理解编译器是如何进行优化的，以及为什么我们应该以一种特定的方式编写代码。

我将留给您一个`switch`语句的输出，看看您是否能算出它是从哪个 C#生成的:

```
IL_0000: ldarg.1
IL_0001: ldc.i4.1
IL_0002: beq.s IL_000a

IL_0004: ldarg.1
IL_0005: ldc.i4.2
IL_0006: beq.s IL_0015

IL_0008: br.s IL_0021

IL_000a: ldstr "One"
IL_000f: call void [mscorlib]System.Console::WriteLine(string)
IL_0014: ret

IL_0015: ldstr "Two"
IL_001a: call void [mscorlib]System.Console::WriteLine(string)
IL_001f: br.s IL_000a

IL_0021: ldstr "Default"
IL_0026: call void [mscorlib]System.Console::WriteLine(string)
IL_002b: ret 
```

Enter fullscreen mode Exit fullscreen mode

祝你好运！