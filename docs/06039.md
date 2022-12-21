# Kentico 12:设计模式第 12 部分——数据库查询缓存模式

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-12-database-query-caching-patterns-43hc>

<figure>

[![Cash, not cache](img/0a4e61a1663c8c73fb71f5ca320213a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TFdE1ZBB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1zf6m4yfglqdxumsek3n.jpg)

<figcaption>Photo by [Anthony Abruzzo](https://unsplash.com/@anevab26) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

用 Kentico 的门户引擎技术构建的网站通过缓存提供了很高的性能，这是⚡.技术的一部分

当构建 Kentico 12 MVC 站点时，有效缓存的责任落在了开发人员的肩上——但是我们仍然可以利用 Kentico 的 API 来帮助我们实现这个目标👍。

应用程序可以在许多层利用缓存来提高响应能力，包括客户端缓存和输出缓存。

在本帖中，我们将特别关注数据库查询缓存。

* * *

## 我们在缓存什么？数据、上下文和有效性

### 数据:`ObjectQuery`和`DocumentQuery`

在我们的应用程序中，缓存数据最常见的场景是当我们从数据库中检索信息时。

任何时候我们调用 Kentico 的`*Provider`类的方法，或者使用`ObjectQuery<T>`或`DocumentQuery<T>`的实例，我们都可以使用缓存来避免下次需要数据时再次去数据库。

### 上下文:缓存项名称&查询参数

当我们缓存这些数据时，我们不仅仅缓存数据本身，还缓存查询数据的上下文🤔。

*   我们给这个特定的缓存数据起什么名字？
*   什么是通用查询参数(站点名称、数据库标识符、区域设置)？
*   是否为请求启用了预览模式(是否正在查询未发布的内容)？
*   查询是在匿名请求过程中进行的还是针对已知用户的请求？
*   如果请求是为一个已知用户发出的，那么被查询的数据是不是只有他们有权访问的？

如果我们没有在缓存的数据中对上下文进行编码，那么需要类似数据的后续请求可能会错误地响应不正确的缓存数据😨。

因此，我们需要以某种方式用唯一的名称存储缓存的数据，并对查询数据的上下文进行编码。

### 有效性:缓存依赖和生存期

缓存的结果不是永远有效的，因为它们只是数据库有限部分的快速访问快照，而且数据库中的数据会定期更改。

我们需要在缓存中编码哪些数据库更改会使缓存的项无效，我们将这些更改称为缓存依赖。

我们还希望能够给缓存的数据一个通用的生命周期，因为如果它最近没有被查询，就没有必要保存在内存中。

该生存期通常以分钟或秒为单位。

* * *

## 我们是如何缓存的？`CacheHelper.Cache()`

Kentico 在`CacheHelper`类上提供了一个通用的、类型安全的方法，有两个重载，来帮助我们缓存数据:

```
public static TData Cache<TData>(Func<TData> loadMethod, CacheSettings settings); 
```

Enter fullscreen mode Exit fullscreen mode

和

```
public static TData Cache<TData>(Func<CacheSettings, TData> loadMethod, CacheSettings settings); 
```

Enter fullscreen mode Exit fullscreen mode

当使用`CacheHelper.Cache()`时，我们将需要始终提供 3 个值(或者给`Cache`方法或者给`CacheSettings`参数):

1.  Data:返回我们想要缓存的数据的委托。
2.  Context:我们希望缓存的值具有的名称(键),它包括查询它的上下文。
3.  有效性:缓存值所依赖的值的名称(键)和缓存的生命周期。

这个方法最简单的用法可以在 Kentico 关于 MVC 站点上[缓存的文档中找到。](https://docs.kentico.com/k12sp/configuring-kentico/configuring-caching/caching-on-mvc-sites)

下面，我从文档中注释了这个例子:

```
public IEnumerable<Article> GetArticles(
    int count, 
    string culture, 
    string siteName)
{
    // ✅ The Data - DocumentQuery<Article> 
    Func<IEnumerable<Article>> dataLoadMethod = () =>
        ArticleProvider.GetArticles()
            .OnSite(siteName)
            .Culture(culture)
            .TopN(count)
            .OrderByDescending("DocumentPublishFrom")
            .TypedResult;

    // ✅ The Validity - Lifetime
    int cacheMinutes = 10;

    var cacheSettings = new CacheSettings(
        cacheMinutes, 

        // ✅ The Context - Cache Item Name
        "myapp|data|articles", 

        // ✅ The Context - Query Parameters
        siteName, culture, count)
    {
        // ✅ The Validity - Dependencies
        GetCacheDependency = () =>
        {
            string dependencyCacheKey = String.Format("nodes|{0}|{1}|all", siteName, Article.CLASS_NAME);
            return CacheHelper.GetCacheDependency(dependencyCacheKey);
        }
    };

    return CacheHelper.Cache(dataLoadMethod, cacheSettings);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，我们之前概述的所有必需的部分都出现了——数据、上下文和有效性。

所以这就是我们需要的，对吗？

* * *

## 我们缓存在哪里？

当我们只需要缓存几个数据库查询时，上面的例子可以很好地工作，但是对于任何合理规模的应用程序，它的伸缩性都很差。

为什么？😕

### 一种简单的方法:显式的、重复的、程序化的缓存调用

一个应用程序通常会有许多访问数据库的方法，所有这些方法都需要用正确的`CacheSettings`封装在对`CacheHelper.Cache()`的调用中。

在执行数据库查询的同一个方法中包含对`CacheHelper.Cache()`的调用使得应用一致的缓存变得困难，因为它将被重复多次(在每个数据库查询调用点)。

这违背了经验法则，[不要重复自己](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) (DRY)，这是一个不要在应用程序中重复概念的建议(如果代码行不代表相同的概念，它们可以重复)。

它还违反了[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle) (SRP)，在同一个地方执行数据访问和缓存。

违反 SRP 将使测试更加困难，方法更加难以推理😦。

还可能看到数据访问代码与缓存代码交织在一起，随着两者的混合，它们将变得更加相互依赖，这使得重构变得困难。

我们可以通过利用面向方面编程(AOP)将缓存作为一个层应用于所有执行数据访问的方法来遵守 SRP。

### 非常高级的方法:通过基于 AOP 的拦截进行缓存

在 DancingGoat 示例 MVC 应用程序中，我们看到了一种有趣的方法，通过截取使用[面向方面编程](https://en.wikipedia.org/wiki/Aspect-oriented_programming) (AOP)来解决这些问题。

下面是该类的主`Intercept`方法的一个示例:

```
public void Intercept(IInvocation invocation)
{
    if (!mCacheEnabled || 
        !invocation.Method.Name.StartsWith("Get", StringComparison.Ordinal))
    {
        invocation.Proceed();

        return;
    }

    Type returnType = invocation.Method.ReturnType;

    List<CacheDependencyAttribute> cacheDependencyAttributes = invocation
        .MethodInvocationTarget
        .GetCustomAttributes<CacheDependencyAttribute>()
        .ToList();

    if (cacheDependencyAttributes.Count > 0)
    {
        invocation.ReturnValue = GetCachedResult(
            invocation,
            GetDependencyCacheKeyFromAttributes(
                cacheDependencyAttributes, 
                invocation.Arguments));
    }
    else if (typeof(TreeNode).IsAssignableFrom(returnType))
    {
        invocation.ReturnValue = GetCachedResult(
            invocation, 
            GetDependencyCacheKeyForPage(returnType));
    }

    // Continues with various use-cases
} 
```

Enter fullscreen mode Exit fullscreen mode

AOP 是我非常喜欢的一种模式——它有助于集中横切关注点，比如日志和缓存，这使它们远离我们的业务逻辑。这反过来帮助我们确保我们的代码遵循 SRP 和许多其他坚实的原则🤓。

然而，我并不喜欢它在 DancingGoat 代码库中的应用方式😒。

`CachingRepositoryDecorator`依靠`IInvocation`，来自[城堡。核心](https://github.com/castleproject/Core)包:

```
public interface IInvocation
{
    object[] Arguments { get; }
    Type[] GenericArguments { get; }
    object InvocationTarget { get; }
    MethodInfo Method { get; }
    MethodInfo MethodInvocationTarget { get; }
    object Proxy { get; }
    object ReturnValue { get; set; }
    Type TargetType { get; }

    object GetArgumentValue(int index);
    MethodInfo GetConcreteMethod();
    MethodInfo GetConcreteMethodInvocationTarget();
    void Proceed();
    void SetArgumentValue(int index, object value);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 AOP 通过截取的一个例子。

我们不是在通用契约(接口)和已知类型(泛型类型约束)之间应用我们的方面(缓存)，而是通过拦截所有调用并执行基于约定或运行时参数匹配的条件逻辑。

在`CachingRepositoryDecorator.Intercept()`方法中，我们只对名称以字符`"Get"`开头、区分大小写、应用了特殊的`CacheDependencyAttribute`或者返回类型是`TreeNode`或`BaseInfo`的几种变体之一的方法感兴趣。

这个 decorator 类的控制容器注册的反转确保我们只拦截实现`IRepository`的类，但是由于这只是一个标记接口(它没有方法或属性)，它不能帮助我们在运行时了解任何关于我们拦截的方法调用的信息。

使用`IInvocation`接口并不那么愉快，需要挖掘大量的反射 API🙁。

仅仅通过查看代码，很难理解和预测`CachingRepositoryDecorator`构建上下文和有效性缓存键以存储缓存数据的方式。

看看将要被拦截的方法，很难描述缓存项名称和依赖项的缓存键是什么。

那么，我们从哪里知道在运行时为我们的`CacheHelper.Cache()` API 调用生成了什么缓存键呢😖？

当向存储库添加一个新的`"Get"`方法时，确保它被正确缓存将需要一些手工测试，或者可能需要对`CachingRepositoryDecorator`进行新的测试。

这似乎...对我来说，由于在向存储库添加新方法时应该测试的行为是存储库，而不是存储库不应该知道的层(缓存)。

虽然(动态)拦截作为一种模式非常强大，可以实现原本不可能实现的事情，但这是一把大锤子🔨在 C#上摇摆不定，有其健壮的类型系统，和一个[反转控制](https://en.wikipedia.org/wiki/Inversion_of_control) (IoC)容器，像 Autofac，可以免费给我们。

### 我的首选方法:通过[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection) (DI)基于 AOP 的修饰进行缓存

事实证明，我们的应用程序中已经有了一些工具，可以通过 AOP 以更简单、更友好的方式将缓存应用到我们的应用程序中😯。

DI 装饰要求一个类型透明地包装在另一个类型中，后者在运行时实现它的一个契约(接口)。

这允许包装类型自己处理方法调用，将调用转发给包装类型，或者两者的某种组合。

下面是我们如何实现 DI 修饰，利用我们之前简单的缓存例子:

```
// The Contract that both the wrapping type
// and the wrapped type must implement
public interface IArticleRepository
{
    IEnumerable<Article> GetArticles(int count);
}

// The wrapped type that performs the data access logic
public class ArticleRepository : IArticleRepository
{
    public IEnumerable<Article> GetArticles(
        int count, 
        string culture, 
        string siteName)
    {
        // Call to ArticleProvider.GetArticles()
        // No caching is performed here (SRP!)

        return articles;
    }
}

// The wrapping type that performs the caching logic
public class ArticleRepositoryCacheDecorator : IArticleRepository
{
    // This is an instance of the above ArticleRepository at runtime
    private readonly IArticleRepository repo;

    public ArticleRepositoryCacheDecorator(
        IArticleRepository repo) => this.repo = repo;

    public IEnumerable<Article> GetArticles(
        int count, 
        string culture, 
        string siteName)
    {
        // Forward the call to the original ArticleRepository
        // No data access here (SRP!)
        Func<IEnumerable<Article> dataLoadMethod = () => 
            repo.GetArticles(count, culture, siteName);

        var cacheSettings = new CacheSettings(
            10, "myapp|data|articles", siteName, culture, count)
        {
            GetCacheDependency = () =>
            {
                string dependencyCacheKey = String.Format(
                    "nodes|{0}|{1}|all", siteName, Article.CLASS_NAME);
                return CacheHelper.GetCacheDependency(dependencyCacheKey);
            }
        };

        return CacheHelper.Cache(dataLoadMethod, cacheSettings);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们执行的是 DI 修饰，而不是基于约定的拦截，所以我们可以访问所有原始的参数名和类型，以及[修饰的](https://en.wikipedia.org/wiki/Decorator_pattern)类的方法。

*   ✅这应该更容易理解和测试。
*   对`ArticleRepository`的✅更改将通过 C#类型系统在`ArticleRepositoryCacheDecorator`上强制执行。
*   ✅数据访问和缓存的分离非常明显。
*   指定应该装饰哪些类型的✅可以通过我们的 IoC 容器来配置。

在我看来，这是一种更加优雅和可验证的 AOP 方法。

如果您喜欢以上方法，而不是深入研究。NET 的反射 API，您可能想知道为什么 Kentico 选择在 DancingGoat 应用程序中进行拦截🤔。

答案来自基于存储库的数据访问层架构。

## 仓库和 DI 装修 AOP 的问题。

我在本系列的前一篇文章中提到过，[库往往是方法](https://dev.to/seangwright/kentico-12-design-patterns-part-11-unit-testing-custom-page-types-2c11#a-quick-note-on-repositories-)的包。

这些方法通常不会实现与任何其他存储库的模式相匹配的契约。

事实上，没有一个好的方法来强制一个需求(基于类型系统并使用泛型)，一个库的方法遵守这个需求，匹配另一个[库](https://deviq.com/repository-pattern/)中方法的名称、参数和返回类型，而不使它们都实现一个公共接口。

下面是这个问题的一个简单例子:

```
public IArticleRepository
{
    IEnumerable<Article> GetArticles(int count);
}

public ICoffeeRepository
{
    IEnumerable<Coffee> GetCoffees(IRepositoryFilter filter, int count);
}

public ICafeRepository
{
    IEnumerable<Cafe> GetPartnerCafes();
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些存储库接口都有不同的方法名、不同的方法签名和许多其他不同的方法。

仓库中常见的方法签名的这种自由迫使我们使用基于 AOP 的拦截。

基于 DI 的 AOP 要求所有需要修饰的类型都有一致的方法签名，而存储库并不是那种在不同存储库之间产生一致方法签名的架构模式。

看来我们陷入了僵局😤。

还是我们...？

我的建议是:**去掉存储库**😅。

它们不符合我们的缓存和日志目标，并且它们已经作为对象关系映射(ORM)层存在于我们的应用程序中(在 Kentico 中是`DocumentHelper`，`*InfoProvider`)，所以我们不要用相同的模式创建两个抽象层。

> 如果你想了解更多关于 DI 装饰和动态拦截之间的区别，看看这篇博文[为什么选择 DI 拦截而不是面向方面编程？](https://www.kenneth-truyers.net/2013/05/16/why-choose-di-interception-over-aspect-oriented-programming/)

## 解决方案:查询，QueryHandler 模式

我们真正想要的是一个用于所有数据库查询的单一方法。

如果我们只有一个处理所有数据库查询的方法，那么我们只有一个通过 DI 来应用缓存的方法。

但是，如果我们只有一个方法签名，它如何处理我们想要在数据库上执行的各种查询呢？

嗯，我们可以利用 C#泛型，它提供了一种方法来改变我们的方法参数和返回类型，但保持方法签名一致。

我们来看解决方案:

```
public interface IQuery<TResult>
{
}

public interface IQueryHandler<TQuery, TResult> 
    where TQuery : IQuery<TResult>
{
    TResult Exceute(TQuery query);
} 
```

Enter fullscreen mode Exit fullscreen mode

Tada 👏🤘!

我们现在有了一个`IQuery`接口，它是针对给定查询返回的类型的通用接口，我们将把所有想要用来查询数据库的参数放在这里。

我们还有一个`IQueryHandler`接口，它处理的查询类型和特定查询的返回类型都是通用的。

每个数据访问方法调用都将是对某个具有`TResult`返回类型的`IQuery<TResult>`的`Execute()`调用。

> 请看 Steven van Deursen 的 StackOverflow 回答，它详细描述了这种模式在实践中是如何工作的。
> 
> 查询中有一个有用的部分，QueryHandler 关系称为 Dispatcher。如果您选择走这条架构之路，我会推荐您使用一个，但这不是必需的。
> 
> 如果您想要所有 3 个部分(Query、QueryHandler、Dispatcher)的示例实现，可以查看一下[Mediator](https://github.com/jbogard/MediatR)的源代码，这是 Jimmy Bogard 经常使用的 Mediator 设计模式的实现(但是没有特定于数据访问的查询语义)。

让我们看一个使用我们最初的`ArticleRepository.GetArticles()`方法的示例实现:

```
public ArticlesQuery : IQuery<IEnumerable<Article>>
{
    public int Count { get; set; }
    public string SiteName { get; set; }
    public string Culture { get; set; }
}

public ArticlesQueryHandler : IQueryHandler<ArticlesQuery, IEnumerable<Article>>
{
    public IEnumerable<Article> Execute(ArticlesQuery query)
    {
        return ArticleProvider.GetArticles()
            .OnSite(query.SiteName)
            .Culture(query.Culture)
            .TopN(query.Count)
            .OrderByDescending("DocumentPublishFrom")
            .TypedResult;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们看看如何使用这些:

```
var query = new ArticlesQuery
{
    Count = 10,
    Culture = "en-us",
    SiteName = "sandbox"
};

// This should be supplied through Dependency Injection
// as IArticlesQueryHandler

var handler = new ArticlesQueryHandler();

var articles = handler.Execute(query); 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们可以应用缓存了——不仅仅是对这个查询处理程序，而是对应用程序中的所有查询处理程序！

但是等等，在我们到达那里之前，先说一个题外话😋！

## 用`IQueryCacheKeysCreator`生成缓存键

DancingGoat 代码库通过以下逻辑生成缓存项名称键(为简洁起见进行了编辑):

```
 var builder = new StringBuilder(127);

    ...

    foreach (var value in invocation.Arguments)
    {
        builder.AppendFormat(
            CultureInfo.InvariantCulture, 
            "|{0}", 
            GetArgumentCacheKey(value));
    }

...

private string GetArgumentCacheKey(object argument)
{
    if (argument == null)
    {
        return string.Empty;
    }

    var keyArgument = argument as ICacheKey;
    if (keyArgument != null)
    {
        return keyArgument.GetCacheKey();
    }

    return argument.ToString();
} 
```

Enter fullscreen mode Exit fullscreen mode

它通过只查看拦截的方法返回类型或`CacheDependencyAttribute`来生成缓存依赖键，这些返回类型或`CacheDependencyAttribute`可能在返回要缓存的数据的方法上。

这是最初让我感到难以理解的逻辑😣因此，让我们看看生成这些密钥的不同方式，再次利用 C#类型系统和我们的`IQuery`、`IQueryHandler`类型。

```
public interface IQueryCacheKeysCreator<TQuery, TResult> 
    where TQuery : IQuery<TResult>
{
    string[] DependencyKeys(TQuery query, TResult result);
    object[] ItemNameParts(TQuery query);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个接口将我们的查询实现的类型传递给所有的方法调用，并且可以以一种独特的、类型安全的方式为每种查询类型实现👍。

下面是我们`ArticlesQuery` :
的一个例子

```
public class ArticlesQueryCacheKeysCreator :
    IQueryCacheKeysCreator<ArticlesQuery, IEnumerable<Article>>
{
    public string[] DependencyKeys(ArticlesQuery query, IEnumerable<Article> result) =>
        new object[]
        {
            $"nodes|{query.SiteName}|{Article.CLASS_NAME}|all" 
        };

        // or, if it's appropriate for the kind of query we are executing,
        // and since we have access to the query result

        result.Select(a => $"nodeid|{a.NodeID}").ToArray();

    public object[] ItemNameParts(ArticlesQuery query) =>
        new [] 
        { 
            "myapp|data|articles", 
            query.SiteName, 
            query.Culture, 
            query.Count.ToString() 
        };
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我通常在与`IQueryCacheKeysCreator`相同的文件中定义`IQuery`实现。我喜欢把`IQueryCacheKeysCreator`看作是用属性注释`IQuery`实现类的一种更灵活的替代方式，所以我觉得没有必要把它们分开。
> 
> 如果您不想在`ArticlesQuery`(如`siteName`、`culture`或“启用预览”)中提供一堆“环境上下文”(基于`HttpContext`)运行时数据，您可以通过 DI 将这些值提供给`ArticlesQueryCacheKeysCreator`。
> 
> 这是我在使用这种模式时所做的。

## DI 基础装修用查询/ QueryHandler

现在我们终于可以为所有的`IQueryHandler`实例定义一个简单优雅的缓存装饰类了。

```
public class QueryHandlerCacheDecorator<TQuery, TResult> 
    : IQueryHandler<TQuery, TResult> 
    where TQuery : IQuery<TResult>
{
    private readonly IQueryHandlerSync<TQuery, TResult> handler;
    private readonly IQueryCacheKeysCreator<TQuery, TResult> cacheKeysCreator;

    public QueryHandlerCacheDecorator(
        IQueryHandler<TQuery, TResult> handler,
        IQueryCacheKeysCreator<TQuery, TResult> cacheKeysCreator)
    {
        this.handler = handler;
        this.cacheKeysCreator = cacheKeysCreator;
    }

    public TResult Execute(TQuery query) =>
        CacheHelper.Cache(
            (cacheSettings) => 
            {
                TResult result = handler.Execute(query);

                if (cacheSettings.Cached)
                {
                    cacheSettings.GetCacheDependency = () =>
                        CacheHelper.GetCacheDependency(
                            cacheKeysCreator.DependencyKeys(query, result));
                }

                return result;
            },
            new CacheSettings(
               cacheMinutes: 10,
               useSlidingExpiration: true,
               cacheItemNameParts: cacheKeysCreator.ItemNameParts(query)));
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为这比基于拦截的缓存 AOP 实现更具可读性、可测试性、简单性和类型安全性🤗。

`IQueryCacheKeysCreator`的实例是*非常*可测试的，对现有实现的任何改变或者`IQuery`的新实现只需要对那些类型进行测试😀。

`QueryHandlerCacheDecorator`的实现如此简单，以至于你只需为它编写一次测试，就知道不会出现只有在以后构建额外的查询后才会出现的极限情况。

* * *

我觉得我应该提到的一个警告是，我推荐的方法会产生更多的类，通常会产生更多的代码。

存储库:1 个接口，1 个实现。

Query/QueryHandler: (1 个查询类，1 个查询缓存类，1 个处理程序类)x 通常存储库中的方法数。

但是当我从“方法袋”/“神级”设计转向强调 SRP 和坚实原则的设计时，我看到这种情况经常发生。

也就是说，我欢迎反馈和建议！

## 总结

作为 Kentico 12 MVC 开发人员，我们有效缓存从数据库返回的数据的能力是构建性能良好且可伸缩的内容交付应用程序的关键组成部分。

Kentico 缓存 API 为我们提供了核心调用，以提供缓存数据时所需的 3 个部分——数据、上下文和有效性🧐.

然而，这些相同的缓存 API 是非常低级的，我们需要额外的架构来扩展它们在我们的代码库中的使用，同时保持单一责任原则(SRP)和不要重复自己(DRY)的咒语。

Kentico 在他们使用面向方面编程(AOP)的 DancingGoat 代码库中提供了这种架构的一个例子，这是我们开发人员应该效仿的。

可惜(在我看来！)它对存储库模式的依赖迫使它使用截取而不是依赖注入(DI)修饰😔。

通过用 Query 和 QueryHandler 类型替换存储库模式，我们可以通过 DI 修饰将缓存应用于数据库查询。

产生的代码更清晰、更易测试，允许每个查询更灵活地应用自己的方法为缓存 API 调用⚡.提供上下文和有效性

> 我没有提供对 IoC 容器(在我的例子中是 Autofac)的所有调用来注册我创建的这些类型，但是如果有人感兴趣，我很乐意给出一些代码示例。

我很想听听你对这些想法的想法，所以如果你想分享，请在下面留下评论。

感谢阅读！🙏

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 Kentico 博客系列:

*   [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)
*   [Kentico CMS 快速提示](https://dev.to/search?q=Kentico%20CMS%20Quick%20Tip)