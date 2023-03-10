# Kentico 12:设计模式第 6 部分——在 Kentico 12 MVC 中呈现元标签

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-6-rendering-meta-tags-in-kentico-12-mvc-e0l>

<figure>

[![](img/ac214356de47a7e6117e82242f842acb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VDquYM7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ea4ihs7myvmhiahzfii3.jpg)

<figcaption>Photo by [Sai Kiran Anagani](https://unsplash.com/@_imkiran) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

## Meta 标签的重要性

HTML 元标签不向我们的访问者显示信息，而是向其他系统或网络爬虫显示信息，这是一种有用的提示，可以显示关于网站和网页内容的信息。

如果我们想要为我们的站点管理显示在搜索引擎结果中的内容，那么我们需要使用元标签来提供尽可能好的描述。

如果我们希望我们的网站能够通过社交媒体方便、准确地分享，我们需要在页面上添加正确的元标签，告诉那些社交平台(例如:[脸书](https://developers.facebook.com/docs/sharing/webmasters)、 [Pinterest](https://developers.pinterest.com/docs/rich-pins/overview/) 和 [Twitter](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/guides/getting-started) )正在分享什么。

好的，那么我们如何确保这些 meta 标签被正确地应用到由我们的 Kentico 12 MVC 应用程序呈现的页面上呢？🤔

## 简单的方法

我们的`<meta>`标签需要添加到 HTML 文档的`<head>`中。在 ASP.NET MVC 中，`<head>`通常位于`Views\_Layout.cshtml`文件中。

舞蹈船样本网站向我们展示了这样的东西:

```
<!DOCTYPE html>

<html>
<head id="head">
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta charset="UTF-8" />
    @ViewBag.Title - Dancing Goat
    <link rel="icon" href="@Url.Content("~/conteimg/favicon.png")" type="image/png" />
    <link href="@Url.Content("~/Content/Styles/Site.css")" rel="stylesheet" type="text/css" />
    @RenderSection("styles", required: false)

    <!-- We can add some meta tags -->
    <meta name="description" content="A description of the page!" />
</head> 
```

Enter fullscreen mode Exit fullscreen mode

该文件实际上是站点的“母版页”,所有页面特定的内容都呈现在其中的一个部分中。

我们想在上面的`<head>`中添加`<meta>`标签，但是我们遇到了一个问题。

我们想要放入`<meta>`标签的信息来自每个特定的页面，但是这个布局被所有的页面使用。🤨

我们需要根据请求动态地设置这些信息。

MVC 以[视图包](https://www.tutorialsteacher.com/mvc/viewbag-in-asp.net-mvc)的形式向我们展示了一个解决方案。

`ViewBag`是一个动态(读取:非类型化)的数据集合，我们可以在视图中访问它，并且可以在 MVC 管道中的任何地方为它赋值。

它在我们的所有视图中都是全球可用的，包括`_Layout.cshtml`。

因此，标准的方法是给一个`Controller`类中的`ViewBag`赋值，然后访问布局中的数据:

```
// ProductsController.cs

public class ProductController
{
    public ActionResult Index()
    {
         ViewBag.OGTitle = "Acme Products 🎂";
         ViewBag.OGImage = "https://acme-products.com/logo.png";
         ViewBag.Description = "A description of the page!";

         return View();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- _Layout.cshtml -->

<head>
   <!-- ... -->
   <meta name="og:title" content="@ViewBag.OGTitle" />
   <meta name="og:image" content="@ViewBag.OGImage" />
   <meta name="description" = content="@ViewBag.Description" />
</head> 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是有几个问题...

*   ❌:在整个代码中，我们最终执行相同类型的赋值，就像我们在示例控制器中一样——除了没有对所有必需的`ViewBag`键都被赋值的数据或需求进行验证
*   ❌是动态的，这意味着你可以输入一个属性名，而不需要任何我们通常从 C#中得到的编译器支持
*   ❌ `ViewBag`通常不鼓励在规模超过演示的应用程序中使用，在这里使用这个`ViewBag`可能会鼓励更多的使用，上面的警告会成倍增加。
*   ❌:单元真的不可测试吗...苏...😣

我们指尖有 C#和 MVC 的力量，让我们用它们来解决这个困境吧！

## 设计图案救场

我们可以使用几种常见的软件设计模式来帮助我们得到一个更加优雅和健壮的解决方案。😎

### 领域驱动设计(DDD) -保护不变量

首先，让我们定义一个类型来表示页面中我们想要的“元”信息。

```
public class SiteMeta
{
    public const string OGImage = "og:image";
    public const string OGSiteName = "og:site_name";

    private readonly Dictionary<string, string> metas;

    public string Title { get; } = "";

    public IReadOnlyDictionary<string, string> Metas => metas;

    public SiteMeta(
        string title,
        Dictionary<string, string> metas = null)
    {
        Guard.Against.NullOrWhiteSpace(title, nameof(title));

        Title = title;

        this.metas = metas is null
            ? new Dictionary<string, string>()
            : metas;
    }

    public void SetOGImage(string value)
    {
        Guard.Against.NullOrWhiteSpace(value, nameof(value)); 

        metas[OGImage] = value;       
    }

    public void AddMeta(string key, string value)
    {
        Guard.Against.NullOrWhiteSpace(key, nameof(key));
        Guard.Against.NullOrWhiteSpace(value, nameof(value));

        metas[key] = value;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经定义了一个类，[通过对构造函数和方法调用使用](https://softwareengineering.stackexchange.com/a/32755/87445)[保护子句](https://deviq.com/guard-clause/)来保护它的不变量。👍

我们还用`SetOGImage()`添加了一些强类型，解决了动态`ViewBag`的问题。😄

这是确保我们的应用程序按照我们期望的方式工作的良好的第一步(而且它也是非常单元可测试的代码！)

### 【依赖注入(DI)】-构成图层

现在让我们创建一个类作为`SiteMeta`类型的存储，允许应用程序的一部分设置值，另一部分检索它。

```
public interface ISiteMetaService
{
    SiteMeta Get();
    void Set(SiteMeta siteMeta);
}

public class SiteMetaService : ISiteMetaService
{
    private SiteMeta currentSiteMeta;

    public SiteMeta Get() => currentSiteMeta ?? new SiteMeta("Default");

    public void Set(SiteMeta siteMeta)
    {
        Guard.Against.Null(siteMeta, nameof(siteMeta));

        currentSiteMeta = siteMeta;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用我们的控制反转(IoC)容器注册这个类型，然后在需要使用它的地方将它作为构造函数参数提供——比如在`Controller` ( [注入我们的依赖](https://en.wikipedia.org/wiki/Dependency_injection))中。

> 我们希望我们的`ISiteMetaService`在我们的 IoC 容器中有一个终生的“每请求”。这有助于确保前一个请求中的`SiteMeta`不会意外地用在后一个请求中。

```
public class ProductController
{
    private readonly ISiteMetaService service;

    public ProductController(ISiteMetaService service)
    {
         Guard.Against.Null(service, nameof(service));

         this.service = service;
    }

    public ActionResult Index()
    {
         // These values could also come from a custom Kentico PageType

         var meta = new SiteMeta("Acme Products 🎂");

         meta.SetOGImage("https://acme-products.com/logo.png");

         service.Set(meta);

         return View();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以通过 DI 属性注入而不是构造函数注入来访问这个服务——在`ActionFilterAttribute` :
中

```
public class SiteMetaActionFilterAttribute : ActionFilterAttribute
{
    public ISiteMetaService SiteMetaService { get; set; }

    public override void OnActionExecuted(ActionExecutedContext filterContext)
    {
        filterContext.Controller.ViewBag.SiteMeta = SiteMetaService.Get();

        base.OnActionExecuted(filterContext);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`_Layout.cshtml`中，我们保证在类型为`SiteMeta` :
的`ViewBag`上有一个`SiteMeta`属性

```
<head>
   @{
       var siteMeta = ViewBag.SiteMeta as SiteMeta;
   }
   @siteMeta.Title

   @foreach(var meta in siteMeta.Metas)
   {
       <meta name="@meta.Key" content="@meta.Value" />
   }
</head> 
```

Enter fullscreen mode Exit fullscreen mode

那不是很好吗！👏👏

信不信由你，即使有这么好的 C#，我们仍然有没有处理的用例。😮

如果我们想为整个站点设置类似于`og:site_name`的东西，但又想从配置中获取这个值，该怎么办？

或者，我们可能想提供一个后备图像，以防在`SiteMeta`中没有设置？

我们甚至会遇到这样的场景:一个`SiteMeta`从未被传递给`SiteMetaService.Set()`调用，这意味着当我们试图访问它的属性时，我们会在运行时得到一个`NullReferenceException`！😨

### 单一责任原则(SRP)

我们可以将生成`SiteMeta`的验证和配置放在`SiteMetaService`中，但是增加的复杂性将使测试更加困难。

相反，让我们专注于我们每个类的角色，确保遵守[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)。🤗

下面的这个类帮助我们基于上面的需求标准化我们的`SiteMeta`的值:

```
public interface ISiteMetaStandardizer
{
    SiteMeta Standardize(SiteMeta siteMeta);
}

public class SiteMetaStandardizer : ISiteMetaStandardizer
{
    private readonly Dictionary<string, string> defaultMeta;

    public SiteMetaStandardizer(string defaultSiteName, string defaultImage)
    {
        Guard.Against.NullOrWhiteSpace(defaultSiteName, nameof(defaultSiteName));
        Guard.Against.NullOrWhiteSpace(defaultImage, nameof(defaultImage));

        defaultMeta = new Dictionary<string, string>
        {
            { SiteMeta.OGImage, defaultImage },
            { SiteMeta.OGSiteName, defaultSiteName }
        };
    }

    public SiteMeta Standardize(SiteMeta siteMeta)
    {
        if (siteMeta is null)
        {
            return new SiteMeta(defaultMeta[SiteMeta.OGSiteName], defaultMeta);
        }

        foreach (var meta in defaultMeta)
        {
            if (!siteMeta.Metas.ContainsKey(meta.Key))
            {
                siteMeta.AddMeta(meta.Key, meta.Value);
            }
        }

        return siteMeta;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 IoC 容器中将这种类型的实例注册为单例，用来自 Kentico 的`SettingsKeyInfoProvider`或`AppSettings`的值来构造它。

现在我们只需要更新我们的`SiteMetaService`来使用`ISiteMetaStandardizer` :

```
public class SiteMetaService : ISiteMetaService
{
    private SiteMeta currentSiteMeta;
    private readonly standardizer;

    public SiteMetaService(ISiteMetaStandardizer standardizer)
    {
        Guard.Against.Null(standardizer, nameof(standardizer));

        this.standardizer = standardizer;
    }

    public SiteMeta Get() => standardizer.Standardize(currentSiteMeta);

    public void Set(SiteMeta siteMeta)
    {
        Guard.Against.Null(siteMeta, nameof(siteMeta));

        currentSiteMeta = siteMeta;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你一直在编码，现在你应该能够在你的一些 MVC 路线中使用`ISiteMetaService`，从你从数据库获取的 Kentico 页面中提取数据。😉

## 总结起来

这是大量的编码工作，希望对您有所帮助和启发。

我们完成了什么？

*   ✅:我们现在有了一种类型安全且经过验证的方法，可以在应用程序的任何地方设置和访问我们的`SiteMeta`值。
*   我们利用 ASP.NET MVC 的管道将我们的`SiteMeta`值应用于每个响应的`ViewBag`。
*   ✅:我们创建了集中的、可测试的类，这些类组成了一个灵活的、强大的系统。

想要增强我们在这里建立的东西吗？

你可以尝试给`SiteMeta`类添加额外的方法来设置特定的`<meta>`标签，比如`og:image`。

你可以给`SiteMetaStandardizer`添加更多的逻辑，根据当前的 url、控制器或 Kentico、ASP.NET 和 MVC 提供给我们的任何东西来设置默认值。

我还在考虑给 Kentico 的`*Info`或`PageType`类添加扩展方法，把它们变成`SiteMeta`(例如:`skuInfo.ToSiteMeta();`)

如果你有什么建议，我很想听听。也许我们可以把它变成一个开源项目，供其他 Kentico 开发者使用？！💪

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)系列。