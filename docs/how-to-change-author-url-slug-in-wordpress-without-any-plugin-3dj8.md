# 如何在没有任何插件的情况下在 WordPress 中更改作者 URL Slug

> 原文：<https://dev.to/speedysense/how-to-change-author-url-slug-in-wordpress-without-any-plugin-3dj8>

[![Alt Text](img/3d1066273bdf09c922f1a8669e2c9d96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VrEdoSfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4rnxyuraq8ku4o499r9.png)

WordPress 默认作者 URL slug 与作者用户名相同。因此，任何人都可以知道作者的用户名。出于安全原因，你想隐藏你的真实用户名从作者的网址。在这篇文章中，我们正在讨论如何改变作者的鼻涕虫是没有任何插件的作者网址的最后一部分。

例如，我们这个网站的作者 URL 是 https://speedysense.com/author/sseditorial 的

而我们的作者 slug 是“seditorial”。我们必须改变作者 slug“speedy sense ”,使我们这个网站的新作者网址是
[http://speedysense.com/author/speedysense](http://speedysense.com/author/speedysense)

WordPress 已经有了用户名和昵称字段。用户名用于验证登录。但是，nicename 用于创建作者永久链接和帖子永久链接。当创建一个新用户时，WordPress 会自动生成一个昵称(与给定的用户名相同)。一旦用户创建，你不能改变用户名以后在管理区。因此，我们在用户配置文件屏幕中显示 nicename。

让我们看看如何在不使用任何插件的情况下将 nicename 更改为您喜欢的任何独特名称。在这里你可以阅读完整的文章以及 shotcode。