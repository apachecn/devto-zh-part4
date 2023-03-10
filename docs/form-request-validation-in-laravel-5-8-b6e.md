# Laravel 5.8 中的表单请求验证

> 原文：<https://dev.to/skipperhoa/form-request-validation-in-laravel-5-8-b6e>

首先，我们需要安装 laravel 5.8

```
composer create-project --prefer-dist laravel/laravel blog "5.8.*" 
```

继续！在文件夹数据库/迁移表中创建文件迁移帖子

```
php artisan make:migration create_posts_table --create=posts 
```

在文件夹数据库/迁移中打开文件新建，在
后配置以下代码

```
Schema::create('posts', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('title');
    $table->string('keywords');
    $table->string('description');
    $table->string('slug');
    $table->string('image');
    $table->text('body');
    $table->timestamps();
}); 
```

# 创建表单请求验证

```
php artisan make:request FormPost 
```

您在 App\Http\Request 文件夹中看到文件 FormPost，您在
之后配置了以下代码

```
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class FormPost extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'keywords'=>'required',
            'description'=>'required',
            'image' => 'mimes:jpeg,jpg,png,gif|required|max:10000',
            'body' => 'required'
        ];
    }

    public function messages()
    {
        return [
            'title.required' => 'Title is required!',
            'keywords.required' => 'Keywords is required!',
            'description.required' => 'Description is required!',
            'image.required' => 'Image is required!',
            'body.required' =>'Body is required'
        ];
    }
} 
```

# 在 Laravel 中创建模型

```
php artisan make:model Post 
```

```
namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected $fillable = [
        'title', 'keywords', 'description','slug','image','body'
    ];
} 
```

# 在 laravel 中创建模板叶片

我们需要在文件夹视图/页面中创建文件 form-validation.blade.php

```
@extends('layouts.app')
@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-6">
        @if (session('success'))
            <div class="alert alert-success">
                {{ session('success') }}
            </div>
        @endif
            <form action="{{route('formpost.store')}}" method="post" enctype="multipart/form-data">
            @csrf
                <div class="form-group">
                    <label for="title">Title</label>
                    <input type="text" name="title" placeholder="Title" class="form-control"/>
                  @error('title')
                        <small class="form-text text-muted">{{ $message }}</small>
                    @enderror
                </div>
                <div class="form-group">
                    <label for="keywords">keywords</label>
                    <input type="text" name="keywords" placeholder="Keywords" class="form-control"/>
                    @error('keywords')
                        <small class="form-text text-muted">{{ $message }}</small>
                    @enderror
                </div>
                <div class="form-group">
                    <label for="description">Description</label>
                    <input type="text" name="description" placeholder="Description" class="form-control"/>
                    @error('description')
                        <small class="form-text text-muted">{{ $message }}</small>
                    @enderror
                </div>
                <div class="form-group">
                    <label for="image">Image</label>
                    <input type="file" name="image"/>
                    @error('image')
                        <small class="form-text text-muted">{{ $message }}</small>
                    @enderror
                </div>
                <div class="form-group">
                    <label for="body">Body</label>
                    <textarea name="body" cols="30" rows="10" class="form-control">

                    </textarea>
                    @error('body')
                        <small class="form-text text-muted">{{ $message }}</small>
                    @enderror
                </div>
                <div class="form-group">
                    <input type="submit" class="btn btn-success" value="Submit" />
                    <input type="reset" class="btn btn-primary" value="Reset" />
                </div>

            </form> 
        </div>
    </div>
</div> 
```

# 在 Laravel 中创建控制器

```
php artisan make:controller FormValidationController.php 
```

当运行上面的命令后，你到文件夹 App\Http\Controller\并打开文件 create new，在
后配置下面的代码

```
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests\FormPost;
use App\Post;
class FormValidationController extends Controller
{
    public function index(){
        return View('pages.form-validation');
    }
    /**
     * Store
     */
    public function store(FormPost $request){
        $validated = $request->validated();
        $validated['slug']=str_slug($request->title);
       // dd($validated);
        Post::create($validated);
        return redirect('/form-validation')->with('success', 'Add Success!');

    }
} 
```

你需要包括一张邮寄到 FormValidationController.php 的表格，并使用 App\Post

# 在 Laravel 中配置路线

你对文件夹 Routes 和 web.php 打开文件，在
后添加以下代码

```
Route::prefix('form-validation')->group(function () {
    Route::get('/','FormValidationController@index')->name('formpost.index');
    Route::post('/post','FormValidationController@store')->name('formpost.store');
}); 
```

# 测试表单请求在 Laravel 中验证

```
php artisan server 
```

[http://localhost:8000/form-validation](http://localhost:8000/form-validation)

post:[Laravel 5.8 中的表单请求验证](https://hoanguyenit.com/form-request-validation-in-laravel-58.html)