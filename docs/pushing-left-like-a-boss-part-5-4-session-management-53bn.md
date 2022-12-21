# 像老板一样向左推—第 5.4 部分—会话管理

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-4-session-management-53bn>

### [这个系列，还有我的博客，都感动了！](https://wehackpurple.com/pushing-left-like-a-boss-part-5-4-session-management/)

我坚定地认为，只有您的框架中的会话管理特性才应该用于管理身份或用户会话。HTTP 1.1 协议从来不是为管理这些概念而设计的，因此没有内置的默认方式来做到这一点。当你选择一个框架，比如[。Net](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2&WT.mc_id=devto-blog-tajanca) 、Ruby 或者 Spring，它们都内置了处理这个的特性，你应该一直使用那些特性。不要试图认为你自己可以做得更好，让专家来为你处理。

[![As an ex-dev, I know the temptation to write your own. Trust me, don’t.](img/ae1939a702749ef0e8d2963a0b332208.png "As an ex-dev, I know the temptation to write your own. Trust me, don’t.")](https://res.cloudinary.com/practicaldev/image/fetch/s--jkiju07J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/11ucokpbxb4yifg4begh.jpeg) 作为一名前开发人员，*我知道自己写*的诱惑。相信我，不要。

这一部分的大部分信息都是从劳尔·西莱斯写的 OWASP 备忘单——会话管理中学来的。要更深入地了解这个话题，请查看。

以下是会话管理的一般指南。同样，请始终使用框架中的特性，否则请使用下面的建议...

### [在我的新博客上阅读这篇文章的其余部分！](https://wehackpurple.com/pushing-left-like-a-boss-part-5-4-session-management/)