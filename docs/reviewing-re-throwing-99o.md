# 复习再投掷

> 原文：<https://dev.to/timyhac/reviewing-re-throwing-99o>

# TL；速度三角形定位法(dead reckoning)

*   隐式重新抛出允许您对异常做一些事情，然后让它冒泡，就好像您的代码没有碰到它一样。
*   显式重新抛出通过重置堆栈跟踪，在传递异常对象之前对其进行变异
*   在这两种情况下，异常对象都是通过引用传递的。

* * *

我已经涉足 c#有一段时间了，但是我认为我的 python 和 javascript 知识应该给我一点优势。我当然不是从零开始，但我也不会认为自己是这两种语言的专家。

所以，为了向自己证明我理解并能使用 c#，我给自己设定了一个目标，要在 2019 年底通过 [70-483](https://www.microsoft.com/en-us/learning/exam-70-483.aspx) 考试。

我决定看看以前的考试，偶然发现了这个网站。给我的第一个问题是关于重新抛出一个异常，包括它的原始堆栈跟踪。这些要求是:

*   使用`ExceptionLogger`类的`LogException()`方法记录所有异常。
*   重新引发原始异常，包括整个异常堆栈。

这是一道选择题，选项如下:

```
// Option A
catch (Exception ex)
{
    ExceptionLogger.LogException(ex);
    throw;
}

// Option B
catch (Exception ex)
{
    ExceptionLogger.LogException(ex);
    throw ex;
}

// Option C
catch
{
    ExceptionLogger.LogException(new Exception());
    throw;
}

// Option D
catch
{
    ExceptionLogger.LogException(ex);
    throw;
} 
```

我马上就知道选项 C 不可能是它，因为 ExceptionLogger 被传递了一个新的`Exception`(显然没有原始异常的任何数据)，就此而言，`catch`不是必须有一个括号来描述`Exception`类型吗？它甚至可能无法编译！(后来我发现确实如此...)

它也不可能是选项 D，因为它显然不能编译(`ex`在任何地方都没有定义)，尽管说实话，我认为它不会编译，因为`catch`语句上的括号“丢失”。

所以只能选择 A 和 b 了。

我选择了选项 b。

答案是选项 a。

* * *

回想起来，我真的不确定`throw`无一例外做了什么。我以前见过它，但我想我认为它和`throw ex`不会有什么不同。

选项 B 被称为**显式**重掷，而选项 A 被称为**隐式**重掷。在大多数情况下，你希望使用隐式重抛出，因为它保留了堆栈，并且这是一个众所周知的[规则](https://docs.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2012/ms182363(v=vs.110))。

但我的目标是深入理解，而不仅仅是盲目照搬规则，所以我开始写一些代码来测试到底发生了什么...

* * *

## 迭代 1 次

为了验证答案是否正确，我决定创建一些代码，让我看看在显式抛出异常时会发生什么:

```
try
{
    try
    {
        throw new DivideByZeroException();
    }
    catch (Exception ex1)
    {
        Console.WriteLine(ex1); // System.DivideByZeroException ... Line 13
        throw ex1;  // or throw(ex1);
    }
}
catch (Exception ex2)
{
    Console.WriteLine(ex2); // System.DivideByZeroException ... Line 18
} 
```

控制台显示第 13 行和第 18 行有一个`DivdeByZeroException`。

即使我已经通读了前面提到的文章，它仍然令人惊讶！毕竟，异常只发生在第 13 行。

因此，我从 catch 块中删除了显式重新抛出，以查看它们所说的是否真:

```
try
{
    try
    {
        throw new DivideByZeroException();
    }
    catch (Exception ex1)
    {
        Console.WriteLine(ex1); // System.DivideByZeroException ... Line 13
        throw;
    }
}
catch (Exception ex2)
{
    Console.WriteLine(ex2); // System.DivideByZeroException ... Line 13
} 
```

两次调用都显示异常发生在第 13 行。

## 迭代 2 次

我推断，一定有一个新的异常正在被创建，这就是为什么第二个 WriteLine()调用在第 18 行显示了异常！

所以我修改了我的代码如下:

```
Exception A = null;
Exception B = null;
try
{
    try
    {
        throw new DivideByZeroException();
    }
    catch (Exception ex1)
    {
        A = ex1;
        throw ex1;
    }
}
catch (Exception ex2)
{
    B = ex2;
}
Console.WriteLine(A); // System.DivideByZeroException ... Line 15
Console.WriteLine(B); // System.DivideByZeroException ... Line 15 
```

现在*的两个* `WriteLine()`调用都显示异常发生在第 20 行，而不是第 15 行(现在是`DivideByZeroException`的位置)。

我惊呆了！

经过一段时间的努力，我意识到当我们抛出一个异常时，异常对象是通过引用传递给 catch 语句的，而不是通过值。因此，A 和 B 指向同一个对象。通过查看对象是否相等，我可以快速检查这个理论:

```
Console.WriteLine(A == B);    // Output: True 
```

事后看来，这似乎是显而易见的——但它对我理解这里发生的事情至关重要。

因此，我之前的迭代和这次迭代之间的唯一区别是异常被序列化为文本的点；投掷前和投掷后。

异常 A 通过作为 throw 语句的参数被传递给第二个 catch 块，这就是使它发生变异的原因:

**关键提示:**显式抛出实际上是通过重置异常对象的堆栈跟踪来改变异常对象。

* * *

Jalpesh Vadjama 的这篇博文也帮助我理解了内部工作原理。