# 不要忽略捕获的异常

> 原文：<https://dev.to/pipan/don-t-ignore-caught-exceptions-4cg4>

因为一个函数，我度过了糟糕的一天，它忽略了所有被捕获的异常。代码看起来像这样，它是我正在使用的第三方库的一部分。当时我不知道实施细节。

```
function doSomething() {
    try {
        // some code
    } catch (err) {}
} 
```

像这样的调试功能，从不崩溃，也不打印任何错误信息，简直是一场噩梦。我通常信任第三方库，起初认为问题出在我的代码上。这就是为什么我花了几个小时调试这个。

那么，如何处理异常呢？我认为让其他程序员(使用这个库的人)来处理这个异常是有效的。尤其是，如果你不知道该怎么处理它。

你怎么想呢?有没有这种情况可以接受？也许是生产环境？我个人不这么认为，因为你想了解生产中的错误，并将其存储在某个地方。