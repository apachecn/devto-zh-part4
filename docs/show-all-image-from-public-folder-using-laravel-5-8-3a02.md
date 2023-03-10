# 使用 Laravel 5.8 显示公共文件夹中的所有图像

> 原文：<https://dev.to/skipperhoa/show-all-image-from-public-folder-using-laravel-5-8-3a02>

显示文件夹中的所有图像，我们可以使用 laravel
创建文件**FormController.php**T3】

```
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class FormController extends Controller
{
    public function index(){

        $images = \File::allFiles(public_path('images'));

        return View('pages.form')->with(array('images'=>$images));

    }
} 
```

我们在文件夹 view/pages/form.blade.php
中创建文件**form.blade.php**

```
@extends('layouts.app')

@section('content')

<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-10">
            <form action="">
                <h2>Show All Image from public folder using Laravel</h2>

                <ul>
                @foreach ($images as $image)
                     <li style="width:80px;display:inline-block;margin:5px 0px">
                         <input type="checkbox" name="images[]" value="{{$image->getFilename()}}"/>
                         <img src="{{ asset('images/' . $image->getFilename()) }}" width="50" height="50">
                     </li>
                @endforeach
                </ul>

            </form>
        </div>
    </div>
</div>

@endsection 
```

在 routes/web.php 文件夹中配置 web.php，代码如下

```
Route::prefix('form')->group(function () {
    Route::get('/','FormController@index')->name('form.index');
}); 
```

测试:
**php 匠服**

[![hoanguyenit.com](img/709f88995442fbc5675415bd06269aba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Aq94e_73--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8v3wq0jswm531zqc4ek1.jpg)

帖子:[使用 Laravel 5.8 显示公共文件夹中的所有图片](https://hoanguyenit.com/show-all-image-from-public-folder-using-laravel-58.html)
你可以在这里看到更多帖子:[分享编程知识](https://hoanguyenit.com)