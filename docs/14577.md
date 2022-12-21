# 千万不要在 PHP 的循环中使用 array_merge

> 原文：<https://dev.to/klnjmm/never-use-arraymerge-in-a-for-loop-in-php-5go1>

我经常看到人们在一个`for` / `foreach` / `while`循环中使用`array_merge`函数😱像这样:

```
$arraysToMerge = [ [1, 2], [2, 3], [5, 8] ];

$arraysMerged = [];
foreach($arraysToMerge as $array) {
    $arraysMerged = array_merge($arraysMerged, $array);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常糟糕的做法，因为它是一个性能杀手(尤其是在内存中)。

从 PHP 5.6 开始，有了一个新的操作符:spread 操作符

```
$arraysToMerge = [ [1, 2], [2, 3], [5,8] ];

$arraysMerged = array_merge([], ...$arraysToMerge); 
```

Enter fullscreen mode Exit fullscreen mode

*   不再有性能问题
*   奖励:不再有`for` / `foreach` / `while`循环
*   奖金:在*一行*中处理

现在查看您的代码库，找到可以改进的代码👩‍💻👨‍💻！

* * *

## 感谢您的阅读，保持联系！

如果你喜欢这篇文章，请分享。我也在写一封你可能会喜欢的开发信。