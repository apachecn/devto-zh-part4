# 常见。网络陷阱

> 原文：<https://dev.to/techelevator/common-net-gotchas-6nd>

。NET 开发，尤其是 C#已经走过了漫长的道路。我从 2001 年 beta 2 开始使用它，我很喜欢它，但是当你刚刚开始使用时，有一些常见的问题很容易犯。

# 异常情况

## 抛出异常

如果需要抛出异常，不要做以下操作:

```
throw new Exception("Customer cannot be null"); 
```

Enter fullscreen mode Exit fullscreen mode

相反，做一些更有针对性的事情来应对你的例外情况:

```
throw new ArgumentNullException(nameof(customer), "Customer cannot be null"); 
```

Enter fullscreen mode Exit fullscreen mode

这很重要的原因将在下一节解释。

## 捕捉异常

当一个通用的`Exception`实例被抛出并且你希望能够处理它时，你不得不做如下的事情:

```
try {
   DoSomethingRisky();
} catch (Exception ex) {
   if (ex.Message == "Customer cannot be null") {
     // Some specific logic goes here
   } else {
     // Another handler or rethrow
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

捕捉异常捕捉所有形式的异常，但这很少是您实际应该做的。相反，您应该根据您所调用的内容来寻找一些有针对性的异常类型，并且应该有处理程序来处理这些异常，让更多的意外异常出现。

```
try {
   DoSomethingRisky();
} catch (ArgumentNullException ex) {
   // Some specific logic goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

## 再次抛出异常

当捕获一个异常时，您有时想要重新抛出它——特别是当它不符合特定的标准时。正确重新抛出异常的语法与您预期的不同，因为它与最初抛出异常的语法不同。

而不是:

```
try {

  DoSomethingRisky();

} catch (InvalidOperationException ex) {

  if (ex.Message.StartsWith("Cannot access the")) {
     // Some specialized handling logic
  } else {
     throw ex; // We don't have a specific solution for this. Rethrow it
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

做:

```
try {

  DoSomethingRisky();

} catch (InvalidOperationException ex) {

  if (ex.Message.StartsWith("Cannot access the")) {
     // Some specialized handling logic
  } else {
     throw; // The ex is implicit
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你需要这样做的原因是因为。网络堆栈跟踪有效。您希望保留原始堆栈跟踪，而不是使异常看起来像 catch 块中的新异常。如果你使用的是`throw ex`(或者类似的),你会错过一些异常的原始上下文。

# 设计

## 处理不可变类型

有些类型，像`DateTime`据说是不可变的，因为你可以创建一个类型，但是在创建之后你不能改变它。这些类公开了允许您执行基于它们自己的数据创建新实例的操作的方法，但是这些方法不会改变它们被调用的对象，这可能会产生误导。

例如，对于日期时间，如果您试图将跟踪变量提前一天，您应该**而不是**这样做:

```
myMeeting.Date.AddDays(1); 
```

Enter fullscreen mode Exit fullscreen mode

该语句将正确执行和运行，但是`myMeeting.Date`的值将保持原来的值，因为`AddDays`返回新值，而不是修改现有对象。

改变我的会面。日期，您应该改为执行以下操作:

```
myMeeting.Date = myMeeting.Date.AddDays(1); 
```

Enter fullscreen mode Exit fullscreen mode

## TimeSpan 属性

说到日期，TimeSpan 揭示了一些有趣的属性，这些属性可能会引起误解。例如，如果您查看一个 TimeSpan，您可能会试图查看毫秒以了解某件事情花费了多长时间，但是如果它花费了一秒钟或更长时间，您将只获得用于显示目的的毫秒部分，而不是总毫秒数。

不要这样:

```
TimeSpan result = myStopWatch.Elapsed;
Console.Debug("The operation took " + result.Milliseconds + " ms"); 
```

Enter fullscreen mode Exit fullscreen mode

而是像这样使用`TotalX`系列方法:

```
TimeSpan result = myStopWatch.Elapsed;
Console.Debug("The operation took " + result.TotalMilliseconds + " ms"); 
```

Enter fullscreen mode Exit fullscreen mode

## 双比较

当比较双打时，很容易认为你可以做:

```
bool areEqual = myDouble == myOtherDouble; 
```

Enter fullscreen mode Exit fullscreen mode

但是由于双重数学的敏感性，在处理分数时，这些数字可能会略有出入。

相反，要么使用`decimal`类，要么使用ε:
来比较这些数字是否非常接近

```
bool areEqual = Math.Abs(myDouble - myOtherDouble) < Double.Epsilon; 
```

Enter fullscreen mode Exit fullscreen mode

坦率地说，为了避免这样的语法，我倾向于避开`double`而选择`decimal`。

# 杂项

## 字符串追加

在处理字符串时，执行大量的字符串追加逻辑可能会对性能造成很大影响，因为需要为过程中遇到的每个组合创建新的字符串，从而导致更高频率的垃圾收集和明显的性能峰值。

如果您希望平均向一个字符串追加 3 次以上，那么您应该使用一个`StringBuilder`,它在内部使用 techno ninja voo doo 诡计来优化从较小的字符串构建字符串的内存开销。

而不是:

```
string numbers = "";
for (int i = 0; i < 1000; i++) {
   numbers += i;
}

return numbers; 
```

Enter fullscreen mode Exit fullscreen mode

做:

```
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
   sb.Append(i);
}
return sb.ToString(); // Actually builds the final string 
```

Enter fullscreen mode Exit fullscreen mode

## 使用报表

当使用`IDisposable`实例时，确保正确调用`Dispose`很重要——包括遇到异常的情况。未能处置像`SqlConnection`这样的东西会导致数据库没有新请求的可用连接的情况，这会使生产服务器突然停止。

而不是:

```
var conn = new SqlConnection(dbConnStr);
conn.Open();

// Do some things that could throw errors

conn.Close(); 
```

Enter fullscreen mode Exit fullscreen mode

这样做:

```
using (var conn = new SqlConnection(dbConnStr)) {

   conn.Open();

   // Do some things that could throw errors

} // Note that IDisposable will take care of closing the connection if active 
```

Enter fullscreen mode Exit fullscreen mode

如果`conn`不为空，这相当于`try` / `finally`在`conn`上调用`Dispose()`。还要注意，数据库适配器将关闭连接，作为其`IDisposable`实现的一部分。

整体使用导致更干净和更安全的代码。

## 异步作废

当你声明一个不返回任何东西的异步方法时，从语法上来说很容易这样声明:

```
public async void SendEmailAsync(EmailInfo email) { /* ... */ } 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果发生异常，由于线程逻辑在幕后的工作方式，信息将不会正确地传播给调用者。这意味着调用者中的任何`try` / `catch`逻辑都不会按照您预期的方式工作，您将会有错误的异常处理行为。

而是这样做:

```
public async Task SendEmailAsync(EmailInfo email) { /* ... */ } 
```

Enter fullscreen mode Exit fullscreen mode

`Task`返回类型允许。NET 将异常信息发送回调用者，正如您通常所期望的那样。

## 预处理语句

预处理器语句只是简单的老掉牙的东西。对于那些不熟悉的人来说，预处理器语句允许你在编译之前执行编译选项中定义或未定义的操作。

```
 bool allowClipboardUse = true;

#if SILVERLIGHT 
allowClipboardUse = false; // Browser security is fun
// Also, probably weeping and switching to Angular

#end if 
```

Enter fullscreen mode Exit fullscreen mode

预处理器语句的正确使用是针对特定环境的，例如使用 x64 架构的库而不是 x86 架构的另一个库，或者包括移动应用程序的一些逻辑，但不包括共享相同代码的其他应用程序的逻辑。

问题是，人们利用这种能力，并试图在客户特定的逻辑中烘焙，有效地将代码分段，以允许它针对不同的目标进行编译，但是哪一组逻辑规则或 UI 样式是所期望的。

这变得难以维护和测试，并且不能很好地扩展。这也使得重构时容易引入错误，并且随着时间的推移，总体上会降低团队的速度。

有些人主张使用`DEBUG`预处理器定义来允许在本地开发副本上测试逻辑，但是要非常小心。我曾经遇到过一个与反序列化相关的生产 bug，其中开发版本每次都工作得很好，因为它在一个`DEBUG`预处理器语句中定义了一个属性 setter，但是反序列化在该字段的生产中失败了，导致了错误的行为。

同样，要非常小心，并倾向于面向对象的模式，如特定于客户端的逻辑或其他类型的行为逻辑的策略或命令模式。

## 反序列化

说到反序列化，要注意私有变量、没有设置器的属性以及属性设置器或获取器中存在的逻辑。不同的序列化/反序列化库处理事情的方式不同，但这些领域往往会引入一些不明显的错误，在反序列化过程中属性不会正确填充。

* * *

这些是一些常见的。我看到人们遇到的净错误。还有哪些是我没有提到的？