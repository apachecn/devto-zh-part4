# 差分输入 WP _ 注册 _ 脚本 y wp _ 入队 _ 脚本

> 原文：<https://dev.to/juananruiz/diferencia-entre-wpregisterscript-y-wpenqueuescript-4o4>

条目[WP _ register _ script 与 wp_enqueue_script](https://kungfupress.com/diferencia-entre-wp_register_script-y-wp_enqueue_script/) 之间的区别首先由[功夫出版社](https://kungfupress.com/author/kungfupress/)刊登在 [KungFuPress](https://kungfupress.com) 上

我经常想，在`wp_enqueue_script()`之前应该什么时候使用`wp_register_script()`，以及第一次是否真的有用。

如果每次加载页面时都需要加载 javascript 文件，则可以省去调用“`wp_register_script()`”的麻烦，而只使用“`wp_enqueue_script()`”。

如果只使用`wp_register_script()`脚本将不会加载。正如他自己的名字所表明的那样，这个功能所做的唯一事情就是记录脚本，说你想按什么顺序加载它，如果它有任何依赖关系，但要真正加载脚本就需要`wp_enqueue_script()`。

## 那么我要用 wp_register_script 吗？

好吧，假设您使用短代码来显示表单、透视表或需要调用 JavaScript 文件的任何其它内容。

仅当当前页面或条目使用了短代码时，才应加载此脚本。问题是，加载短代码时调用脚本已为时过晚，因为标头已加载。

许多开发人员所做的最终工作是随插件一起加载脚本，但随后总是加载脚本，而不仅仅是在使用短代码时加载脚本。为了解决这个问题，有功能`wp_register_script()`。

使用此函数时，所有参数都可以直接传递给它，然后调用`wp_enqueue_scripts()`时，只需传递脚本中的参数即可。

下面我给你举一个例子，说明你应该如何正确加载脚本:

```
<?php
// Cuando WP cargue la cabecera llama a este hook y tu script quedará registrado

add_action( 'wp_enqueue_scripts', 'kfp_Idea_List_scripts' );

function Kfp_Idea_List_scripts(){ 
  wp_register_script(
    'kfp-vote-link', 
    plugins_url('../js/vote-link.js', __FILE__), 
    array('jquery')
  );
}
add_shortcode('kfp_idea_list', 'Kfp_Idea_list');

function Kfp_Idea_list(){ 
  // El script sólo se carga si se utiliza el shortcode 
  wp_enqueue_script('kfp-vote-link’); 
  /* El shortcode hace lo que tenga que hacer */
} 
```

如果你想在你的上下文中看到这个代码访问[【https://github . com/kungfupress/kfp-vota-ideas】](https://github.com/kungfupress/kfp-vota-ideas)

## 如果我必须使用 wp_localize_script？

同样的情况也适用。在最简单的情况下，可以使用`wp_enqueue_script()`然后`wp_localize_script()`将参数传递给脚本。但始终按此顺序进行，因为在`wp_localize_script()`中使用的脚本参数或标识符将此情况定义为`wp_enqueue_script()`。

使用`wp_register_script()`的话，可以马上打电话给`wp_localize_script()`，打电话给`wp_enqueue_script()`时已经准备好了

## Cuál es la differencia entre WP _ register _ style y WP _ enqueue _ style？

因为它与介于`wp_register_script()`和`wp_enqueue_script()`之间的相同。以上我告诉你的一切都适用于这两个。

顺便提醒一下，对他们俩来说≤总是一样的:`'wp_enqueue_scripts'`。不存在〔t1〕

## 把我卷入这一切麻烦，而不仅仅是用 wp_enqueue？

性能和资源优化。您应该尽量加载所需的最少脚本和样式表，但由于 WordPress 的加载顺序，有时您可能不知道需要加载脚本或样式表，直到页面加载过程非常高级。

因此，在这种情况下，必须先用`wp_register_script()`或`wp_register_style()`注册它们

## 【我可以强迫我的剧本上传到页脚而不是头部吗？

这是`wp_register_script()`和`wp_enqueue_script()`功能的选项之一。必须将函数接收的最后一个参数设置为 true。

`wp_register_script( string $handle, string|bool $src, array $deps = array(), string|bool|null $ver = false, bool $in_footer = false )`

顺便说一下，WordPress 代码中有一个“[”完整列表，列出了默认情况下使用 WordPress](https://developer.wordpress.org/themes/basics/including-css-javascript/#default-scripts-included-and-registered-by-wordpress) 加载的所有脚本。值得一看。

## 参考文献

*   [WP _ enqueue _ script()vs WP _ register _ script()](https://jhtechservices.com/wordpress-wp_enqueue_script-vs-wp_register_script/)–JH 技术服务公司
*   WordPress 主题手册:包含 CSS & JavaScript-[https://developer . WordPress . org/themes/basics/Including-CSS-JavaScript/](https://developer.wordpress.org/themes/basics/including-css-javascript/)
*   [Codex:函数 wp_register_script](https://developer.wordpress.org/reference/functions/wp_register_script/)
*   [Codex:函数 wp_enqueue_script](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)
*   [Codex:函数 wp_localize_script](https://developer.wordpress.org/reference/functions/wp_localize_script/)

## 相关条目

*   [是 is_single()和 is_singular()之间的区别吗？](https://dev.to/juananruiz/cual-es-la-diferencia-entre-issingle-e-issingular-5gjk)

条目[WP _ register _ script 与 wp_enqueue_script](https://kungfupress.com/diferencia-entre-wp_register_script-y-wp_enqueue_script/) 之间的区别首先由[功夫出版社](https://kungfupress.com/author/kungfupress/)刊登在 [KungFuPress](https://kungfupress.com) 上