# 在 CraftCMS 中显示 RSS 提要

> 原文：<https://dev.to/epicosity/displaying-an-rss-feed-in-craftcms-50h9>

CraftCMS 似乎没有一种内置的方式来显示 RSS 提要，而且通常情况下，客户希望在他们的主网站上显示他们最近或相关的博客帖子。

我们为解决这个挑战做的第一件事是安装 Craft RSS 插件，由[incident 作为](https://plugins.craftcms.com/craft-rss)。

该插件的 GitHub 页面有一个如何在你的模板中显示 RSS 提要的例子，非常简单。

```
{% set blogFeed = craft.rss.loadRss(“https://helgesverre.com/blog/feed") %}
{% if blogFeed %}
<h1>{{ blogFeed.title }}</h1>
<a href=”{{ blogFeed.link }}”>{{ blogFeed.link }}</a>
<ul>
{% for post in blogFeed.item %}
<li><a href=”{{ post.link }}”>{{ post.title }} — {{ post.pubDate }}</a></li>
{% endfor %}
</ul>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

只需插入你的 RSS 源网址就可以了。

很自然，您会希望以最符合您的站点设计的方式来排列提要的结果。

您可能需要限制显示的帖子数量。为此，我们在块的开始处设置计数器= 0(下面的第 2 行)。然后，在每个“for post in blogFeed.item”循环之前，我们检查计数器是否小于我们想要显示的帖子数(第 9 行)。就在循环结束之前，将计数器加 1(第 17 行)。

```
{% set blogFeed = craft.rss.loadRss(block.linkUrl) %}
{% set counter = 0 %}
{% if blogFeed %}
<h1>{{ blogFeed.title }}</h1>
<a href=”{{ blogFeed.link }}”>{{ blogFeed.link }}</a>
<ul>
{% if counter < 3 %}
{% for post in blogFeed.item %}
<li>
<a href=”{{ post.link }}”>{{ post.title }} — {{ post.pubDate }}</a><br/>
<span>{{ post.description | raw }}</span>
</li>
{% set counter = counter + 1 %}
{% endfor %}
{% endif %}
</ul>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让它更灵活一点，你可以在 Craft 中创建一个字段来接收博客提要的 URL，可以是每个条目，也可以是全局的。

如果你真的想变得有趣，你可以创建几种不同类型的显示选项(卡片、列表等)，让用户选择他们想要如何显示信息。

ETA:我最近在给一个客户的网站添加提要时偶然看到了这篇文章。原来代码是有点不正确的，不能复制和粘贴非常恼人，所以我正在修复这些问题。您可能还需要直接查看您的 feed，以确定您可以引入哪些内容，在本例中，我们必须引入完整的描述作为原始文本，而不是 html，以便获得图像和摘录。

* * *