# Laravel 5.8 中的文件管理器

> 原文：<https://dev.to/skipperhoa/filemanger-in-laravel-5-8-2c6h>

安装 laravel-filemanger

# composer 需要 unisharp/laravel-filemanager

Config file config/app.php

//提供者添加第二行
Unisharp \ Laravelfilemanager \ LaravelFilemanagerServiceProvider::class，
Intervention \ Image \ ImageServiceProvider::class，

//别名添加行

Intervention \ Image \ Facades \ Image::class，

如果 Laravel 版本大于 5.5，您不需要命令安装

# php artisan 厂商:publish - tag=lfm_config

# php artisan 厂商:publish - tag=lfm_public

删除缓存:

# php 工匠路线:明确

# php artisan 配置:清除

您可以在 config/lfm.php 中设置 chmod 文件夹上传图像

在 laravel 中安装 auth

# php 工匠制作:Auth

//confing web.php

Route::group([' middleware ' = > ' auth ']，function(){
Route::get('/laravel-show '，function(){
return View(' file manager ')；
})；
Route::get('/laravel-file manager '，' \ uni sharp \ LaravelFilemanager \ Controllers \ LFM controller @ show ')；
Route::post('/laravel-file manager/upload '，' \ uni sharp \ LaravelFilemanager \ Controllers \ upload controller @ upload ')；

```
// list all lfm routes here... 
```

});

您需要在文件夹视图/filemanger.blade.php 中创建文件

var options = {
filebrowserImageBrowseUrl:'/laravel-file manager？type=Images '，
filebrowserImageUploadUrl:'/laravel-file manager/upload？type=Images & _token= '，
filebrowserBrowseUrl:'/laravel-file manager？type=Files '，
filebrowserUploadUrl:'/laravel-file manager/upload？type = Files&_ token = '
}；
cke editor . replace('编辑器'，选项)；

在 laravel 中安装 filemanager 成功

# php 匠服务器

您需要在登录之前进入 laravel，之后您就可以打开浏览器
[http://localhost:8000/laravel-show/](http://localhost:8000/laravel-show/)

帖子:[http://hoanguyenit.com/filemanger-in-laravel-5-8.html](http://hoanguyenit.com/filemanger-in-laravel-5-8.html)