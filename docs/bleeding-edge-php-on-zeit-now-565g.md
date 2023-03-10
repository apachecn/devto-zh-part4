# 现在 ZEIT 上的前沿 PHP

> 原文：<https://dev.to/nx1/bleeding-edge-php-on-zeit-now-565g>

今天我想向大家展示 ZEIT Now 平台。嗯，你可能听说过。关于这项令人敬畏的技术有许多博客帖子([https://dev.to/search?q=zeit%20now](https://dev.to/search?q=zeit%20now))。

但是，我有一些好消息给 PHP 开发者。自 2019 年 6 月以来，我一直在为 ZEIT Now 开发增强型 PHP 构建器。

这些构建器支持:

*   多个 PHP 版本(7.2.20、7.3.7、7.4.0alpha2)🚀
    *   官方的 PHP 构建器是 7.1.22
*   多种运行模式(服务器、cgi、cli、fpm) 🧙‍♂️
    *   随便挑你喜欢的
*   许多预装的 PHP 扩展📦
*   通过 Composer 安装依赖项🤩

你可以在 Github [juicyfx/now-php](https://github.com/juicyfx/now-php) 上找到更新的构建器，我会感谢任何反馈。

怎么测试？看一看[例题](https://github.com/juicyfx/now-php/tree/master/examples)

* * *

如何在 1 分钟内展开？在你的项目文件夹中创建`index.php`和`now.json`。如果你现在还不熟悉 ZEIT，[在官网上看一下](https://zeit.co/now)。

```
<?php 

phpinfo(); 
```

```
{  "version":  2,  "builds":  [  {  "src":  "old.php",  "use":  "@now/php"  },  {  "src":  "new.php",  "use":  "now-php"  }  ]  } 
```

是的，就这些。而现在叫`now`。🤓

你应该看到类似的东西。

[![](img/c90f404421a6ba309e6b21fdeaff4841.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4MZ9w3hZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oyvm8n1l6m360q2b2bfn.png)

* * *

最后，给出了一些例子。

*   官方-[https://php.now.sh/](https://php.now.sh/)
*   https://php.jfx.cz/
*   扩展-[https://php.jfx.cz/ext/](https://php.jfx.cz/ext/)
*   ini-[https://php.jfx.cz/ini/](https://php.jfx.cz/ini/)
*   JSON API-[https://php.jfx.cz/api/users.php](https://php.jfx.cz/api/users.php)
*   测试-[https://php.jfx.cz/test.php](https://php.jfx.cz/test.php)

我也在用 FPM 和 [Bref.sh](http://bref.sh) 测试球童服务器。有许多你可能正在寻找的特征。

* * *

PHP builder 从 zeit/now-builders 中分离出来，成为 solo repository-[https://github.com/juicyfx/now-php](https://github.com/juicyfx/now-php)。看一看。

* * *

测试愉快，
菲利克斯