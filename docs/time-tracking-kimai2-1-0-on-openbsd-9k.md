# 时间跟踪:OpenBSD 上的 Kimai2 1.0

> 原文：<https://dev.to/nabbisen/time-tracking-kimai2-1-0-on-openbsd-9k>

## 简介

[Kimai2 1.0 发布](https://github.com/kevinpapst/kimai2/releases/tag/1.0)！！

[Kimai](https://www.kimai.org/) 是开源的时间追踪 app。
有两大版本: [Kimai1](https://github.com/kimai/kimai) ，是 [GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html) 行货， [Kimai2](https://github.com/kevinpapst/kimai2) ，是 [MIT](https://opensource.org/licenses/MIT) 行货。

在这篇文章中，我将展示如何安装最新的 Kimai2。它基于 [Symfony](https://symfony.com/) 、 [PHP](https://php.net) 健壮框架。
此外，Kimai2 非常适合[保持 Symfony 的更新](https://github.com/kevinpapst/kimai2/pull/710):)

#### 环境

*   OS: [OpenBSD](https://www.openbsd.org/) 6.5
*   数据库: [MariaDB](https://mariadb.org/) 10.0
*   Web 服务器: [httpd](https://man.openbsd.org/httpd.8)
*   应用服务器: [PHP](https://php.net/) 7.3
    *   CGI: [PHP-FPM](https://php-fpm.org/)
    *   包管理员:[作曲者](https://getcomposer.org/)
*   时间追踪应用程序:Kimai2 1.0

## 教程

官方安装手册这里是[这里是](https://www.kimai.org/documentation/installation.html)。

#### 要求

首先，你必须具备:

*   [httpd 作为网络服务器](https://dev.to/nabbisen/setting-up-openbsds-httpd-web-server-4p9f)
*   [php-fpm](https://dev.to/nabbisen/php-fpm-on-openbsd-2iof)
*   PHP [作曲](https://dev.to/nabbisen/almost-php-72-composer-with-openbsd-64-100o)
*   [MariaDB 作为数据库服务器](https://dev.to/nabbisen/installing-mariadb-server-on-openbsd-5lm)

另外，[推荐 HTTPS](https://dev.to/nabbisen/lets-encrypt-certbot-for-openbsds-httpd-3ofd)。

这个部分所有的链接都是我写的教程。

#### 数据库服务器

创建数据库和用户:

```
CREATE DATABASE <database> DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON <database>.* TO <dbuser> IDENTIFIED BY '<dbpass>'; 
```

#### 应用服务器

获取包:

```
$  git clone -b 1.0 --depth 1 https://github.com/kevinpapst/kimai2.git
$  cd kimai2/ 
```

然后修改权限:

```
#  chown -R :www .
#  chmod -R g+r .
#  chmod -R g+rw var/ 
```

还要配置系统:

```
$  cp -p .env.dist .env
$  nvim .env 
```

这里是`.env` :
中至少要改变的地方

```
- APP_SECRET=change_this_to_something_unique + APP_SECRET=<some_salt_key_long_enough> ...
- DATABASE_URL=sqlite:///%kernel.project_dir%/var/data/kimai.sqlite
+ DATABASE_URL=mysql://<dbuser>:<dbpass>@<dbhost>:<dbport>/<database> 
```

*注:根据 [Symfony 的文档](https://symfony.com/doc/current/reference/configuration/framework.html#secret) , `secret`值“应该是随机选择的一系列字符、数字和符号，建议长度在 32 个字符左右。”我用了 [LastPass 发生器](https://www.lastpass.com/password-generator)。*

让我们通过 Composer 安装应用程序。
使用`php-7.* /usr/local/libexec/composer.phar`是 OpenBSD 安装的诀窍。

```
$  php-7.3 /usr/local/libexec/composer.phar install --no-dev --optimize-autoloader 
```

结果是:

```
Loading composer repositories with package information
Installing dependencies from lock file
Package operations: 136 installs, 0 updates, 0 removals
  - Installing ocramius/package-versions (1.4.0): Downloading (100%)         
  - Installing kimai/kimai2-composer (0.1): Downloading (100%)         
  - Installing symfony/flex (v1.2.5): Downloading (100%) ...
 - Installing white-october/pagerfanta-bundle (v1.2.4): Loading from cache
Generating optimized autoload files
ocramius/package-versions:  Generating version class...
ocramius/package-versions: ...done generating version class
Executing script cache:clear [OK]
Executing script assets:install [OK] 
```

*注意:不需要创建模式和表，这不同于[安装 Kimai2 0.9](https://dev.to/nabbisen/time-tracking-kimai2-on-openbsd-2mk9) 的方式。*

好了，我们继续吧:

```
$  php-7.3 bin/console cache:warmup --env=prod 
```

结果是:

```
 // Warming up the cache for the prod environment with debug false                                                      

 [OK] Cache for the "prod" environment (debug=false) was successfully warmed. 
```

系统现在几乎准备好了。
让我们准备 web 服务器。
将下面的定义添加到 [`/etc/httpd.conf`](https://man.openbsd.org/httpd.conf.5) :

```
server "<fqdn>" {
        listen on $ext_addr port 80
        block return 301 "https://$SERVER_NAME$REQUEST_URI"
}
server "<fqdn>" {
        listen on $ext_addr tls port 443
        tls {
                certificate     "/etc/letsencrypt/live/<fqdn>/fullchain.pem"
                key             "/etc/letsencrypt/live/<fqdn>/privkey.pem"
        }
        # create unique log files (optional):
        log {
                access  "<fqdn>-access.log"
                error   "<fqdn>-error.log"
        }

        # the document root is the directory named `public`:
        root "/<...>/kimai2/public"
        directory index index.php

        location "/*.php" { 
                fastcgi socket "/run/php-fpm.sock"
        } 
        location "/*.php[/?]*" {
                fastcgi socket "/run/php-fpm.sock"
        }
        # if directories are accessed to, call `index.php` with url parameter:
        location match "^/(.*)/[^\.]+/?$" {
                fastcgi socket "/run/php-fpm.sock"
                request rewrite "/index.php/%1"
        }
} 
```

重启:

```
#  rcctl restart httpd 
```

最后，进入你的 FQDN aka https:// <fqdn>，你可以选择“注册新账户”链接:</fqdn>

[![login](img/c444b7bddd97ff961dd7a2b62b016e7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KCM83kk0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o0wyckfm0ntwj0a7ylpi.png)

另外，您也可以通过命令行 :
创建一个用户作为系统管理员

```
$  php-7.3 bin/console kimai:create-user <username> <email-address> ROLE_SUPER_ADMIN 
```

*结果是* :

```
Please enter the password: 

 [OK] Success! Created user: <username> 
```

## 结论

现在所有的服务器，数据库/应用程序/web，都准备好了并在监听。
让我们通过任何网络浏览器访问< fqdn >:

[![screenshot](img/94ed6989f5a0b413df2dc5d5dc315612.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--00sPVajM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5f1ueqlryin1318t3i5z.png)

瞧，🥳
一个漂亮的时间跟踪应用程序，干净的用户界面，简单的菜单和几种导出时间表的方法在这里！

感谢您的阅读。
快乐计算。