# PHP 中生产友好的配置文件

> 原文：<https://dev.to/supunkavinda/production-friendly-configuration-files-in-php-5gge>

当开发一个 PHP 应用程序时，我们经常需要将一些配置保存在常量中。这样，我们可以在应用程序的任何地方访问它。

但是，问题是我们需要有不同的开发和生产配置(+可能是测试)。这里有一个简单的技巧，你可以有效地使用它。

您将需要 3 个文件(通常我将包含文件保存在`/inc`目录中)。

*   这是您将要包含的文件
*   `inc/config.server.php` -生产配置
*   `inc/config.dev.php` -开发配置
*   `inc/config.default.php`(可选)-默认配置(生产和开发通用)

在`config.php`中，首先我们根据服务器名称包含正确的配置文件。确保在下面的代码中用生产和开发域替换这些域。然后，我们可以包含默认配置文件。

**config.php**T2】

```
<?php
if ($_SERVER['SERVER_NAME'] === 'production.com') {
    include_once 'config.server.php';
} else if ($_SERVER['SERVER_NAME'] === 'localdomain.com') {
    include_once 'config.dev.php';
}

include_once 'config.default.php'; 
```

Enter fullscreen mode Exit fullscreen mode

每个配置文件可以包含如下代码。

```
// database config
define('DB_HOST', 'localhost');
define('DB_USERNAME', 'root'); 
// ...

// domain config
define('PROTOCOL', 'https://');
// ...

// and any thing 
```

Enter fullscreen mode Exit fullscreen mode

默认配置文件可以包含生产和开发通用的配置。

```
define('MAX_USERNAME_LENGTH', 15);

// etc. 
```

Enter fullscreen mode Exit fullscreen mode

现在，在任何其他需要包含配置的 PHP 文件中，您可以只包含`config.php`文件。

```
<?php
include_once '/path/to/config.php'; 
```

Enter fullscreen mode Exit fullscreen mode

你完了！

您可能以前已经使用过这种技术。但是，如果你没有，我很高兴你学到了新的东西。我认为这是保存配置的最好方法，因为每次上传时不需要在生产服务器中重命名文件，这没有意义。(我见过一些人这样做)

:)