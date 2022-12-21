# 在 WordPress 上创建自定义作者框

> 原文：<https://dev.to/mortoray/creating-a-custom-author-box-on-wordpress-26hg>

在 WordPress 上定制作者框比我想象的更有挑战性。我检查了几个插件，并选择在主题中创建一个 PHP 函数。这里我来看看我做了什么。

# 所有插件

我在[采访中得到了我的第一位客座作者。代码](https://interview.codes/)并希望确保适当的认证。到目前为止，我的主要博客上还没有客座作者，因此我不担心作者框。通常我会把页脚信息直接放到页面内容中。这一次我想使用一个合适的作者框，以保持一致性并避免源文件中的冗余。

作者框出现在很多网站上。做一个插件搜索产生了各种各样的定制框的结果。它们大多不是我想要的，或者对我的目的来说过于复杂。

我真正想要的是为我的作者框制作一个模板并插入动态字段的能力。我正在使用[元素或](https://elementor.com/?ref=5007)，所以我已经可以创建模板了。然而，没有标准的短代码来获取作者数据。

# 作者数据短码

我仔细检查了插件，看看是否有作者提供的短代码。有几个这样做了，但是我不想要只针对那些短代码的庞大插件。另外，他们缺少文档，所以我不能确定代码是否足够。

我决定创造我自己的短代码。我试着远离 WordPress 中的低级位，但是这次看起来一些 PHP 才是正确的方法。

我跳进主题编辑器来做些改变。我使用了[子主题配置器](https://www.childthemeconfigurator.com)，它让我将我的更改隔离到子主题中。父主题保持不变。

我在`functions.php`文件中添加了以下内容。

```
function shortcode_author_data( $atts ){
    $atts = shortcode_atts(
        array(
            'field' => 'user_name',
        ), $atts, 'author_data' );
    $field = $atts['field'];
    if( $field == 'posts_url') {
        return get_author_posts_url( get_the_author_meta( 'ID' ), get_the_author_meta( 'user_nicename' ) );
    }
    return get_the_author_meta( $field );
}
add_shortcode( 'author_data', 'shortcode_author_data' ); 
```

Enter fullscreen mode Exit fullscreen mode

在我的模板中，我用下面的 HTML 片段创建了一个 Elementor“文本编辑器”组件。

```
<h4><a href="[author_data field='posts_url']">[author_data field='display_name']</a></h4>

[author_data field='description']
<a href="[author_data field='user_url']">[author_data field='user_url']</a> 
```

Enter fullscreen mode Exit fullscreen mode

我想不使用基于块的编辑器的模板也可以做到这一点，但是我对它们了解不多。我只在 Elementor 中使用过模板。

我把 HTML 和作者的简介图片结合起来，Elementor 提供了一些东西。对于[的新文章](https://interview.codes/success/soft-questions-in-a-data-analyst-interview/),这将创建一个如下所示的作者框。

[![](img/8183db8da683e47ad691f80422508d2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---4vra95W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2019/06/author-box-sample.png)

# 字段和`get_the_author_meta`

`shortcode_author_data`功能很简单。它遵循了 WordPress 文档中短代码的常见模式。

短代码带有一个`field`参数。该字段被传递给`get_the_author_meta`函数，该函数检索关于当前作者的信息。这些名字是标准的，可以在[文档](https://developer.wordpress.org/reference/functions/get_the_author_meta/)中找到。

然而，缺少的是用户帖子的链接。这可通过功能`get_author_posts_url`获得。我不想创建一个唯一的短代码，所以我重载了同一个代码。如果字段名是`posts_url`，我将使用不同的函数。

随着我的网站的发展，我会添加更多的特殊案例。任何与用户相关的内容都将添加到`author_data`短代码中。

# 下一步

由于我需要在多个网站上，我可能会考虑创建一个插件。一个插件将让我在多个地方使用相同的代码，保持主题更加整洁，让我使用一个合适的编程环境，并更好地管理更新。不过，这并不是我优先考虑的事情，所以可能需要一段时间。

目前，主题中的这个简单函数运行得非常好。