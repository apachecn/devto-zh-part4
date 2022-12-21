# 将 C#转换成 VB.Net-需要一些帮助吗

> 原文：<https://dev.to/bugmagnet/converting-c-to-vb-net-need-some-help-11k5>

```
using System;
using System.Collections.Generic;

namespace PreHOPL
{
    static class Program
    {
        private static readonly Dictionary<string, Tuple<int, Delegate>> dict = new Dictionary<string, Tuple<int, Delegate>>()
        {
            ["SAYLIT"] = new Tuple<int, Delegate>(1, (Action<string>)System.Console.WriteLine),
        };

        private static void Main(string[] args)
        {
            dict["SAYLIT"].Item2.DynamicInvoke("Hello World!");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚尝试了 4 种不同的在线工具( [Telerik](http://converter.telerik.com/) 、 [ICSharpCode](https://codeconverter.icsharpcode.net/) 、 [Carlosag](https://www.carlosag.net/tools/codetranslator/) 和 [DeveloperFusion](https://www.developerfusion.com/tools/convert/csharp-to-vb/?batchId=1a979e6f-201c-4454-a6a7-1e3b944c8501) )来为一位同事将它转换成 VB.Net。三个发出了一条长长的错误信息，第四个正在原地打转。想法？