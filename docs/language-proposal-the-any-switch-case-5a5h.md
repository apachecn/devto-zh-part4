# 语言建议:“任意”转换情况

> 原文：<https://dev.to/nektro/language-proposal-the-any-switch-case-5a5h>

这个特性提议是语言不可知的，因为我以前没有在任何语言中看到过它。如果你知道更好的方法，请告诉我:)

这个特性的标语就像是`switch`有一个`finally`阻塞，但是只有当成功找到的`case`不是`default`时才运行。

下面是它在代码中的样子:

```
switch (number) {
  case 1:
    ...
  case 2:
    ...
  case 3:
    ...
  any:
    ...
  default:
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

当且仅当情况 1、2 或 3 也被捕获时，才会运行`any`块。

至于这个块是在给定的`case`块之前还是之后运行，还有待讨论，我可以看到任何一个都有用的场景。所以两者都允许是最理想的，但是我不清楚如何区分“T2”从句和“T1”从句之间的区别。

过去几天我一直在思考这个问题。欢迎反馈！😃