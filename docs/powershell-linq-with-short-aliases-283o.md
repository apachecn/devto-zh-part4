# 具有短别名的 PowerShell LINQ

> 原文：<https://dev.to/sebnilsson/powershell-linq-with-short-aliases-283o>

今天，大多数现代应用程序或代码都处理某种过滤或查询。在 C#和。NET，我们有[语言集成查询(LINQ)](https://dev.to/blog/tag/?t=linq) ，我们也可以在 PowerShell 中访问，因为它是建立在. NET 之上的

要列出 Windows 临时文件夹中前 10 个最大的文件(大于 1 Mb，以字母 W 开头，跳过前 5 个，按大小排序)，带有 LINQ 的 c#-代码看起来有点像这样:

```
new System.IO.DirectoryInfo(@"C:\Windows\Temp")
    .GetFiles()
    .Where(x => x.Length > 1024 && x.Name.StartsWith("W"))
    .OrderByDescending(x => x.Length)
    .Select(x => new { x.Name, x.Length })
    .Skip(5)
    .Take(10)
    .ToList()
    .ForEach(x => Console.WriteLine($"{x.Name} ({x.Length})")); 
```

PowerShell 中的等价逻辑有一点令人畏惧的语法，尤其是如果你不习惯的话:

```
Get-ChildItem "C:\Windows\Temp" `
| Where-Object {$_.Length -gt 1024 -and $_.Name.StartsWith("W")} `
| Sort-Object {$_.Length} -Descending `
| Select-Object -Property Name, Length -First 10 -Skip 5 `
| ForEach-Object {Write-Host "$($_.Name) ($($_.Length))"} 
```

那是**有点显而易见和冗长的**，但是如果你在 PowerShell 中使用命令 `Get-Alias`，你会看到很多有用的别名，这使得语法更简洁，更容易得到关于:
的概述

```
gci "C:\Windows\Temp" `
| ?{$_.Length -gt 1024 -and $_.Name.StartsWith("W")} `
| sort{$_.Length} -Descending `
| select Name, Length -First 10 -Skip 5 `
| %{write "$($_.Name) ($($_.Length))"} 
```

在实际场景中，您可能不会将每个结果都写入控制台，而是让 PowerShell 以默认的网格格式呈现结果。