# 连接 Laravel Debugbar 顶点以调试 Rest API Routes

> 原文：<https://dev.to/uatthaphon/laravel-debugbar-rest-api-routes-hca>

# [t1㎡问题](#%E0%B8%9B%E0%B8%B1%E0%B8%8D%E0%B8%AB%E0%B8%B2)

本文从实际经验出发，我所做的项目在响应速度方面存在一些问题，因为有些端点需要超过 10s 的响应时间。

我想，如果我们不继续做这个项目，假设下一个来抓这个项目的人不仅有一个任务，而且他还会浪费很多时间和你在一起。

所以我安装了 Debugbar 软件包以防它被调试，但坚持认为 debugbar 无法在 route API 中显示结果。

## 框架&包⚙️

*   [Laravel 5.4](https://github.com/laravel/laravel/tree/5.4)
*   [不可调试的 2.4](https://github.com/barryvdh/laravel-debugbar/tree/2.4)
*   [野狗 Api](https://github.com/dingo/api)

```
# install laravel 5.4
composer create-project --prefer-dist laravel/laravel yourProjectName "5.4.*"
# install debugbar 2.4 that compatible with laravel 5.4
composer require barryvdh/laravel-debugbar:~2.4
# install dingo/api
composer require dingo/api 
```

我不是在每一步都显示 install 方法，朋友可以从提供的连结中读取 install 和 config 方法。

## [t1㎡怎样做˙咦§](#%E0%B8%A7%E0%B8%B4%E0%B8%98%E0%B8%B5%E0%B8%97%E0%B8%B3)

从这里可以看出，这个项目使用的是 laravel v5。4，debugbar v2 . 4，debugbar v2。4 和 dingo api v1 当我们从浏览器或 postman 执行 api route 访问时，我们需要的是**【debugbar 显示】。**

从创建中间件开始

```
<?php

namespace App\Http\Middleware;

use Closure;
use Dingo\Api\Http\Response;

class ProfileDingoHttpResponse
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        if ($response instanceof Response &&
            app()->bound('debugbar') &&
            app('debugbar')->isEnabled()
        ) {
            $queries_data = $this->sqlFilter(app('debugbar')->getData());
            $response->setContent(json_decode($response->morph()->getContent(), true) + [
                '_debugbar' => [
                    'total_queries' => count($queries_data),
                    'queries' => $queries_data,
                    'time' => app('debugbar')->getData()['time'],
                    'memory' => app('debugbar')->getData()['memory'],
                ],
            ]);
        }

        return $response;
    }

    /**
     * Get only sql and each duration
     *
     * @param $debugbar_data
     * @return array
     */
    protected function sqlFilter($debugbar_data)
    {
        $result = array_get($debugbar_data, 'queries.statements');

        return array_map(function ($item) {
            return [
                'sql' => array_get($item, 'sql'),
                'duration' => array_get($item, 'duration_str'),
            ];
        }, $result);
    }
} 
```

然后在`Kernel.php`文件中添加我们刚刚创建的中间件 t0⾹它位于`yourProjectName/app/Http/Kernel.php`中。

```
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    ...

    protected $routeMiddleware = [
        ...
        'profile-dingo-http' => \App\Http\Middleware\ProfileDingoHttpResponse::class,
    ];
} 
```

在我们在`$routeMiddleware`中添加`'profile-dingo-http' => \App\Http\Middleware\ProfileDingoHttpResponse::class,`之后，我们就可以添加中间件了。在这个项目中，我们使用了 Dingo api，它将看到方法。

```
<?php
$api = app('Dingo\Api\Routing\Router');

$api->version('v1', ['middleware' => 'profile-dingo-http'], function ($api) {
    ...
}); 
```

ᶨ麒麟﹞完了，先生。

## [t1㎡结果㎡合格](#%E0%B8%9C%E0%B8%A5%E0%B8%A5%E0%B8%B1%E0%B8%9E%E0%B8%98%E0%B9%8C)

在从 url 中调用 api 后，我们将得到一个结果，这是我们在中间件`ProfileDingoHttpResponse`中创建的 debugbar 中的数据，最后被挂起。

[![](img/8f563aa338704a1a65a96553bfe82e24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PjzvQABD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fm5emewm63fbta3c25is.jpg)

这是在我们添加中间件时得到的额外的挂起结果，先生，如果不明显的话，可以从下面查看示例，先生。

```
"_debugbar":  {  "total_queries":  1,  "queries":  [  {  "sql":  "select `id`, `name`, `description` from `product` where `id` = '2' limit 1",  "duration":  "14.01ms"  }  ],  "time":  {  "start":  1561688591.7021,  "end":  1561688591.8966,  "duration":  0.19451189041138,  "duration_str":  "194.51ms",  "measures":  [  {  "label":  "Booting",  "start":  1561688591.7021,  "relative_start":  0,  "end":  1561688591.786,  "relative_end":  1561688591.786,  "duration":  0.083874940872192,  "duration_str":  "83.87ms",  "params":  [],  "collector":  null  },  {  "label":  "Application",  "start":  1561688591.7738,  "relative_start":  0.071721792221069,  "end":  1561688591.8966,  "relative_end":  0.0000050067901611328,  "duration":  0.12279510498047,  "duration_str":  "122.8ms",  "params":  [],  "collector":  null  }  ]  },  "memory":  {  "peak_usage":  19660800,  "peak_usage_str":  "18.75MB"  }  } 
```

正因为如此，它得到了这样的结果，而现在，无论是对旧的还是新的，debug 都会变得更容易，这可以让我们找到事情的原因。

#### 来源&非常感谢❤️🎉🥰

[REST API 剖析#252](https://github.com/barryvdh/laravel-debugbar/issues/252)