# 基本 API -更新

> 原文：<https://dev.to/gdotdesign/base-api-updates-24p0>

自从一个月前[基础 API](https://www.base-api.io) 发布以来，我把它贴在了[产品搜索](https://www.producthunt.com/posts/base-api)(当天的#2 产品)和[黑客新闻](https://news.ycombinator.com/item?id=20797828)(拿到了头版)上，得到了很多不错的反馈。

最近几周，我增加了一些新功能:

### 为用户自定义数据

这是一个被高度要求的特性，现在您可以在`custom_data`字段中以 JSON 格式存储用户的任意数据。

### 邮件列表

您现在可以从界面创建[邮件列表](https://www.base-api.io/reference/mailing-lists)，并可以使用它的`id`来:

*   **订阅**封邮件
*   **取消订阅**封电子邮件
*   向所有订户发送相同的电子邮件

API 很简单，所有当前的客户端和示例都已更新，您可以在 [Github 组织](https://github.com/base-api-io)上的存储库中查看。

* * *

如果你对 Base 的开发感兴趣，那就去看看[开发板](https://trello.com/b/PyPW6uwG/base-features)吧，我会在那里记录特性和它们的状态。

如果你有任何问题或想法，请在评论中告诉我，或者直接给我发信息。