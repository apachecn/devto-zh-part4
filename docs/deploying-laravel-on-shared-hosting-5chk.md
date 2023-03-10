# 在共享主机上部署 Laravel

> 原文：<https://dev.to/ndiecodes/deploying-laravel-on-shared-hosting-5chk>

如果你在这里，你可能正在探索 laravel 如何在共享主机上工作，在一些低预算的项目上工作，或者只是有你喜欢共享主机的理由。好吧，我们言归正传。

下面是让 laravel 在共享主机上运行的步骤:

(1)运行`php artisan config:clear`；这将清除缓存配置。因为您将在线编辑`.env`;您不需要缓存本地配置。

(2)将迁移后的数据库导出到一个没有`create database`语句的文件(db.sql)中，比如:

```
DROP TABLE IF EXISTS `users`;

CREATE TABLE `users` (
 `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
 `title` varchar(255) DEFAULT NULL,
 `name` varchar(255) NOT NULL,
 `facebook_id` varchar(255) DEFAULT NULL,
 `google_id` varchar(255) DEFAULT NULL,
 `linkedin_id` varchar(255) DEFAULT NULL,
 `tagline` varchar(255) DEFAULT NULL,
 `email` varchar(255) DEFAULT NULL,
 `username` varchar(255) DEFAULT NULL,
 `email_verification_code` varchar(255) DEFAULT NULL,
 `email_verified_at` timestamp NULL DEFAULT NULL,

 `password` varchar(255) DEFAULT NULL,
 `remember_token` varchar(100) DEFAULT NULL,
 `created_at` timestamp NULL DEFAULT NULL,
 `updated_at` timestamp NULL DEFAULT NULL,
 PRIMARY KEY (`id`)
) ENGINE=InnoDB;

DROP TABLE IF EXISTS `password_resets`;

CREATE TABLE `password_resets` (
 `email` varchar(254) NOT NULL,
 `token` varchar(254) NOT NULL,
 `created_at` timestamp NULL DEFAULT NULL,
 KEY `password_resets_email_index` (`email`)
) ENGINE=InnoDB; 
```

Enter fullscreen mode Exit fullscreen mode

(3)转到 c 面板上的`MultiPHP Manager`,确认你有 php 7.1 或更高版本。

(4)压缩完成的项目(我们从现在开始称这个 **myproject** )并通过文件管理器(c-panel)或 FileZilla 上传到托管存储的根目录；应该是类似`/home/domain-name`的东西。

(5)提取托管存储根目录(`/home/domain-name`)下的 myproject。

(6)启用“显示隐藏文件”。这样你就可以看到类似。htaccess，。env 等。

(7)在`myproject/public`中，将其所有内容移至`/public_html`。

(8)现在编辑`/public_html/index.php`指向你的 laravel 项目，像这样:

```
//previous state
require __DIR__.'/../vendor/autoload.php';

// changed to
require __DIR__.'/../myproject/vendor/autoload.php';

//previous state
$app = require_once __DIR__.'/../bootstrap/app.php';

//changed to
$app = require_once __DIR__.'/../myproject/bootstrap/app.php'; 
```

Enter fullscreen mode Exit fullscreen mode

(9)编辑。使用您的配置键在`/myproject`中封装

(10)通过 c-panel 上的 phpmyadmin 导入并运行您之前在您创建的数据库上导出的`db.sql`脚本。

结束了！