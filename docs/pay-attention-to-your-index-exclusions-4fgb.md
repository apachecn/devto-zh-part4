# 注意你的索引排除

> 原文：<https://dev.to/jermdavis/pay-attention-to-your-index-exclusions-4fgb>

我最近遇到了一个有趣的问题:一些在 Sitecore 的 QA 实例上运行良好的代码已经被部署到 UAT，现在却失败了，并出现一条奇怪的错误消息。虽然这个问题完全是我们的错，但我在谷歌上看到的错误信息并不多，所以我今天试图纠正这个问题…

## 这个问题

有问题的代码运行在 Sitecore 7 实例上。这个错误落在了 UAT 身上:

```
[ArgumentNullException: Value cannot be null.Parameter name: key]
  System.ThrowHelper.ThrowArgumentNullException(ExceptionArgument argument) +49
  System.Collections.Generic.Dictionary`2.FindEntry(TKey key) +14545882
  System.Collections.Generic.Dictionary`2.TryGetValue(TKey key, TValue& value) +20
  Sitecore.ContentSearch.ContentSearchManager.GetIndex(String name) +52
  Sitecore.ContentSearch.ContentSearchManager.CreateSearchContext(IIndexable indexable) +17 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个非常有用的错误，所以我花了一些时间来跟踪它。从堆栈跟踪中你可以看到，问题似乎出在将一个`IIndexable`翻译成一个搜索上下文上，但是它没有说任何有帮助的关于为什么…

在表面之下，崩溃的代码看起来像:

```
var root = Sitecore.Context.GetItem("{08D9E0A1-BB72-48FD-AAB2-EFCD6F3B3C92}");
using (var context = ContentSearchManager.CreateSearchContext(new SitecoreIndexableItem(root)))
{
   // run some search queries starting from the "root" item...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 起因

通过与我的老朋友 ILSpy 的深入探讨，我发现这里抛出的异常是因为上面的`CreateSearchContext()`做了两件事:

```
public static IProviderSearchContext CreateSearchContext(IIndexable indexable)
{ 
  return GetIndex(indexable).CreateSearchContext(SearchSecurityOptions.EnableSecurityCheck);
} 
```

Enter fullscreen mode Exit fullscreen mode

它将`IIndexable`转换成该项目的有效索引。然后在该索引上创建一个搜索上下文。

抛出异常是因为翻译返回 null——该项没有映射到任何索引。在表面之下，有一个管道运行来完成这种转换，并迭代所有定义的索引。并依次询问它们是否包含所讨论的项目。

在我所看到的场景中，当被问到这个问题时，所有的索引都返回“no ”,当这种情况发生时，管道中的代码返回 null…所以后来当 Sitecore 试图通过基于 null 名称查找索引来获取它的索引时，您会看到上面的异常。

进一步的挖掘发现了“工作的”服务器和“坏的”服务器之间的一个关键区别:

*   在它工作的机器上，Sitecore 的搜索索引被配置为“索引所有模板，除了一些特定的模板”
*   但是被破坏的服务器被配置为“排除所有模板，除了一些特定的”…

所以这个问题的真正原因是传入`ContentSearchManager.CreateSearchContext()`的 Sitecore 条目是基于一个被明确排除在搜索索引之外的模板。我一解决这个问题，代码就开始工作了。

## 结论

嗯，显而易见的结论是:1)不要从你的索引中排除你将要调用`ContentSearchManager.CreateSearchContext()`的项目。2)跨部署更好地管理您的配置… [![😉](img/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png)

做了一些进一步的测试后，上面的错误消息似乎是针对旧版本的 Sitecore 的。我也在 V9.1 上测试过，在这种情况下它仍然会崩溃，但是它显示了一个不同的异常:

```
[NullReferenceException: Object reference not set to an instance of an object.] 

 Sitecore.ContentSearch.SitecoreItemCrawler.IsExcludedFromIndex(SitecoreIndexableItem indexable, Boolean checkLocation) +69
  Sitecore.ContentSearch.SitecoreItemCrawler.GetContextIndexRanking(IIndexable indexable) +113
  System.Linq.WhereSelectListIterator`2.MoveNext() +116
  System.Linq.Enumerable.Min(IEnumerable`1 source) +72

 Sitecore.ContentSearch.AbstractSearchIndex.Sitecore.ContentSearch.Pipelines.GetContextIndex.IContextIndexRankable.GetContextIndexRanking(IIndexable indexable) +119
  Sitecore.ContentSearch.Pipelines.GetContextIndex.<>c\_\_DisplayClass6\_0.<RankContextIndexes>b\_\_0(ISearchIndex i) +71
  System.Linq.WhereSelectEnumerableIterator`2.MoveNext() +237
  System.Linq.Buffer`1..ctor(IEnumerable`1 source) +280
  System.Linq.<GetEnumerator>d__1.MoveNext() +115
  System.Linq.Buffer`1..ctor(IEnumerable`1 source) +280
  System.Linq.Enumerable.ToArray(IEnumerable`1 source) +89

 Sitecore.ContentSearch.Pipelines.GetContextIndex.FetchIndex.GetContextIndex(IIndexable indexable, GetContextIndexArgs args) +699

 Sitecore.ContentSearch.Pipelines.GetContextIndex.FetchIndex.Process(GetContextIndexArgs args) +48 (Object , Object ) +13
  Sitecore.Pipelines.CorePipeline.Run(PipelineArgs args) +483
  Sitecore.Pipelines.DefaultCorePipelineManager.Run(String pipelineName, PipelineArgs args, String pipelineDomain, Boolean failIfNotExists) +235
  Sitecore.Pipelines.DefaultCorePipelineManager.Run(String pipelineName, PipelineArgs args, String pipelineDomain) +21
  Sitecore.Abstractions.CorePipelineWrapper.Run(String pipelineName, PipelineArgs args) +73

 Sitecore.ContentSearch.Pipelines.GetContextIndex.GetContextIndexPipeline.Run(ICorePipeline pipeline, GetContextIndexArgs args) +38 
```

Enter fullscreen mode Exit fullscreen mode

这种痕迹更加明显——但是对于不熟悉 Sitecore 索引配置过程的人来说，可能仍然会感到困惑。因此，如果您看到上面显示的任何一条错误消息，请检查您从索引中排除了什么…