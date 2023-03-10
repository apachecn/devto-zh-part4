# PHP artisan make:laravel 6.0 中的 auth

> 原文：<https://dev.to/msamgan/php-artisan-make-auth-in-laravel-6-0-hc>

您可能已经知道，laravel 6.0 已经推出。虽然预计在主要版本中会有很多变化，但在 6.0 中没有太多变化。

变化方面更像 5.9。

但是 laravel 6.0 中引入的一个主要变化是删除了

```
php artisan make:auth
```

命令。您可能知道，这是用来创建身份验证框架的。虽然这个概念没有被删除，但实现的方式已经改变了。

**新的方式**

现在，在一个软件包的帮助下，增加了身份验证支持。你可以在[这里](https://laravel.com/docs/6.0/authentication)详细阅读

*实现 Auth 的命令如下。*

```
composer require laravel/ui
php artisan ui vue --auth

```

其他都和 5.8 一样。相同的路线，控制器和所有的东西。

[https://www.youtube.com/embed/Mqs0O0jDQTg](https://www.youtube.com/embed/Mqs0O0jDQTg)

希望这对你有所帮助
请在评论区分享你的想法。

PS:视频中也有一些北印度语，但我相信你会得到要点。