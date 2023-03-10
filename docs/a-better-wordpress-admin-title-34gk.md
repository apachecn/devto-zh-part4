# 一个更好的 WordPress 管理标题。

> 原文：<https://dev.to/adampatterson/a-better-wordpress-admin-title-34gk>

目前，我有 5 个职位开放编辑。看着他们，我只看到“**编辑帖子<亚当·帕特森- WordPress** ”。

当然，5 个标签并不多，也不难分类，但我开始想知道为什么 WordPress 不简单地显示页面和文章的标题。

幸运的是，这并不难。

首先，我们需要创建一个新的过滤器，在生成管理页面标题时将调用该过滤器。这个滤镜叫做`admin_title`。

接下来，我们将从全局范围`$post, $title, $action, $current_screen`中取出一些变量。

之后，我们检查我们在管理页面上，动作是编辑，而不是添加。剩下要做的就是确保我们处于编辑文章或页面的环境中。我认为你可以在这里做一些实验，但这正好符合我的需要。

最后，构建适合你的页面标题。

我最后选择了“**编辑帖子<帖子名称——亚当·帕特森**”或者“**编辑页面<帖子名称——亚当·帕特森**”。

我想纯粹主义者可能想在标题中保留 WordPress，但是它太长了，我看不出来，所以没有它我也很好。

只需将下面的代码添加到你的主题文件`functions.php`中，你就万事俱备了！

```
add_filter('admin_title', 'better_admin_title', 10, 2);

function better_admin_title($admin_title, $title)
{
    global $post, $title, $action, $current_screen;
    if (is_admin() and $action == 'edit' and in_array($current_screen->id, ['post', 'page'])) {
        return $title . ' ‹ ' . $post->post_title . ' — ' . get_bloginfo('name');
    }

    return $admin_title;
} 
```

阅读更多关于[管理职位](https://developer.wordpress.org/reference/hooks/admin_title/)的信息