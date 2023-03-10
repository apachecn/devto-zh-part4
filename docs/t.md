# IAsyncEnumerable <t>in 有什么大不了的。网芯 3.0？</t>

> 原文：<https://dev.to/dotnet/what-s-the-big-deal-with-iasyncenumerable-t-in-net-core-3-1eii>

最激动人心的特性之一。NET Core 3.0 和 C# 8.0 已经增加了`IAsyncEnumerable<T>`(又名异步流)。但是它有什么特别之处呢？我们现在能做以前不可能的事情吗？

在本文中，我们将看看`IAsyncEnumerable<T>`打算解决什么挑战，如何在我们自己的应用中实现它，以及为什么`IAsyncEnumerable<T>`将在许多情况下取代`Task<IEnumerable<T>>`。

> 查看中所有的[新特性。网络核心 3](https://docs.microsoft.com/en-us/dotnet/core/whats-new/dotnet-core-3-0?WT.mc_id=devto-blog-antchu)

## 之前的生活`IAsyncEnumerable<T>`

也许说明为什么`IAsyncEnumerable<T>`有用的最好方式是看看没有它会有什么挑战。

假设我们正在构建一个数据访问库，我们需要一个方法来查询数据存储或 API 中的一些数据。该方法返回`Task<IEnumerable<T>>`是很常见的，比如:

```
public async Task<IEnumerable<Product>> GetAllProducts() 
```

Enter fullscreen mode Exit fullscreen mode

为了实现该方法，我们通常异步执行一些数据访问，然后在完成时返回所有数据。当我们需要进行多次异步调用来获取数据时，这个问题变得更加明显。例如，我们的数据库或 API 可以在页面中返回数据，就像这个使用 Azure Cosmos DB:
的实现

```
public async Task<IEnumerable<Product>> GetAllProducts()
{
    Container container = cosmosClient.GetContainer(DatabaseId, ContainerId);
    var iterator = container.GetItemQueryIterator<Product>("SELECT * FROM c");
    var products = new List<Product>();
    while (iterator.HasMoreResults)
    {
        foreach (var product in await iterator.ReadNextAsync())
        {
            products.Add(product);
        }
    }
    return products;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们在 while 循环中对所有结果进行分页，实例化所有产品对象，将它们放入一个`List<Product>`中，最后我们返回整个结果。这是非常低效的，尤其是对于较大的数据集。

也许我们可以通过改变我们的方法来创建一个更有效的实现，一次返回一页结果:

```
public IEnumerable<Task<IEnumerable<Product>>> GetAllProducts()
{
    Container container = cosmosClient.GetContainer(DatabaseId, ContainerId);
    var iterator = container.GetItemQueryIterator<Product>("SELECT * FROM c");
    while (iterator.HasMoreResults)
    {
        yield return iterator.ReadNextAsync().ContinueWith(t => 
        {
            return (IEnumerable<Product>)t.Result;
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

调用者会像这样使用这个方法:

```
foreach (var productsTask in productsRepository.GetAllProducts())
{
    foreach (var product in await productsTask)
    {
        Console.WriteLine(product.Name);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种实现更有效，但是该方法现在返回`IEnumerable<Task<IEnumerable<Product>>>`。正如我们在调用代码中看到的，理解如何调用方法和处理数据并不直观。更重要的是，分页是数据访问方法的一个实现细节，调用者应该对此一无所知。

## `IAsyncEnumerable<T>`前来救援

我们真正想做的是从我们的数据库中异步检索数据，并在结果可用时将结果返回给调用者。

在同步代码中，返回`IEnumerable<T>`的方法可以使用`yield return`语句将每条数据从数据库返回时返回给调用者。

```
public IEnumerable<Product> GetAllProducts()
{
    Container container = cosmosClient.GetContainer(DatabaseId, ContainerId);
    var iterator = container.GetItemQueryIterator<Product>("SELECT * FROM c");
    while (iterator.HasMoreResults)
    {
        foreach (var product in iterator.ReadNextAsync().Result)
        {
            yield return product;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，*不要这样做*！上面的代码将异步数据库调用变成了阻塞调用，并且不会伸缩。

如果我们能使用`yield return`和*异步*方法就好了！这是不可能的...直到现在。

`IAsyncEnumerable<T>`被引进于。网芯 3(。网标 2.1)。它公开了一个具有可以等待的`MoveNextAsync()`方法的枚举器。这意味着生产者可以在产生结果之间进行异步调用。

我们的方法现在可以返回`IAsyncEnumerable<T>`并使用`yield return`发出数据，而不是返回一个`Task<IEnumerable<T>>`。

```
public async IAsyncEnumerable<Product> GetAllProducts()
{
    Container container = cosmosClient.GetContainer(DatabaseId, ContainerId);
    var iterator = container.GetItemQueryIterator<Product>("SELECT * FROM c");
    while (iterator.HasMoreResults)
    {
        foreach (var product in await iterator.ReadNextAsync())
        {
            yield return product;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使用结果，我们需要使用 C# 8 中新的`await foreach()`语法:

```
await foreach (var product in productsRepository.GetAllProducts())
{
    Console.WriteLine(product);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个好多了。该方法产生可用的数据。调用代码以自己的速度消耗数据。

## `IAsyncEnumerable<T>`和 ASP.NET 核心

从[开始。ASP.NET 能够从 API 控制器动作返回`IAsyncEnumerable<T>`。这意味着我们可以直接返回方法的结果——有效地将数据从数据库流式传输到 HTTP 响应。](https://devblogs.microsoft.com/aspnet/asp-net-core-and-blazor-updates-in-net-core-3-0-preview-7/?WT.mc_id=devto-blog-antchu) 

```
[HttpGet]
public IAsyncEnumerable<Product> Get()
    => productsRepository.GetAllProducts(); 
```

Enter fullscreen mode Exit fullscreen mode

## 用`IAsyncEnumerable<T>`代替`Task<IEnumerable<T>>`

随着时间的推移和收养。网络核心 3 和。NET Standard 2.1 的发展，期望看到`IAsyncEnumerable<T>`被用在我们通常使用`Task<IEnumerable<T>>`的地方。

我期待看到图书馆支持`IAsyncEnumerable<T>`。在整篇文章中，我们已经看到了类似这样的使用 Azure Cosmos DB 3.0 SDK 查询数据的代码:

```
var iterator = container.GetItemQueryIterator<Product>("SELECT * FROM c");
while (iterator.HasMoreResults)
{
    foreach (var product in await iterator.ReadNextAsync())
    {
        Console.WriteLine(product.Name);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

像我们前面的例子一样，Cosmos DB 自己的 SDK 也泄露了它的分页实现细节，这使得处理查询结果变得很困难。

为了看看如果`GetItemQueryIterator<Product>()`返回`IAsyncEnumerable<T>`会是什么样子，我们可以在`FeedIterator` :
上创建一个扩展方法

```
public static class FeedIteratorExtensions
{
    public static async IAsyncEnumerable<T> ToAsyncEnumerable<T>(this FeedIterator<T> iterator)
    {
        while (iterator.HasMoreResults)
        {
            foreach(var item in await iterator.ReadNextAsync())
            {
                yield return item;
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用一种更简洁的方式处理我们的查询结果:

```
var products = container
    .GetItemQueryIterator<Product>("SELECT * FROM c")
    .ToAsyncEnumerable();
await foreach (var product in products)
{
    Console.WriteLine(product.Name);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

是一个受欢迎的补充。并且在许多情况下会使代码更加干净和高效。通过以下资源了解更多信息:

*   教程:使用 C# 8.0 和。网芯 3.0
*   [C#语言提案-异步流](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-8.0/async-streams?WT.mc_id=devto-blog-antchu)