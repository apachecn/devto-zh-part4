# 用伪造品嘲弄数据

> 原文：<https://dev.to/integerman/mocking-data-with-bogus-25ac>

曾经需要一堆随机数据来进行测试、UI 原型设计，甚至作为应用程序的一部分吗？我做了，我发现了一个很好的选项来生成它。NET 应用程序。

[Bogus](https://github.com/bchavez/Bogus) 松散地基于 PHP 的 [Faker 库](https://github.com/fzaninotto/Faker)，与测试库如 [AutoFixture](https://github.com/AutoFixture/AutoFixture) 不同，它提供的数据与你在生产中看到的非常相似。

如果您对 AutoFixture 感兴趣，请继续关注，我计划在以后的文章中深入探讨它为测试目的提供的好处。

我以前写过在单元测试中使用伪来[提取不相关的值，但是我想向您展示进一步使用它来生成整个对象或对象集合。](https://dev.to/integerman/refactoring-c-unit-tests-5bh6)

[![Tech Elevator](img/60dbe8f82d6c8f731a7da84274458485.png)![](img/c4617a5228f5ea62d27f47eb980b349d.png)](/techelevator) [## 重构 C#单元测试

### matt Eland for Tech Elevator 8 月 31 日 195 分钟阅读

#testing #csharp #dotnet #codequality](/techelevator/refactoring-c-unit-tests-5bh6)

虽然这种技术有助于单元测试数据的生成，但我在这里演示的代码实际上是作为占位符内容，同时作为用户界面的原型，以及在我从事的一个项目中随机生成内容。

* * *

在本教程中，我们在一个场景下工作，我希望有大量随机生成的数据来代表星际飞船上的典型活动(我说过这是一个[有趣的附带项目](https://dev.to/integerman/fun-project-starship-tech-support-angular-ngrx-1913)，对吗？).

具体来说，我需要生成船员、可能崩溃的飞船系统列表以及与随机系统相关的问题。所有这些对象都包装在一个单独的`GameState`类中，这个类是一个结果值，我将它返回给我的[的调用者。NET 核心 API](https://dev.to/integerman/creating-a-net-core-api-3n6d) 。

我生成新游戏状态的代码非常少:

```
public GameState BuildNewGameState(int id)
{
    var state = new GameState(id);
    state.Time = new GameTime(8, 4, 2422, 3, 20);

    state.GenerateCrewMembers(8)
         .GenerateSystems(10)
         .GenerateWorkItems(15);

    return state;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有趣的是我针对`GameState`类编写的三个扩展方法，它们为生成和配置该对象提供了流畅的 API。

我们先来看看`GenerateCrewMembers`方法:

```
internal static GameState GenerateCrewMembers(this GameState state, int count)
{
    int nextId = state.NextCrewId;

    var faker = new Faker<CrewMember>()
        .RuleFor(c => c.Id, f => nextId++)
        .RuleFor(c => c.FirstName, f => f.Person.FirstName)
        .RuleFor(c => c.LastName, f => f.Person.LastName)
        .RuleFor(c => c.Department, f => f.PickRandom<Department>())
        .RuleFor(c => c.Rank, f => f.PickRandom<Rank>())
        .RuleFor(c => c.DaysInRank, f => f.Random.Int(0, 500));

    state.Add(faker.Generate(count));

    return state;
} 
```

Enter fullscreen mode Exit fullscreen mode

首先要注意的是，这是一个`static`类中的`static`方法，并且在第一个参数前面加上了`this`关键字。这使得`GenerateCrewMembers`方法像一个扩展方法，调用它的对象作为第一个参数传入。这与最后返回相同的`state`对象相结合，创建了一个流畅的 API 类型的环境，允许将多个方法调用链接在一起。

这个方法所做的大部分工作是生成一个`Faker`实例，并配置其行为规则。每个`RuleFor`传递一对 lambda 表达式——一个用于配置被构造对象的属性，另一个用于使用 faker 为该属性生成一个值。

例如，`.RuleFor(c => c.FirstName, f => f.Person.FirstName)`行告诉 Bogus，当它看到正在配置的对象上的`FirstName`字段时，它应该使用 Bogus faker `f`的`Person.FirstName`属性从特定语言的数据集中随机生成一个名字。

`Department`和`Rank`都是枚举，所以例如，`f.PickRandom<Rank>()`语句将获取一个随机的`Rank`枚举成员，并将其赋给对象的`Rank`属性。

正如我们在`.RuleFor(c => c.Id, f => nextId++)`中看到的，你不必使用伪来生成一个值。在我的例子中，我选择生成一个数字 id 的顺序列表。

最后，如果你确实想要一个随机数值，就像我在`DaysInRank`中所做的那样，语句`.RuleFor(c => c.DaysInRank, f => f.Random.Int(0, 500));`将获取一个 0 到 500 之间的随机整数值。

如果你是一个随机数迷，想知道在数字生成的背后发生了什么，Bogus 只是简单地包装`System.Random`并调用它的一个实例(如果你需要一致的随机数据，你可以为它提供一个种子)。

* * *

既然有了船员代，那就来看看`ShipSystem`代代码。这比较棘手，因为`ShipSystem`没有无参数的构造函数，需要传入一个 int id。

在这种情况下，我们的代码看起来像这样:

```
internal static GameState GenerateSystems(this GameState state, int count)
{
    int nextId = state.NextSystemId;

    var faker = new Faker<ShipSystem>()
        .CustomInstantiator(f => new ShipSystem(nextId++))
        .RuleFor(c => c.Name, f => f.Commerce.ProductName());

    state.Add(faker.Generate(count));

    return state;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的第一个区别是,`CustomInstantiator`行允许您配置如何创建实例。幸运的是，这种方法的语法和调用构造函数一样简单，我发现这比试图记住参数顺序和仅仅传入一个对象数组更好，例如，就像使用反射来实例化对象一样。

第二个区别是我们使用了`Faker`实例的`Commerce.ProductName`属性。这产生了神奇的宝石，如*乡村混凝土披萨*和*手工花岗岩鸡肉*等等。虽然不完全是 star ship 系统名称，但它可以用于测试和原型制作。

这里还有一点需要注意——如果我们想确保正在创建的类型上的每个公共属性都已设置，我们可以在`Faker<T>`实例上添加一个对`.StrictMode(true)`的调用。如果创建了没有配置规则的新属性，这将引发异常。

* * *

最后，让我们开始配置与船相关联的`WorkItems`。这是一个有趣的集合，因为它与 crew 和 Systems 集合交互，以便将工作项归因于一个 Crew 成员和受影响系统的问题。

代码如下:

```
internal static GameState GenerateWorkItems(this GameState state, int count)
{
    int nextId = 1;
    if (state.WorkItems.Any())
    {
        nextId = state.WorkItems.Max(c => c.Id) + 1;
    }

    var faker = new Faker<WorkItem>()
        .CustomInstantiator(f => new WorkItem(nextId++))
        .RuleFor(c => c.Title, f => f.Hacker.Phrase())
        .RuleFor(c => c.Department, f => f.PickRandom<Department>())
        .RuleFor(c => c.Status, f => WorkItemStatus.New)
        .RuleFor(c => c.Priority, f => f.PickRandom<Priority>())
        .RuleFor(c => c.WorkItemType, f => WorkItemType.Incident)
        .RuleFor(c => c.CreatedByCrewId, f => f.PickRandom(state.Crew).Id)
        .RuleFor(c => c.SystemId, f => f.PickRandom(state.Systems).Id)
        .RuleFor(c => c.AssignedCrewId, f => f.PickRandom(state.Crew.Select(c => c.Id))
                                              .OrNull(f, 0.85f));

    state.Add(faker.Generate(count));

    return state;
} 
```

Enter fullscreen mode Exit fullscreen mode

除了一些例外，这里的一切都与我们之前看到的相似。

使用`CreatedByCrewId`和`SystemId`，我们使用`PickRandom`从集合中随机选取一个成员，然后访问该条目的一个属性。

`AssignedCrewId`的工作方式类似，但我也想支持空值，并在大多数情况下期待它们，所以我从`Crew`的每个成员中选择 Id 属性，然后调用`Faker`扩展`OrNull`，传入`Faker`实例和一个介于 0 和 1 之间的百分比，以确定该值为空的可能性。Faker 然后将使用它来改变赋值的值。

最后，Title 使用`Hacker.Phrase`进行内容生成。这可能是我最喜欢的伪造品生成器，我曾与它一起工作，并吐出你可能在犯罪现场调查电视节目中找到的线条。以下是我最喜欢的几个:

> 我们需要复制 1080p JSON 防火墙！
> 
> 我将导航虚拟 RAM 端口，它应该连接 RAM 端口！
> 
> 我将复制触觉 SSL 系统，那应该系统 SSL 系统！

补充说明:如果有人有多余的，我想要一个触觉 SSL 系统。

当我把所有这些放在一起并运行一个缩小版本的生成过程时，我得到了这个可爱的输出:

```
[  {  "workItems":[  {  "title":"Try to synthesize the AI microchip, maybe it will synthesize the virtual microchip!",  "id":1,  "department":0,  "assignedCrewId":null,  "createdByCrewId":2,  "systemId":1,  "workItemType":0,  "status":0,  "priority":3  }  ],  "crew":[  {  "firstName":"Rogelio",  "lastName":"Morissette",  "rank":13,  "department":0,  "daysInRank":253,  "id":1  },  {  "firstName":"Isabel",  "lastName":"Langosh",  "rank":12,  "department":5,  "daysInRank":184,  "id":2  }  ],  "systems":[  {  "id":1,  "name":"Practical Plastic Bike"  },  {  "id":2,  "name":"Rustic Concrete Towels"  },  {  "id":3,  "name":"Sleek Concrete Computer"  }  ],  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

虽然这在很大程度上是随机的，但对于原型和测试来说，这是有用的数据，对我帮助很大。

通过其多语言支持、URL 生成方法、数据库字符串、颜色、用户评论、mac 地址、电子邮件地址、电话号码，甚至美国社会安全号码、加拿大 SINs 和英国汽车牌照等特定于文化的东西，Bogus 可以做得更多。

希望这向您展示了一个像 Bogus 这样的库是如何在原型化或测试复杂数据时加速您的工作流的。

* * *

*Eric prou zet 在 Unsplash 上拍摄的封面照片*