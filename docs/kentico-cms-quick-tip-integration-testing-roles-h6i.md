# Kentico CMS 快速提示:集成测试角色

> 原文：<https://dev.to/wiredviews/kentico-cms-quick-tip-integration-testing-roles-h6i>

<figure>

[![](img/2ed2c3f4d0a6ca1186cb19f0268e1403.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0AOWvA2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3m03fz5cuocblxc2hpu6.jpg)

<figcaption>Photo by [Jase Bloor](https://unsplash.com/@jasebloor) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

## 了解自己的角色！🧐

当回顾我们的 Kentico 12 MVC 应用程序时，我们可能会有一些类似如下的代码行:

```
int userId = ...

if (userManager.IsInRole(userId, "Manager"))
{
    IEnumerable<string> protectedData = GetTheData();

    return protectedData;
}
else
{
    return Enumerable.Empty<string>();
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使代码正确运行，当您的站点运行时，`"Manager"`角色必须存在于数据库中，并被分配给正确的用户。

我们怎么知道，在任何给定的时间，这是真的？😕

当我们对代码或数据库中的角色进行更改(添加、删除或重命名)时会发生什么？😟

有没有一种方法可以轻松地验证我们代码中的所有角色都与 CMS 中定义的角色相匹配？

## 避散弦

我的第一个建议是避免使用“分散的字符串”。

在我们的代码中有多少这样的行？

```
if (userManager.IsInRole(userId, "Manager")) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

```
userManager.AddToRole(userId, "SpecialUser")); 
```

Enter fullscreen mode Exit fullscreen mode

```
userManager.RemoveFromRole(userId, "Admin")); 
```

Enter fullscreen mode Exit fullscreen mode

这些角色名称会很快分散在我们的应用程序中，这使得它们很难跟踪和更新。错别字很容易被遗漏，即使是代码审查。😣

这些不是[神奇的字符串](https://en.wikipedia.org/wiki/Magic_string),因为它们的目的很明确——但这是一个编译器无法帮助我们的值，因为`string`值没有商业意义。

就编译器而言，输入错误的`string`与具有正确值的`string`相同。

> 对于存储具有重要业务意义的值，基元类型可能不是一个好的选择。如果你发现自己在代码库中遇到分散字符串的问题，你可能有一种[原始痴迷](http://wiki.c2.com/?PrimitiveObsession)的情况

我们可以用包含我们所有角色的静态角色类来代替它们。

```
public static class UserRoles
{
    public const string MANAGER = nameof(MANAGER);
    public const string SPECIAL_USER = nameof(SPECIAL_USER);
    public const string ADMIN = nameof(ADMIN);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 使用 [nameof()操作符](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/nameof)有助于确保常量的名称和它的值永远不会不同步。

现在我们可以在通常使用角色名字符串的地方使用这个类。😉

```
if (userManager.IsInRole(userId, UserRoles.MANAGER)) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

通过在添加、移除或更新角色名称时依靠 C#语言和 Visual Studio 重构工具，避免分散的字符串有助于减少输入错误并使更改更容易。😀

但是，这并不妨碍我们在代码中定义一个数据库中不存在的角色。😯

* * *

## 编写集成测试

检查数据库是否总是符合我们代码期望的最好方法是编写，然后运行一些集成测试。

> 如果您以前没有为您的 Kentico 站点编写过集成测试，请查看我的帖子“设置集成测试”:
> 
> [![seangwright](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/seangwright) [## Kentico 12:设计模式第 8 部分——设置集成测试
> 
> ### Sean g . Wright 7 月 22 日 1910 分钟阅读
> 
> #dotnet #kentico #integrationtests #testing](/seangwright/kentico-12-design-patterns-part-8-setting-up-integration-tests-14jg)

那么我们的集成测试会是什么样的呢？

我们可以为我们的每个角色查询数据库，并确保它不是`null` :

```
[TestFixture]
public class UserRolesTests : IntegrationTests
{
    private readonly int siteId = 1;

    [Test]
    public void UserRoles_Will_Be_In_The_Database()
    {    
        RoleInfo managerRole = RoleInfoProvider
            .GetRoleInfo(UserRoles.MANAGER, siteId);

        RoleInfo specialUserRole = RoleInfoProvider
            .GetRoleInfo(UserRoles.SPECIAL_USER, siteId);

        RoleInfo adminRole = RoleInfoProvider
            .GetRoleInfo(UserRoles.ADMIN, siteId);

        managerRole.Should().NotBeNull();
        specialUserRole.Should().NotBeNull();
        adminRole.Should().NotBeNull();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我正在使用[fluent assessments](https://fluentassertions.com/)来让我访问行为驱动设计(BDD)风格的`.Should()`扩展方法。

这很管用！👍

如果我们查询的任何角色在数据库中缺失，那么我们的集成测试将会失败。👏

然而，在上面的测试中，似乎很容易遗漏一个角色，特别是当我们的应用程序中有大量的角色时。💣

那个角色查询和断言看起来也在一遍又一遍地做着同样的事情。

我们再试一次:

```
[TestFixture]
public class UserRolesTests : IntegrationTests
{
    private readonly int siteId = 1;

    [Test]
    public void UserRoles_Will_Be_In_The_Database()
    {
        List<string> appRoleNames = typeof(UserRoles)
            .GetFields(BindingFlags.Public | BindingFlags.Static)
            .Select(f => f.Name)
            .ToList();

        List<string> roleNames = RoleInfoProvider.GetRoles()
            .WhereEquals(nameof(RoleInfo.SiteID), siteId)
            .WhereIn(nameof(RoleInfo.RoleName), userRoleNames)
            .Column(nameof(RoleInfo.RoleName))
            .AsEnumerable()
            .Select(r => r.RoleName)
            .ToList();

        roleNames.Should().BeEquivalentTo(appRoleNames);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试中，我们使用反射来收集`UserRoles`类的所有字段名称。🧠

我们使用`RoleInfoProvider.GetRoles()`方法，结合强大的 [ObjectQuery](https://docs.kentico.com/k12sp/custom-development/retrieving-database-data-using-objectquery-api) 扩展方法，从数据库中检索相同的角色集。💪

然后，我们断言这两个字符串集合匹配。

不错！🎉

> 注意我们如何在`ObjectQuery`中使用`nameof()`操作符。我建议您使用它，而不是键入数据库列的字符串名称，以避免我们在角色中看到的分散字符串问题。
> 
> 由于 Kentico 中的约定是将类属性名命名为与数据库列名相同的名称，因此,`nameof()`操作符是避免输入错误的好方法。🤗

* * *

## 总结

数据源和应用程序代码之间总是会有脱节。我们的应用程序通常被设计成无状态的，以允许伸缩，而我们的数据源是我们存储所有共享应用程序状态的地方。

当我们需要在我们的应用程序中有基于该状态的逻辑时，我们将来自数据源的值(如角色名)编码到我们的代码中。

集成测试是确保我们的应用程序的期望与数据源提供给我们的相匹配的一个很好的方法。😎

感谢阅读！

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 Kentico 博客系列:

*   [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)
*   [Kentico CMS 快速提示](https://dev.to/search?q=Kentico%20CMS%20Quick%20Tip)