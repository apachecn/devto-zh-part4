# 在 Laravel 5.8 中连接多个数据库

> 原文：<https://dev.to/skipperhoa/connecting-multiple-databases-in-laravel-5-8-47jb>

你需要配置文件**。env**

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=database1
DB_USERNAME=root
DB_PASSWORD=secret

DB_CONNECTION_SECOND=mysql
DB_HOST_SECOND=127.0.0.1
DB_PORT_SECOND=3306
DB_DATABASE_SECOND=database2
DB_USERNAME_SECOND=root
DB_PASSWORD_SECOND=secret 
```

继续，打开文件夹 config 中的 config/database.php 文件，添加列表连接

```
'mysql' => [
    'driver'    => env('DB_CONNECTION'),
    'host'      => env('DB_HOST'),
    'port'      => env('DB_PORT'),
    'database'  => env('DB_DATABASE'),
    'username'  => env('DB_USERNAME'),
    'password'  => env('DB_PASSWORD'),
],

'mysql2' => [
    'driver'    => env('DB_CONNECTION_SECOND'),
    'host'      => env('DB_HOST_SECOND'),
    'port'      => env('DB_PORT_SECOND'),
    'database'  => env('DB_DATABASE_SECOND'),
    'username'  => env('DB_USERNAME_SECOND'),
    'password'  => env('DB_PASSWORD_SECOND'),
], 
```

当你编辑成功时，你可以使用它

```
//shema in migration database
Schema::connection('mysql2')->create('some_table', function($table)
{
$table->increments('id'):
}); 
```

您也可以使用查询生成器定义自定义连接，如下图所示

```
DB::connection('mysql2')->select(...)->(...); 
```

使用 Laravel 口才中的连接模型，您可以将$connection 添加到模型

```
class Post extends Model
{
     protected $connection = 'mysql2';
} 
```

在文件**PostController.php**中，你需要使用
设置与数据库的连接

```
user App\Post;
public function getAllPost()
    {
        $post = new Post;
        $post->setConnection('mysql2');
        $data = $post->find(1);
        return $data;
    } 
```

Post: [在 Laravel 5.8 中连接多个数据库](https://hoanguyenit.com/connecting-multiple-databases-in-laravel-58.html)