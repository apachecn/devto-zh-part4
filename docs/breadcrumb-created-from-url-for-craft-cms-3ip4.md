# 从 URL 为 Craft CMS 创建的面包屑

> 原文：<https://dev.to/piotrpog/breadcrumb-created-from-url-for-craft-cms-3ip4>

这篇[文章](http://craftsnippets.com/articles/breadcrumb-created-from-url-for-craft-cms)最初发布在关于 Craft CMS 的博客【craftsnippets.com。去那里寻找更多与工艺相关的文章，并准备好使用模板组件。

* * *

## 组件概述

这个模板组件可以放到任何项目中——它**开箱即用**，无需任何修改。以下是它的特点总结:

*   组件是使用 [bulma 类](https://bulma.io/documentation/components/breadcrumb/)来设计的。
*   它有谷歌的结构化数据属性来改善你的搜索引擎优化。
*   它也有适当的[咏叹调](https://www.w3.org/TR/wai-aria-practices/examples/breadcrumb/index.html)属性。ARIA 代表[无障碍丰富互联网应用](https://www.sitepoint.com/how-to-use-aria-effectively-with-html5/)——这是一个标准，旨在帮助残疾人使用屏幕阅读器等工具浏览网站。
*   面包屑只有在多个链接存在的情况下才会出现，因为只有一个链接的面包屑并不真正有用。

面包屑组件本身:

```
{# v3 #}
{# http://craftsnippets.com/articles/breadcrumb-created-from-url-for-craft-cms #}

{# settings #}
{% set nonElementLinks = false %}

{# populate breadcrumbLinks array if no array of links was provided #}
{% if breadcrumbLinks is not defined %}
    {% set breadcrumbLinks = [] %}
    {# home #}
    {% set home = craft.app.getElements().getElementByUri('__home__', currentSite.id) %}
    {% set breadcrumbLinks = breadcrumbLinks|merge([{
        url: home.url ?? alias(currentSite.baseUrl),
        title: home.title ?? 'homepage'|t,
    }]) %}

    {# get elements #}
    {% set segments = craft.app.request.segments %}
    {% for segment in segments %}
            {% set uriPart = segments[0:loop.index]|join('/')|literal %}
            {% set element = craft.app.elements.getElementByUri(uriPart, currentSite.id) %}
            {% if element %}
                {% set breadcrumbLinks = breadcrumbLinks|merge([{
                    url: element.url,
                    title: element.title,
                }]) %}
            {% elseif nonElementLinks %}
                {% set breadcrumbLinks = breadcrumbLinks|merge([{
                    url: url(uriPart),
                    title: segment|t,
                }]) %}
            {% endif %}
    {% endfor %}
{% endif %}

{# render breadcrumb #}
{% if breadcrumbLinks|length > 1 %}
<nav class="breadcrumb" aria-label="{{'breadcrumbs'|t}}">
    <ul itemscope itemtype="http://schema.org/BreadcrumbList">
        {% for link in breadcrumbLinks %}
            <li class="{{loop.last ? 'is-active'}}" {{loop.last ? 'aria-current="page"' }} itemprop="itemListElement" itemscope itemtype="http://schema.org/ListItem">
                <a href="{{link.url}}" itemtype="http://schema.org/Thing" itemprop="item">
                    <span itemprop="name">{{ link.title }}</span>
                </a>
            </li>
        {% endfor %}
    </ul>
</nav>
{% endif %} 
```

## 它是如何工作的？

对于每个 URL 段，使用`getElementByUri`方法进行元素查询。该查询使用由**这个片段**和**它前面的所有片段**组成的 URI 参数。如果查询成功，返回对象的`title`作为具体的面包屑链接文本值。`getElementByUri`将检查[所有具有`URI`属性的元素](https://docs.craftcms.com/v3/extend/element-types.html)——条目、类别，甚至是插件添加的自定义元素。

第一个面包屑链接不是由 URL 段表示的——它是**主页链接**。与 URI 为空的条目相关联，通过特殊 slug `__home__`查询。该条目的`title`将被用作链接的文本值。如果不存在这样的条目，将使用通过翻译过滤器的字符串`homepage`。

## 自定义路线链接

如果`getElementByUri`方法返回 nothing，则意味着 URL 段不与条目、类别或任何其他自定义元素页面相关联。很可能它代表一些自定义的 URL 路由。在这种情况下，您可以执行以下操作之一:

*   自己建立一个链接数组，手动将其作为`breadcrumbLinks`传递给组件——就像这样:`{% include 'breadcrumb' with {breadcrumbLinks: breadcrumbLinks} %}`。如果组件收到这样的数组，它不会自己生成链接，而是使用传递给它的链接。
*   将组件开始时的变量`nonElementLinks`设置为`true`。这将导致与元素页面不相关的链接被追加到面包屑中。它们的文本值将从 URL 段中提取并通过`|t`过滤器，这样你就可以在[静态翻译](https://docs.craftcms.com/v3/static-translations.html)中设置它们。

## 面包屑与 Seomatic

如果你已经在你的网站上使用了 **[Seomatic](https://plugins.craftcms.com/seomatic)** ，你可以使用它的内置功能来获取渲染链接所需的数据。下面是为 Seomatic 调整的 breadcrumb 组件:

```
{% set breadcrumbLinks = seomatic.jsonLd.get('breadcrumbList').itemListElement %}
{% if breadcrumbLinks|length > 1 %}
<nav class="breadcrumb" aria-label="{{'breadcrumbs'|t}}">
    <ul>
        {% for link in breadcrumbLinks %}
            <li class="{{loop.last ? 'is-active'}}" {{loop.last ? 'aria-current="page"'}}>
                <a href="{{ link.item['@id'] }}">
                    <span itemprop="name">{{ link.item['name'] }}</span>
                </a>
            </li>
        {% endfor %}
    </ul>
</nav>
{% endif %} 
```

请注意，这个版本的 breadcrumb 去掉了它的结构化数据属性——Seomatic 为 breadcrumb 呈现了正确的 JSON-LD 代码，它完成了相同的工作。

Seomatic breadcrumb 也不包含与元素页面无关的链接。要了解如何将它们添加到 breadcrumb，请前往 [Seomatic docs](https://github.com/nystudio107/craft-seomatic) 并寻找“完全替换页面上现有的 bread crumb 列表”。

## 这样的 breacrumb 组件为什么有用？

Craft CMS 允许我们建立由多个部分组成的 URL 的网站。如果你的网站有许多类型的类别和部分，你的 URL 逻辑会变得相当复杂。例如，让我们考虑这样的文章页面的 URL:

`category-list/1-level-category/2-level-category/article-page`

以下是实现这种 URL 结构所需的各个部分和类别的 URL 设置:

*   文章页面部分需要有`categoryField`类别字段和这样的网址设置:`{categoryField.last.uri}/{slug}`。
*   类别有多级，所以对于类别组，URL 设置为:`{parent.uri ?? craft.entries.section('category_list').one.uri}/{slug}`。
*   类别列表是单一类型部分，所以只需要硬编码条目标题:`category-list`。

如您所见，这些 URL 设置使用了元素查询和条件操作符。我们**可以**在 Twig 的 breadcrumb 组件中重现所有这些逻辑。但是我们不需要。记住——[不要重复自己](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。

## 面包屑插件

作为本文描述的 breadcrumb 模板组件的替代，您可以使用 [Breadcrumb 插件](https://github.com/youandmedigital/craft-breadcrumb)。它的工作方式基本相同，但有几个有用的配置选项，如:

*   自定义指向主页的第一个面包屑链接的标题。
*   设置面包屑链接的最大数量
*   跳过特定片段

就像所有插件一样——在安装之前，考虑一下你是否真的需要它。请记住，每个插件都会增加你的网站的复杂性。

## 文章更新历史

*   2019 年 7 月 1 日-面包屑主页链接现在从主页 url 或通过`alias`函数传递的`currentSite.baseUrl`创建-以考虑当网站基本 url 设置为`@web`时的情况。