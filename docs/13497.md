# 用 C#构建一个更好的库加载器——第 2 部分

> 原文：<https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-2-3ek>

在第 1 部分中，我讲述了用 C#加载原生库的挑战。然后，我提出了一个值得构建的 API，它将使图书馆消费者的生活更加简单。它包括准备一个定义所有 C 函数的接口，以及一个将 C#方法名映射到 C 函数名的委托。

现在，是时候开始建设了。在继续之前，让我澄清一些事情:这不是一个关于所有事情的深入教程。净反射。我们将利用最少的资源来建造我们的装载机。反思是一个令人难以置信的深刻话题，可以做惊人的事情。如果你放任它，它会吞噬你。

## 开始新的类型

记住，我们的目标是构建一个实现接口`T`的类型。为了创建类型，它必须包含在模块中，而模块又必须包含在程序集中。记得加`using System.Reflection.Emit;`。

```
bool isWindows = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
IntPtr libraryHandle = isWindows ?
    WinLoader.LoadLibrary(library) :
    UnixLoader.Open(library, 1);

if (libraryHandle == IntPtr.Zero)
    throw new Exception("Unable to load library: " + library);

// We don't care what types these are.
// I'm just gonna use 'var'.
var assemblyName = new AssemblyName { Name = "MyAssembly" };
var assemblyBuilder = AssemblyBuilder.DefineDynamicAssembly(
    assemblyName,
    AssemblyBuilderAccess.Run);
var moduleBuilder = assemblyBuilder.DefineDynamicModule(assemblyName.Name);
var typeBuilder = moduleBuilder.DefineType(
    "NativeLibrary",
    TypeAttributes.Class | TypeAttributes.Public);

typeBuilder.AddInterfaceImplementation(typeof(T));
typeBuilder.DefineDefaultConstructor(MethodAttributes.Public); 
```

Enter fullscreen mode Exit fullscreen mode

我们正在构建类`MyAssembly.NativeLibrary`。它实现了接口`T`，并且有一个空的构造函数。在构造函数中我们不需要任何代码。方法`LoadLibrary<T>`本身可以处理任何必要的构造，因为返回值无论如何都是一个构造的对象。

## 实施方法

```
var methodAttributes =
    MethodAttributes.Public |
    MethodAttributes.Final |
    MethodAttributes.HideBySig |
    MethodAttributes.NewSlot |
    MethodAttributes.Virtual;

foreach (MethodInfo method in typeof(T).GetMethods())
{
    ParameterInfo[] parameters = method.GetParameters();

    // Create a method that matches the interface method
    // definition perfectly. Whatever modifiers are
    // applied to the interface method's parameters or
    // return value are applied to the implementations
    // parameters and return value.
    MethodBuilder methodBuilder = typeBuilder.DefineMethod(
        method.Name,
        methodAttributes,
        CallingConventions.HasThis,
        method.ReturnType,
        method.ReturnParameter.GetRequiredCustomModifiers(),
        method.ReturnParameter.GetOptionalCustomModifiers(),
        parameters.Select(p => p.ParameterType).ToArray(),
        parameters.Select(p => p.GetRequiredCustomModifiers()).ToArray(),
        parameters.Select(p => p.GetOptionalCustomModifiers()).ToArray());

    typeBuilder.DefineMethodOverride(methodBuilder, method);

    // To be continued...
} 
```

Enter fullscreen mode Exit fullscreen mode

首先我来说一下`methodAttributes`。我肯定你想知道这些旗子代表什么。你可能会惊讶地发现我不知道。良好的...这不是真的，但这是一个重要的教学时刻。关键是，当我第一次创建这段代码时，我不需要知道它们是什么意思。

我的目标是从一个接口实现一个方法。所以，我简单地求助于真理来知道在这里放什么。我用的是 [SharpLab](https://sharplab.io/) 。这个工具太神奇了。现在打开它，[把这个代码放进](https://sharplab.io/#v2:EYLgxg9gTgpgtADwGwBYA0AXEBLANmgExAGoAfAAQGYACcgJmoGFqRaBGAOgEkARbAZwAOEfgENguGNQCwAKADecgJBVaKanyEiYACgCUc6keqLZx6gF85FoA===)。

```
public class C : System.IDisposable 
{
    public void Dispose()
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将右窗格切换到 **IL** 模式。您应该会看到这样的输出:

```
.class private auto ansi '<Module>'
{
} // end of class <Module>

.class public auto ansi beforefieldinit C
    extends [System.Private.CoreLib]System.Object
    implements [System.Private.CoreLib]System.IDisposable
{
    // Methods
    .method public final hidebysig newslot virtual 
        instance void Dispose () cil managed 
    {
        // Method begins at RVA 0x2050
        // Code size 2 (0x2)
        .maxstack 8

        IL_0000: nop
        IL_0001: ret
    } // end of method C::Dispose

    .method public hidebysig specialname rtspecialname 
        instance void .ctor () cil managed 
    {
        // Method begins at RVA 0x2053
        // Code size 8 (0x8)
        .maxstack 8

        IL_0000: ldarg.0
        IL_0001: call instance void [System.Private.CoreLib]System.Object::.ctor()
        IL_0006: nop
        IL_0007: ret
    } // end of method C::.ctor

} // end of class C 
```

Enter fullscreen mode Exit fullscreen mode

看到我的`Dispose`方法了吗？看到定义里的汤这个词了吗？它被标记为`public final hidebysig newslot virtual`。因为这是对接口实现的期望，所以我把它们都包括进来了。他们实际上的意思对于我们想要完成的事情并不重要。如果你进入了 [CIL 的兔子洞](https://en.wikipedia.org/wiki/Common_Intermediate_Language)，就无处可逃。这是一个迷人的世界，但我们有游戏要做！专注！

```
 // Continuing inside the loop...

    string functionName = methodNameToFunctionName(method.Name);

    // You may remember this from part 1.
    IntPtr procAddress = isWindows ?
        WinLoader.GetProcAddress(libraryHandle, functionName) :
        UnixLoader.LoadSymbol(libraryHandle, functionName);

    if (procAddress == IntPtr.Zero)
        throw new Exception("Unable to load function " + functionName); 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了函数指针。这就是奇迹发生的地方。系好安全带。

```
 // Continuing inside the loop...

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

这是发出实际方法体的部分。CIL 是一台[堆垛机](https://en.wikipedia.org/wiki/Stack_machine)。目标是将每个参数转发到 C 函数上，因此每个参数都被推送到堆栈上。然后将 C 函数地址压入堆栈。然后通过`Calli`调用本地调用。返回值(如果有)被推回堆栈，并转发回该方法的调用方。

这种方法的一个好处是函数指针被有效地硬编码到方法体中。无法从其他地址读取数据。代码本身将一个常数加载到堆栈上。

## 整理类型

好吧。我们完成了那个循环。是时候做对象了。

```
Type type = typeBuilder.CreateType();
return (T)Activator.CreateInstance(type); 
```

Enter fullscreen mode Exit fullscreen mode

如果没有抛出异常，我们就可以开始了。这意味着类型被成功创建，CIL(足够)正确，并且对象被实例化。你现在可以通过这个接口调用 C 函数了！

在[第 3 部分](https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-3-2hkc)中，我们将讨论在它成为生产就绪工具之前需要解决的细节。