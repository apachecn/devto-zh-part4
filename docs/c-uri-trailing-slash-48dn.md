# C# Uri 尾部斜杠

> 原文：<https://dev.to/nordyj/c-uri-trailing-slash-48dn>

[![](img/2a69d647620cb2af70d7b27576259589.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m3zWm1xh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jamienordmeyer.net/wp-content/uploads/2019/06/what.png)

许多处理路径的库(获取路径“片段”并将它们附加在一起以形成一个更大的路径)并不关心是否添加尾部分隔符。如果你提供给他们，他们就会放过碎片。如果没有，则自动添加片段分隔符。例如，两个节点的*路径*库，以及。NET 的系统。IO.Path 方法，允许你提供文件路径片段，如果之间缺少路径分隔符，会自动添加。

显然，Uri 在. NET 中就是这种情况。通过查看[文档](https://docs.microsoft.com/en-us/dotnet/api/system.uri?view=netframework-4.8)，没有直接等效的方法来连接 Uri 路径片段。您必须这样做的方法是使用 Uri 构造函数。上周我在工作中遇到了这个问题，我花了几个小时来解决这个问题。看看这段代码，我在 LinqPad 中运行了这段代码:

```
void Main()
{
    var baseUri1 = new Uri("http://www.somesite.com/api/person/100/hobbies");
    var baseUri2 = new Uri("http://www.somesite.com/api/person/100/hobbies/");

    var compositeUri1 = new Uri(baseUri1, "13");
    var compositeUri2 = new Uri(baseUri2, "13");

    Console.WriteLine(compositeUri1.ToString());
    Console.WriteLine(compositeUri2.ToString());
} 
```

Enter fullscreen mode Exit fullscreen mode

baseUri1 在 Uri 中没有尾随斜杠，而 baseUri2 有。如果 Uri 类像其他路径类一样工作，我希望两者都是控制台。WriteLine 调用来产生[http://www.somesite.com/api/person/100/hobbies/13](http://www.somesite.com/api/person/100/hobbies/13)。然而，事实并非如此。相反，我得到了以下内容:

> [http://www.somesite.com/api/person/100/13](http://www.somesite.com/api/person/100/13)http://www.somesite.com/api/person/100/hobbies/13T2

注意第一个 Uri 中完全没有*爱好*。它不只是将 13 附加到爱好上…它实际上*在附加 13 之前将*爱好从 Uri 中完全移除。什么…？！？我假设 Uri 类会添加缺少的尾部斜杠，所以当我的代码在部署到我们的开发环境后无法工作时，并不清楚为什么它不能到达它所依赖的 REST API。只有在添加了额外的日志记录之后，我才能够发现用于与我们的 API 对话的 URI 是无效的。

我尽最大努力不做假设，尤其是在使用。NET BCL 类和方法。然而，当某些东西以一种方式工作，而我们期望它以另一种方式工作时，看起来我们仍然会时不时地被咬…