# 你好，从 WordPress 到 Laravel Nova 的 Dolly

> 原文：<https://dev.to/abr4xas/hello-dolly-from-wordpress-to-laravel-nova-kd2>

这是一个简单的软件包来显示你好多丽宋对你的拉勒维尔新星仪表板

## 安装

你可以通过 composer:
将这个包安装到使用 [Nova](https://nova.laravel.com) 的 Laravel 应用程序中

```
composer require abr4xas/nova-hello-dolly 
```

Enter fullscreen mode Exit fullscreen mode

接下来，你必须向 Nova 注册卡。这通常在`NovaServiceProvider`的`cards`方法中完成。

```
// in app/Providers/NovaServiceProvider.php

// ...

public function cards()
{
    return [
        // ...
        new NovaHelloDolly,
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode