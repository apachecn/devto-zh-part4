# 如何自定义 BuddyPress 成员和组目录页面的分页链接

> 原文：<https://dev.to/upnrunnhq/how-to-customize-paginated-links-for-buddypress-members-groups-directory-pages-3bjf>

BuddyPress 使用 [paginate_links()](https://codex.wordpress.org/Function_Reference/paginate_links) 函数在成员和组目录页面上构建分页导航链接。 [paginate_links()](https://codex.wordpress.org/Function_Reference/paginate_links) 函数在目录页面上运行良好，但是我们想对输出进行一点定制。

[![](img/4de2075249da7de615ff63843429fc1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IBuTVWxL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xcxdyz1g2v31ko0udsd8.png)

我们想对 [paginate_links()](https://codex.wordpress.org/Function_Reference/paginate_links) 函数的默认输出添加以下更改。

*   我们将显示项目总数。
*   我们将添加文本输入，而不是禁用当前页面链接，这样用户可以输入任何数字来轻松导航页面。
*   我们有上一页和下一页的链接，但也会添加两个额外的链接，以方便地导航到第一页和最后一页，并删除不必要的链接。

我们开始吧。‌

首先，我们将准备我们的主函数，它将根据参数显示分页链接。我们定制了 WordPress 现有的 [paginate_links()](https://codex.wordpress.org/Function_Reference/paginate_links) 函数来创建我们自己的`my_theme_buddypress_paginate_links()`函数。‌

你可以查看 WordPress Codex 来了解更多关于 [paginate_links()](https://codex.wordpress.org/Function_Reference/paginate_links) 函数是如何工作的。‌

让我们从`my_theme_buddypress_paginate_links()`函数开始，它现在将返回空字符串，而不是分页链接。

我们将定义默认参数，这些参数将在调用函数时用您传递的参数进行解析。您可以传递一个参数数组来生成分页链接，您的参数将使用默认值进行解析。

有条件地，我们必须禁用第一个、上一个、下一个和最后一个按钮，这样用户就不能点击这些按钮来导航。让我们添加条件来更新我们的论点。

现在更新`$output`变量来显示商品总数。

我们将把分页链接保存在一个数组中，并使用`join()`函数将它们添加到输出中。

我们为每个链接添加了一个类`button`,这将阻止 BuddyPress 为我们的自定义分页链接运行默认的 JavaScript 函数。‌

现在我们有了自己的`my_theme_buddypress_paginate_links()`函数，在显示 BuddyPress 成员和组分页链接之前，可以使用它来过滤这些链接。‌

我们将过滤`bp_get_members_pagination_links`和`bp_get_groups_pagination_links`钩子。‌

下面的代码用于添加一个钩子回调函数来过滤成员的分页链接。

下面的代码用于添加一个钩子回调函数来过滤成员的分页链接。

我们快完成了！‌

除非我们添加一些 Javascript 来发送 Ajax 请求以更新结果，否则分页链接将无法正常工作。‌

让我们用 WordPress [wp_footer](https://developer.wordpress.org/reference/functions/wp_footer/) hook 在你的网站页脚添加内联 Javascript。

上面的 Javascript 会在你点击分页链接的时候监听 onclick JavaScript 事件，发送 Ajax filter 请求显示当前页面的更新结果。‌

让我们用下面的代码更新`my_theme_footer_scripts`回调函数。

当您在当前页面的输入字段中输入新值并发送 Ajax filter 请求以显示当前页面的更新结果时，上面的 Javascript 将侦听 onchange JavaScript 事件。‌

完整的源代码可以在 GitHub 上下载，作为一个 WordPress 插件，你可以很容易地下载或克隆这个库。‌

尽情享受吧！

帖子[如何为 BuddyPress 成员&群组目录页面](https://upnrunn.com/blog/2019/06/how-to-customize-paginated-links-for-buddypress-directory-pages/)定制分页链接最早出现在 [upnrunn | Build &部署强大应用](https://upnrunn.com)上。