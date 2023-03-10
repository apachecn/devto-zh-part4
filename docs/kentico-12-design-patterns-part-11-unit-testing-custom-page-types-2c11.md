# Kentico 12:设计模式第 11 部分-单元测试定制页面类型

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-11-unit-testing-custom-page-types-2c11>

<figure>

[![Books and pages](img/4a1dc4fa73a374867929169f470a55ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JC7OvzaM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bya0yinp23e4bm4jvq1r.jpg)

<figcaption>Photo by [Patrick Tomasso](https://unsplash.com/@impatrickt) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

大多数时候，当你为你的 Kentico 12 MVC 应用程序编写单元测试时，你可以依靠[文档化的 Kentico 测试 API](https://docs.kentico.com/k12sp/custom-development/writing-automated-tests/faking-info-and-provider-objects-in-unit-tests)，比如`UnitTests`基类和`Fake<>()`方法。

这些 API 允许清除依赖于 Kentico 数据库的类型和数据源，而不依赖于与数据库的实时连接💪。

但是一些测试场景的解决方案需要深入了解 Kentico 的内部工作原理😮。

下面我们将看一个如何测试代码的例子，这个代码使用一个定制的页面类型，这个页面类型有一个“文件”类型的数据库字段。

> 要了解更多关于页面类型的信息，请查看 [Kentico 的文档](https://docs.kentico.com/k12sp/developing-websites/defining-website-content-structure/creating-and-configuring-page-types/creating-content-only-page-types)或[来自 BizStream 的这篇文章👍。](https://www.bizstream.com/blog/february-2019/kentico-101-page-types)

* * *

## 定义场景

### 自定义页面类型- `EmployeeContent`

让我们设置一个想要测试的场景。

假设我们有一个带有以下字段的`EmployeeContent`自定义页面类型:

*   `EmployeeContentID` - `int`
*   `EmployeeContentImage` - `Guid`
*   `EmployeeContentName` - `string`
*   `EmployeeContentHireDate` - `DateTime`

下面是 Kentico 为这种页面类型自动生成的一些代码:

```
public partial class EmployeeContent : TreeNode
{
    public const string CLASS_NAME = "Sandbox.EmployeeContent";

    private readonly EmployeeContentFields mFields;

    [DatabaseIDField]
    public int EmployeeContentID
    {
        get => ValidationHelper
            .GetInteger(GetValue("EmployeeContentID"), 0);
        set => SetValue("EmployeeContentID", value);
    }

    [DatabaseField]
    public Guid EmployeeContentImage
    {
        get => ValidationHelper
            .GetGuid(GetValue("EmployeeContentImage"), Guid.Empty);
        set => SetValue("EmployeeContentImage", value);
    }

    [DatabaseField]
    public string EmployeeContentName
    {
        get => ValidationHelper
            .GetString(GetValue("EmployeeContentName"), @"");
        set => SetValue("EmployeeContentName", value);
    }

    [DatabaseField]
    public DateTime EmployeeContentHireDate
    {
        get => ValidationHelper
            .GetDateTime(GetValue("EmployeeContentHireDate"), DateTimeHelper.ZERO_TIME);
        set => SetValue("EmployeeContentHireDate", value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的初始类和属性定义之后，我们可以找到`EmployeeContentFields`嵌套类，这给了我们一种更方便的方式来访问不是从`TreeNode`继承的字段(这些将是上面定义的定制字段)。

```
[RegisterAllProperties]
public partial class EmployeeContentFields :
    AbstractHierarchicalObject<EmployeeContentFields>
{
    private readonly EmployeeContent mInstance;

    public EmployeeContentFields(EmployeeContent instance) =>
        mInstance = instance;

    public int ID
    {
        get => mInstance.EmployeeContentID;
        set => mInstance.EmployeeContentID = value;
    }

    public DocumentAttachment Image =>
        mInstance.GetFieldDocumentAttachment("EmployeeContentImage");

    public string Name
    {
        get => mInstance.EmployeeContentName;
        set => mInstance.EmployeeContentName = value;
    }

    public DateTime HireDate
    {
        get => mInstance.EmployeeContentHireDate;
        set => mInstance.EmployeeContentHireDate = value;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了让我们快速访问特定于自定义页面类型的字段之外，`EmployeeContentFields`让我们将某些类型的字段视为我们期望的数据类型👍。

`EmployeeContent.EmployeeContentImage`属性和`EmployeeContent.EmployeeContentFields.Image`属性是带有`File`数据类型字段的自定义页面类型的指示器。

> 你可以在 Kentico 的文档中阅读更多关于管理页面类型字段的信息。

这是一个唯一命名的页面附件，与`EmployeeContent`继承的`TreeNode.Attachments`属性相反。

`TreeNode.Attachments`是页面附件的集合，这些附件与页面本身没有命名关联。它们不是暴露在页面类型类上的属性——只是一包附件🤔。

这不同于页面类型上具有指定为“文件”的数据类型的字段。由于这些是特定字段，因此它们在页面类型类上具有特定的属性，允许您访问附件🧐.

注意，虽然`EmployeeContentImage`是类型`Guid`(这是与该字段相关联的图像附件的`Guid`)，但是在`EmployeeContentFields`类中，我们有一个类型`DocumentAttachment`的`Image`属性。

```
[DatabaseField]
public Guid EmployeeContentImage
{
    get => ValidationHelper
       .GetGuid(GetValue("EmployeeContentImage"), Guid.Empty);
    set => SetValue("EmployeeContentImage", value);
} 
```

Enter fullscreen mode Exit fullscreen mode

vs

```
public DocumentAttachment Image =>
    mInstance.GetFieldDocumentAttachment("EmployeeContentImage"); 
```

Enter fullscreen mode Exit fullscreen mode

虽然`Guid`是存储在数据库列中的真实原始值，但是`DocumentAttachment`可能是我们在使用这个定制页面类型类时想要与之交互的对象。

> `DocumentAttachment`为我们提供了对`.GetPath()`方法的访问，该方法使用 Kentico 的内部机制来构建附件的 URL👏，而不是我们自己做 URL 的字符串构建。

这个`Image`属性确实帮助了我们，但是它也使得我们的类很难测试。该属性是只读的！我们不能将一个测试值分配给我们可能为测试而创建的`EmployeeContent`的实例😞。

还有，`.GetFieldDocumentAttachment()`是做什么的？我们无法拦截这个呼叫，所以我们需要依靠 Kentico 的测试 API 来帮助🙄。

### 请求处理程序- `EmployeesListPageRequestHandler`

遵循[中介模式](https://blogs.msdn.microsoft.com/cdndevs/2016/01/26/simplifying-development-and-separating-concerns-with-mediatr/),我们将业务逻辑和请求处理从 MVC 控制器中转移到请求处理器中。

> 我们将使用[mediator](https://github.com/jbogard/MediatR)库来帮助我们构建这个基础设施。

这个请求处理器类，我们将命名为`EmployeesListPageRequestHandler`，将从控制器获取请求，处理它，并返回一个视图模型作为响应。

我们可以从自动生成的`EmployeeContentProvider`中检索数据，但是这可能会迫使我们为这个请求处理程序编写一个集成测试。

相反，我们将把数据访问实现的细节隐藏在外观后面。

它可以是一个存储库、一个查询或一个将查询映射到查询处理程序的调度程序(类似于 Mediatr 的工作方式)。

关键是，您需要某种抽象来保持请求处理程序与数据访问技术的松散耦合。

#### 关于存储库的快速说明...

出于几个原因，我并不喜欢定制的存储库。

1.  我们经常使用已经是存储库的对象关系映射器(例如:实体框架或 Kentico 的`ObjectQuery<T>`、`DocumentQuery<T>`和静态`*Provider`类型)，所以[将一个存储库包装在另一个](https://lostechies.com/jimmybogard/2012/10/08/favor-query-objects-over-repositories/)中感觉像是滥用模式。

2.  自定义编写的存储库变成了处理相同类型(例如:`EmployeeContent`)但用途不同的方法的大袋。它们打破了单一响应原则，通常没有理由包含它们包含的所有方法，因为存储库类本身在方法之间没有任何共享状态。

这就是为什么我更喜欢使用命令/查询责任分离模式，并使用[查询、查询处理程序和调度程序](https://weblogs.asp.net/shijuvarghese/cqrs-commands-command-handlers-and-command-dispatcher)💪。

下面我将使用一个简单的`Query`，但是使用你最熟悉的模式🤗。

当然，为了可测试性，我们将使用基于构造函数的依赖注入😎！

* * *

下面是我们的请求处理程序的简单实现，这是我们将要为其编写单元测试的代码。

```
public class EmployeesListPageRequestHandler
    : RequestHandler<EmployeeListPageRequest, EmployeeListPageViewModel>
{
    private readonly IEmployeeQuery query;

    public EmployeesListPageRequestHandler(IEmployeesQuery query)
    {
        this.query = query;
    }

    protected override EmployeeListPageViewModel Handle(
        EmployeeListPageRequest request)
    {
        IEnumerable<EmployeeContent> employees = query.Execute(request.EmployeeCount);

        if (employees is null)
        {
            return new EmployeeListPageViewModel(
                employees: Enumerable.Empty<EmployeeContent>(),
                count: 0);
        }

        var employeeViewModels = employees.Select(e =>
        {
            string imageUrl = e.Fields.Image.GetPath();

            new EmployeeViewModel(
                name: e.Fields.Name,
                imageUrl: imageUrl,
                hireDate: e.Fields.HireDate
        }));

        return new EmployeeListPageViewModel(
                employees: employeeViewModels,
                count: employeeViewModels.Count());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类大概从数据库中检索`EmployeeContent`，处理无效数据，并返回有效的视图模型实例。

非常普通的用例！

## 设置我们的测试

既然我们已经搭建出了所有想要测试的代码，并且确定了我们的测试将与之交互的关键领域，那么让我们开始创建我们的单元测试🥳吧！

### 创建测试类- `EmployeesListPageRequestHandlerTests`

首先，让我们按照 Kentico 的单元测试文档创建一个测试类和测试方法。

```
using CMS.Tests;
using NUnit.Framework;
using using System.Threading.Tasks;

[TestFixture]
public class EmployeesListPageRequestHandlerTests : UnitTests
{
    [Test]
    public async Task Handle_Will_Initialize_ViewModel()
    {
        // Arrange

        // Act

        // Assert
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要从`CMS.Tests`名称空间中的`UnitTests`继承。

这将让我们告诉 Kentico，我们希望这个测试使用通常来自数据库(我们的自定义页面类型实例)的数据，但是我们将在测试中自己提供数据👍。

我们也将把我们的测试方法分成三个阶段——安排、行动和断言。

### 安排我们的测试-自动修复、n 替代和 Kentico 假货

现在让我们开始测试的“安排”阶段。

我们将使用 [AutoFixture](https://github.com/AutoFixture/AutoFixture) 来为我们的测试创建值。

大多数时候，测试中使用的实际值并不重要。

这些值被传递并可能被修改，但是只要测试结束时的值与开始时的值一致——给定测试对象的过程(`sut`)——我们并不真正关心原始值是什么。

AutoFixture 允许我们动态创建有效值。

下面是一个例子:

```
using AutoFixture;

...

// Arrange

var fixture = new DomainFixture();

string name = fixture.Create<string>();

// Do something with name ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要告诉 Kentico，我们想要创建我们的`EmployeeContent`页面类型的假实例，我们将把它们作为从我们的`EmployeesQuery`返回的值来提供。

首先，让我们创建假的`EmployeeContent`实例。

对于从 Kentico 导入名称空间，我们需要下面的`using`语句:

```
using CMS.DataEngine;
using CMS.DocumentEngine;
using CMS.DocumentEngine.Types.Sandbox;
using CMS.Tests;
using Tests.DocumentEngine; 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用从`UnitTests`继承而来的`Fake()`方法来告诉 Kentico 我们想要伪造对一些数据的访问。

我们还将使用`DocumentType<T>()`扩展方法来告诉 Kentico 我们将伪造什么类型的页面类型。

```
Fake().DocumentType<EmployeeContent>(EmployeeContent.CLASS_NAME); 
```

Enter fullscreen mode Exit fullscreen mode

上面的行只告诉 Kentico 我们的意图——它实际上并没有创建任何`EmployeeContent`的实例，现在让我们来做:

```
var employee = TreeNode.New<EmployeeContent>().With(e =>
{
    e.Fields.Name = fixture.Create<string>();
    e.Fields.HireDate = fixture.Create<DateTime>();
    e.EmployeeContentImage = fixture.Create<Guid>();
}); 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们没有给`e.Fields.Image`赋值，因为它是只读的，所以我们给它的后台字段`EmployeeContentImage`赋值`Guid`。

但是当我们调用`EmployeeContent.Fields.Image`时，我们如何将那个`Guid`转换成`DocumentImage`的实例呢🤔？

我们将需要 Kentico 为我们做这些，但这将需要模仿更多的数据

```
var attachmentGuid = fixture.Create<Guid>();

Fake<AttachmentInfo, AttachmentInfoProvider>()
    .WithData(new AttachmentInfo
    {
        AttachmentGUID = attachmentGuid
    });

Fake().DocumentType<EmployeeContent>(EmployeeContent.CLASS_NAME);

var employee = TreeNode.New<EmployeeContent>().With(e =>
{
    e.Fields.Name = fixture.Create<string>();
    e.Fields.HireDate = fixture.Create<DateTime>();
    e.EmployeeContentImage = attachmentGuid;
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何将`attachmentGuid`值用于伪造的`AttachmentInfo.AttachmentGUID`属性和`EmployeeContent.EmployeeContentImage`属性的。

这将允许 Kentico 在测试运行时将这两个实例联系在一起。

原来`TreeNode.GetFieldDocumentAttachment()`调用`AttachmentInfoProvider.GetAttachmentInfo()`，传递要检索的附件的`Guid`。

因此，只要我们使用`AttachmentInfoProvider`伪造一个`AttachmentInfo`，我们将能够设置我们的测试状态，使其完全在内存中工作，而不需要数据库🤘🎉🔥。

* * *

现在我们需要一种方法来提供我们上面创建的`employee`，作为我们的`IEmployeeQuery`为`Execute()`调用返回的值。

我们可以使用 [NSubstitute](https://nsubstitute.github.io/) 用最少的实现来存根我们的接口，这些实现只返回值而不执行任何操作。

```
using NSubstitute;

// Arrange

var query = Substitute.For<IEmployeeQuery>();

query
    .Execute(Arg.Is(request.EmployeeCount))
    .Returns(new [] { employee }); 
```

Enter fullscreen mode Exit fullscreen mode

让我们把测试的安排阶段的所有部分放在一起:

```
[Test, AutoDomainData]
public async Task public async Task Handle_Will_Initialize_ViewModel()
{
    // Arrange

    var attachmentGuid = fixture.Create<Guid>();

    Fake<AttachmentInfo, AttachmentInfoProvider>()
        .WithData(new AttachmentInfo
        {
            AttachmentGUID = attachmentGuid
        });

    Fake().DocumentType<EmployeeContent>(EmployeeContent.CLASS_NAME);

    var employee = TreeNode.New<EmployeeContent>().With(e =>
    {
        e.Fields.Name = fixture.Create<string>();
        e.Fields.HireDate = fixture.Create<DateTime>();
        e.EmployeeContentImage = attachmentGuid;
    });

    var request = fixture.Create<EmployeeListPageRequest>();

    var query = Substitute.For<IEmployeeQuery>();

    query
        .Execute(Arg.Is(request.EmployeeCount))
        .Returns(new [] { employee });

    IRequestHandler<EmployeeListPageRequest, EmployeeListPageViewModel> sut =
        new NewsRequestHandler(query);

    // Act

    // Assert
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 表演我们的测试用例

现在是简单的部分——行动阶段:

```
var viewModel = await sut.Handle(request, default); 
```

Enter fullscreen mode Exit fullscreen mode

是的，就是这样。所有的工作通常都进入测试的安排和断言阶段，因为 Act 阶段已经编写好了——这是您正在测试的方法的主体！

### 在我们的测试结果上断言-流量评估

我们现在需要断言测试数据的状态正是我们在 Act 阶段之后所期望的状态。

我们可以使用。NET 的内置断言调用(在`Microsoft.VisualStudio.TestTools.UnitTesting`名称空间中)，但是`NUnit`已经在`NUnit.Framework`名称空间中为我们提供了一些。

也就是说，我更喜欢使用像[fluent assessments](https://fluentassertions.com/introduction)这样的库来提供更加行为驱动的断言开发风格，我发现这使得测试更具可读性😉。

FluentAssertions 使用扩展方法提供如下语法:

```
SomeViewModel viewModel = ...

viewModel.Should().NotBeNull();

viewModel.FirstName.Should().Be("abc"); 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们在对测试对象采取行动后，使用 FluentAssertions 来断言测试的状态:

> 我将在编曲和表演阶段加入一些片段以供参考。

```
// Arrange

...

var employee = TreeNode.New<EmployeeContent>().With(e =>
{
    e.Fields.Name = fixture.Create<string>();
    e.Fields.HireDate = fixture.Create<DateTime>();
    e.EmployeeContentImage = attachmentGuid;
});

...

// Act

var viewModel = await sut.Handle(request, default);

// Assert

viewModel.Count.Should().Be(1);

var employeeViewModel = viewModel.Employees.First();

employeeViewModel.Name.Should().Be(employee.Fields.Name);
employeeViewModel.HireDate.Should().Be(employee.Fields.HireDate);

employeeViewModel.ImageUrl
    .Should()
    .Contain(employee.EmployeeContentImage.ToString()); 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里的断言陈述了以下内容:

*   视图模型中应该只有(1)个雇员。
*   `EmployeeViewModel`的值应该与`EmployeeContent`实例的值相匹配。
*   `EmployeeViewModel.ImageUrl`应该包含与`EmployeeViewModel.EmployeeContentImage`的`Guid`匹配的字符串`Guid`。

对于最后一个断言，我们可以更具体一些，确保由 Kentico 的`DocumentAttachment.GetPath()`方法创建的 URL 与我们期望的匹配，一个字符一个字符地匹配，但是我对我们所拥有的很满意。

## 总结

看看我们制造的美丽的混乱🤣！

我们查看了一些设计模式，它们将帮助我们构建我们的 Kentico MVC 应用程序，并设置我们的业务逻辑(此时主要是数据访问)，使其 100%可测试👍！

我们还使用 Kentico 的测试 API 来帮助我们模拟数据，并确保我们的测试可以作为单元测试而不是集成测试来运行👍。

这将使测试运行得更快，并且随着我们的应用程序的变化而变得不那么脆弱👍。

最后我们利用了一些。NET 测试库(AutoFixture、NSubstitute 和 FluentAssertions)来帮助我们关注测试的关键阶段——安排、执行和断言👍。

> 我们忽略了一个关键的测试场景——如果我们的`IEmployeesQuery`返回的数据是`null`会怎么样？我们在请求处理程序中处理这个问题，但是我们没有针对这个场景进行测试。
> 
> 这个测试用例应该是一个单独的测试方法，我们在其中重新安排、操作和断言，但是这一次安排和断言阶段是为一个`null`数据场景设置的。
> 
> 我将把这留给您来实现🤓！

如果你有任何反馈，或者推荐你喜欢的写测试的方法，我很乐意在下面的评论中听到。

感谢阅读！

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 Kentico 博客系列:

*   [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)
*   [Kentico CMS 快速提示](https://dev.to/search?q=Kentico%20CMS%20Quick%20Tip)