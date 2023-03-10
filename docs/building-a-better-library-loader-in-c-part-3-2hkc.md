# 用 C#构建一个更好的库加载器——第 3 部分

> 原文：<https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-3-2hkc>

在[第 2 部分](https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-2-3ek)中，我通过`System.Reflection.Emit`回顾了运行时类型的创建。我们能够定义我们想要使用的接口，我们方便的 dandy `LoadLibrary<T>`方法将为我们处理所有的细节。这本身就很神奇。

但我们还没完。我们需要一些生活质量的改善。

## 返回字符串

让我们回到我们的 SQLite 接口。

```
interface ISqlite3
{
    int Open(string file, out IntPtr database);
    int Close(IntPtr database);
} 
```

Enter fullscreen mode Exit fullscreen mode

看到那个`Open`方法如何为它的一个参数取一个`string`了吗？它真的*不应该*工作，因为`System.String`与`sqlite3_open` [期望](https://www.sqlite.org/c3ref/open.html)的`const char*`是完全不同的数据结构。第一个问题是`System.Char`是 2 个字节，C 中的`char`是 1 个字节。第二个问题是。NET 嵌入它们的长度，C #字符串使用空终止。

谢天谢地，。NET 预见到了这一点，[为我们解决了这个问题](https://docs.microsoft.com/en-us/dotnet/standard/native-interop/type-marshaling#default-rules-for-marshaling-common-types)。因此，这种方法的工作方式与您预期的一样！您可以毫无困难地简单调用`sqlite3.Open("stuff.sqlite", out var db);`,因为您的字符串被自动转换为您的 C 库实际上可以处理的以 null 结尾的 C 字符串。

然而，如果我们想要一个*返回*一个字符串的方法呢？再来看`sqlite3_errmsg`。

```
string Errmsg(IntPtr database); 
```

Enter fullscreen mode Exit fullscreen mode

我有坏消息要告诉你。如果你调用这个，你的程序会崩溃。如果它不崩溃，无论如何它都会表现得很糟糕。我们对传入值的良好类型封送显然不适用于传出值。那么，我们如何解决这个问题？好吧，既然 C 函数返回一个指针(`const char*`)，我们需要把它捕获为一个指针。

```
IntPtr Errmsg(IntPtr database); 
```

Enter fullscreen mode Exit fullscreen mode

太好了。我如何从中获得一个字符串呢？如果你用了。网芯，[答案很简单](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.marshal.ptrtostringutf8?view=netcore-2.2#System_Runtime_InteropServices_Marshal_PtrToStringUTF8_System_IntPtr_System_Int32_)。

```
public static string GetString(IntPtr cString)
{
    return cString == IntPtr.Zero ? null : Marshal.PtrToStringUTF8(cString);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不吸毒。网络核心...你真可耻。开始使用。网芯立马！如果你绝对不能使用。NET Core(或足够高版本的。NET 标准)，您将不得不自己解析该字符串。

```
public static string GetStringTheHardWay(IntPtr cString)
{
    if (cString == IntPtr.Zero)
        return null;

    var bytes = new List<byte>();
    int n = 0;

    while (true)
    {
        byte b = Marshal.ReadByte(cString, n++);

        if (b == 0)
            break;

        bytes.Add(b);
    }

    return Encoding.UTF8.GetString(bytes.ToArray());
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了一种方法来读取从 C 函数返回的字符串，但这仍然是太多的工作。现在，**每次**我想从我的方法中得到一个字符串，我必须调用`GetString(returnedValue)`。那不行！我想比那更懒！所以，让我们重新看看我们的代码生成器。

```
ILGenerator generator = methodBuilder.GetILGenerator();

for (int i = 0; i < parameters.Length; ++i)
    generator.Emit(OpCodes.Ldarg, i + 1); // Arg 0 is 'this'. Don't need that one.

if (Environment.Is64BitProcess)
    generator.Emit(OpCodes.Ldc_I8, ptr.ToInt64());
else
    generator.Emit(OpCodes.Ldc_I4, ptr.ToInt32());

generator.EmitCalli(
    OpCodes.Calli,
    CallingConvention.Cdecl,
    method.ReturnType,
    parameterTypes);

generator.Emit(OpCodes.Ret); 
```

Enter fullscreen mode Exit fullscreen mode

`Calli`指令将 C 函数的返回值放到堆栈上，后续的`Ret`指令将其转发给调用者。这里有一个机会:我们提前知道回报类型是什么。为什么不直接在这里处理转换呢？如果我们知道接口上的方法想要返回`string`，我们可以负责在内部返回`IntPtr`，并在返回之前对其进行转换。

```
// This should be stored outside the loop iterating over the interface methods.
MethodInfo getStringMethod = typeof(WhateverClassHasTheMethod).GetMethod(nameof(GetString));

ILGenerator generator = methodBuilder.GetILGenerator();

for (int i = 0; i < parameters.Length; ++i)
    generator.Emit(OpCodes.Ldarg, i + 1); // Arg 0 is 'this'. Don't need that one.

if (Environment.Is64BitProcess)
    generator.Emit(OpCodes.Ldc_I8, ptr.ToInt64());
else
    generator.Emit(OpCodes.Ldc_I4, ptr.ToInt32());

bool returnsString = method.ReturnType == typeof(string);

generator.EmitCalli(
    OpCodes.Calli,
    CallingConvention.Cdecl,
    returnsString ? typeof(IntPtr) : method.ReturnType,
    parameterTypes);

if (returnsString)
    generator.Emit(OpCodes.Call, getStringMethod);

generator.Emit(OpCodes.Ret); 
```

Enter fullscreen mode Exit fullscreen mode

你看到我们做了什么吗？发送到`EmitCalli`的返回类型现在有特殊处理。如果我们最终想要一个`string`回来，我们首先要捕获一个`IntPtr`。因为那个`IntPtr`值在堆栈的顶部，在`Calli`指令之后，所以它在合适的位置作为调用`GetString`的参数。多方便啊！对`GetString`的调用将把`string`结果放到堆栈上，然后我们的`Ret`指令将把它返回给调用者。

瞧。返回`string`的方法现在工作正常。

## 库清理

还记得在[第 1 部分](https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-1-1446)中我们谈到的装载和卸载库吗？我们的幻想愉快地装载了库，但却没有办法卸载库。这对许多开发者来说可能无关紧要。游戏加载库，在应用程序的整个生命周期中使用它们，然后在最后跳过卸载，这是很正常的。不管怎样，我认为我们最好至少提供*某种方式*来卸载这些库，因为随着时间的推移，有一些应用程序需要加载和卸载库。

这里我们可以采取几种方法。我建议利用`IDisposable`,因为它是惯用的，并且带来了一些好处。最大的好处(在我看来)是能够将你的库放到一个`using`块中。

```
using (ISqlite3 sqlite3 = LoadLibrary<ISqlite3>("custom_sqlite3.dll", GetSqliteFunctionName))
{
    sqlite3.Open("my_data.sqlite", out IntPtr database);
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个好处是，我们可以在`LoadLibrary<T>`定义本身中强制执行这一点。忘记将接口标记为`IDisposable`会导致编译器错误。

```
public interface ISqlite3 : IDisposable
{
    int Open(string file, out IntPtr database);
    int Close(IntPtr database);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public static T LoadLibrary<T>(
    string library,
    Func<string, string> methodNameToFunctionName)
    where T : class, IDisposable
{
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，让我们制作一个 helper 方法来实现`Dispose`方法。

```
private static void CreateDisposeMethod(
    TypeBuilder typeBuilder,
    IntPtr libraryHandle)
{
    MethodBuilder methodBuilder= typeBuilder.DefineMethod(
        nameof(IDisposable.Dispose),
        MyMethodAttributes);
    typeBuilder.DefineMethodOverride(
        methodBuilder,
        typeof(IDisposable).GetMethod(nameof(IDisposable.Dispose)));

    Type ptrType = Environment.Is64BitProcess ? typeof(long) : typeof(int);
    ConstructorInfo intPtrConstructor = typeof(IntPtr).GetConstructor(new Type[] {ptrType});
    MethodInfo closeMethod = RuntimeInformation.IsOSPlatform(OSPlatform.Windows) ?
        typeof(WinLoader).GetMethod(nameof(WinLoader.FreeLibrary)) :
        typeof(UnixLoader).GetMethod(nameof(UnixLoader.Close));

    ILGenerator generator = methodBuilder.GetILGenerator();

    if (Environment.Is64BitProcess)
        generator.Emit(OpCodes.Ldc_I8, libraryHandle.ToInt64());
    else
        generator.Emit(OpCodes.Ldc_I4, libraryHandle.ToInt32());

    generator.Emit(OpCodes.Newobj, intPtrConstructor);
    generator.Emit(OpCodes.Call, closeMethod);
    generator.Emit(OpCodes.Pop); // Toss the returned int.
    generator.Emit(OpCodes.Ret);
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这个方法必须构造`IntPtr`实例，因为没有办法将常量`IntPtr`嵌入到代码中。正如我之前提到的，我推荐使用[夏普实验室](https://sharplab.io/)来帮助确定你想要发射什么样的离子液体。我不是信息技术专家；我只是为我想要完成的事情编写 C#代码，并研究产生的 IL。

只要记住在你的`LoadLibrary<T>`方法中调用`CreateDisposeMethod`，你就成功了。在第 4 部分中，我们将针对游戏开发做更多的升级。:)