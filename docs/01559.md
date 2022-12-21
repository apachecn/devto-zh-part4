# 所以我一直在阅读/学习领域驱动设计

> 原文：<https://dev.to/joro550/so-i-ve-been-reading-learning-about-domain-driven-design-40fi>

# 这是什么？

埃里克·埃文斯先生写了一本非常有名的书，书中谈到了领域驱动设计...沃尔蒂。但这就是重点，埃文斯先生是一位使用英语来表达观点的大师，我们应该让我们的领域来驱动我们代码的设计。

我不打算讲太多细节，因为这个话题已经被比我优秀得多的人详细讨论过了。这里有一个马丁·福勒博客的链接，他在那里谈到了每一个不同的概念:[https://martinfowler.com/tags/domain%20driven%20design.html](https://martinfowler.com/tags/domain%20driven%20design.html)

# 那么你想谈什么

我想谈谈领域事件。

因此，域事件是一种 CQS 思想，当你在系统中做一些事情，它需要跨越一个有界限的上下文的边界时，你不想把任何一个上下文的水搅浑，所以你要做的是创建一个事件对象来携带从一个上下文到另一个上下文所需的信息，这是一个简单的想法，但在实践中有点难看。让我来演示一下，我将要展示的这段代码来自一个复数视觉课程，可以从 github 中自由复制:[https://github.com/vkhorikov/DddInAction](https://github.com/vkhorikov/DddInAction):

首先，我们有领域事件本身:

```
public static class DomainEvents
{
    private static List<Type> _handlers;

    public static void Init()
    {
        _handlers = Assembly.GetExecutingAssembly()
            .GetTypes()
            .Where(x => x.GetInterfaces().Any(y => y.IsGenericType && y.GetGenericTypeDefinition() == typeof(IHandler<>)))
            .ToList();
    }

    public static void Dispatch(IDomainEvent domainEvent)
    {
        foreach (Type handlerType in _handlers)
        {
            bool canHandleEvent = handlerType.GetInterfaces()
                .Any(x => x.IsGenericType
                    && x.GetGenericTypeDefinition() == typeof(IHandler<>)
                    && x.GenericTypeArguments[0] == domainEvent.GetType());

            if (canHandleEvent)
            {
                dynamic handler = Activator.CreateInstance(handlerType);
                handler.Handle((dynamic)domainEvent);
            }
        }
    }
} 
```

啊！反思，准备好了我不喜欢这个，但是让我们继续，我们有一个可以发布事件的广义根:

```
public abstract class AggregateRoot : Entity
{
    private readonly List<IDomainEvent> _domainEvents = new List<IDomainEvent>();
    public virtual IReadOnlyList<IDomainEvent> DomainEvents => _domainEvents;

    protected virtual void AddDomainEvent(IDomainEvent newEvent)
    {
        _domainEvents.Add(newEvent);
    }

    public virtual void ClearEvents()
    {
        _domainEvents.Clear();
    }
} 
```

事件:

```
public class BalanceChangedEvent : IDomainEvent
{
    public decimal Delta { get; private set; }

    public BalanceChangedEvent(decimal delta)
    {
        Delta = delta;
    }
} 
```

然后你就有了实际发送事件的方法:

```
public virtual void TakeMoney(decimal amount)
    {
        if (CanTakeMoney(amount) != string.Empty)
            throw new InvalidOperationException();

        Money output = MoneyInside.Allocate(amount);
        MoneyInside -= output;

        decimal amountWithCommission = CaluculateAmountWithCommission(amount);
        MoneyCharged += amountWithCommission;

        AddDomainEvent(new BalanceChangedEvent(amountWithCommission));
    } 
```

我不知道你怎么想，但我讨厌反思。

所以我想知道是否有更好的方法来实现这一点，我已经考虑了一段时间，但几乎没有成功。以下是一些解决方案，以及为什么我认为它们不是很好的解决方案:

*   有一个观察者模式
    *   你会在哪里订阅这些活动？
    *   这会打破开闭原则吗？
*   调解人
    *   与上述相同的问题
*   [https://github.com/jbogard/MediatR](https://github.com/jbogard/MediatR)啊，你说对了

好的，mediator 是一个项目，本质上是 mediator 模式作为一个库的实现。我所看到的这个库的唯一问题是它完全依赖于依赖注入，我不知道这是好事还是坏事。

基本上，如果你创建了一个有界的上下文，你不希望任何东西从这个库泄露出去，任何愚蠢的实现细节，比如 DI 和使用其他库，都不应该是任何使用这个库的人所关心的，他们应该能够依赖你的库，而不必知道你依赖什么(这是我的观点)。

因此，要实现这一点，不仅要隐藏 MediatR 的实现，还要隐藏 DI 的实现。

所以我们开始吧:

得到了一个类来添加包括中介在内的所有 di

```
internal static class DependencyExtensions
{
    public static IServiceCollection AddConfig(this IServiceCollection serviceCollection, IList<Assembly> assemblies)
    {
        assemblies.Add(Assembly.GetAssembly(typeof(DependencyExtensions)));
        serviceCollection.AddMediatR(assemblies.ToArray());

        serviceCollection.AddScoped<ITellerMachine, Atm>();
        serviceCollection.AddScoped(typeof(ConfigFactory<>));
        return serviceCollection;
    }
} 
```

有一门课可以引导事物进入生活:

```
public static class Config
{
    public static IConfigContext Init(List<Assembly> assemblies)
    {
        var serviceProvider = new ServiceCollection()
            .AddConfig(assemblies)
            .BuildServiceProvider();
        return new ConfigContext(serviceProvider);
    }     
} 
```

这就是奇怪的地方，一个上下文类:

```
internal class ConfigContext : IConfigContext
{
    private readonly IServiceProvider _serviceProvider;

    public ConfigContext(IServiceProvider serviceProvider) 
        => _serviceProvider = serviceProvider;

    public T Create<T>() 
        => _serviceProvider.GetService<ConfigFactory<T>>().Produce();
} 
```

和一个制造物品的工厂:

```
internal class ConfigFactory<T>
{
    private readonly IServiceProvider _serviceProvider;

    public ConfigFactory(IServiceProvider serviceProvider) 
        => _serviceProvider = serviceProvider;

    public T Produce() 
        => _serviceProvider.GetService<T>();
} 
```

所有这些都是为了让我能做些像这样的事情:

```
 var context = Config.Init(new List<Assembly>());
        var tellerMachine = context.Create<ITellerMachine>(); 
```

这隐藏了库的所有实现，我不确定这是我完全疯了还是这真的有什么好处。

那里的程序集列表(如果您想知道的话)是这样的，您可以传入任何正在侦听域事件的程序集来注册一个处理程序，您只需添加一个像这样的类:

```
public class ThingHandler : IDomainEventHandler<BalanceChangedEvent>
{
    public Task Handle(BalanceChangedEvent notification, CancellationToken cancellationToken)
    {
        return Task.CompletedTask;
    }
} 
```

我再次将 MediatR 请求响应紧密地包装到我的库中，这样消费者就不必知道了。

在这一点上，我觉得自己像弗兰肯斯坦博士，我创造了一个东拼西凑的怪物，我只是不确定我是想和它交朋友还是让它自由自在地生活在这个世界上。