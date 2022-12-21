# 关于 Google Blogger 的绝妙技巧

> 原文：<https://dev.to/jefferyyuancn/awesome-tips-about-google-blogger-57d>

### TL；速度三角形定位法(dead reckoning)

*   如何高效使用 Google Blogger？
*   [更改日期和时间以提升/移动帖子。](#change_date_time)
*   有效管理/使用标签。
*   关于 SEO 和推广帖子的技巧。

* * *

### [关于谷歌产品的牛逼提示系列](https://lifelongprogrammer.blogspot.com/search/label/Google_Series)

* * *

### [关于博主的牛逼小贴士系列](https://lifelongprogrammer.blogspot.com/search/label/Blogger_Series)

* * *

### 更改日期和时间以提升/移动帖子

Chrome 使用`published data`来决定帖子在`Blog Archive`或 feed 中显示的位置，我们可以在发布后更改`published data`来移动帖子。

*   将发布日期设置为将来时:
    *   更新现有的:这不会改变 url 中的日期和时间，但会改变它在`Blog Archive`的位置
    *   发布一个新的或再次:这将安排文章在未来发布
*   我们可以将旧帖子更改为较新的日期，以移动和推广它
    *   例:[开发者键盘快捷键](https://lifelongprogrammer.blogspot.com/2018/05/keyboard-shortcuts-for-developers.html)从 2018/05 移动到 2019/04
*   我们可以将类似的帖子放在一起，或者将它们分配到不同的月份/年份。

#### 给过去添加一个帖子

*   首先发布帖子(使用自动时间)
*   将其恢复为草稿
*   将日期时间更改为过去，然后再次发布
*   例:[关于 Chrome Vimium 的精彩提示](https://lifelongprogrammer.blogspot.com/2019/04/awesome-tips-about-chrome-vimium.html)于 2019 年 5 月 30 日添加，并将其日期时间更改为`April 08, 2019`

* * *

### 有效管理/使用标签

*   [多标签搜索](https://exde601e.blogspot.com/2012/12/search-operators-for-Blogger-labels.html)
    *   并查询:[/搜索/标签/开发提示+Chrome](https://lifelongprogrammer.blogspot.com/search/label/Dev%20Tips+Chrome)
    *   或者查询: [/search/？q=label:“问题解决”|label:故障排除](https://lifelongprogrammer.blogspot.com/search/?q=label:)
    *   在标签中搜索关键字:添加 q=%s
    *   [/搜索/标签/开发提示+Chrome？q =降价](https://lifelongprogrammer.blogspot.com/search/label/Dev%20Tips+Chrome?q=Markdown)
*   [为现有帖子添加更多标签](http://9jaonpointed.blogspot.com/2015/06/how-to-add-more-labels-to-existing-post.html)
*   [批量移除标签](https://www.makingdifferent.com/how-to-change-remove-labels-at-once-in-blogger/)
    *   [在标签动作](https://www.youtube.com/watch?v=PfNJRTadzMI)中点击要移除的标签即可
*   [隐藏标签计数< X 来自标签小工具](https://lifelongprogrammer.blogspot.com/2014/05/blogger-hide-labels-of-small-amount-of-posts.html)

```
<b:loop values='data:labels' var='label'>
  <b:if cond='data:label.count &gt;= 10'>
    <!-- ....  -->
  </b:if>
</b:loop> 
```

*   排除`Configure Labels`处的标签

#### [博主供稿 API](https://www.exeideas.com/2016/02/parameters-in-blogspot-feed.html)

*   feeds/posts/default/feeds/posts/default/-/THE _ LABLE
*   max-results:要检索的最大结果数
*   排序依据:已更新，已发布
*   [在 Blogger 订阅源中搜索多个标签(和)](http://waltz.blogspot.com/2007/07/search-multiple-labels-in-blogger-feeds.html)
    *   并查询: [feeds/posts/default/-///.../](https://lifelongprogrammer.blogspot.com/feeds/posts/default/-/Dev%20Tips/Chrome?orderby=updated)
    *   或者查询: [/feeds/posts/default？q =标签:标签 1 |标签:标签 2](https://lifelongprogrammer.blogspot.com/feeds/posts/default?q=label:Chrome%7Clabel:Markdown)

* * *

### 关于 SEO 和推广帖子的小技巧

*   [在博客标题前显示文章标题](https://masterblogging.com/post-title-before-blog-title/)
*   [添加`search description`](https://xomisse.com/blog/enable-search-description-blogger)
    *   您必须向框中添加内容，如果它是空的或者您将来删除了描述，则`search description`功能将会消失/
    *   [它出现在谷歌搜索中，或者当我们分享到 Linkedin、脸书、Twitter 时](https://www.techprevue.com/post-search-description-blogger-seo/)
*   自定义位于`Links -> Permalink`的 url，使其更具可读性

#### 更改了网址后添加`Custom Redirects`

*   例如:[/2018/06/chrome-tips-and-tricks . html](https://lifelongprogrammer.blogspot.com/2018/06/chrome-tips-and-tricks.html)会被重定向到[/2018/06/awesome-tips-about-chrome . html](https://lifelongprogrammer.blogspot.com/2018/06/awesome-tips-about-chrome.html)

#### [创建自定义的“未找到的页面”](https://lifelongprogrammer.blogspot.com/2019/05/how-to-promote-your-content-by-creating-sticking-index-page-in-blogger.html#page_not_found)

*   例:[不适用](https://lifelongprogrammer.blogspot.com/na)

#### [通过创建和粘贴索引页来推广你的内容](https://lifelongprogrammer.blogspot.com/2019/05/how-to-promote-your-content-by-creating-sticking-index-page-in-blogger.html)

*   首先发布文章，然后将日期更改为将来的日期
*   [首页](https://lifelongprogrammer.blogspot.com/2019/05/home.html)

#### [如何根据标签和新近度推广帖子](https://lifelongprogrammer.blogspot.com/2019/03/how-to-promote-posts-based-on-label-and-recency-in-blogger.html)

* * *

### 提示

*   [删除自动添加的代码:Dir="Ltr" Trbidi="On"](https://www.howbloggerz.com/2016/07/remove-ltr-trbidi-code-from-blogger-posts.html)
    *   通过关闭**语言和格式设置**中的“启用音译”来禁用此功能
    *   如果“启用音译”处于打开状态，blogger 将在检测到没有此代码时自动添加此代码
    *   如果你的文章中有定制的 javascript 代码，这种行为会破坏 js 代码。
*   [隐藏或删除 Blogger 属性小工具](https://ultimatebloggerguide.blogspot.com/2016/06/remove-powered-by-blogger-attribution-widget.html)
*   [在新标签页中自动打开外部链接](https://yihui.name/en/2018/09/target-blank/)
*   [向章节标题添加锚定链接](https://yihui.name/en/2018/11/md-js-tricks/)
*   [创建一个列出所有帖子的页面](https://www.toptip.ca/2012/03/blogger-retrieve-list-of-all-posts.html)
*   [左/右键转到旧/新帖子](http://theblogtrickz.blogspot.com/2015/03/navigate-through-blogger-posts-using.html)
    *   使用一个小部件来添加 javascript，而不是直接更新模板:这更简单，即使更改模板也仍然有效。

#### 网站地图网页

*   [生成包含所有标签的网站地图 html 页面](https://www.howbloggerz.com/2017/03/generate-html-sitemap-page-blogger.html)
*   [生成包含(多个)特定标签的网站地图 html 页面](https://mybloggeraide.blogspot.com/2017/07/create-html-sitemap-for-specific-label.html)

### [搜索控制台](//search.google.com/search-console)

*   通过手动请求索引，将您的帖子添加到[搜索控制台](//search.google.com/search-console)
*   检查`Coverage -> Excluded`并请求`Discovered - currently not indexed`和`Crawled - currently not indexed`的索引柱