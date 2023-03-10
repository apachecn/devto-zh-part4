# 什么功能，动作？

> 原文：<https://dev.to/canro91/what-the-func-action-4c7g>

`Func`和`Action`有什么区别？这是一个常见的 C#面试问题。让我们去发现它！

**`Func`和`Action`的区别在于它们指向的方法的返回类型。**`Func`和`Action`都是代表。它们指向一个方法，而不是内置或自定义类型。一方面，`Action`引用了一个 void 方法，一个没有返回类型的方法。另一方面，`Func`引用了一个具有返回类型的方法。

## 什么是代表？

一切都始于代表。委托是指向带有一些输入参数和可能的返回类型的方法的指针。换句话说，委托是一个变量，可以保存任何具有给定签名的方法。`Func`和`Action`是内置的委托类型。

委托在处理高阶函数时很有帮助。这是将函数作为参数或返回另一个函数的函数。比如 Javascript 的回调或者 Python 的 decorators 都是高阶函数。

现在，很清楚什么是委托，让我们看看一些`Func`和`Action`声明。举个例子，

*   `Action<Employee>`持有一个接受`Employee`作为参数的 void 方法。
*   `Action`，一个没有任何参数的 void 方法。
*   `Func<Employee, string>`表示接收`Employee`并返回`string`的方法。
*   `Func<string>`没有任何参数，返回`string`。

<figure>

[![What the Func, Action?](img/45ae59d2d5ec6bdfa84280a65f9ff5d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v_EGyT3r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1483821838526-8d9756a6e1ed%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D800%26h%3D400%26fit%3Dcrop)

<figcaption>Let's get Funcy. Photo by [Greyson Joralemon](https://unsplash.com/@greysonjoralemon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

## 如何在一个方法中使用`Func`和`Action`？

你已经用过`Func`，如果你用过 LINQ 的话。但是，一般来说，你用它们作为 lambda 表达式。lambda 表达式是一种匿名方法。只使用主体和参数列表来编写方法是一种简化的表示法。

比如让我们找出工作十年以上的员工。

```
var allEmployees = new List<Employee> { /* Some employees here */ };

Func<Employee, bool> p = (t) => t.YearsWorked >= 10;
allEmployees.Where(p); 
```

Enter fullscreen mode Exit fullscreen mode

或者只是简单的

```
allEmployees.Where(t => t.YearsWorked >= 10); 
```

Enter fullscreen mode Exit fullscreen mode

## 如何声明一个接收`Func`或者`Action`的方法？

要声明一个使用`Func`或`Action`作为输入参数的方法，你必须像使用常规参数一样使用它们。然后，你必须调用`Invoke`或者在名字两边加上圆括号，传递合适的参数值。

让我们看一个使用`Func`的方法的例子。

```
public Employee DoSomething(Func<Employee, string> f)
{
    // Create an employee
    var employee = new Employee();

    // string result = f.Invoke(employee);
    // Or simply
    string result = f(employee);

    // Do something with the result here

    return employee;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 真实世界的例子

`Func`和`Action`作为小厂方法很棒。它们可以用在帮助器或实用程序方法中，将业务逻辑与通用代码分开。

让我们看看`Func`的行动吧！这里有一个来自[洞察力的`Func`的例子。数据库](https://github.com/jonwagner/Insight.Database)创建一个[可靠连接](https://github.com/jonwagner/Insight.Database/wiki/ReliableConnection-and-Cloud-Databases)，一个在某些错误时自动重试的数据库连接。

`ExecuteWithRetry`方法重试事情，并使用`Func`进行重试操作。为了简洁起见，删除了一些代码。

```
public class RetryStrategy : IRetryStrategy
{
    public TResult ExecuteWithRetry<TResult>(IDbCommand commandContext, Func<TResult> func)
    {
        int attempt = 0;
        TimeSpan delay = MinBackOff;

        while (true)
        {
            try
            {
                return func();
            }
            catch (Exception ex)
            {
                // if it's not a transient error, then let it go
                if (!IsTransientException(ex))
                    throw;

                // if the number of retries has been exceeded then throw
                if (attempt >= MaxRetryCount)
                    throw;

                // some lines removed for brevity

                // wait before retrying the command
                // unless this is the first attempt or first retry is disabled
                if (attempt > 0 || !FastFirstRetry)
                {
                    Thread.Sleep(delay);

                    // update the increment
                    delay += IncrementalBackOff;
                    if (delay > MaxBackOff)
                        delay = MaxBackOff;
                }

                // increment the attempt
                attempt++;
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是如何使用方法打开连接。

```
public class ReliableConnection : DbConnectionWrapper
{
    public override void Open()
    {
        RetryStrategy.ExecuteWithRetry(null, () => { InnerConnection.Open(); return true; });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。这就是`Func`和`Action`的区别。请记住，它们只代表一个方法的签名。您可以稍后定义或传递几何体。

快乐的时光！