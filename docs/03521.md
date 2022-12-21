# 内存+分布式缓存。网络核心

> 原文：<https://dev.to/nikolicbojan/memory-distributed-caching-in-net-core-5hm3>

我们正在为一些第三方服务构建另一个适配器。他们在他们这边缓存结果 1 天(其中一条路线是 7 天)，但是每次呼叫都要收费。好吧，如果你想这样玩...我们也可以缓存！

> TL；DR；我创建了一个小的服务类，它允许你在内存和一些分布式缓存中进行缓存，两全其美。
> 访问包含代码、单元测试和示例控制台应用程序的 Git 库【https://github.com/nikolic-bojan/common-utilitiesT2
> 。
> 复制/粘贴代码，打造您的风格。

# 缓存在核心中

文档在中非常有用。NET Core，所以这个也不例外。缓存时有两种选择——IMemoryCache 和 IDistributedCache。

由于我们的应用程序运行在 IIS 上，即使它们被设置为始终运行，我们的运营团队也将应用程序池设置为每 29 小时重启一次。因此，仅缓存到内存可能有效，但我们有几台服务器，我们可以重新部署，一些服务器重新启动...

好的，让我们在分布式缓存中完成它。我们有 Redis 设置，这一切都很好，但它比内存缓存慢。但是如果...

# 两全其美

为什么不同时缓存到内存和 Redis(或者其他分布式缓存)？我搜索了一下，无意中发现了 Nick Craver 的一个很棒的帖子，是关于栈溢出是如何做到的[我们如何做应用缓存](https://nickcraver.com/blog/2019/08/06/stack-overflow-how-we-do-app-caching/)

好吧，我们离缓存的请求和需求的数量还差得很远，但是为什么不为一些缓存服务编写一些可重用的代码呢？这些代码可以选择内存和分布式缓存。

逻辑是这样的:

*   检查您是否在内存缓存中缓存了项目(在服务器上)
*   如果有，一切都好，不需要做其他任何事情
*   如果没有，请转到分布式缓存并在那里进行检查
*   如果它在那里，获取它并将其存储在内存缓存中
*   如果不是，则执行某个**“factory”**方法，该方法将为缓存创建值，并将其存储在分布式缓存中，然后存储在内存缓存中

以下是如何调用缓存服务的示例

```
await cachingService.GetOrCreateAsync<TestObject>(
    "key", 
    () => Task.FromResult(new TestObject()), 
    TimeSpan.FromMinutes(1), 
    TimeSpan.FromMinutes(5)); 
```

Enter fullscreen mode Exit fullscreen mode

最有趣的部分是第二个参数，那是一个`Func<Task<T>>`。只有当缓存服务在内存和分布式缓存中都没有找到**键**时，才会执行该功能。从代码执行或昂贵的第三方调用的角度来看，该函数将包含一些“昂贵”的部分。

# 给我们看看代码！

这是 GitHub 上代码的当前状态。我故意不想创建一些 NuGet，因为我还没有为我们的本地使用这样做。当我从几个项目中找出需求时，我将构建真正可重用的东西，即使它现在非常可重用。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [尼科利奇-博扬](https://github.com/nikolic-bojan) / [公用事业](https://github.com/nikolic-bojan/common-utilities)

### 可以在任何项目中重复使用的通用工具

<article class="markdown-body entry-content container-lg" itemprop="text">

# 内存+分布式缓存。网络核心

我们正在为一些第三方服务构建另一个适配器。他们在他们那边缓存结果 1 天(其中一条路线 7 天)，但是如果你想这样玩的话，可以按次收费...我们也可以缓存！

> TL；DR I 创建了一个小的服务类，允许你在内存和一些分布式缓存中进行缓存，两全其美。复制/粘贴代码并制作您的风格。

#核心缓存

文档在中非常有用。NET Core，所以这个也不例外。缓存时有两种选择——IMemoryCache 和 IDistributedCache。

由于我们的应用程序运行在 IIS 上，即使它们被设置为始终运行，我们的运营团队也将应用程序池设置为每 29 小时重启一次。所以，只缓存到内存…

</article>

[View on GitHub](https://github.com/nikolic-bojan/common-utilities)

界面相当简单

```
public interface ICommonCachingService
{
    Task<T> GetOrCreateAsync<T>(string key, Func<Task<T>> factory, TimeSpan memoryCacheExpiration, TimeSpan? distributedCacheExpiration = null) where T : class;

    Task<string> GetOrCreateStringAsync(string key, Func<Task<string>> factory, TimeSpan memoryCacheExpiration, TimeSpan? distributedCacheExpiration = null);

    Task<T> GetOrCreateAsync<T>(IConverter<T> converter, string key, Func<Task<T>> factory, TimeSpan memoryCacheExpiration, TimeSpan? distributedCacheExpiration);
} 
```

Enter fullscreen mode Exit fullscreen mode

你有三种方法:

1.  用 JSON 序列化缓存对象。
2.  缓存没有序列化的字符串(因为 JSON 序列化对我不起作用；我懒得去想为什么)
3.  使用自定义转换器缓存任何内容(实现您的序列化)

前两个只是调用第三个，第三个是实际的实现，带有预先选择的转换器。

这些方法的参数是:

*   **key** -一个要缓存的 key，做一些方法生成唯一的一个。
*   **工厂**——我之前解释过了。
*   **memoryCacheExpiration** -内存缓存中的生存时间(TTL)。没有滑动选项。
*   **分布式缓存失效** -分布式缓存中的 TTL。
*   **转换器** -简单的序列化/反序列化接口，如下面的代码所示。

```
public interface IConverter<T>
{
    string Serialize(object obj);

    T Deserialize(string value);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 内存缓存

```
public async Task<T> GetOrCreateAsync<T>(IConverter<T> converter, string key, Func<Task<T>> factory, TimeSpan memoryCacheExpiration, TimeSpan? distributedCacheExpiration)
{
    var local = await _memoryCache.GetOrCreateAsync(key, entry =>
    {
        TimeSpan calculatedDistributedCacheExpiration = distributedCacheExpiration ?? memoryCacheExpiration;

        entry.AbsoluteExpiration = DateTime.UtcNow.Add(memoryCacheExpiration);
        return GetFromDistributedCache(converter, key, factory, calculatedDistributedCacheExpiration);
    });

    return local;
} 
```

Enter fullscreen mode Exit fullscreen mode

MemoryCache 已经有了这个方便的`GetOrCreateAsync`方法，它接受**“工厂”**方法。我只是添加了过期设置和带有所有参数的转发函数调用。

## 分布式缓存

```
private async Task<T> GetFromDistributedCache<T>(IConverter<T> converter, string generatedKey, Func<Task<T>> factory, TimeSpan calculatedDistributedCacheExpiration)
{
    _logger.LogDebug("Getting cached value from Distributed cache for key {Key}", generatedKey);
    try
    {
        var cachedItem = await _distributedCache.GetStringAsync(generatedKey);
        if (cachedItem != null)
        {
            _logger.LogDebug("Read cached value from Distributed cache for key {Key}", generatedKey);
            var value = converter.Deserialize(cachedItem);
            return value;
        }
    }
    catch (Exception e)
    {
        _logger.LogWarning(e, "Exception getting cached item from Distributed cache.");
    }

    var item = await factory.Invoke();
    if (item != null)
    {
        try
        {
            var cacheEntryOptions = new DistributedCacheEntryOptions { AbsoluteExpirationRelativeToNow = calculatedDistributedCacheExpiration };
            var serializedValue = converter.Serialize(item);
            await _distributedCache.SetStringAsync(generatedKey, serializedValue, cacheEntryOptions, CancellationToken.None);
            _logger.LogDebug("Stored in Distributed cache for key {Key}", generatedKey);
        }
        catch (Exception e)
        {
            _logger.LogWarning(e, "Exception storing cached item in Distributed cache.");
        } 
    }

    return item;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个小家伙有更多的逻辑:

*   尝试从分布式缓存中获取值并返回可用值，
*   如果不存在，则调用 **"factory"** 方法来创建值，然后将其存储在缓存中。

我还添加了基本的 try-catch，所以如果 Redis 或其他分布式缓存出现问题，读取和存储都不会出错。

# 举例

你已经在开头看过了，但是让我们再来一遍

```
await cachingService.GetOrCreateAsync<TestObject>(
    "key", 
    () => Task.FromResult(new TestObject()), 
    TimeSpan.FromMinutes(1), 
    TimeSpan.FromMinutes(5)); 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道这讲述了一个故事- **尝试从内存/分布式缓存中获取一个带有 key“key”的值；如果没有找到，用“工厂”方法创建它，并在内存缓存中保存 1 分钟，在分布式缓存中保存 5 分钟**。

你可以在你的控制器、核心/域或者你的基础设施中使用它(有些东西指的是 Onion/Clean/随便什么名字的架构)。我不认为在哪里使用它只有一个答案。实际上，任何地方都有！

# 改进？

*   为更细粒度的缓存设置添加一些`CacheEntryOptions`类。
*   增加对分布式缓存调用的弹性，如使用断路器重试。
*   允许一个以上的分布式缓存(为什么老兄？只是更头疼而已！).
*   消除对日志的依赖，因为我只是用它来编写单元测试，这样就不会那么麻烦了。

# 小心的话！

因为编程中最困难的事情之一是缓存，所以要小心所有潜在的问题，比如:

*   如果您在缓存中保存大量较大的对象，内存压力会很大
*   大对象堆，如果您的对象大于 85k
*   无效——我甚至没有在这里考虑它，但是你可能需要它。

为什么我不关心我们用例中的那些:

*   我设置了较低的内存缓存过期时间(例如 10 分钟)，所以很难出现内存压力的情况。
*   我知道我的物体有多大。
*   我还设置了分布式缓存过期时间，我知道这不会让我后悔没有使用 Invalidate 方法。

**了解你的东西！**

非常欢迎评论、问题和建议！

最诚挚的问候，Bojan