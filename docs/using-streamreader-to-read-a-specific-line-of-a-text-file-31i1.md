# 在 C#中读取文本文件的特定行

> 原文：<https://dev.to/bradwellsb/using-streamreader-to-read-a-specific-line-of-a-text-file-31i1>

你有没有在 C#中需要从一个文本文档的特定行号中获取一个值？毫无疑问，您已经了解到使用 StreamReader 对象没有一个本地方法可以做到这一点。该类包含一个 ReadLine()方法，但不幸的是，它没有一个可以接受行号作为参数的重载构造函数。

在本教程中，您将编写一个自定义方法，当您需要从文本文件中读取特定行的内容时，可以随时调用该方法。

这是关于 C#中文件 I/O 的系列文章的第四部分。

[阅读教程](https://wellsb.com/csharp/beginners/async-write-to-text-file-csharp/)