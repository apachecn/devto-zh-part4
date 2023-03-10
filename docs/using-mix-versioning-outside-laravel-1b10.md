# 在 Laravel 外使用混合版本控制

> 原文：<https://dev.to/matthewbdaly/using-mix-versioning-outside-laravel-1b10>

Laravel Mix 是一个非常方便的前端支架，不仅仅是在 Laravel 应用程序的上下文中。去年，我将它添加到我维护的一个遗留应用程序中，取得了积极的效果，并且我正在将它包含在我正在开发的一个 CMS 中。

然而，我总是在尝试在 Laravel 应用程序之外实现版本控制时遇到问题。我过去经常使用这里描述的[时间戳技术，但是现在我大部分工作都是在 Lando 容器中完成的，我遇到了很多时间戳更改没有被发现的问题，迫使我在处理前端资产时定期重启我的容器。切换到使用混合版本似乎是解决这个问题的好方法，但是当然`mix()`助手在其他地方是不可用的。](https://matthewdaly.co.uk/blog/2016/11/26/easy-static-asset-versioning-in-php/)

幸运的是，实现自己的解决方案并不难。在这种情况下，混合版本控制的工作方式如下:

*   构建生成一个已编译静态资产的数组，键是资产的路径，值是附加了查询字符串的路径，然后将其保存为`mix-manifest.json`
*   `mix()`助手加载`mix-manifest.json`文件，将其转换为 JSON，通过路径获取数组条目，然后返回适当的值，以便从助手传递回来

考虑到这一点，我编写了下面的 Twig 过滤器来处理 Mix 版本化的资产:

```
<?php declare(strict_types=1);

namespace Project\Core\Views\Filters;

use Exception;

final class Mix
{
    public function __invoke(string $path): string
    {
        $manifest = json_decode(file_get_contents('mix-manifest.json'), true);
        if (! array_key_exists("/" . $path, $manifest)) {
            throw new Exception(
                "Unable to locate Mix file: {$path}"
            );
        }
        if (!file_exists($path)) {
            throw new Exception('Included file does not exist');
        }
        return $manifest["/" . $path];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的基础是 web 根目录设置在`public/`文件夹中，编译后的 CSS 和 Javascript 文件放在那里——如果不是这样，您可能需要相应地进行修改。

您还需要将`version()`呼叫添加到您的`webpack.mix.js` :

```
const mix = require('laravel-mix');

/*
 |--------------------------------------------------------------------------
 | Mix Asset Management
 |--------------------------------------------------------------------------
 |
 | Mix provides a clean, fluent API for defining some Webpack build steps
 | for your Laravel application. By default, we are compiling the Sass
 | file for the application as well as bundling up all the JS files.
 |
 */

mix
  .setPublicPath('public/')
  .js('resources/js/app.js', 'public/js')
  .sass('resources/sass/app.scss', 'public/css')
  .version(); 
```

Enter fullscreen mode Exit fullscreen mode

然后，当您实例化 Twig 时，您可以使用如下方式添加新的过滤器:

```
$twig = new Environment($container->get('Twig\Loader\FilesystemLoader'), $config);
$mix = $container->get('Project\Core\Views\Filters\Mix');
$twig->addFilter(new TwigFilter('mix', $mix)); 
```

Enter fullscreen mode Exit fullscreen mode

现在，过滤器应该可以在你的树枝视图中使用，如下所示:

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <link rel="stylesheet" href="{{ 'css/app.css'| mix }}" />

    {{ title }}
  </head>
  <body>
    {% include 'header.html' %}
    {% block body %}
    {% endblock %}

    <script src="{{ 'js/app.js'| mix }}"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用不同的框架或模板系统，应该有一种创建助手的方法，并且应该可以相当容易地实现这种技术。我能够在遗留 Zend 应用程序的上下文中做到这一点，因此对于其他遗留框架(如 CodeIgniter)也应该是可能的。