# 绕过 Twitter 的 t.co 域名

> 原文：<https://dev.to/n8chz/bypassing-twitter-s-t-co-domain-4ccn>

我通过在 dev.to b/c twitter 上搜索“Twitter”找到了[这篇文章](https://dev.to/paulleclercq/twitter-without-short-links--1cig)。现在是 2019 年，有了一个“新”的 twitter。我想对这个帖子发表评论，但这是一个旧帖子，我不确定关于向旧瓶子里添加新酒的网络礼仪，所以我决定发表一个帖子而不是评论。这篇文章是我在 dev.to 上的第一篇文章，所以请考虑温和一点。

然而，twitter 的链接包装政治似乎基本没有改变。我也是一个扩展开发者(在我的例子中是 FF，但我至少尝试将我的移植到 Chromium)。我基本上也将 URL 从“title”属性移动到“href”属性，用于内联外部链接([参见 repo](https://gitlab.com/n8chz/twitter-usability/blob/master/content.js#L117) 中的代码):

```
$("a[title^='http']").each(function () {
  $(this).attr("href", $(this).attr("title"));
  $(this).removeAttr("title");
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是也有在盒子里嵌入图片的推文，链接到网络上的一些文章。对于这些，在任何类似嵌套玩偶的 Twitter 元素中都没有提供未包装的链接(我检查过)。因此，我通过使用链接文章标题中的单词和托管网站的域名(这些细节由 Twitter 提供)，强行对这些链接进行了 d-t.co-ization，如下所示:

```
$(".r-1mi0q7o").each(function () {
  if ($(this).text() != "") { // kludge
    let title = $(this).children().first().text();
    let query = title.split(/\W+/).join(" ").trim().replace(/ /g, "+");
    let tld = $(this).children().last().text();
    let site = encodeURIComponent(tld);
    let url = `https://duckduckgo.com/?q=${query}+site%3A${site}`;
    $(this).closest("a").attr("href", url);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

类名为`r-1mi0q7o`的元素(如有更改，恕不另行通知，我很确定)有三个顶级子元素，它们的`textContent`分别是文章标题、文章示例文本和文章域(无路径)。这个 jQuery 表达式相当暴力，让我不得不跳过单击搜索结果上的链接这一中间环节，但从好的方面来看，我发现遗漏的情况非常罕见，而且通常情况下，所讨论的文章是搜索结果中的第一篇。

我的浏览器扩展名为 Twitter 可用性套件，可在 https://addons . Mozilla . org/en-US/Firefox/addon/Twitter-Usability-Suite/获得。

我还没有成功地在 Chromium 上运行它。这两种浏览器似乎在内容脚本的附件或页面加载事件的触发方面有所不同，但我不完全确定问题出在哪里。内容脚本似乎在它寻找的元素到达之前就已经运行了。