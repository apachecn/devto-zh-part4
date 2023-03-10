# 拉勒维尔= ❤️

> 原文：<https://dev.to/sustained/laravel-vue-4530>

> 缺少或不正确的东西？这篇[文章的来源在 Github](https://github.com/sustained/dev.to-article-sources/) 上。
> 
> 请随时[提出问题](https://github.com/sustained/dev.to-article-sources/issues/new)或[发送 PR](https://github.com/sustained/dev.to-article-sources/compare) 。

# 目录

*   [简介](#introduction)
*   [同时使用 Vue 和 Laravel 的原因](#reasons-to-use-vue-and-laravel-together)
    *   [默认脚手架](#scaffolded-by-default)
    *   [Laravel Mix](#laravel-mix)
    *   [广泛的社区采用](#widespread-community-adoption)
*   [一起使用 Vue 和 Laravel 时的常见问题](#common-gotchas-when-using-vue-and-laravel-together)
    *   [叶片和 Vue 插补语法](#blade-and-vue-interpolation-syntax)
    *   [传递 PHP 变量作为 Vue 组件道具](#passing-php-variables-as-vue-component-props)
    *   [Laravel 路由器和 vue 路由器](#laravel-router-and-vuerouter)
        *   [设置好一切](#setting-it-all-up)
            *   [安装 vue 路由器](#install-vuerouter)
            *   [创建一个路由器实例和一些路由](#create-a-router-instance-and-some-routes)
            *   [创建一个简单的应用组件](#create-a-simple-app-component)
            *   [创建几个页面组件](#create-a-few-page-components)
            *   [配置根 Vue 实例](#configure-the-root-vue-instance)
            *   [配置 Laravel 路由器](#configure-the-laravel-router)
            *   [创建控制器和动作](#create-the-controller-and-action)
            *   [创建视图](#create-the-view)
    *   [使用 Laravel Mix 时缺少 Webpack 别名](#lack-of-webpack-aliases-when-using-laravel-mix)
*   [谢谢，再见](#thanks-and-good-bye)

# 简介

最近发布了优秀的 Vue 社区指南。关于 Vue 所有事情的指南--由 Vue 社区为 Vue 社区编写。

*   [Github](https://github.com/dobromir-hristov/vuecommunity)
*   [网站](https://vue-community.org/)

这篇文章的结构将精确地反映 Vue 社区指南的 Laravel 部分的布局。

其目的是提供一个活的资源(即，如果/当必要时，它将随着时间的推移而更新),该资源比指南更详细(包括代码示例/演示)(其目的是更简洁和更容易理解)。

[返回页首](#table-of-contents)

# 一起使用 Vue 和 Laravel 的理由

## 默认脚手架

默认安装的 Laravel [拥有您开始用 Vue 组件增强您的服务器渲染(刀片)模板所需的一切。不需要设置。](https://laravel.com/docs/frontend#writing-vue-components)

创建 Laravel 项目后，您将获得:

*   `bootstrap.js`:
    *   加载一些库，如 lodash、axios 和 Popper
    *   为 CSRF 配置 axios
*   `app.js`:
    *   使 Vue 全球可用
    *   注册`ExampleComponent.vue`
    *   提供如何自动注册 Vue 组件的示例代码
    *   初始化视图
*   `ExampleComponent.vue`:

你当然可以根据自己的需要自由定制这些文件，比如删除不需要的/不想要的库。

[返回页首](#table-of-contents)

## Laravel 混合

Laravel Mix-web pack 上的一个流体 API，也包含在包装盒中-为您减轻了资产编译的痛苦，因此您可以专注于创作 Vue 组件。

你只需要运行`npm run watch`，然后你就可以直接开始编写你的 Vue 组件了！

它并不十分[@ vue/CLI](https://cli.vuejs.org/)-令人敬畏，但它实际上是一个非常好的库，我推荐它。它与 Laravel 没有任何联系——你可以在任何项目中使用它。

[返回页首](#table-of-contents)

## 广泛的社区采用

Laravel 社区的几位杰出成员是 Vue(及其生态系统)的支持者。

这包括但不限于:

*   泰勒·奥特韦尔
    *   拉勒维尔本身的创始人(和仁慈的终身独裁者)
    *   Laravel 和 Vue 的已知用户
*   亚当·沃森
    *   [TailwindCSS](https://tailwindcss.com/) 的作者
    *   [高级 Vue 组件设计课程](https://adamwathan.me/advanced-vue-component-design/)的创始人
    *   Laravel 和 Vue 的已知用户
*   [杰弗里道](https://twitter.com/jeffrey_way)
    *   [Laravel 测试解码](https://leanpub.com/u/jeffreyway)的作者
    *   在[拉拉康](https://laracon.eu/)重现扬声器
    *   几个 Laravel/Vue 课程的创建者
    *   Laravel 和 Vue 的已知用户

据我所知，这种对 Vue 的倡导和支持延伸到了整个 Laravel 社区，这意味着大多数 Laravel 开发人员可能会比 React 或 Angular 更熟悉 Vue。

很自然，接下来会有:

*   帮助您学习的更多资源(指南、教程、文章)
*   更多有 Vue+Laravel 经验的人(帮助、支持、指导)
*   总的来说，一个健康的生态系统(插件、样板等。)

[返回页首](#table-of-contents)

# 一起使用 Vue 和 Laravel 时的常见问题

## Blade 和 Vue 插补语法

如你所知，[叶片模板](https://laravel.com/docs/blade#displaying-data)和 [Vue 模板](https://vuejs.org/v2/guide/#Declarative-Rendering)都使用小胡子语法(即`{{ message }}`)进行变量插值，这带来了一个问题。

幸运的是，解决方案很简单——只需在小胡子语句前面加上一个`@`。这将[指示刀片模板渲染引擎](https://laravel.com/docs/blade#blade-and-javascript-frameworks)忽略该语句，让 Vue 稍后处理。

```
<p>You have @{{ messageCount }} new message(s).</p> 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要对几个小胡子语句进行转义，你可以使用`@verbatim`指令。

```
@verbatim
    <div class="container">
        <p>Welcome {{ user.name }} ({{ user.id }})!</p>
        <p>Your last visit was: {{ user.lastVisit }}.</p>
    </div>
@endverbatim 
```

Enter fullscreen mode Exit fullscreen mode

[返回页首](#table-of-contents)

## 传递 PHP 变量作为 Vue 组件道具

如果你需要从一个[刀片模板](https://laravel.com/docs/blade)中将一个刀片变量作为[道具](https://vuejs.org/v2/guide/components-props.html)传递到一个 [Vue 组件](https://vuejs.org/v2/guide/components.html)中，那么你可能会尝试使用`json_encode`，但是你应该使用`@json`指令:

```
<user-profile :user='@json($user)' /> 
```

Enter fullscreen mode Exit fullscreen mode

[返回页首](#table-of-contents)

## Laravel 路由器和 vue 路由器

你完全可以不费力地将 [Laravel router](https://laravel.com/docs/routing) 和 [vue-router](https://router.vuejs.org/) 一起使用。

也许你想让 vue-router 处理所有的路由；或者让它只处理一些而让 Laravel 处理其他的；或者使用一款 Laravel 应用服务多个水疗中心。所有这些以及更多都是可能的。

### 设置好一切

> **注意:**本节假定对 Vue、vue-router、Laravel 和命令行有相对基本的了解。

Laravel 提供的样板文件不包括 vue-router，但设置起来不会太麻烦。

#### 安装 vue-路由器

```
npm install vue-router --save 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建一个路由器实例和一些路由

创建`resources/js/router.js` :

```
import Vue from "vue";
import VueRouter from "vue-router";

import PageHome from "./pages/Home";
import PageAbout from "./pages/About";

Vue.use(VueRouter);

const router = new VueRouter({
    mode: "history",
    routes: [
        {
            path: "/",
            component: PageHome
        },
        {
            path: "/about",
            component: PageAbout
        }
    ]
});

export default router; 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建一个简单的 App 组件

创建`resources/js/components/App.vue` :

```
<template>
    <div>
        <nav>
            <ul>
                <li>
                    <router-link to="/">Home</router-link>
                </li>

                <li>
                    <router-link to="/about">About</router-link>
                </li>
            </ul>
        </nav>

        <main>
            <router-view></router-view>
        </main>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建几个页面组件

创建`resources/js/pages/Home.vue` :

```
<template>
    <div>Home</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

创建`resources/js/pages/About.vue` :

```
<template>
    <div>About</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置根 Vue 实例

修改`resources/js/app.js` :

```
import router from "./router";
import App from "./components/App";

const app = new Vue({
    el: '#app',
    router,               // <-- register router with Vue
    render: (h) => h(App) // <-- render App component
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置 Laravel 路由器

这是重要的部分——我们需要指示 Laravel 将所有请求路由到`SPAController`上的`index`动作。

更换`routes/web.php`:T1

```
<?php
Route::get('/{vue}', 'SPAController@index')->where('vue', '.*'); 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**在此总括之前注册的任何路线仍将起作用。
> 
> 这就是我们如何用 Laravel 处理一些路由，用 vue-router 处理其他路由。

#### 创建控制器和动作

创建`app/Http/Controllers/SPAController.php` :

```
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class SPAController extends Controller
{
    public function index()
    {
        return view("spa");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建视图

创建`resources/views/spa.blade.php` :

```
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <meta name="csrf-token" content="{{ csrf_token() }}" />

        Laravel + Vue = ❤️

        <link href="{{ asset('css/app.css') }}" rel="stylesheet" />
    </head>

    <body>
        <div id="app"></div>

        <script src="{{ asset('js/app.js') }}"></script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您现在使用 Laravel 提供 Vue SPA 服务，vue-router 负责所有路由(除了在全面控制之前定义的任何路由，这些路由将由 Laravel 处理！).

[返回页首](#table-of-contents)

## 使用 Laravel Mix 时缺少 Webpack 别名

如果你使用过 [vue-cli](https://cli.vuejs.org/) ，那么你可能会熟悉(并习惯使用)别名[，比如`@`和`~`T5。](https://cli.vuejs.org/guide/html-and-static-assets.html#url-transform-rules)

不幸的是，这不是默认设置。幸运的是，我们- [有一个插件](https://laravel-mix.com/extensions/alias)。

[返回页首](#table-of-contents)

* * *

# 谢谢，再见

感谢阅读，并享受与 Laravel 和 Vue 一起开发。

另外，一定要去看看[新的 Vue 社区](https://vue-community.org/)。

> 缺少或不正确的东西？这篇[文章的来源在 Github](https://github.com/sustained/dev.to-article-sources/) 上。
> 
> 请随时[提出问题](https://github.com/sustained/dev.to-article-sources/issues/new)或[发送 PR](https://github.com/sustained/dev.to-article-sources/compare) 。

[返回页首](#table-of-contents)