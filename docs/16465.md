# ASP.NET 核心中 HTML 编码标签助手

> 原文：<https://dev.to/sebnilsson/html-encode-taghelper-in-asp-net-core-51gp>

对于最近的一个特定场景，我想在 ASP.NET 核心中显示一个[标记助手](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro)的 HTML 编码输出。所以我想使用 TagHelper，但不输出它的实际结果，而是看到了包含在我的模板中的**原始 HTML** 。

[![HTML](img/ab862f4a885f91615615c62d78106329.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lNppC5TL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.sebnilsson.com/wimg/html-encode-aspnet-core/html.jpg)

所以我创建了另一个 TagHelper，它允许我**在 ASP.NET 核心和其他 TagHelper**中包装任何 HTML、内联代码，并让 tag helper 的标签内的所有内容都被 HTML 编码，就像这样:

```
<html-encode>
    <a href="@Url.Action("Index")">Read More</a>
    @Html.TextBox("No_Longer_Recommended-TagHelpers_Preferred")
    <my-other-tag-helper />
</html-encode> 
```

由此，我将获得带有 UrlHelper-result 的链接的原始 HTML，HTML-helper 的结果和我的另一个 TagHelper 的结果。

`html-encode` -TagHelper 的源代码如下:

```
[HtmlTargetElement("html-encode", TagStructure = TagStructure.NormalOrSelfClosing)]
public class HtmlEncodeTagHelper : TagHelper
{
    public override async Task ProcessAsync(TagHelperContext context, TagHelperOutput output)
    {
        var childContent = output.Content.IsModified
            ? output.Content.GetContent()
            : (await output.GetChildContentAsync()).GetContent();

        string encodedChildContent = WebUtility.HtmlEncode(childContent ?? string.Empty);

        output.TagName = null;
        output.Content.SetHtmlContent(encodedChildContent);
    }
} 
```