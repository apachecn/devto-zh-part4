# 学习 F# —一个简单的解析器

> 原文：<https://dev.to/citizen428/learning-f-a-simple-parser-46f9>

距离我上一篇帖子已经过去几周了，我仍然非常喜欢学习 F#。因此，当我发现这篇名为[“仿真器基础:堆栈和注册机”](http://notes.eatonphil.com/emulator-basics-a-stack-and-register-machine.html)的有趣帖子时，作者在其中构建了一个运行简单 C 程序的虚拟机，我决定从 EcmaScript 移植代码。

我不会详细讨论实际的 VM 设计和它的指令集，最初的帖子已经很好地解释了它。让我们关注我们将要解析的源代码的类型:

```
plus:
  ADD RDI, RSI
  MOV RAX, RDI
  RET

main:
  MOV RDI, 1
  MOV RSI, 2
  CALL plus
  RET 
```

Enter fullscreen mode Exit fullscreen mode

该程序将从标签`main:`开始，将 2 个数字添加到寄存器中，然后调用标签`plus:`后定义的程序来添加它们。加法(3)的结果将是这个程序的退出代码。

## 解析器

我决定将解析器的结果表示为一个名为`ParseResult`的[记录](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/records)类型，它由包含指令及其可选参数(例如`("add", ["RDI"; "RSI"])`或`("ret", [])`)的元组列表以及一个跟踪标签及其对应行号的映射组成:

```
type ParseResult =
    { instructions: (string * string list) list
      labels: Map<string, int> } 
```

Enter fullscreen mode Exit fullscreen mode

### 图案

F#的[活动模式](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/active-patterns)是我最喜欢的语言特性之一。像大多数这门语言的新手一样，我倾向于过度使用它们，尽管在这种特殊的情况下，它们看起来很适合作为[解析器组合子](https://fsharpforfunandprofit.com/posts/understanding-parser-combinators/)或类似解决方案的轻量级替代。

```
let (|Empty|Directive|LineComment|Label|Instruction|) (line : string) =
    if line = "" then Empty
    else if line.StartsWith(".") then Directive
    else if line.StartsWith(";") || line.StartsWith("#") then LineComment
    else if line.Contains(":") then Label (line.Split(":").[0])
    else Instruction (line.Split([|';'; '#'|]).[0]) 
```

Enter fullscreen mode Exit fullscreen mode

活动模式允许我们定义数据的命名分区，这些分区可以用在模式匹配表达式中，就像区别并集的构造函数一样。这个特定的模式首先检查行是否为空，在这种情况下，它返回`Empty`标识符。以`.`字符开头的行是指令(`Directive`)，将被忽略，就像以`;`或`#`开头的整行注释(`LineComment`)。`Label`模式匹配任何包含`:`的行，并且只返回冒号之前的部分(例如`main:`变成了`main`)。所有其他行表示指令，并将返回去掉注释的内容。

### 解析

实际解析逻辑比较简单:

```
let parse (source : string) : ParseResult =
    source.Split("\n")
    |> Array.fold (fun result (line : string) ->
        match line.Trim() with
        | Directive | Empty | LineComment -> result
        | Label l ->
            let labels = Map.add l result.instructions.Length result.labels
            { result with labels = labels }
        | Instruction i ->
            let instruction = parseInstruction i
            { result with instructions = (instruction :: result.instructions) }
    ) { instructions=[]; labels=Map.empty }
    |> fun result -> { result with instructions = List.rev result.instructions } 
```

Enter fullscreen mode Exit fullscreen mode

输入源被分成多行，然后使用 [`Array.fold`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/array.fold%5B%27t%2C%27state%5D-function-%5Bfsharp%5D) 对每一行进行模式匹配，并根据需要更新`ParseResult`记录。`Directive`、`Empty`和`LineComment`对程序没有影响，因此结果将被不加修改地返回。`Label`使用一个[复制和更新记录表达式](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/copy-and-update-record-expressions)向映射添加一个新值，而`Instruction`使用下面的`parseInstruction`辅助函数为元组列表做类似的事情:

```
let private parseInstruction (instruction : string) =
    let operation = instruction.Split(" ").[0].ToLower()
    let operands =
        instruction.Substring(operation.Length).Split(",")
        |> Array.map (fun (s : string) -> s.Trim())
        |> Array.filter ((<>) "")
        |> Array.toList

    operation, operands 
```

Enter fullscreen mode Exit fullscreen mode

这将操作与其操作数分开，然后清理后者。

### 结果

用上面列出的示例程序调用它，会在 F# Interactive 中给出以下输出:

```
parse source;;
 val it : ParseResult =
   {instructions =
     [("add", ["RDI"; "RSI"]); ("mov", ["RAX"; "RDI"]); ("ret", []);
      ("mov", ["RDI"; "1"]); ("mov", ["RSI"; "2"]); ("call", ["plus"]);
      ("ret", [])];
    labels = map [("main", 3); ("plus", 0)];} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

像最初的解析器一样，这是脆弱的。另一方面，它展示了一个很好的 F#特性(主动模式),整个代码的重量只有大约 30 SLOC。考虑到我对这门语言还比较陌生，这可能不是最好的一段代码，但是它写起来很快，而且在类型检查完毕后的第一次尝试中就非常有效。请继续关注本系列的下一期，我将处理原帖子的解释部分。