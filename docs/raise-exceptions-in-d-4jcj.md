# 在 D 中引发异常

> 原文：<https://dev.to/jessekphillips/raise-exceptions-in-d-4jcj>

我想在我的 Python 脚本中抛出异常。这里是你需要知道的 D.

```
throw new Exception("Error message.");

import std.exception;
enforce(true, "Error message"); 
```

Enter fullscreen mode Exit fullscreen mode

标准库提供了一个函数来检查条件，如果条件为假，它将抛出错误消息。

也可以指定一个定制的异常来使用。在 D 中，从`Throwable`派生的任何东西都可以被抛出或捕捉。`Error`从 Throwable 派生，并且不打算被捕获，因为编译器可能不会展开堆栈，因此除了处理代码之外不会调用(不保证会调用 catch)。

```
class MyException : Exception {
... /// https://tour.dlang.org/tour/en/basics/exceptions
} 
```

Enter fullscreen mode Exit fullscreen mode