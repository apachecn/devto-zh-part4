# 如何在 Lumen API 中使用 mongoDB

> 原文：<https://dev.to/rafaelcpalmeida/how-to-use-mongodb-with-your-lumen-api-4kgm>

我目前正在使用 [Lumen](https://lumen.laravel.com/) 构建一个 RESTful API，它来自于给我们带来 [Laravel](https://laravel.com/) 的那些家伙。Lumen 是一个很棒的微框架，拥有 Laravel 的大多数引人注目的特性，如雄辩、缓存、验证、路由、中间件等等。我正在构建的 API 应该是为一个尚未开发的 iOS 应用程序服务的，该应用程序旨在支持我所在学院的信息系统，该系统不能正确支持移动设备。

这将是一个非常简单的项目，但**不幸的是，**他们不会让我的生活变得容易，也不会让我访问现有的数据库。糟透了，对吧？然而，他们有一个我可以使用的 [SOAP Web 服务](https://en.wikipedia.org/wiki/SOAP)。但是……如果我每次提出请求时都使用他们的 web 服务和我的 API，那么要花很长时间才能得到我所请求的信息。很明显，这是一种糟糕的做事方式。所以我决定实现一个数据库，我可以自己存储数据。

我完全意识到我们不应该重复数据，但这次我找不到更好的方法。我已经决定开发一些 Python 机器人，它们将使用 SOAP web 服务定期获取信息，并将其存储在数据库中，以便稍后使用 REST API 提供服务。我决定使用 [mongoDB](https://www.mongodb.com/) 主要是因为它的灵活性、性能和可伸缩性。我用的是 mongo 3.4，所以下面所有的命令都是针对这个版本测试的。

> 那么…如何设置 Lumen 来使用 mongoDB

[延斯·塞格斯](https://jenssegers.com/)有一个很棒的 Composer 包把 mongoDB 和 Laravel 一起使用: [Laravel MongoDB](https://github.com/jenssegers/laravel-mongodb) 。查看回购指令，您可以看到，为了与 Lumen 一起使用，在您安装软件包之后，编辑文件`bootstrap/app.php`，使其看起来像:

```
$app->register(Jenssegers\Mongodb\MongodbServiceProvider::class);

$app->withEloquent(); 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，你应该创建`config/database.php`文件，它的内容是:

```
<?php
    return [
        'default' => 'mongodb',
        'connections' => [
            'mongodb' => [
                'driver' => 'mongodb',
                'host' => env('DB_HOST', 'localhost'),
                'port' => env('DB_PORT', 27017),
                'database' => env('DB_DATABASE'),
                'username' => env('DB_USERNAME'),
                'password' => env('DB_PASSWORD'),
                'options' => [
                    'database' => 'admin' // sets the authentication database required by mongo 3
                ]
            ],
        ],
        'migrations' => 'migrations',
    ]; 
```

Enter fullscreen mode Exit fullscreen mode

这将覆盖`vendor/laravel/lumen/config/database.php`,并允许你在 Lumen 项目中使用 mongo。在这之后，你需要修改我们的`.env`文件并添加:

```
DB_CONNECTION=mongodb
DB_HOST=<dbHost>
DB_PORT=27017
DB_DATABASE=<dbName>
DB_USERNAME=<dbUser>
DB_PASSWORD=<dbUserPassword> 
```

Enter fullscreen mode Exit fullscreen mode

开箱即用的 mongo 没有任何认证细节。您可以使用
将自己的用户添加到其中

```
use admin
db.createUser( { user: "<dbUser>",
          pwd: "<dbUserPassword>",
          roles: [ "userAdminAnyDatabase",
                   "dbAdminAnyDatabase",
                   "readWriteAnyDatabase"

] } ) 
```

Enter fullscreen mode Exit fullscreen mode

现在我能够使用 Lumen 的`artisan`命令，例如，我可以使用
创建我的`users`表

```
php artisan make:migration create_users_table --create=users 
```

Enter fullscreen mode Exit fullscreen mode

我已经修改了迁移文件，如:

```
public function up() {
    Schema::create('users', function (Blueprint $table) {
        $table->increments('_id');
        $table->string('username');
        $table->string('password');
        $table->timestamps();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以使用`php artisan migrate`了，我会看到我新创建的收藏。