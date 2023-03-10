# 用 C#构建一个更好的库加载器——第 1 部分

> 原文：<https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-1-1446>

与原生库的互操作在游戏开发中非常重要。总有一些驱动程序或低级系统库没有。净支持。C#提供了一种基本的方法来映射和调用本机库中的函数。观察:

```
static class Sqlite3
{
    [DllImport("sqlite3.dll", EntryPoint = "sqlite3_open", CallingConvention = CallingConvention.Cdecl)]
    public static extern int Open(string filename, out IntPtr database);
} 
```

Enter fullscreen mode Exit fullscreen mode

`EntryPoint`字段是可选的，但我喜欢使用它，因为我喜欢将漂亮的 Pascal case C#代码映射到 C 库选择的任何不可靠的风格上。我真的希望我可以简单地在一个方法中定义样式转换，而不是输出每一个映射。

另一个问题是这个库必须在编译时指定**。那是...好吧，我想...*有时是*。如果我有多个变体/架构，并想在运行时选择它，该怎么办？在调用任何本地方法之前，我可以做一些粗略的事情，比如将`sqlite3_x86.dll`重命名为`sqlite3.dll`，但是这很脆弱，也很粗糙。如果不允许我移动图书馆怎么办？如果我需要在应用程序启动时确定库的位置，该怎么办？如果我需要区分四种可能的选择呢？**

 **## 手动装载机

可悲的是。NET 并没有真正提供一种在运行时加载库的平滑方法。我们必须自己手动加载库。这些是必要的方法:

```
public static class WinLoader
{
    private const string Library = "kernel32";

    [DllImport(Library)]
    public static extern IntPtr LoadLibrary(string dllToLoad);

    [DllImport(Library)]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procedureName);

    [DllImport(Library)]
    public static extern int FreeLibrary(IntPtr hModule);
}

public static class UnixLoader
{
    private const string Library = "dl";

    [DllImport(Library, EntryPoint = "dlopen")]
    public static extern IntPtr Open(string path, int mode);

    [DllImport(Library, EntryPoint = "dlsym")]
    public static extern IntPtr LoadSymbol(IntPtr handle, string name);

    [DllImport(Library, EntryPoint = "dlclose")]
    public static extern int Close(IntPtr handle);
} 
```

Enter fullscreen mode Exit fullscreen mode

快速说明:[。NET Core 3 引入了对这些函数](https://devblogs.microsoft.com/dotnet/announcing-net-core-3-preview-3/)的抽象，称为`NativeLibrary`。它将减少基于操作系统选择功能的复杂性。在那之前，这是我们需要手动加载一个本地函数并调用它的代码。首先，我们需要定义一个与我们想要调用的本地函数的结构相匹配的委托。

```
public delegate int OpenDelegate(string file, out IntPtr database); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要实际加载委托。

```
string library = "path/to/custom/sqlite3.dll";
bool isWindows = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
IntPtr libraryHandle = isWindows ?
    WinLoader.LoadLibrary(library) :
    UnixLoader.Open(library, 1);
IntPtr procAddress = isWindows ?
    WinLoader.GetProcAddress(libraryHandle, "sqlite3_open") :
    UnixLoader.LoadSymbol(libraryHandle, "sqlite3_open");

OpenDelegate open = Marshal.GetDelegateForFunctionPointer<OpenDelegate>(procAddress);
open("data.sqlite", out IntPtr database);
// Do stuff.

// When we are all done, we can free up the library.
if (isWindows)
    WinLoader.FreeLibrary(libraryHandle);
else
    UnixLoader.Close(libraryHandle); 
```

Enter fullscreen mode Exit fullscreen mode

加载和使用一个本地函数需要做大量的工作。我们甚至还没有错误处理！对于每一个本机函数，我必须创建一个新的委托类型(除非两个函数碰巧有相同的签名...祝你好运，给这个委托起个合理的名字)，加载函数指针，创建一个委托实例来保存函数指针，然后把这个委托放到有用的地方。即使在上面的小例子中，人们如何实际使用那个`open`委托呢？？？

祝你[旅途](https://www.sqlite.org/c3ref/funclist.html)顺利。

## 梦想载入器

那里*有*是更好的方式。在深入技术细节之前，让我们看看我们*希望*如何加载库。就我个人而言，我只想这样绘制图书馆。

```
interface ISqlite3
{
    int Open(string file, out IntPtr database);
    int Close(IntPtr database);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，在其他地方，我想像这样加载和使用它:

```
ISqlite3 sqlite3 = LoadLibrary<ISqlite3>("path/to/custom/sqlite3.dll");

sqlite3.Open("data.sqlite", out IntPtr db);
sqlite3.Close(db); 
```

Enter fullscreen mode Exit fullscreen mode

那不是很好吗？那是我喜欢的装载机。能做到吗？感谢 C#的魔力，答案是肯定的。它需要冒险深入`System.Reflection`的深处。对于门外汉来说，这是一个可怕的地方，但我保证这趟旅程是值得的。和我在一起。:)

### 迭代方法

让我们从头开始，定义我们的 loader 方法。不幸的是，没有办法约束泛型需要一个接口，所以我们只在运行时检查它。

```
public static T LoadLibrary<T>(string library) where T : class
{
    if (!typeof(T).IsInterface)
        throw new Exception($"Type T must be an interface. {typeof(T)} is not an interface.");

    foreach (var method in typeof(T).GetMethods())
        Console.WriteLine(method.Name); // Let's just see what we find.

    return null; // Patience. :)
} 
```

Enter fullscreen mode Exit fullscreen mode

看到那个叫`GetMethods()`的了吗？这让我们可以在运行时分析接口，并迈出实现它的第一步。正如你所知道的，一个接口需要一个具体的类型来运行。不存在这样的类型...所以我们要做一个...在运行时。`System.Reflection`让我们看看类型信息；`System.Reflection.Emit`让我们**创建类型**并动态编写代码。

一步一步来。

首先，我们需要解决一个小问题:我们需要将 C#方法名与 C 函数名关联起来。我们可以使用属性直接映射它们。

```
interface ISqlite3
{
    [FunctionName("sqlite3_open")]
    int Open(string file, out IntPtr database);

    [FunctionName("sqlite3_close")]
    int Close(IntPtr database);
} 
```

Enter fullscreen mode Exit fullscreen mode

我是说，那很有用，但是，呃...又...[和那个](https://www.sqlite.org/c3ref/funclist.html)玩得开心。

我不应该单独指定每个函数。这里有一个非常清晰的模式:`MethodName`变成了`sqlite3_method_name`。因此，让我们扩充我们的解决方案，允许委托将方法名转换为函数名。

```
public static T LoadLibrary<T>(
    string library,
    Func<string, string> methodNameToFunctionName)
    where T : class
{
    if (!typeof(T).IsInterface)
        throw new Exception($"Type T must be an interface. {typeof(T)} is not an interface.");

    foreach (var method in typeof(T).GetMethods())
        Console.WriteLine(method.Name + " -> " + methodNameToFunctionName(method.Name));

    return null; // Patience. :)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在谈话。现在我不需要绘制每一个地图。懒惰是王道。现在，让我们编写一个方法来专门处理 SQLite 命名约定。这很简单:函数总是以`sqlite3`开头，每个大写字母都需要用下划线和小写字母替换。

```
public static string GetSqliteFunctionName(string methodName)
{
    var prefix = "sqlite3";
    var chars = new char[prefix.Length + methodName.Length * 2];
    prefix.AsSpan().CopyTo(chars);

    int n = prefix.Length;

    for (int i = 0; i < methodName.Length; ++i)
    {
        char c = methodName[i];

        if (char.IsUpper(c))
        {
            chars[n++] = '_';
            chars[n++] = char.ToLowerInvariant(c);
        }
        else
        {
            chars[n++] = c;
        }
    }

    // Real game devs avoid StringBuilder.
    return new string(chars, 0, n);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备好了。现在我们可以像这样加载我们的库。

```
ISqlite3 sqlite3 = LoadLibrary<ISqlite3>("sqlite3.dll", GetSqliteFunctionName); 
```

Enter fullscreen mode Exit fullscreen mode

在[第 2 部分](https://dev.to/thebuzzsaw/building-a-better-library-loader-in-c-part-2-3ek)中，我们将迈出第一步，实际实现一个实现`ISqlite3`的类。**