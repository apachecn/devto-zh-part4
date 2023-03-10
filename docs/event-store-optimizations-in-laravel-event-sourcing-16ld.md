# ★laravel-Event-sourcing 中的事件存储优化

> 原文：<https://dev.to/freekmurze/event-store-optimizations-in-laravel-event-sourcing-16ld>

大约一年前，我们[发布了](https://freek.dev/1101-introducing-laravel-event-projector-the-easiest-way-to-get-started-with-event-sourcing-in-a-laravel-app) laravel-event-projector。它专注于为 Laravel 添加投影仪，这是活动采购中的一个重要概念。

在软件包发布后，我们不断地改进它。我们添加了[聚合](https://freek.dev/1326-implementing-event-sourcing-aggregates)，一种[方式来测试](https://freek.dev/1409-implementing-event-sourcing-testing-aggregates)，在我们的文档中添加了一个全新的[新部分](https://docs.spatie.be/laravel-event-sourcing/v1/getting-familiar-with-event-sourcing/introduction/)来解释事件来源，以及 [DX 全面改进](https://freek.dev/1329-implementing-event-sourcing-improving-the-developer-experience)。

现在，我们确信这个包是在 Laravel 开始活动采购的一个很好的起点。这就是为什么我们把这个包重新命名为`laravel-event-sourcing`。

该包还包括一些内存优化，这是我的同事 [Rias](https://twitter.com/riasvdv) 添加的。在这篇博文中，我将带您了解这些变化。

## 使用自己的事件存储

在先前版本的 laravel-event-projector 中，事件的存储依赖于雄辩。我们有一个`StoredEvent`模型来处理事件的(反)序列化。

在使用聚合之前，需要从以前的事件中重新构建它。如果您有一个非常活跃的聚合，需要使用大量事件来重建，那么序列化和反序列化`StoredEvent`模型的时间将会增加。

这就是为什么我们添加了一个界面，允许您使用任何您想要的存储。这将允许您创建一个事件存储，在其中手动构建查询(例如使用`DB::`语句，而不使用雄辩)。

```
namespace Spatie\EventSourcing;

use Illuminate\Support\LazyCollection;

interface StoredEventRepository
{
    public function retrieveAll(string $uuid = null): LazyCollection;

    public function retrieveAllStartingFrom(int $startingFrom, string $uuid = null): LazyCollection;

    public function persist(ShouldBeStored $event, string $uuid = null): StoredEvent;

    public function persistMany(array $events, string $uuid = null): LazyCollection;

    public function update(StoredEvent $storedEvent): StoredEvent;
} 
```

如果你需要一个例子，你可以看看我们是如何实现 [`EloquentStoredEventRepository`](https://github.com/spatie/laravel-event-sourcing/blob/17b6e875d9ebd75c9929f4e461b32bed66f38759/src/EloquentStoredEventRepository.php) 的，这是默认情况下将使用的存储库。

在创建您的实现之后，不要忘记在配置文件的[键中指定您的库的类名。](https://github.com/spatie/laravel-event-sourcing/blob/17b6e875d9ebd75c9929f4e461b32bed66f38759/config/event-sourcing.php#L51-L56)

## 内存优化

你可能已经注意到了`StoredEventRepository`中`LazyCollection`的用法。这是[在 Laravel 6 中引入](https://laravel.com/docs/6.x/collections#lazy-collections)的一个特性。在幕后，它利用 PHP 的生成器来处理大型集合，同时仍然保持较低的内存使用。

让我们来看看`EloquentStoredEventRepository`
中`retrieveAll`的实现

```
public function retrieveAll(string $uuid = null): LazyCollection
{
    /** @var \Illuminate\Database\Query\Builder $query */
    $query = $this->storedEventModel::query();

    if ($uuid) {
        $query->uuid($uuid);
    }

    return $query->orderBy('id')->cursor()->map(function (EloquentStoredEvent $storedEvent) {
        return $storedEvent->toStoredEvent();
    });
} 
```

注意这里`cursor()`的用法。这将极大地减少内存的使用，因为 Laravel 在任何给定的时刻都只会在内存中保存一个模型。在引擎盖下，它使用了 [MySQL 游标](http://www.mysqltutorial.org/mysql-cursor/)。按照我的理解，MySQL 不会将结果保存在 laravel 应用程序的内存中，而是保存一个结果集，并将结果一个接一个地发送到 Laravel 应用程序。

Rias 使用他的 Macbook Pro(处理器:2.3 Ghz 英特尔酷睿 i5)执行了一些基准测试。这些是调用`retrieveAll`的数字，有 100 000 个事件需要检索。

带`Collection`的 laravel-event-projector:

*   内存使用峰值:267MB 内存
*   执行时间:7.007209472656 秒

laravel-事件源与`LazyCollection`:

*   内存使用峰值:18MB 内存
*   执行时间:7.478010892868 秒

显然，使用`LazyCollection`的峰值内存使用量更低/更好，代价是执行时间稍微差一些。

## 从 laravel-event-projector 升级软件包

如果你在`laravel-event-projector`的`v3`上，升级到`laravel-event-sourcing`很容易。您必须执行以下步骤:

1.  将`"laravel-event-projector":"^3.0"`改为`"laravel-event-sourcing":"^v1.0"`并运行`composer update`
2.  名称空间已经改变，所以您需要在整个项目中用`Spatie\EventSourcing`替换`Spatie\EventProjector`。

如果你还在`laravel-event-projector`的`v1`或`v2`上，那么你需要像[我们的升级指南](https://github.com/spatie/laravel-event-projector/blob/master/UPGRADING.md)中描述的那样先升级到`v3`。

## 在关闭

我很自豪我们的包是如何从处理投影仪的简单方法成长为在 Laravel 开始活动采购的最简单方法。这不是我一个人做的。我的同事们 [Seb](https://twitter.com/sebdedeyne) & [Rias](https://twitter.com/riasvdv) 做了很多润色，我从我的好友 [Dries](https://twitter.com/driesvints) 那里得到了一些很好的建议。Frank De Jonge 的的 [EventSauce](https://eventsauce.io/) 包启发了一些特性。

[laravel-event-sourcing](https://docs.spatie.be/laravel-event-sourcing/v1/introduction/) 并不是第一个由 [Spatie](https://spatie.be/) 开发的包。看看[这个我们已经开源的 Laravel 和 PHP 的大列表](https://spatie.be/open-source/packages)。