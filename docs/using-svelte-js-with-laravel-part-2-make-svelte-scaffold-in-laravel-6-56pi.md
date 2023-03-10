# 在 Laravel 中使用 Svelte.js 第 2 部分:在 Laravel 6 中制作苗条的支架

> 原文：<https://dev.to/shuv1824/using-svelte-js-with-laravel-part-2-make-svelte-scaffold-in-laravel-6-56pi>

Svelte 是构建用户界面的一种全新方法。在我的上一篇文章中，我使用了一个名为 laravel-svelte-preset 的包来在 Laravel 5.8 中获得苗条的脚手架。但是这个包还不能兼容新的 Laravel 6。在这篇文章中，我们将在 Laravel 6 中手工制作一个苗条的脚手架。为此我们将重新开始。

首先安装一个新鲜的 Laravel 6 项目。

```
$ composer create-project --prefer-dist laravel/laravel laravelt 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，移动到项目目录。

```
$ cd laravelt 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须安装一个`npm`包。这个包也是来自 [WeWowWeb](https://github.com/wewowweb) 。这个包叫做。这将简单地增加 Laravel Mix 编译苗条代码的能力。

```
$ npm install wewowweb/laravel-mix-svelte 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，打开你的`webpack.mix.js`文件，如下图所示。

```
// webpack.mix.js
const mix = require('laravel-mix');

require('laravel-mix-svelte');

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

mix.js('resources/js/app.js', 'public/js')
    .sass('resources/sass/app.scss', 'public/css')
    .svelte(); 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，安装以下依赖项。

```
$ npm install --save jquery popper.js bootstrap svelte 
```

Enter fullscreen mode Exit fullscreen mode

现在要手动做一些文件，自己一点点编码。在你的`resources/js`目录中创建一个名为`components`的目录，在`components`中创建一个名为`App.svelte`的文件。你的`App.svelte`应该是这样的。

```
// App.svelte
<script>
    import { onMount } from "svelte";

    onMount(() => {
      console.log("the component has mounted");
    });
</script> 
<main>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">
                        Example Component
                    </div>
                    <div class="card-body">
                        I'm an example component.
                    </div>
                </div>
            </div>
        </div>
    </div>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

现在编辑你的`resources/js/app.js`。

```
// app.js
require('./bootstrap');

import App from "./components/App.svelte";

const app = new App({
  target: document.body
});

window.app = app;

export default app; 
```

Enter fullscreen mode Exit fullscreen mode

现在来看一下`resources/views/welcome.blade.php`文件。它应该看起来像下面这样。

```
// welcome.blade.php
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">

        Laravel

        <!-- Fonts -->
        <link href="https://fonts.googleapis.com/css?family=Nunito:200,600" rel="stylesheet">
        <link rel="stylesheet" href="{{ asset('css/app.css') }}">

    </head>
    <body>

        <script src="{{ asset('js/app.js') }}"></script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们必须添加自举。在`resources/sass`目录下制作两个`.scss`文件，分别命名为`_variables.scss`和`app.scss`。

```
// _variables.scss
// Body
$body-bg: #f8fafc;

// Typography
$font-family-sans-serif: 'Nunito', sans-serif;
$font-size-base: 0.9rem;
$line-height-base: 1.6;

// Colors
$blue: #3490dc;
$indigo: #6574cd;
$purple: #9561e2;
$pink: #f66d9b;
$red: #e3342f;
$orange: #f6993f;
$yellow: #ffed4a;
$green: #38c172;
$teal: #4dc0b5;
$cyan: #6cb2eb; 
```

Enter fullscreen mode Exit fullscreen mode

```
// app.scss
// Fonts
@import url('https://fonts.googleapis.com/css?family=Nunito');

// Variables
@import 'variables';

// Bootstrap
@import '~bootstrap/scss/bootstrap'; 
```

Enter fullscreen mode Exit fullscreen mode

我们差不多完成了。我们在 Laravel 6 应用程序中实际上制作了一个 Svelte.js 支架。现在，我们可以通过 webpack 传输苗条，并在 Laravel 应用程序中获得我们的苗条组件。

```
$ npm run dev && php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们浏览本地服务器时，我们有希望看到一个包含样本苗条组件的页面。我希望你们能尝试一下，并请让我知道进展如何。我只是尝试了一下。希望你们都喜欢。干杯。