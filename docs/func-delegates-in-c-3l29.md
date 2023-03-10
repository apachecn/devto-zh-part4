# C#中的 Func 委托

> 原文：<https://dev.to/bugmagnet/func-delegates-in-c-3l29>

今天我向 Lychen 添加了另一个委托，并且可能在不久的将来将它添加到其他内容中。这张是著名的(？出现在各种语言中。

```
 v8.Script.glob = (Func<string, string[]>)Glob; 
```

Enter fullscreen mode Exit fullscreen mode

`Func<>`与`Action<>`的不同之处在于尖括号内的最后一个参数定义了返回类型。`Action<>`是为了`void`的回报。`Func<>`对于其他一切(除了第三件我还不明白的事。)

Glob 是这样实现的。

```
 private static string[] Glob(string wildcard)
        {
            var path = Path.GetDirectoryName(wildcard);
            if (path.Length == 0)
            {
                path = ".\\";
            }
            wildcard = Path.GetFileName(wildcard);
            return Directory.GetFiles(path, wildcard);
        } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，委托的定义必须与实现相匹配。在这种情况下，委托被声明为具有字符串输入和字符串数组输出。

Glob 使用`System.IO.Path`函数将传入的参数分成路径和通配符，然后将两者都交给`Directory.GetFiles`。在未来的某个时候，我将不得不考虑可选的第三个参数给`GetFiles`，但是现在这满足了需要。

另一个问题是一个非本机数组被返回给 Lychen。这意味着`.length`不起作用。相反，您使用`.Length`来获取数组中的项数。事实上，`.map`和`.forEach`等也不行。这个问题也有解决的办法，但还是缺乏动力。