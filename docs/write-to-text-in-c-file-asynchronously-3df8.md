# 用 C#异步写入文本文件

> 原文：<https://dev.to/bradwellsb/write-to-text-in-c-file-asynchronously-3df8>

这是 C#文件 I/O 系列的第二部分。上周，你学习了一个写文件的同步方法。本周，你将学习[如何在异步方法中使用 StreamWriter 构造函数异步写入 C#](https://wellsb.com/csharp/beginners/async-write-to-text-file-csharp/) 中的文本文件。

异步写入文件允许您的应用程序在写入文件时继续执行其他任务。这在写入大量数据或不需要立即从文件中读取内容时非常有用。

异步执行读/写操作对 GUI 应用程序很有用，因为在完成文件 I/O 操作的同时，程序的用户界面仍然可以被重绘(例如，如果调整了窗口的大小)。该技术在性能很重要的应用程序中也很有用，比如开发 ASP.NET web 应用程序。

点击源以了解如何:[源](https://wellsb.com/csharp/beginners/async-write-to-text-file-csharp/)