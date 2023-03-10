# 探索 C# -在 Lychen*中动态附加程序集

> 原文：<https://dev.to/bugmagnet/explorations-in-c-attaching-assemblies-on-the-fly-in-lychen-4i60>

我有这些没人要的脚本工具:Lychen 和 LychenBASIC。(这还没算上我在 github 知识库中的其他 DOS 特有的文件。)我不在乎，真的，因为这些工具只是供我试验用的。

所以今天我扩展了 Lychen 和 LychenBASIC，使它们可以在脚本运行期间加载程序集。ClearScript 通过他们的 [`.Script`属性](https://microsoft.github.io/ClearScript/Reference/html/P_Microsoft_ClearScript_Windows_WindowsScriptEngine_Script.htm)使这变得非常容易，该属性可用于将 C#端项目直接暴露到 V8、JScript 和 VBScript 引擎中。

在我的[Action delegate give print to VBScript](https://dev.to/bugmagnet/action-delegate-gives-print-to-vbscript-46o8)文章中，我演示了使用`.Script`机制向 LychenBASIC 添加一个`print`命令。我现在已经对 Lychen(针对 V8)做了同样的事情，并且可以想象对内部工具 RulesetRunner(针对 JScript)也做了同样的事情。

所以这里是把`print`加到 Lychen
上

```
v8.Script.print = (Action<object>)Console.WriteLine;
v8.Script.attach = (Action<string, string>)Attach; 
```

Enter fullscreen mode Exit fullscreen mode

那么这个`attach`是关于什么的呢？嗯，它是在脚本运行期间加载点网程序集的函数的挂钩。

`Attach`根据前面的演员表，是一个带两根弦的`Action`。第一个是我们想要附加的 dotnet dll 的路径。第二个是 dll 的 PropertyBag 附加到的可选符号。通常，我们可以将第二个参数保留为空字符串，但是偶尔我们需要在其中加入一些内容。稍后我会讨论原因。

所以 Attach 的实现如下:

```
private static void Attach(string dllPath, string name = "")
{
    var htc = new HostTypeCollection();
    try
    {
        var assem = Assembly.LoadFrom(dllPath);
        htc.AddAssembly(assem);
        if (name.Length == 0)
        {
            name = assem.FullName.Split(',')[0];
        }
        v8.AddHostObject(name, htc);
        Console.WriteLine($"Attached {name}");
    }
    catch (FileNotFoundException fnfe)
    {
        Console.WriteLine(fnfe.Message);
    }
    catch (ReflectionTypeLoadException ctle)
    {
        Console.WriteLine(ctle.Message);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在编译成 Lychen 和 LychenBASIC 二进制文件后，我们现在有了一个`attach`过程，它使得下面的事情成为可能:

```
attach "C:\programdata\lychen\newtonsoft.json.dll", "J"
a = J.Newtonsoft.Json.jsonconvert.deserializeobject("{a:1,b:2,c:{d:1}}")
print a.c.d 
```

Enter fullscreen mode Exit fullscreen mode

VBScript 忽略大小写，在 VBScript 中过了一段时间，JavaScript 对区分大小写的上瘾真的需要一些时间来适应。

现在类似的东西出现在 Lychen (V8)

```
attach("C:\\programdata\\lychen\\newtonsoft.json.dll", "J");
var a = J.Newtonsoft.Json.JsonConvert.SerializeObject({a:1,b:2,c:{d:1}});
print(a); 
```

Enter fullscreen mode Exit fullscreen mode

上面指定一个名称来附加 Newtonsoft 的原因。Json 的 PropertyBag 是，如果你不这样做，我的 ClearScript 代码将创建一个从程序集的全名派生的名称，在这种情况下，这个名称是“Newtonsoft”。Json”。这让主持人很困惑。的“.”暗示有一个带有`Json`属性的`Newtonsoft` PropertyBag。如果你编写`Newtonsoft.Json.JsonConvert ...`代码，主机就会崩溃。我应该对此做些什么，但此时解决方案只是提供一个显式的连接点。

委托为点网络应用程序提供了很大的扩展空间。随着我继续在 Lychen 和 LychenBASIC 上进行实验，期望看到更多关于这个主题的内容。