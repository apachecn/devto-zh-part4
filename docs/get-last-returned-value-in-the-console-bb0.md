# 获取控制台中最后返回的值

> 原文：<https://dev.to/gadimbaylisahil/get-last-returned-value-in-the-console-bb0>

当我们在控制台上工作时，我们需要利用 last 返回值。当我们忘记将它赋给一个变量来使用它，而不得不重新输入/复制它时，这也是很烦人的。

当这种情况发生时，我们可以方便地使用下划线`_`来检索最后的返回值。

```
$ irb

['I am last returned array']
_
['I am last returned array'] 
```

Enter fullscreen mode Exit fullscreen mode