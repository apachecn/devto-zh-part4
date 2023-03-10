# Kentico CMS 快速提示:带有分面文档类别的 Azure 搜索

> 原文：<https://dev.to/wiredviews/kentico-cms-quick-tip-azure-search-with-faceted-document-categories-5697>

<figure>

[![](img/2f14c5a44e9e51e2e9e3e5926cc5b8ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--unpdf9A9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3nqvvw3197p3bbu7t1xl.jpg)

<figcaption>Photo by [Anthony Martino](https://unsplash.com/@amartino20) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

## 肯蒂科💕Azure 搜索

Kentico 在很长一段时间内就已经包含了自定义搜索索引，并且传统上通过 Lucene.NET 的[提供这一功能。](https://lucenenet.apache.org/)

本地(进程内)托管的搜索索引[很容易启用](https://docs.kentico.com/k12sp/configuring-kentico/setting-up-search-on-your-website/using-locally-stored-search-indexes)，并且可以很好地与 CMS 一起扩展，但是有一些警告。😯

> 注意:这些警告可能不适用于你。如果他们没有，Kentico 内置的本地智能搜索是一个很好的解决方案。

*   由于高流量，❌扩展搜索索引和查询需要扩展整个 CMS。
*   不支持❌更复杂的搜索功能，如[分面导航](https://docs.microsoft.com/en-us/azure/search/search-faceted-navigation)。
*   像[认知搜索](https://docs.microsoft.com/en-us/azure/search/cognitive-search-concept-intro)和[自动完成](https://docs.microsoft.com/en-us/azure/search/tutorial-csharp-type-ahead-and-suggestions)这样的❌高级功能并不现实。

幸运的是，Kentico 还可以开箱即用地深度集成到 [Azure Search](https://docs.microsoft.com/en-us/azure/search/search-what-is-azure-search) 中。👍

它支持简单的用例[而不需要任何定制代码](https://docs.kentico.com/k12sp/configuring-kentico/setting-up-search-on-your-website/using-azure-search/customizing-azure-search)，就像本地智能搜索一样。

更复杂的用例可以通过挂钩到[搜索索引创建过程](https://docs.kentico.com/k12sp/configuring-kentico/setting-up-search-on-your-website/using-azure-search/customizing-azure-search)来解决。

由于 Azure Search 是作为企业级搜索解决方案存在的，开发人员可以使用[微软直接利用它的全套查询选项。Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search) 直接获取包或者 OData 查询 API。🙂

## 刻面

Faceting 是一个很棒的功能，可以帮助你的网站用户找到他们想要的东西，或者以一种更直观的方式探索你的内容。

引用 Azure 搜索文档:

> 刻面可以帮助你找到你正在寻找的东西，同时确保你不会得到零结果。[...]在在线零售应用程序中，分面导航通常建立在品牌、部门(童鞋)、大小、价格、受欢迎程度和评级之上。

如果我们看看[HomeDepot.com](https://www.homedepot.com/),我们可以看到刻面在起作用:

[![](img/394345e7d237c9bcac7933f06b5fbb1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RRcspKCr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b8x10dl47kliiyfe89ds.jpg)

在这个列表中可以看到页面上落入每个可用分类的结果的数量。🤔

在 Kentico 中，将内容分组到一组分类法中的直观方法是使用[类别](https://docs.kentico.com/k12sp/managing-website-content/working-with-pages/categorizing-pages/assigning-pages-to-categories)。

对于一个文档的所有类别，Kentico 在搜索索引中分别以`documentcategories`和`documentcategoryids`字段的形式包含了`CategoryInfo.CategoryDisplayName`和`CategoryInfo.CategoryID`值。

但是，默认情况下，这些字段不是 facetable。😞

我们想要的是让 Kentico 在我们的 Azure 搜索实例中生成的搜索索引成为一个或另一个 facetable。在下面的例子中，我将使用`documentcategories`字段。

当我们在 Azure 门户中查看我们的索引字段时，下面是我们应该得到的结果:

[![](img/1a745a4e154b04f49457bb3229fd4496.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--czDhkBOs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l3v0t4qej5n9gy6ji2gu.jpg)

## 自定义 Azure 搜索模块

Kentico 端的定制实际上非常简单——我们只需要创建一个定制的`Module`,并利用 Kentico 公开的 Azure 搜索索引创建事件。🧐

```
 public class CustomAzureSearchModule : Module
    {
        public const string AZURE_SEARCH_INDEX = "put-your-index-code-name-here";

        public CustomAzureSearchModule() : base(nameof(CustomAzureSearchModule)) { }

        protected override void OnInit()
        {
            base.OnInit();

            DocumentFieldCreator.Instance.CreatingField.After += CreatingField_After;
        }

        private void CreatingField_After(object sender, CreateFieldEventArgs e)
        {
            // If you have multiple indexes and only want category faceting on one
            if (!e.SearchIndex.IndexCodeName.Equals(AZURE_SEARCH_INDEX, StringComparison.OrdinalIgnoreCase))
            {
                return;
            }

            var field = e.Field;

            if (field.Name.Equals("DocumentCategories", StringComparison.OrdinalIgnoreCase))
            {
                field.IsFacetable = true;
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们在这里做什么？

首先，我们创建一个定制的`Module`，Kentico 通过反射获取它，并在应用程序启动时执行。

我们为`DocumentFieldCreator.Instance.CreatingField.After`创建了一个事件处理程序，这样我们就可以在 Kentico 为一个字段设置了默认值之后为该字段定制搜索索引配置。

最后，我们匹配添加到搜索索引中的特定字段- `DocumentCategories` -并将该字段的`IsFacetable`属性设置为`true`。

将这个定制添加到您的站点后，确保您在 CMS“智能搜索”模块中重新构建了 Azure 搜索索引。

别紧张，皮斯。😎

## 示例:文档类别刻面

`DocumentCategories`字段现在是 facetable，这意味着当我们查询 Azure Search API 的搜索结果时，它将返回特定结果的每个类别的方面计数。

假设我们有(3)个被分类为“酷”、“有趣”和“土豆”的文档(也许我们应该和内容编辑谈谈🤣).

1.  “酷”、“好玩”、“土豆”
2.  “好玩”
3.  “好玩”、“土豆”

默认搜索(不应用过滤)应该返回所有文档和以下方面计数:

*   “酷”(1)
*   “好玩”(3)
*   “土豆”(2)

如果您想测试您的搜索索引配置，请转到 Azure 搜索实例的 Azure Portal blade，选择正确的索引，并在“搜索浏览器”选项卡的“查询字符串”字段中键入以下内容:

`facet=documentcategories&$filter=documentcategories/any(dc: dc eq 'Potato')`

这将返回(2)个结果，文档 1 和 3。👌

## 从 Kentico 12 MVC 查询

要将这种分面集成到您的 Kentico 12 MVC 站点中，您需要做更多的编码工作。在 Kentico 文档中有一个非常完整的例子[，里面有大量的样本代码。👏](https://docs.kentico.com/k12sp/configuring-kentico/setting-up-search-on-your-website/using-azure-search/integrating-azure-search-into-mvc-projects)

然而，这个例子不包括与集合的分面。

> Azure 搜索索引中的所有字段都有特定的数据类型。`documentcategories`字段的类型为`Collection(Edm.String)`,它不同于 Kentico docs 示例中查询的字段。

为了查询一个由值组成的集合的 facetable 字段，您需要将一个稍微高级一点的查询字符串传递给您的`Microsoft.Azure.Search.Models.SearchParameters`实例。

> 提示:这是上面用于测试 Azure“搜索浏览器”的那个。😁

下面是一个如何生成这个查询字符串的例子:

```
public class FacetFilterViewModel
{
    public string Name { get; set; }

    public string Value { get; set; }

    public bool IsSelected { get; set; }

    public FacetFilterViewModel()
    {
        Name = "";
        Value = "";
        IsSelected = false;
    }
}

// In a separate class

public const string FACET_CATEGORIES = "documentcategories";

public SearchParameters BuildSearchParameters(IEnumerable<FacetFilterViewModel> facetFilters)
{
    var categoryFilters = facetFilters
        .Where(x => x.IsSelected)
        .Select(item => $"{FACET_CATEGORIES}/any(dc: dc eq '{item.Value.Replace("'", "''")}')");

    string categoryFilterTerm = string.Join(" or ", categoryFilters);

    return new SearchParameters
    {
        Filter = categoryFilterTerm,
        Facets = new List<string>
        {
            FACET_CATEGORIES
        }
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

这些`SearchParameters`可以传递给对`ISearchIndexClient`(在 Kentico docs 代码示例中找到)的调用，如下所示:

```
// These come from the form submission, which is defined in a Razor View

// IEnumerable<FacetFilterViewModel> facetFilters

// string searchTerm

var searchParameters = BuildSearchParameters(facetFilters);

var result = await client.Documents.SearchAsync(searchTerm, searchParameters);

// See the Kentico docs example code on how to use "result" 
```

Enter fullscreen mode Exit fullscreen mode

这应该给你分面的分类结果，可以在一个 Razor 视图中呈现出来，很像上面家得宝的截图(设计由你决定！).🤓

## 总结

回顾过去，我们可以看到我们现在知道了以下内容:

1.  Kentico 支持本地搜索索引和 Azure 搜索索引。
2.  Azure Search 提供了更高级的扩展和功能。
3.  在 Kentico 中集成 Azure Search 非常简单。
4.  Faceting 是一个很好的功能，可以帮助网站访问者搜索你的内容。
5.  默认情况下，文档类别不是 facetable，但这是可以定制的。
6.  在 Kentico 中定制 Azure 搜索是通过索引事件来完成的。
7.  要呈现集合字段的分面搜索结果，您需要生成一个更复杂的查询字符串。

这篇文章旨在为集成 Kentico 和 Azure Search 提供一个有帮助的演练——特别是当应用于文档类别时的 faceting 特性。

感谢阅读！🙏

* * *

我希望你喜欢这个快速提示。我计划在这个系列中发布[更多的技巧。](https://dev.to/search?q=Kentico%20Quick%20Tips)

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%20-%20Design%20Patterns)系列。