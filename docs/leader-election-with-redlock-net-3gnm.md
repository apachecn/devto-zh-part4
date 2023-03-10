# 与 Redlock.net 的领导人选举

> 原文：<https://dev.to/bohdanstupak1/leader-election-with-redlock-net-3gnm>

## 为什么锁定事物

微服务架构如今被广泛采用。它提供的好处之一是水平扩展的可能性，这使我们能够显著提高应用程序的性能。然而，存在多个服务实例面临对一些共享资源的争用的情况。考虑一个服务，除了其他功能之外，它每天运行一次应该在单个实例中执行的任务关键型作业。与此同时，部署单个实例会适得其反，因为微服务承载的其他功能会受益于水平扩展。有人可能会说，我们可以将这种微服务拆分成更小的微服务，但我会谨慎地反对将微服务划分得太细。
作为一个解决方案，我提议选举一个领导者，在一个时间点专门处理共享资源(在我们的例子中是关键任务)。
众所周知的领导者选举算法，如 Bully 算法或 Ring 算法，需要大量的礼仪和系统逻辑拓扑知识才能实现。这就是我们为什么要使用分布式锁来研究领导者选举的原因。
当分布式应用程序中的任务需要仔细协调并且没有天生的领导者时，您应该使用这种模式。
作为分布式锁的存储，我们将使用 Redis。Redis 是内存中的键值存储，所以我们将利用它的速度。已经有[个库](https://github.com/samcook/RedLock.net)实现了 Redis 上的分布式锁。所以我们必须利用它。

## 代码

样本代码可以在 [github](https://github.com/Wkalmar/LeaderElection) 上获得。让我们来分析一下这里实际发生了什么。
通过分布式锁进行领导者选举背后的思想是，谁获得共享资源的锁，谁就成为领导者。因此，很自然地，我们有了一个与内置 C# `lock`构造非常相似的锁键。

```
private const string _resource = "the-thing-we-are-locking-on"; 
```

显然，存储是一个单点故障，因此我们必须确保它的可靠性。我们在本例中使用的 RedLock.net 允许我们使用 Redis 的多个实例，而不是单个实例，以提高可靠性。下面是我们如何在启动时创建与 Redis 的连接。

```
var endPoints = new List<RedLockEndPoint>
{
    new DnsEndPoint("redis1", 6379)
    new DnsEndPoint("redis2", 6379)
    new DnsEndPoint("redis3", 6379)
};
_distributedLockFactory = RedLockFactory.Create(endPoints); 
```

每个实例都会在给定时间段内尝试获取一次锁。如果它成功了，它就会成为领导者。如果没有，它将稍后再试一次。

```
private readonly TimeSpan _expiry = TimeSpan.FromSeconds(_expirySecondsCount);

_acquireLockTimer = new Timer(async state => await TryAcquireLock((CancellationToken)state), _cts.Token, 0, _expirySecondsCount * 1000); 
```

然而，领导者不需要重新获取锁，因为它具有自动扩展特征。在第一次遇到 RedLock.net 时，这可能是不直观的，所以应该注意到。我们来看看 TryAcquireLock 方法。

```
private async Task TryAcquireLock(CancellationToken token)
{
    if (token.IsCancellationRequested)
        return;

    var distributedLock = await _distributedLockFactory.CreateLockAsync(_resource, _expiry);
    if (distributedLock.IsAcquired)
    {
        DoLeaderJob();
        _acquireLockTimer.Dispose(); //no need to renew lock because of autoextend
    }   
} 
```

如上所述，只要一个实例利用自动扩展特性成为领导者，我们就取消重新获取定时器。一旦实例失败，锁就会被释放，并由其他实例来获取。

## 总结

正如我们所看到的，通过分布式锁实现领导者选举非常简单。不过，应该小心使用，因为每次锁定都会增加微服务实例之间的争用，从而降低水平扩展的优势。