# 为什么 C#中的接口默认实现是一件伟大的事情

> 原文：<https://dev.to/lolle2000la/why-interface-default-implementations-in-c-are-a-great-thing-52nj>

所以最近我读了一篇关于 C# 8.0 接口变化有多糟糕的文章。我认为这值得你自己去阅读，但是对于这篇文章的大部分内容来说，这是没有必要的。

[![htissink image](img/49416516489259bb07336dc093a73412.png)](/htissink) [## C#中的伪特征

### henrick tis sink 1919 年 9 月 4 日 4 分钟阅读

#csharp #dotnet](/htissink/pseudo-traits-in-c-lnp)

让我们先来看看这篇文章的中心论点:

## 对于 C#来说，使用接口来实现特征并不是一个好主意

是的，我同意。它改变了接口的含义。我也同意抽象类的多重继承更适合实现特征。

然而，我认为您真正应该感到兴奋的是使用默认实现的所有其他可能性:

## 与 Java 等语言的互操作性

我认为可以肯定地说，大多数 android 开发者都从中获利。与 Java 库的互操作可能是为 android 开发东西的很大一部分，Java 有默认的接口实现。所以在 C#中拥有这个特性应该有助于两者之间的语言平等。

## 保持(汇编级)向后兼容性

[![Meme, in it is written "It's older code sir, but it checks out"](img/202a920dc7ad26d3c81f12cccd46a8dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NJnZjLVD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cz3nwg1yokmfiq5qwc5c.png)

#### 一个假设的场景

你的公司有一个巨大的代码块，分成 100 多个彼此依赖的库。

现在，您的团队负责向一个库中添加一个特性，比方说(因为这看起来像是一个常见的例子)记录错误和警告。

当前`ILogger`的接口，像`DatabaseLogger`或普通`ConsoleLogger`实现的记录器是这样的:

```
public interface ILogger
{
    void Log(string message);
} 
```

在还没有很多接口的实现和消费者的时候，没有添加更多的警告和错误方法是一个严重的疏忽。

目前，每个人都以不同的方式记录这些错误(例如，通过在前面加上“[ERROR]”或“ERROR:”来记录错误和警告)，该公司希望通过向其他团队提供正确记录错误和警告的方法来实现一个记录错误和警告的约定。

现在，公司中几乎所有其他库都依赖它来保持兼容(通常是在汇编级)。他们中的许多人不经常更新，其中一些人的源代码已经丢失，或者根本不属于公司。

这意味着日志库的新版本必须与旧版本兼容，其中一些包含核心实现。因此，所有新的库版本(以及它们产生的程序集)必须完全兼容，并且不会破坏其他库。

所以现在回到问题上来。我们如何将特性添加到日志库中？

如果我们只是给`ILogger`添加新成员，我们就破坏了所有现有的实现。

```
public interface ILogger
{
    void Log(string message);
    void LogWarning(string message); // this breaks all existing 
    void LogError(Exception ex);     // implementations.
} 
```

所以

```
public class DatabaseLogger // this is legacy
{
    void Log(string message)
    {
        // this is unknown
    }
} 
```

不再管用了。

我们能做些什么呢？

目前，我们可以为此做一些事情，但没有一件是真正令人满意的。

例如，我们可以分别为错误和警告记录器添加新的接口。这看起来应该是这样的:

```
public interface IWarningLogger
{
    void LogWarning(string message);
}

public interface IErrorLogger
{
    void LogError(Exception ex);
} 
```

现在，所有新的和更新的实现都可以实现这三个功能。然而有一个问题。让我们假设在每个类的构造函数中，记录器是通过依赖注入传递的。我们如何将所有这些类型的记录器传递给一个类呢？

一种可能性是为记录器设置一个类型参数，如下所示:

```
public class SomeConsumingClass<Logger> 
    where Logger : ILogger, IWarningLogger, IErrorLogger
{
    public SomeConsumingClass(Logger logger) {}
} 
```

这感觉自然吗？你想用这个污染每个班级吗？这甚至适用于大多数依赖注入框架吗？没有吗？那我们继续吧。

另一种可能性是为每种记录器类型设置单独的构造函数参数，如下所示:

```
public SomeConsumingClass(ILogger logger, IWarningLogger warningLogger, IErrorLogger errorLogger) {} 
```

*Eww* ，好痛。这是如此的**长**。想象一下，有另外四个参数，然后几个月后读到这个。

我想起了另一件事。例如，您可以检查`ILogger`是`IErrorLogger`还是`IWarningLogger`。

```
if (logger is IErrorLogger errorLogger) 
{
    errorLogger.LogError(exception);
} 
```

现在我们可以为所有类型传递一个日志记录器给这个类。

```
public SomeConsumingClass(ILogger logger) 
{
    if (logger is IErrorLogger errorLogger) 
    {
        // example: errorLogger.LogError(exception);
    }
} 
```

太好了。现在我们只剩下一个问题了。我们要么在使用`IErrorLogger`的任何地方进行这样的检查，要么在构造函数中进行一次，将其保存到本地状态。

这两种方法都很难看，而且浪费空间，并且检查使用情况的计算量很大，至少在检查成功并且必须进行强制转换时是这样(即使有更快的方法来比较类型)。

那么我们怎样才能做得更好呢？

### 输入默认实现

当然，有更多的方法来处理这个问题，但是默认实现是最简单和最有效的方法。

我们可以将成员添加到接口中，并在 C# 8.0 中为它们提供实现，如果实现者没有显式实现它们，就会使用这些实现。

基本上是这样的:

```
public interface ILogger
{
    void Log(string message);

    void LogWarning(string message)  // this doesn't need to be implemented,
    {                                // and therefore does not break 
        Log($"[Warning] {message}"); // compatibility
    }

    void LogError(Exception ex)  // same for this
    {
        Log($"[Error] {ex.Message}\n{ex.StackTrace}");
    }
} 
```

就是这样。新成员的实现是可选的，现有的实现仍然有效。此外，您现在可以很容易地从`ILogger`中使用`LogWarning`和`LogError`，即使实现者没有显式地实现它们。

所以当你写:

```
logger.LogWarning("This is a warning!"); 
```

您至少可以期望条目看起来像这样:

```
...
[Warning] This is a warning
... 
```

太好了。也许实现者决定做一些更具体的事情，比如将错误记录在数据库的另一个表上或一个单独的文件中。

但是，如果他还不知道这些成员，一切仍然很好！

## 局部实现

最后一个例子实际上显示了另一个好处。如果您只是将日志写入一个纯文本文件会怎么样？您是否希望实现这三种方法中的每一种，只是在这里添加一些其他文本，并在那里记录一条异常消息和 stacktrace？

拥有默认的实现意味着在这样一个例子中，它是纯文本的，除了前面描述的以外，不会对文本做任何特殊的处理，我们不再需要实现所有的方法。只是一个将消息写入文件的函数。

默认情况下，所有其他方法都调用这一个方法，因此您可以专注于写入文件，而不是正确格式化日志条目。👌

```
public class FileLogger : ILogger, IDisposable
{
    private StreamWriter file; // handle for the file

    public FileLogger(string fileName) 
    {
        var fileStream = File.Open(fileName, FileMode.Append); // open the file

        file = new StreamWriter(fileStream); // make it easy to write to
    }

    public void Log(string message) // this is the important part!!!!
    {
        file.WriteLine(message);
    }

    // Disposable pattern stuff omitted here

    // LogWarning and LogError don't have to be implemented and use
    // the default implementation.
}

var logger = new FileLogger("log.txt");

logger.LogWarning("This is a warning"); 
```

log.txt:

```
...
[Warning] This is a warning
... 
```

## 结论

接口默认实现有很多很好的用途。通过它们来实现特征并不是最好的主意，但是正如我们所看到的，你可以用它们做很多其他的事情。

我真的很期待看到它们在 C#的下一个主要版本中登陆，它们是我个人对 C# 8.0 感到兴奋的最大原因。

谢谢你把这个看完。最后，我有几个问题要问你。

***如何看待 C#中的默认实现？你还能想到什么其他用法？你认为这是个好主意还是坏主意？期待大家的回答！*T3】**