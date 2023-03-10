# 。NET 程序集往返

> 原文：<https://dev.to/vijesh_salian/net-assembly-roundtripping-37m1>

### 往返到底是什么？

程序集往返是将程序集反编译为 IL(中间语言)以进行编辑，然后重新编译以生成程序集的行为。

在这篇文章中，我将演示如何往返一个. NET 程序集。
要往返，我们需要
T2】

1.  反编译成 IL
2.  Edit IL
3.  重新编译为程序集

##### 所需工具

*   ildasm
*   Visual Studio 开发人员命令提示符
*   csc.exe-C #编译器

这是我们将在演示中使用的 C#文件。

```
using System;

public class Program
{
    public static void Main()
    {
        int x = 10;
        int y = 20;
        Console.WriteLine("The answer is {0}", x + y);
        Console.WriteLine("Hello, World!");
    }
} 
```

我将把这个文件命名为 roundtrip.cs。

在 VS developer 命令提示符下，导航到文件 roundtrip.cs 的位置。现在，我将把这个 cs 文件编译成一个程序集，在本例中是一个 exe。下面是命令。

```
csc.exe roundtrip.cs 
```

创建了一个程序集。roundtrip.exe。如果您运行这个程序，您应该得到以下输出。这里没什么不正常的。

```
The answer is 30
Hello, World! 
```

##### 反编译成 IL

让我们使用 ildasm 和下面的命令为 roundtrip.exe 创建一个 IL 文件。

```
ildasm /out=roundtrip.il roundtrip.exe 
```

这将为 roundtrip.il 创建一个包含 il 代码的文件 roundtrip.il。

在任何编辑器中打开此 roundtrip.il。您将在文件中找到所有的 IL 代码。C#文件中的 main 方法在 IL 中有相应的部分。在 IL 文件中查找下面一行。

```
.method public hidebysig static void  Main() cil managed 
```

在这一节中，您应该会找到一组分别对应于用 10、20 声明和初始化整数的指令。有一个我们正在使用的字符串。你也会发现的。

```
.locals init (int32 V_0,
             int32 V_1)
    IL_0000:  nop
    IL_0001:  ldc.i4.s   10
    IL_0003:  stloc.0
    IL_0004:  ldc.i4.s   20
    IL_0006:  stloc.1
    IL_0007:  ldstr      "The answer is {0}"
    IL_000c:  ldloc.0
    IL_000d:  ldloc.1
    IL_000e:  add 
```

##### Edit IL

现在对于编辑部分，让我们将加法操作改为乘法操作。替换添加到 mul

```
IL_000e:  mul 
```

有一个 Hello World 字符串，让我们将该字符串改为 Hello Dev。

之前:

```
IL_001a:  ldstr      "Hello, World!" 
```

之后:

```
IL_001a:  ldstr      "Hello, Dev!" 
```

确保保存 IL 文件。

##### 重新编译为程序集

现在是重新编译的时候了。在 VS dev 提示符下，键入以下命令。注意，这次我们使用 ILASM，而不是 ILDASM。我们将编辑过的 IL 文件传递给命令。

```
ilasm /exe roundtrip.il 
```

如果在 IL 中没有出现错误，那么你应该会发现新的 roundtrip.exe。当您运行它时，您应该会看到以下输出。

```
The answer is 200
Hello, Dev! 
```

希望你对此感兴趣。感谢您的关注。

干杯🍺