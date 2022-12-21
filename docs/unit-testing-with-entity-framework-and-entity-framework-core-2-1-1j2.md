# 使用实体框架和实体框架核心 2.1 进行单元测试

> 原文：<https://dev.to/pcmichaels/unit-testing-with-entity-framework-and-entity-framework-core-2-1-1j2>

Entity Framework Core 2.1 附带了一个漂亮的小特性:内存数据库设置。这意味着，通过单一选项设置，您的测试可以直接与数据库交互(或者至少是 EF 对数据库的印象),但实际上不接触任何物理数据库。换句话说，你可以为数据访问编写单元测试；一个例子:

```
// Arrange
DbContextOptions<ApplicationDbContext> options = new DbContextOptionsBuilder<ApplicationDbContext>()
    .UseInMemoryDatabase(Guid.NewGuid().ToString())
    .EnableSensitiveDataLogging()                
    .Options;

using (var context = new ApplicationDbContext(options))
{
    context.Database.EnsureDeleted();
    ResourceCategory resourceCategory = new ResourceCategory()
    {
        Name = "TestCategory"
    }
};

// Act
_applicationDbContext.ResourceCategories.Add(resourceCategory);
_applicationDbContext.SaveChanges();

// Assert                
Assert.Equal("TestCategory", context.ResourceCategories.First().Name); 
```

简单地解释一下这是在做什么:我们有一个名为 ApplicationDbContext 的 DbContext，我们正在该上下文的基础上构建一组选项。然后，我们实例化上下文并清理内存数据库。最后，我们将一条新数据添加到上下文中，然后断言它已经被添加。

告诉过你这很棒。

但是如果你还在使用 Entity Framework 6 呢？

很高兴你问了。

开箱即用，EF 没有这种功能；然而，我最近偶然发现(并参与)了一个 NuGet 库，它正好提供了这样一个工具。它为[最小起订量](https://www.nuget.org/packages/EntityFrameworkMock.Moq/)和[替代量](https://www.nuget.org/packages/EntityFrameworkMock.NSubstitute/)提供了一个包装器。GitHub 回购在这里是。

这个最初贴在[这里](https://www.pmichaels.net/2018/09/16/unit-testing-with-entity-framework-and-entity-framework-core-2-1/)