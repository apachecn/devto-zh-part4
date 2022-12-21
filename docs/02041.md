# Kentico 12:设计模式第 13 部分-生成页面 URL

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-13-generating-page-urls-2cde>

<figure>

[![Chain links](img/d858a23ebd3ea2ad19776a7acfefffad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e6sJp1Or--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4uvnhe00in49zknbhxu7.jpg)

<figcaption>Photo by [Markus Spiske](https://unsplash.com/@markusspiske) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

我们使用 Kentico CMS 构建的 web 应用程序会生成内容丰富的页面(否则，我们为什么要使用 CMS 呢🤷‍♀️?！).

这些内容可以是文本、标记、图像或链接(仅举几个例子)。

任何链接到我们自己的网站(内部链接)需要小心对待...

由于 CMS 平台和 MVC 框架的灵活性，可以创建既不导航到应用程序中的任何控制器或动作，也不代表 CMS 中任何真实内容的 URL😮。

让我们来看看 MVC 帮助我们生成 URL 的各种方式，以及我当前为 CMS 树中的内容生成 URL 的方法。

希望我们的 URL 能像我们的代码和内容管理一样灵活👍！

* * *

## 用 MVC APIs 生成 URL

MVC 框架的大部分都是基于路由、控制器和动作——它们是我们识别和区分内容请求的基础。

幸运的是，MVC 公开了使用框架的这些部分的各种方法，以便一致地，并且(在某些情况下)在强大的类型支持下，生成 URL。

在 MVC 中生成 URL 的最简单的方法是使用`System.Web.Mvc.UrlHelper`类中的任何一个 helper 方法。

假设我们有一个控制器如下:

```
public class HomeController : Controller
{
    public ActionResult Home(int id)
    {
        // ...

        return View();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了生成上述动作和控制器的 url，我们可以如下调用`UrlHelper.Action()`:

```
string url = urlHelper.Action("Index", "Home", new { id = 5 }); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以在 Razor 文件中使用下面的调用来实现这一点:

```
<a href="@(Url.Action("Index", "Home", new { id = 5 }))"></a> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们知道路线的名称(在调用`System.Web.Mvc.RouteCollection.MapRoute()`时已经指定)和路线需要的参数，我们可以如下使用【T1:】T2

```
// Assume "Index" and "Home" are the default
//   action and controller values for this route

string url = urlHelper.RouteUrl(
    routeName: "Default Route", 
    routeValues: new { id = 5 }); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这些是用于在 MVC 中生成 URL 的官方 API，但在我看来它们是缺乏的😕。

它们以字符串和匿名对象作为参数，这非常容易出错😒！

不仅如此，这些参数的值应该与 C#类、方法和参数的名称完全匹配。

重命名某个东西会发生什么🤔？

我们的 URL 生成中断了——但只是在运行时！是时候检查应用程序中的所有链接了😞！

* * *

## T4MVC

这种标准的 URL 生成方法还有其他选择，比如 [T4MVC](https://github.com/T4MVC/T4MVC) 。

T4MVC 生成静态类，其中包含我们可能想要路由到的所有控制器和动作的名称。

我们将如下使用 T4MVC(取自他们的文档):

```
@Html.ActionLink("Dinner Details", MVC.Dinners.Details(Model.DinnerID)) 
```

Enter fullscreen mode Exit fullscreen mode

我对这种方法的问题是，它需要重新构建项目来生成模板化的类，这意味着我们的代码可能在一分钟内是类型安全的，然后只有在编译后才会显示一堆错误。

这不是大多数开发人员对使用 C#/的期望。NET 在 Visual Studio。

也就是说，我认为对于非常大的 MVC 驱动的应用程序，它可以帮助管理 URL 生成增加的复杂性。

* * *

## 使用 MVC 期货

如果我们安装微软的[。AspNet.Mvc.Futures](https://www.nuget.org/packages/Microsoft.AspNet.Mvc.Futures) NuGet 包，我们可以从`Microsoft.Web.Mvc.LinkExtensions`类中访问一些类型安全的 URL 生成。

> `Microsoft.AspNet.Mvc.Futures`是一个由[“特性原型”组成的库，被认为是“MVC 的未来版本”](https://weblogs.asp.net/leftslipper/asp-net-mvc-release-candidate-2-i-declare-myself-to-be-declarative)。
> 
> 显然，由于 ASP.NET 核心已经篡夺了经典的 MVC 开发，未来的代码将永远不会进入 MVC 5，但您仍然可以通过安装包在您的项目中使用它👏。

下面我们可以看到这些扩展如何在我们的应用程序中使用控制器类使用表达式来生成 HTML 锚:

```
@Html.ActionLink<HomeController>(c => c.Index(5), "Home") 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这个 API 没有给我们一个原始的 URL。如果我们想在`UrlHelper`类上得到这样的东西，我们需要在自己的代码中使用方法`Microsoft.Web.Mvc.Internal.ExpressionHelper.GetRouteValuesFromExpression<T>()`。

更不幸的是，这些 API 不是为返回`Task<T>`的动作方法而构建的😑。

如果您将它与`Task`返回动作一起使用，您将得到以下警告:

```
The current method calls an async method that returns a Task
or a Task<TResult> and doesn't apply the await operator to the result.
The call to the async method starts an asynchronous task.
However, because no await operator is applied, the program continues
without waiting for the task to complete... 
```

Enter fullscreen mode Exit fullscreen mode

这并不妨碍代码工作，因为我们实际上并没有执行动作。

但是如果你选择使用这种方法，我希望你不要写一堆`async`控制器动作😢因为那些警告会很烦人。

* * *

我看到过一些老的评论，他们担心用表达式而不是我在开始详述的内置方法来生成 URL 会对性能产生影响。

使用表达式方法肯定会对性能产生影响——与数据库访问时间之类的东西相比，这也肯定无关紧要😄。

有了这些东西，我们应该:

*   选择有助于我们管理应用复杂性的方法
*   衡量我们的绩效
*   设定可接受的绩效水平的目标
*   做些小改变
*   再次衡量，看看我们的目标是否达到，并重复

...我们无论如何都应该使用缓存，对吗🤓？

* * *

## 带有`Func<>`和`nameof()`的更简单的类型安全 URL

我在`UrlHelper`上创建了自己的扩展，它提供了一些类型安全，而不需要使用兔子🐇撕裂表情之洞:

```
public static string Action<T>(
    this UrlHelper helper, 
    Func<T, string> nameOfAction, 
    object routeValues = default) where T : Controller
{
    string controllerTypeName = typeof(T).Name;

    string controllerName = controllerTypeName
        .Substring(0, controllerTypeName.Length - 10);

    return helper.Action(nameOfAction(null), controllerName, routeValues);
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以这样调用(无论是在 Razor 还是 C#):

```
string url = urlHelper.Action<HomeController>(
    nameOfAction: c => nameof(c.Index), 
    routeValues: new { id = 5 }); 
```

Enter fullscreen mode Exit fullscreen mode

这里的好处是，如果我们将`HomeController.Index`重命名为`OtherController.Blah`，如果我们的重命名没有自动重构上述代码，我们将会得到一个编译错误⚡.

同时，`HomeController.Index()`返回一个`ActionResult`或者`Task<ActionResult>`也没关系🙂。

这个方法不处理重命名参数或者确保它们的类型安全——你不可能赢得所有的参数！

我主要将它用于无参数的`Index()`动作方法，因此缺少参数类型安全并不是什么大问题。

* * *

## NodeAliasPath Link

我以前写过关于使用`NodeAliasPath`作为路由机制的文章:

[![seangwright](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/seangwright) [## Kentico 12:设计模式第 10 部分——使用 NodeAliasPath 的 MVC 路由

### 肖恩·g·赖特 8 月 12 日 1914 分钟阅读

#dotnet #kentico #mvc #cms](/seangwright/kentico-12-design-patterns-part-10-mvc-routing-with-nodealiaspath-22l3)

对于任何由 CMS 内容树中的特定节点直接表示的页面，这种路由方法是我的首选。

您可能想知道我们如何使用上面的 MVC 实用程序方法来生成这种路由的 URL...

嗯，我们不能😣。

相反，我们需要逆转我在上一篇文章中使用的路由过程。

> 简而言之，Kentico MVC 应用程序接收一个请求，并检查请求的路径是否与给定的`TreeNode`的`NodeAliasPath`匹配。
> 
> 如果匹配，它将请求发送给控制器动作，该动作处理与那个`TreeNode`的`ClassName`相关联的页面类型的请求。
> 
> 否则，请求由 MVC 的标准路由方法处理(基于约定或属性路由)。

* * *

### 可链接页面

我想出了一个我称之为“可链接页面”的模式，在这个模式中，我们在应用程序代码中链接到 CMS 内容树中可以被路由到的特定节点。

我将这些可路由的节点称为“页面”，但实际上我只希望显式创建链接的节点子集(例如:主页、联系我们、产品登录页面)

然后，我们依靠内容同步来确保 CMS 内容节点和应用程序代码中的“可链接页面”之间的连接保持一致，无论我们的应用程序运行在什么环境中🤔。

### 值对象

我使用值对象模式来确保只有不可变的离散页面集可以被链接🤓。

让我们看看`LinkablePage`类的示例代码:

```
public class LinkablePage
{
    static LinkablePage()
    {
        Home = new LinkablePage(
            new Guid("65bc6106-74c5-456d-bc5b-71ae0d89c92a"));

        ContactUs = new LinkablePage(
            new Guid("2a955262-1fd2-44d6-b0f2-9329b42eefa3"));

        Products = new LinkablePage(
            new Guid("61a5f388-1c19-4645-a628-4a0e4b70fbac"));
    }

    public static LinkablePage Home { get; protected set; }
    public static LinkablePage ContactUs { get; protected set; }
    public static LinkablePage Products { get; protected set; }

    public Guid NodeGuid { get; }

    protected LinkablePage(Guid nodeGuid) => NodeGuid = nodeGuid;
} 
```

Enter fullscreen mode Exit fullscreen mode

该类只有一个构造函数`protected`，这意味着实例只能在类本身(或子类)中创建。

实例是类的静态属性，这意味着它们被很好地定义并且易于访问👍。

该类也是非原始类型(例如:`int`、`string`、`Guid`)，所以它可以是方法的参数，这赋予了这些方法更多的领域含义🙂。

类实例和整个可能的实例值集是不可变的，这使得验证正确的可能值集更加容易😎。

> 我在这里省略了一些值对象的相等比较功能，因为我没有比较`LinkablePage`实例，但是如果你想阅读关于这个主题的更多内容，请查看[值对象解释](https://enterprisecraftsmanship.com/posts/value-objects-explained/)。

### Html 扩展

为了渲染 Razor 视图中的链接，我使用了一个`HtmlHelper`扩展方法:

```
public static void RenderPageLink(
    this HtmlHelper htmlHelper, 
    LinkablePage page, 
    string linkText, 
    string elementClasses = "")
{
    var request = new PageLinkRequest(page, linkText, elementClasses);

    htmlHelper.RenderAction(
        nameof(NavigationController.PageLink),
        "Navigation", 
        new { request });
} 
```

Enter fullscreen mode Exit fullscreen mode

### 子动作

该调用呈现的 Razor 子动作如下:

```
[ChildActionOnly]
public ActionResult PageLink(PageLinkRequest request)
{
    // The following call is wrapped in a caching layer to prevent
    //   repeat trips to the database to generate the same links

    string nodeAliasPath = queryDispatcher
        .Dispatch(new NodeAliasPathByNodeGuidQuery(request.Page));

    string linkUrl = string.IsNullOrWhiteSpace(nodeAliasPath)
        ? ""
        : nodeAliasPath.ToLower();

    var viewModel = new PageLinkViewModel(
        label: request.Text, 
        linkUrl: linkUrl, 
        classes: request.Classes);

    return PartialView(viewModel);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 剃刀视图

而剃刀视图就像下面几行字一样简单:

```
@{ 
    if (string.IsNullOrWhiteSpace(Model.LinkUrl)
      || string.IsNullOrWhiteSpace(Model.Label))
    {
        return;
    }
}

<a href="@Model.LinkUrl" class="@Model.Classes">@Model.Label</a> 
```

Enter fullscreen mode Exit fullscreen mode

### 生成链接

因此，为了将所有这些放在一起，我们在另一个 Razor 视图中使用了`HtmlHelper`扩展，如下所示:

```
<li>
  @{ Html.RenderPageLink(
         LinkablePage.Home, 
         "Home", 
         "btn btn-primary"); 
  }
</li>
<li>
  @{ Html.RenderPageLink(
         LinkablePage.ContactUs, 
         "Contact Us", 
         "btn btn-primary"); 
  }
</li> 
```

Enter fullscreen mode Exit fullscreen mode

对`queryDispatcher.Dispatch(new NodeAliasPathByNodeGuidQuery(nodeGuid));`的调用被包装在缓存中，包含所有正确的依赖键，SQL 查询本身是最小的，只选择`NodeAliasPath`列。

> 如果您正在寻找如何在所有数据库查询中应用一致缓存的想法，请查看下面的帖子👍：
> 
> [![seangwright](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/seangwright) [## Kentico 12:设计模式第 12 部分——数据库查询缓存模式
> 
> ### 肖恩·g·赖特 8 月 26 日 1913 分钟阅读
> 
> #kentico #mvc #caching #csharp](/seangwright/kentico-12-design-patterns-part-12-database-query-caching-patterns-43hc)
> 
> 如果您对创建 Kentico 缓存依赖键的简单、可读的方法感兴趣，请尝试这篇文章👍：
> 
> [![WiredViews](img/efaaf7efc991a9fb21f3fe51a780f261.png)![](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/wiredviews) [## Kentico CMS 快速提示:FluentCacheKeys -一致的缓存依赖项密钥生成
> 
> ### Sean g . Wright for WiredViews 2019 年 9 月 2 日 15 分钟阅读
> 
> #kentico #cms #caching #csharp](/wiredviews/kentico-cms-quick-tip-fluentcachekeys-consistent-cache-dependency-key-generation-9ek)

### 在代码中生成页面 URL

我们也可以使用上述方法在 C#代码中生成 URL。这些可以用在来自控制器的重定向响应或来自 MVC 应用程序的电子邮件中。

下面是一个在`LogoutController` :
中生成重定向 url 的例子

```
public ActionResult Index()
{
    authenticationManager.SignOut(DefaultAuthenticationTypes.ApplicationCookie);

    string nodeAliasPath = queryDispatcher
        .Dispatch(new NodeAliasPathByNodeGuidQuery(LinkablePage.Home));

    string homePageUrl = string.IsNullOrWhiteSpace(nodeAliasPath)
        ? "/"
        : nodeAliasPath.ToLower();

    return Redirect(homePageUrl);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我知道你可能认为这个场景有点做作😋，但我相信`LinkablePage.Home`的可读性比`"/"`本身更好，提供了更多的商业意义。

在我的代码中，我将这个查询逻辑隐藏在一个`IUrlBuilder.BuildNodeAliasPath(LinkablePage page);`方法后面，这使得上面的方法更加精简和易于测试😎。

## 总结

我们看了一下 MVC 提供的所有开箱即用的 URL 生成选项。

不幸的是，它们缺少大多数 C#开发人员所依赖的主要特性——类型安全👎。这些都可以工作，但是不能扩展到更大的应用程序。

像 T4MVC 和 MVC 这样的项目。未来在某种程度上有助于填补空白，每一种都采用不同的方法(模板和表达式)来创建类型安全的 URL。

它们也有自己的警告——但是对于你的团队和项目来说，这种权衡可能是值得的。

我展示了如何使用`nameof()`操作符和`Func<T, string>`生成类型安全的 URL。

这很简单，在我看来，这是最好的部分😉！

最后，我们看了看如何使用`LinkablePage`值对象类基于`NodeAliasPath`路由为页面生成 URL。

这种方法要求我们依赖环境之间的内容同步，还需要一些缓存基础设施来确保我们的数据库不会因查询而过载。

然而，它让我们在代码中将 CMS 树中的内容项视为特定的域对象，并使用树中这些项的路径作为生成的 URL。

如果这些`NodeAliasPath`值改变，我们所有的链接都会更新🤗。

如果这些节点从 CMS 中移除，并且我们移除了特定的`LinkablePage`实例(例如:`LinkablePage.ContactUs`)，我们的代码将不会编译，直到我们改变了对它的所有引用😅。

我很想知道你和你的团队用什么技术在你的 Kentico 12 MVC 代码库中一致可靠地创建 URL。

请在下面的评论中告诉我！

感谢阅读🙏。

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 Kentico 博客系列:

*   [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%3A%20Design%20Patterns)
*   [Kentico CMS 快速提示](https://dev.to/search?q=Kentico%20CMS%20Quick%20Tip)