# 隐式和显式接口的一个怪癖

> 原文：<https://dev.to/dotnet/a-quirk-with-implicit-vs-explicit-interfaces-2835>

前几天我开始工作，我做的第一件事就是打开一个 [IL](https://docs.microsoft.com/en-us/dotnet/standard/glossary?WT.mc_id=devto-blog-aapowell#il) [反汇编器](https://docs.microsoft.com/en-us/dotnet/framework/tools/ildasm-exe-il-disassembler?WT.mc_id=devto-blog-aapowell)，然后去镇上读一些我有问题的代码的 IL。

对大多数人来说，这是一个很正常的开始。NET 开发者日对吗？对不对…？

[![Nope](img/d89cc3b9b706b54c3987f9290932f572.png)](https://giphy.com/gifs/Adpodcast-peterson-conner-connersmodernlife-cZ6FrYaMhnITWZZCzy)

这一切都发生在我对[持久实体](https://docs.microsoft.com/en-gb/azure/azure-functions/durable/durable-functions-preview?WT.mc_id=devto-blog-aapowell#entity-functions)做一些探索的时候，它是在构建时宣布的[持久函数 v2 预览版](https://azure.microsoft.com/en-us/blog/advancing-the-developer-experience-for-serverless-apps-with-azure-functions/?WT.mc_id=devto-blog-aapowell)的一部分。我使用的是 beta-2 版本中出现的新的强类型支持，它允许你编写这样的实体:

```
public interface ICounter
{
    void Add(int count);
    void Clear();
}

[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class Counter : ICounter
{
    [JsonProperty]
    public int Count { get; set; }

    public void Add(int count) => Count += count;
    public void Clear() => Entity.Current.DestructOnExit();

    [FunctionName(nameof(Counter))]
    public Task Run([EntityTrigger] IDurableEntityContext ctx) => ctx.DispatchAsync<Counter>();
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以用强类型的方式调用它，而不是使用神奇的字符串:

```
// ctx is IDurableEntityContext
await ctx.SignalEntityAsync<ICounter>(id, proxy => proxy.Add(1)); 
```

Enter fullscreen mode Exit fullscreen mode

这为您处理实体的方式提供了一些很好的类型安全。

当然，我不是用 C#写的，我用的是 F#，这意味着代码看起来更像这样:

```
type ICounter =
    abstract member Add: int -> unit
    abstract member Clear: unit -> unit

[<JsonObject(MemberSerialization = MemberSerialization.OptIn)>]
type Counter() =
    [<JsonProperty>]
    member val Count = 0 with get, set

    interface ICounter with
        member this.Add count =
            this.Count <- this.Count + count

        member this.Clear() =
            this.Count <- 0

    [<FunctionName("Counter")>]
    member __.Run([<EntityTrigger>] ctx : IDurableEntityContext) =
        ctx.DispatchAsync<Counter>() 
```

Enter fullscreen mode Exit fullscreen mode

并且调用是:

```
do! ctx.SignalEntityAsync<ICounter>(id, (proxy : ICounter) => proxy.Add(1)) |> Async.AwaitTask 
```

Enter fullscreen mode Exit fullscreen mode

但是它一直在持久函数框架中抛出一个非常隐晦的错误，即调用方法`Add`失败了，但是它没有告诉我原因。在对持久函数的源代码进行了一系列调试之后，我找到了失败的原因，在`Counter`实例上没有找到`Add`方法。但是 C#代码工作得很好，所以呢？

事实证明，在 F#中，当你实现一个接口时，它是[显式实现](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/interfaces?WT.mc_id=devto-blog-aapowell#calling-interface-methods)**，而在 C#中，你可以隐式实现一个接口*或* [显式实现](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation?WT.mc_id=devto-blog-aapowell)。**

 **## 接口实现，隐式 vs .显式

在真正理解这个问题之前，我们需要了解一下接口实现是如何工作的。让我们用 C#来做，因为它支持这两种类型，我们将从一个**隐式**实现开始。这是你在处理界面时最可能用到的，看起来像这样:

```
interface IFoo {
    void Bar();
}

class Foo : IFoo {
    public void Bar() { }
} 
```

Enter fullscreen mode Exit fullscreen mode

当你做一个隐式接口实现时，你是在向类中添加`public`非静态方法，它们**有**成为`public`非静态的(见这里的)。这意味着类可以被认为是它自己(`Foo`)或者它的接口(`IFoo`)，接口提供的成员是类的一部分，它们隐含在那里。

好了，那么显式接口实现是什么样子的呢？

```
interface IFoo {
    void Bar();
}

class Foo : IFoo {
    void IFoo.Bar() { }
} 
```

Enter fullscreen mode Exit fullscreen mode

这次的不同之处在于，在我们的`class`中，我们有一个非`public` `Bar`函数，它的前缀是接口名(`IFoo`)。由于成员是非`public`的，如果我们想访问接口提供的成员，我们必须**明确**类型是接口。在[文档](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation?WT.mc_id=devto-blog-aapowell)中，有几个场景说明了为什么你想要使用显式接口实现而不是隐式接口实现，这主要归结于如何在一个类型上处理多个接口。

因为 F# *只有*支持显式接口实现，你只把类型看作是它们的接口，这就是我在 C#中倾向于认为类型实现接口的方式，所以有什么大不了的？

## 不是所有的 IL 都是一样生成的

回到我发现的问题，它告诉我，我提供的类型`Counter`，没有可以访问的成员`Add`，现在我们明白了显式接口的成员是如何定义的，这实际上是有意义的，`Counter`上没有成员`Add`，它的名字实际上是`ICounter.Add`，因为它只是接口实现的一部分。

下面是生成的 IL:

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
} 
```

Enter fullscreen mode Exit fullscreen mode

将其与隐式接口实现进行比较:

```
.method public final hidebysig newslot virtual
    instance void Add (
        int32 count
    ) cil managed
{
    .maxstack 8

    IL_0000: ldarg.0
    IL_0001: ldarg.0
    IL_0002: call instance int32 UserQuery/Counter2::get_Count()
    IL_0007: ldarg.1
    IL_0008: add
    IL_0009: call instance void UserQuery/Counter2::set_Count(int32)
    IL_000e: nop
    IL_000f: ret
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`.method`定义之间的区别，我们的显式是`private`，而隐式是`public`，显式的名字是`UserQuery.ICounter.Add` ( `Namespace.InterfaceName.MemberName`)，而隐式是`Add`。

## 为什么有关系？

好吧，了解编译器生成的 IL 的差异是非常有趣的，但是为什么知道这些很重要呢？

事实证明，如果你在做[反射](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection?WT.mc_id=devto-blog-aapowell)，你需要理解这种差异。假设你有一个类型，你想通过它的名字得到这个类型的方法。为此，您应该编写如下代码:

```
var method = typeof(T).GetMethod(
    methodName,
    BindingFlags.IgnoreCase |
    BindingFlags.Public |
    BindingFlags.NonPublic |
    BindingFlags.Instance
); 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您正在寻找的方法名称是由显式实现的接口提供的，那么这将会失败！

在[try.dot.net](https://try.dot.net):
上试试这段代码

```
using System;
using System.Linq;
using System.Reflection;

public class Program
{
  public static void Main()
  {
    Console.WriteLine(string.Join(", ", typeof(FooE).GetMethods(BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.Instance).Select(m => m.Name)));
    Console.WriteLine(string.Join(", ", typeof(FooI).GetMethods(BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.Instance).Select(m => m.Name)));
  }
}

interface IFoo
{
    void Bar();
}

class FooE : IFoo
{
    void IFoo.Bar() => Console.WriteLine("Explicit Bar");
}

class FooI : IFoo
{
    public void Bar() => Console.WriteLine("Implicit Bar");
} 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
IFoo.Bar, Equals, Finalize, GetHashCode, GetType, MemberwiseClone, ToString
Bar, Equals, Finalize, GetHashCode, GetType, MemberwiseClone, ToString 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们对`GetMethod`的调用和提供`Bar`将返回`null`，因为在那个类型上没有这个名字的方法！

## 结论

这是一个很有趣的问题，我已经很久没有深入研究过了。NET 内部机制，了解处理接口实现方式的不同是非常有趣的。

在持久函数上有一个公开的 bug 来解决这个问题，结果我发现了一些人用这个 bug！**