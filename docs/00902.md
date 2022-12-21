# 在环境变量中存储 Wordpress 配置

> 原文：<https://dev.to/matthewbdaly/storing-wordpress-configuration-in-environment-variables-1f7o>

众所周知，Wordpress 配置是一个严重的问题。在 PHP 文件中硬编码配置细节并不是一种非常安全的存储数据库细节的方式，因为如果服务器配置错误，就会暴露出来。此外，将`wp-config.php`文件复制并填充到新的部署中也是一件麻烦的事情。

十二因素应用程序的一个基本原则是配置应该存储在环境中。虽然 Wordpress 确实早于此，但我们没有理由不遵守这一点。将 Wordpress 配置存储在环境变量而不是`wp-config.php`文件中有以下优点:

*   它更安全，因为配置不是存储在 web 根目录下的文件中，而是存储在 web 服务器配置中
*   它减少了管理`wp-config.php`文件的麻烦——它可以安全地提交给版本控制，并且您不需要更改它来匹配您的本地配置，冒着意外提交并使用损坏的配置进行生产的风险
*   部署到新服务器更加简单，因为不需要更新`wp-config.php`
*   在本地工作时，忽略更改数据库细节和意外搞乱生产数据库的风险几乎被消除了

我见过使用 DotEnv 的解决方案，但是你实际上不需要安装它就能在 Wordpress 中使用环境变量。事实上，在某种程度上，如果没有太多开发人员在生产中使用`.env`文件会更好。PHP 本身具有使用`getenv()`函数从环境变量中获取数据的能力，因此使用它比引入第三方库更容易。

下面是一个`wp-config.php`文件的简短示例，该文件已经过更新，可以从环境变量中提取设置:

```
<?php
// **MySQL settings - You can get this info from your web host** //
/** The name of the database for WordPress */
define( 'DB_NAME', getenv('DB_NAME') );

/** MySQL database username */
define( 'DB_USER', getenv('DB_USER') );

/** MySQL database password */
define( 'DB_PASSWORD', getenv('DB_PASSWORD') );

/** MySQL hostname */
define( 'DB_HOST', getenv('DB_HOST') );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

define( 'AUTH_KEY', getenv('AUTH_KEY') );
define( 'SECURE_AUTH_KEY', getenv('SECURE_AUTH_KEY') );
define( 'LOGGED_IN_KEY', getenv('LOGGED_IN_KEY') );
define( 'NONCE_KEY', getenv('NONCE_KEY') );
define( 'AUTH_SALT', getenv('AUTH_SALT') );
define( 'SECURE_AUTH_SALT', getenv('SECURE_AUTH_SALT') );
define( 'LOGGED_IN_SALT', getenv('LOGGED_IN_SALT') );
define( 'NONCE_SALT', getenv('NONCE_SALT') );

$table_prefix = 'wp_';

define( 'WP_DEBUG', getenv('WP_DEBUG') );

/* That's all, stop editing! Happy publishing. */

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
    define( 'ABSPATH', dirname( __FILE__ ) . '/' );
}

/** Sets up WordPress vars and included files. */
require_once( ABSPATH . 'wp-settings.php' ); 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 Lando 进行本地开发，您将需要指定一个包含您想要设置的环境变量的文件，如下例所示:

```
name: wordpress
recipe: wordpress
config:
  webroot: .
env_file:
  - .env 
```

Enter fullscreen mode Exit fullscreen mode

这个文件名可以是任意选择的名称。然后，您用与在`.env`文件中相同的方式定义这些变量的值。这里有一个简短的例子，不包括加密设置(尽管这些也应该放在这里):

```
DB_NAME=wordpress
DB_USER=wordpress
DB_PASSWORD=wordpress
DB_HOST=database
WP_DEBUG=true
... 
```

Enter fullscreen mode Exit fullscreen mode

这在本地开发过程中可以很好地工作，但是在生产中，或者如果您使用类似于 vagger 的东西进行本地开发，您将希望在服务器配置中设置环境变量。对于 Apache，这最好在 Virtualhost 配置中设置，尽管如果所有其他方法都失败了，您应该能够在一个`.htaccess`文件中设置它。您需要使用`SetEnv`指令，如下例所示:

```
SetEnv DB_NAME wordpress
SetEnv DB_USER wordpress
SetEnv DB_PASSWORD wordpress
SetEnv DB_HOST database
SetEnv WP_DEBUG true 
```

Enter fullscreen mode Exit fullscreen mode

对于 Nginx，假设您使用 FastCGI，您需要使用`fastcgi_param`指令在该站点的服务器配置中设置它，如下所示:

```
fastcgi_param DB_NAME wordpress;
fastcgi_param DB_USER wordpress;
fastcgi_param DB_PASSWORD wordpress;
fastcgi_param DB_HOST database;
fastcgi_param WP_DEBUG true; 
```

Enter fullscreen mode Exit fullscreen mode

由于 Wordpress 没有附带任何类型的命令行任务运行程序，这对大多数安装来说应该足够了。但是，如果您使用 WP CLI，这将会破坏它，因为它不能访问 Apache 或 Nginx 设置的环境变量，所以您还需要为运行 WP CLI 的用户设置它们，方法是以通常的方式将它们添加到 Bash 配置中。