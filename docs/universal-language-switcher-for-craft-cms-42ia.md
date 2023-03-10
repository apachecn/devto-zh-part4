# Craft CMS 通用语言切换器

> 原文：<https://dev.to/piotrpog/universal-language-switcher-for-craft-cms-42ia>

这篇文章是我的 Craft CMS 模板组件库的一部分。更多信息，你可以去 craftsnippets.com

## 概述

本文假设您对 Craft CMS 多站点功能有基本的了解。要了解它，[前往文档](https://docs.craftcms.com/v3/sites.html)。

现在，让我们总结一下语言切换器的工作原理。

*   语言切换器将在网站上显示内容的替代版本的链接-链接到其他网站。将只显示与当前网站属于同一网站用户组的网站链接。
*   如果网站禁用了“此网站有自己的基本 URL”选项，它将不会出现在语言切换器中。
*   如果你是在与元素相关的页面-条目，类别，商业产品-语言切换器将显示链接到这个元素的其他版本(属于其他网站)。假设插件添加的元素有自己的 URL，它甚至可以工作。
*   在站点间循环时，语言切换器在输出正确的链接前会执行一些检查。如果当前元素没有替代版本(它不跨站点传播或不为特定站点启用)或当前页面不与任何元素相关联(因为它是通过自定义路由或模板路由[访问的)，语言切换器链接将指向另一个站点的基本 URL。](https://docs.craftcms.com/v3/routing.html)
*   如果当前网站是其网站组中的唯一网站，则不会显示语言切换器。如果没有东西可以切换，就不需要语言切换器。
*   每个语言切换器链接由语言名称以其本地形式组成(对于英语“english ”,对于德语“deutsche”等。)和国旗。由于有了[国旗图标 CSS](https://github.com/lipis/flag-icon-css) 库，国旗可以自动附加到语言链接上。
*   语言切换器有适当的[咏叹调](https://www.sitepoint.com/how-to-use-aria-effectively-with-html5/)标签，使其可访问- `aria-role`和`aria-label`。Aria 标签文本可以由静态翻译提供。
*   每个语言切换器链接都有`hreflang`参数，让谷歌爬虫识别你的网站的多语言结构。
*   代表当前浏览站点的语言切换器链接有`is-active` CSS 类。

## 语言切换器-小枝代码

这种语言切换器开箱即用。把它放在合适的地方就行了。

```
{# v2 #}
{# http://craftsnippets.com/articles/universal-language-switcher-for-craft-cms #}

{# logic #}
{% set currentElement = craft.app.urlManager.matchedElement %}
{% set sites = craft.app.getSites().getGroupById(currentSite.groupId).getSites() %}
{% set switcherLinks = [] %}

{% for site in sites if site.baseUrl is not empty %}

    {% set title = craft.app.i18n.getLocaleById(site.language).nativeName %}
    {% set url = site.getBaseUrl() %}
    {% if currentElement %}
        {% set otherLocaleElement = craft.app.getElements().getElementById(currentElement.id, currentElement.className(), site.id) %}
        {% if otherLocaleElement and otherLocaleElement.enabledForSite %}
            {% set url = otherLocaleElement.url %}
        {% endif %}
    {% endif %}

    {% set switcherLinks = switcherLinks|merge([{
        url: url, 
        title: title, 
        countryCode: site.language|split('-')|last,
        current: site.id == currentSite.id ? true : false,
        language: site.language,
    }]) %}

{% endfor %}

{# outputting html #}
{% if switcherLinks|length > 1 %}
<nav aria-label="{{'Switch language'|t}}" aria-role="navigation">
<ul>
{% for switcherLink in switcherLinks %}
    <li class="{{switcherLink.current ? 'is-active'}}">
        <a href="{{switcherLink.url}}" hreflang="{{switcherLink.language}}" lang="{{switcherLink.language}}">
        <span>{{ switcherLink.title }}</span>
        <span class="flag-icon flag-icon-{{switcherLink.countryCode}}"></span>
        </a>
    </li>
{% endfor %}
</ul>
</nav>
{% endif %} 
```

## 它是如何工作的？

让我们深入细枝代码，分析语言切换器是如何工作的。理解它的结构将允许你容易地修改它来满足你的需要。

首先，我们使用[craft . app . URL manager . matched element](https://docs.craftcms.com/api/v3/craft-web-urlmanager.html#matchedelement)获取与当前查看页面相关的元素。如果不存在这样的元素，`matchedElement`返回`false`。

然后，我们循环浏览与当前查看的站点在同一组的站点。确保没有公共网址的网站被忽略。

每个站点都有自己的**地区代码**——用`site.language`变量表示。例如，设置为美国地区的站点有`en-US`。第一部分代表语言，第二部分代表国家。我们需要从地区代码中提取出**国家代码**，以便在包含国旗的元素的 [CSS 类](https://github.com/lipis/flag-icon-css#usage)中使用。

使用[craft . app . i18n . getlocalebyid](https://docs.craftcms.com/api/v3/craft-web-twig-variables-i18n.html#method-getlocalebyid)我们获得特定站点的区域数据。我们需要它以人类可读的形式，以其本地拼写显示地区名称。

现在，是时候获取链接的 URL 了。首先，我们首先将`url`变量设置为当前`for`循环中站点的基本 URL。如果页面与元素相关联，那么我们使用 [craft.app.getElements()将`url`值覆盖为该元素的替代版本的 URL。getElementById](https://docs.craftcms.com/api/v3/craft-services-elements.html#method-getelementbyid) 。

最后，我们将所有与链接相关的数据追加到`switcherLinks`数组中。在遍历完站点后，我们可以遍历链接并将它们输出为 HTML。

## 定制语言切换器

这里有一些修改默认语言切换器的想法。

如果像“英语(美国)”这样的语言描述对您来说太显而易见，您可以通过从地区代码:
中提取语言代码，只显示地区名称的“语言”部分

```
{% set languageCode = site.language|split('-')|first %}
{% set title = craft.app.i18n.getLocaleById(languageCode).nativeName %} 
```

您也可以用本地语言在其名称旁边显示国家代码，例如:“英语(EN)”。这是显示标志的很好的替代方法:

```
{% set languageCode = site.language|split('-')|first %}
{% set nativeName = craft.app.i18n.getLocaleById(languageCode).nativeName %}
{% set title = nativeName ~ '(' ~ languageCode ~ ')' %} 
```

如果您不想在语言切换器中显示当前浏览站点的链接，您可以将其排除:

```
{% set sites = craft.app.getSites().getGroupById(currentSite.groupId).getSites()|without(currentSite) %} 
```

## 各国国旗

为了在语言切换链接中显示国家标志，您需要:

*   在你的项目中包含[旗帜图标 CSS](https://github.com/lipis/flag-icon-css) 库。
*   确保每个站点区域设置都有由国家代码和语言代码组成的区域设置代码。所以——不要只用“en”，要用“en-US”。为带有标志的元素设置正确的 CSS 类需要国家代码。

但是你真的应该为你的语言切换器使用标志吗？嗯，**看情况**。

一些国家有多种官方语言。例如，加拿大的官方语言是英语和法语。如果你只显示加拿大国旗，它不会告诉这个版本的网站实际使用的语言。

也有多个国家使用同一种语言，比如美国、英国和澳大利亚使用英语，或者多个国家使用葡萄牙语。你的内容只是翻译成特定的语言，还是为特定的国家量身定制？

每个多语言网站都需要全面考虑。我推荐阅读联合语言集团网站上的这篇文章来扩展你在这方面的知识。

## 郎属性

除了拥有合适的语言切换器，在你的网站上正确设置`lang`属性也很重要。该属性指定内容的语言，并且应该总是在`<html>`元素中指定。你可以这样做:

```
<html lang="{{ currentSite.language }}"> 
```

设置适当的`lang`属性将告诉屏幕阅读器应该如何读文本。在某些字体的情况下，它还会影响文本呈现。然而，它并没有告诉谷歌你的网站使用的是什么语言。

如果你有一些与网站其他部分不同语言的内容，你可以将`lang`属性设置为包含这些内容的元素。这就是为什么每个包含母语拼写的语言名称的语言切换器链接都有适当的`lang`属性。

## 站点切换器插件

站点切换器插件可以输出当前元素的可选版本的 URL。然而，同样的事情完全可以在本文描述的语言切换器中实现。

因此，我看不到使用这个插件的理由。

## 进一步阅读

这里有一些有用的链接，可以帮助你设计合适的语言切换器:

*   [翻译图标:传达翻译内容可用性的最佳实践](http://www.flagsarenotlanguages.com/blog/iconography-for-translations-best-practice-for-communicating-availability-of-translated-content/)
*   [设计语言开关:示例和最佳实践](https://usersnap.com/blog/design-language-switch/)
*   [语言选择器不是标记的位置](https://unitedlanguagegroup.com/blog/inside-design-a-language-selector-is-no-place-for-flags/)
*   [可访问语言选择器:a11y 遇到 i18n/l10n](http://terrillthompson.com/759)
*   [实现语言切换器的最佳方式是什么](https://medium.com/@khalidaljaaidi/whats-the-best-way-to-implement-a-language-switcher-part-1-desktop-web-a118ecd0752c)