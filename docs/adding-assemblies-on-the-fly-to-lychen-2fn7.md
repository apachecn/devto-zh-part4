# 在 C# ClearScript 项目中动态添加程序集

> 原文：<https://dev.to/bugmagnet/adding-assemblies-on-the-fly-to-lychen-2fn7>

对于那些一直关注我在 JavaScript 中字符串反转的古怪行为的人来说，你可能记得我谈到过 [Lychen](https://github.com/axtens/Lychen) ，我在命令行工具中包装了 [V8](https://v8.dev/) JavaScript，通过 [ClearScript](https://github.com/microsoft/ClearScript) 访问 C#对象。这是一个[Claytons](https://en.wikipedia.org/wiki/Claytons)Node . js——当你没有 Node 时你拥有的节点。

我强烈推荐将 ClearScript 作为一种向您的应用程序添加 JavaScript(或者说是 VBScript)的方式。我们也在我工作的地方使用它，尽管我们仍然使用另一个 JavaScript 引擎，[微软的 JScript](https://en.wikipedia.org/wiki/JScript) 。

[ClearScript FAQtorial](https://microsoft.github.io/ClearScript/Tutorial/FAQtorial.html) 的第 22 个问题问:“我能在一个步骤中公开许多主机类型吗？”我一直在使用那里讨论的技术将各种各样的程序集暴露到 Lychen 中，作为挂在“CS”符号上的一个大的 [PropertyBag](https://docs.microsoft.com/en-us/dotnet/api/microsoft.web.management.server.propertybag?view=iis-dotnet) 。这使得像
这样的电话成为可能

```
var mail = CS.System.Net.Mail;
var message = new mail.MailMessage();

message.From = new mail.MailAddress(
    CSScriptINI.IniReadValue("Settings", "ReplyToEmail", ""),
    CSScriptINI.IniReadValue("Settings", "ReplyToName", "")); 
```

Enter fullscreen mode Exit fullscreen mode

和

```
var data = CS.System.IO.File.ReadAllText(logfile).split(/\r\n/g); 
```

Enter fullscreen mode Exit fullscreen mode

就在最近，我一直试图找出如何添加更多。

FAQtorial 的例子给出了

```
using Microsoft.ClearScript;
…
var typeCollection = new HostTypeCollection("mscorlib", "System", "System.Core");

engine.AddHostObject("clr", typeCollection); 
```

Enter fullscreen mode Exit fullscreen mode

直到最近，我还在做同样的事情，添加了一些像 [RestSharp](http://restsharp.org/) 这样的组件。有一段时间，我认为我需要将这些程序集添加到我的项目中。我认为这主要是因为我让 dll 出现在我的\bin\Debug 文件夹中的唯一方法是让它们的项目出现在我的文件夹中。

我也花了一段时间才发现项目名称不需要出现在程序集名称列表中。确切地说，它是 DLL 的名称(减去“.”。dll”扩展名)。

就在上周，我想出了如何添加任何一个。NET DLL 到一个 Lychen 运行。代码如下。

`AddHostSymbols`从创建符号`CSExtendedHost`和`CSHost`开始，以挂钩到匹配的 ClearScript 对象、`ExtendedHostFunctions`和`HostFunctions`。

```
 private static void AddHostSymbols(ref V8ScriptEngine v8)
        {
            v8.AddHostObject("CSExtendedHost", new ExtendedHostFunctions());
            v8.AddHostObject("CSHost", new HostFunctions()); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，它实例化一个 HostTypeCollection 对象，并向其中添加一堆程序集名称。假设这些文件可以从 GAC 或本地 EXE 中获得。

```
 var htc = new HostTypeCollection();
            foreach (var assembly in new string[] { "mscorlib", "System", "System.Core", "System.Data", "RestSharp", "WebDriver", "WebDriver.Support" })
            {
                htc.AddAssembly(assembly);
            } 
```

Enter fullscreen mode Exit fullscreen mode

接下来是我快乐的原因。

我有一个包含命令行参数的设置字典。我检查`/ASSEMBLIES`的存在。如果符号在那里，我假设它的参数是一个逗号分隔的程序集路径列表。我将每个路径传递给`System.Reflection.Assembly.LoadFrom()`，然后将结果程序集传递给 htc 对象。(`.AddAssembly()`将接受一个字符串或一个汇编对象。)有一点错误检查来捕捉 FileNotFound 错误。

```
 if (Settings.ContainsKey("/ASSEMBLIES"))
            {
                var assemblies = Settings["/ASSEMBLIES"].ToString().Split(',');
                foreach (var assembly  in assemblies)
                {
                    System.Reflection.Assembly assem;
                    try
                    {
                        assem = System.Reflection.Assembly.LoadFrom(assembly);
                        htc.AddAssembly(assem);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine(e.Message);
                    }
                }
            } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将 htc 符号交给 ClearScript 引擎。

```
 v8.AddHostObject("CS", htc);
        } 
```

Enter fullscreen mode Exit fullscreen mode

这意味着

```
Lychen /REPL /ASSEMBLIES:"path to dll" 
```

Enter fullscreen mode Exit fullscreen mode

可能是这样的:

```
>LYCHEN /REPL /ASSEMBLIES:"c:\Windows\Microsoft.NET\Framework64\v4.0.30319\Microsoft.VisualBasic.dll"
Lychen>CS.Microsoft.VisualBasic
Microsoft.ClearScript.PropertyBag
Lychen>CS.Microsoft.VisualBasic.DateAndTime
HostType:DateAndTime
Lychen>CS.Microsoft.VisualBasic.DateAndTime.DateString
07-27-2019 
```

Enter fullscreen mode Exit fullscreen mode

各种有趣的 DevOps 脚本的可能性，ہے نہ ؟