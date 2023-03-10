# 在 Go 中格式化错误消息

> 原文：<https://dev.to/manuraj17/formatting-error-messages-in-go-345f>

在 go 中，错误是值，因此错误管理是一个显式的过程。没有异常处理或冒泡，你得到一个错误，你决定如何处理它，这通常会导致如下的冗长场景

```
value, err := someFunc()
if err != nil {
  log.Fatal(err)
} 
```

在最近编写一个工具时，我的一个用例有一个调用一些系统函数的方法。这让我想到了一个返回格式化错误消息的场景，为此我使用了`fmt.Errorf()` [1]

它允许我们返回如下所示的格式化错误

```
result, err := externalFunc()
if err != nil {
  return fmt.Errorf("Calling external function failed: %s", err)
} 
```

`errors.New()`不允许我们格式化文本，只传递一个字符串。否则，我们将不得不使用`Sprintf`或等效物来创建一个格式化的字符串，然后传递给`errors.New()`。尽管，你猜怎么着，这是方法内部做的。

参考资料:
【1】[https://godoc.org/fmt#Errorf](https://godoc.org/fmt#Errorf)