# 操作代理将打印输出给 VBScript

> 原文：<https://dev.to/bugmagnet/action-delegate-gives-print-to-vbscript-46o8>

今天我学习了动作委托，用于那些返回无效的情况。

我正在编写我的 Lychen 工具的 VBScript 版本，并且想要一个“打印”命令。我可以制作一个打印“sub”并使用[执行](https://microsoft.github.io/ClearScript/Reference/html/M_Microsoft_ClearScript_ScriptEngine_Execute_1.htm)或[评估](https://microsoft.github.io/ClearScript/Reference/html/M_Microsoft_ClearScript_ScriptEngine_Evaluate_1.htm)将它评估到 [VBScriptEngine](https://microsoft.github.io/ClearScript/Reference/html/T_Microsoft_ClearScript_Windows_VBScriptEngine.htm) 中，这样就可以很好地工作了。但是我想知道是否可以给名为“Print”的[脚本](https://microsoft.github.io/ClearScript/Reference/html/P_Microsoft_ClearScript_Windows_WindowsScriptEngine_Script.htm)对象分配一个“函数指针”。事实证明你*可以*做到这一点。

首先，它被称为`delegate`而不是“函数指针”，有两种类型:`Action`用于返回`void`的函数，而`Func`用于返回其他值的函数。

所以在我的程序类中，我有下面的声明

```
public static VBScriptEngine vbscriptEngine = new VBScriptEngine(WindowsScriptEngineFlags.EnableDebugging); 
```

Enter fullscreen mode Exit fullscreen mode

后来，在一个初始化例程中，我有

```
vbscriptEngine
    .Script
    .Print = (Action<object>)Console.WriteLine; 
```

Enter fullscreen mode Exit fullscreen mode

这一点神奇之处在于将`Print`添加到 VBScriptEngine 内部的名称空间中，所述名称指向 Console 的实例。接受一个类型为`object`的参数的 WriteLine。

不多，但它支持这样的事情(从 REPL 模式(VBScript 不区分大小写)):

```
Lychen>print 1
1
Lychen>print "lukim yu, wantok"
lukim yu, wantok
Lychen>print CS.System.DateTime.Now.ToString("o")
2019-08-14T12:02:12.9315236+08:00
Lychen> 
```

Enter fullscreen mode Exit fullscreen mode