# 用于 Slack 信息收集的数据表

> 原文：<https://dev.to/nekoruri/slack-2knh>

[不仅限于莫希干 Slack](https://qiita.com/kotakanbe@github/items/32cf4eb3de1741af26fb) 等，我想很多人都像 RSS 阅读器一样使用 Slack，这是追踪特定领域信息时的笔记。

#### Foundation

Slack 在[RSS APP](https://slack.com/apps/A0F81R7U7-rss)中提供了斜线命令，因此只要注册了 APP，添加 feed 时就可以使用以下命令。

> /feed 订阅< RSS 等のURI>

#### RSS 订阅源的 URI

大致上可以追加以下内容。 < keyword >替换为目标关键字( " azure "或" javascript " )。

*   书签[http://b.hatena.ne.jp/search/tag？ q=](http://b.hatena.ne.jp/search/tag?q=) < keyword >&users=3 &mode=rss ※最低想改变总数时使用 users
*   幻灯片分享[https://www.slideshare.net/rss/tag/](https://www.slideshare.net/rss/tag/)
*   qiita[https://qiita.com/tags/](https://qiita.com/tags/)/feed

如果有人在这个领域精力充沛地发布信息，Qiita 和 Slideshare 可以通过以下 URI 进行跟踪。

*   幻灯片分享[https://www.slideshare.net/rss/user/](https://www.slideshare.net/rss/user/)
*   琪塔[https://qiita.com/](https://qiita.com/)/feed.atom

还有，我个人还在追[Developers.IO](https://dev.classmethod.jp/) 。

*   [https://dev.classmethod.jp/category/cloud/](https://dev.classmethod.jp/category/cloud/)/供稿/ とか

想追特定博客的时候，如果是最热门的博客引擎，只要打开源代码搜索“feed”、“atom”、“rss”附近，rss 订阅源的 URI 就会包含在 meta 元素中，所以会从那里拿来。