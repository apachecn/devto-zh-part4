# 在 Ubuntu 上设置 Laravel 本地开发环境🐘🐧

> 原文：<https://dev.to/andrewbrooks/setting-up-laravel-local-dev-environment-on-ubuntu-51kp>

让我们在 Ubuntu desktop 19.04 和 18.04 上运行一个简单的 Laravel 开发环境，包括一个完整的 MySQL 数据库连接。

本文假设您对 Linux 终端有一定的了解。

# 安装 PHP

添加并安装 PHP 7.3 PPA

```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install php7.3 
```

验证安装的 PHP 版本

```
php -v 
```

输出:

```
PHP 7.3.8-1+ubuntu19.04.1+deb.sury.org+1 (cli) (built: Aug  7 2019 09:52:53) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.8, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.8-1+ubuntu19.04.1+deb.sury.org+1, Copyright (c) 1999-2018, by Zend Technologies 
```

# 安装 MySQL 数据库

```
sudo apt-get install php7.3-mysql
sudo apt-get install mysql-server
sudo mysql_secure_installation 
```

现在您将看到一些提示。

对于本地开发环境，我跳过了“验证密码插件”。

您将被要求输入`root`用户的密码。

从那里删除匿名用户，禁止`root`用户远程访问，并删除测试数据库。

当要求重新加载权限表时，选择`yes`。

## 创建具有 root 权限的非 root 用户

稍后在配置 Laravel 应用程序时，我们将使用这个用户。

以`root`的身份登录 MySQL。

```
sudo mysql -u root -p 
```

运行下面的 SQL 来创建一个新用户`admin`。

```
CREATE USER 'admin'@'localhost' IDENTIFIED BY '';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES; 
```

输入`exit`退出 MySQL CLI。

```
mysql> exit 
```

在不使用`sudo`的情况下，通过以`admin`的身份登录来测试新用户。

```
mysql -u admin 
```

您应该会看到类似这样的内容:

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 5.7.27-0ubuntu0.19.04.1 (Ubuntu)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> _ 
```

退出 MySql，我们将继续安装 Composer。

```
mysql> exit 
```

# 安装作曲家

首先，我们需要更新我们的包管理器。

```
sudo apt update 
```

现在，我们将安装一些安装 Composer 所需的依赖项。

*   `curl`下载作曲家
*   运行安装需要使用`php-cli`
*   `php-mbstring`为所使用的库提供功能
*   `git`将允许 Composer 下载项目依赖项
*   `unzip`用于解压缩包

```
sudo apt install curl php-cli php-mbstring git unzip 
```

这样，我们就可以下载 Composer 安装程序了。

首先进入您的主目录

```
cd ~ 
```

并开始下载 Composer 安装程序。

```
curl -sS https://getcomposer.org/installer -o composer-setup.php 
```

在我们运行安装程序之前，需要验证它是否损坏。

访问[https://composer.github.io/pubkeys.html](https://composer.github.io/pubkeys.html)，找到“**安装人员签名(SHA-384)**

使用签名并运行以下命令来验证您的安装程序。

```
HASH=your_hash_goes_here 
```

```
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" 
```

您应该会看到
的输出

```
Installer verified 
```

否则，您需要重新下载安装程序，并再次通过验证步骤。

## 运行安装程序

现在我们可以自由地在系统上全局安装 Composer 了。

```
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer 
```

输出:

```
All settings correct for using Composer
Downloading...

Composer (version 1.9.0) successfully installed to: /usr/local/bin/composer
Use it: php /usr/local/bin/composer 
```

最后，我们需要更新对 composer 目录的权限。

```
sudo chown -R $USER ~/.composer/ 
```

### 验证安装了 Composer

⚠️不运行作曲家作为根使用须藤⚠️

只需运行

```
composer 
```

你应该看到:

```
 ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.9.0 2019-08-02 20:55:32 
```

# 拉腊维尔时间到了！

但首先，一些依赖。

```
sudo apt-get install php7.3-zip 
```

```
sudo apt-get install php7.3-xml 
```

使用 Composer 下载 Laravel 安装程序。

```
composer global require laravel/installer 
```

现在我们需要更新 Linux `$PATH`

```
echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc 
```

更新完`$PATH`后，关闭你的终端窗口，打开一个新的。

# 您好，世界

让我们在本地主机上运行一个样板 Laravel 应用程序。

首先，我们需要创建将要连接的数据库。

使用之前创建的`admin`用户通过终端登录 MySql。

```
mysql -u admin 
```

现在运行下面的 SQL 来创建一个名为`laravel`的数据库。

```
CREATE DATABASE laravel; 
```

随着我们的数据库创建，我们可以移动到事情的 Laravel 方面。

`cd`通过终端进入您想要创建 Laravel 应用程序的文件夹。

对我来说那是`~/dev/laravel/`

运行以下命令来搭建应用:

```
laravel new hello-world 
```

仅仅是应用程序的名字，所以你可以随意命名。

一旦这完成`cd`到你的应用程序的新目录。

```
cd hello-world 
```

此时，我们需要告诉 Laravel 应用程序如何连接到我们的数据库。

为此，我们将编辑应用程序根目录中的`.env`文件。

```
sudo nano .env 
```

连接设置应该是这样的

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=admin
DB_PASSWORD= 
```

您可以使用`ctrl+x`->-`'y'`->-`[ENTER]`保存 nano 的更改

现在我们需要测试这个连接。

为此我们将编辑`/routes/web.php`

它应该看起来像

```
Route::get('/', function () {
    // return view('welcome');
    try {
        DB::connection()->getPdo();
        $caught = false;
    } catch (Exception $e) {
        $caught = true;
        die("Could not connect to the database.  Please check your configuration. error:" . $e );
    }

    if(!$caught){
        echo 'Hello, world.';
    }
}); 
```

现在通过运行
启动服务器

```
php artisan serve 
```

输出:

```
Laravel development server started: <http://127.0.0.1:8000> 
```

现在你可以在你的浏览器中查看它，网址是 http://127.0.0.1:8000

你应该看看“你好，世界”打印到屏幕上，表示成功连接到数据库。

就是这样！🎉

现在，您已经为本地 Ubuntu 开发环境配置了 PHP 7.3、MySql、Composer 和 Laravel 安装程序，并运行了 starter Laravel 应用程序。

* * *

我是安德鲁，一个来自南方腹地的全栈软件开发人员，从事互联网开发。

在推特上给我打电话

如果您对未来的文章有任何问题或要求，请联系我们！