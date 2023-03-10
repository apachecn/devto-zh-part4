# C# 8 -开关表达式

> 原文：<https://dev.to/mteheran/c-8-switch-expression-3eld>

C# 8 有一些有趣的新特性。其中一个我喜欢的是写开关的新方法。

```
int intValueForSwitch = 0;
string state = "";

switch (intValueForSwitch)
{
    case 0:
      state = "Zero";
    break;
    case 1:
      state = "One";
    break;
    case 2:
      state = "Two";
    break;
    default:
      state = "No valid number";
    break;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码是一个特殊的情况，因为我们使用开关只是为了设置状态变量的值。想法是避免嵌套 if 并使用开关。该建议的代码仍然很长。

在 C# 8 中，这种情况有了一种新的方法。

```
//switch as expression
int intValueForSwitch = 0;

var state = (intValueForSwitch) switch
{
    (0) => "Zero",
    (1) => "One",
    (2) => "Two",
     _ => "NO valid number"
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，变量不需要在这之前创建，这意味着少了一行。使用箭头操作符" = > "我们可以根据状态变量的值返回字符串 it。最后，当变量在任何情况下都不成功时，我们需要使用下划线“_”来设置默认值。

这个特性允许我们减少代码。这种情况下，比如 17 行现在正好 7 行。

可以用 Visual 2019 试试看！！

在下面的存储库中查看所有 C#特性的历史:
[https://github.com/Mteheran/CSharpVersionsDemos](https://github.com/Mteheran/CSharpVersionsDemos)