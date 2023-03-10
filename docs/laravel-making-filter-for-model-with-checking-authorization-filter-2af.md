# Laravel:为雄辩模型制作安全过滤器

> 原文：<https://dev.to/mohammadfouladgar/laravel-making-filter-for-model-with-checking-authorization-filter-2af>

## 简介

在上一篇文章中，我介绍了一个包，它允许你在 Laravel 中过滤你的雄辩模型。正如你在这里看到的。

> 这个包的最新版本可以从 Github 资源库获得: [v1.0.1](https://github.com/mohammad-fouladgar/eloquent-builder)

正如我前面解释的，您可能需要在您的项目中过滤一个模型，比如`User`模型。例如，根据性别、年龄、在线/离线状态等过滤用户...。

现在，您可以检查经过身份验证的用户是否有权应用给定的过滤器。例如，您可以确定一个用户是高级帐户还是管理员，可以应用`StatusFilter`来获取在线或离线人员的列表。

下面我们来一个实际的例子，以便更好的理解。

## 设置 EloquentBuilder 包

首先，如果你没有 Laravel，运行下面的命令:

```
$ composer create-project --prefer-dist laravel/laravel authorize-filter 
```

Enter fullscreen mode Exit fullscreen mode

现在转到应用程序的根目录，安装 EloquentBuilder:

```
$ cd authorize-filter
$ composer require mohammad-fouladgar/eloquent-builder 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该在‍‍ `app`目录中创建一个名为`EloquentFilters`的新目录。该目录是模型过滤器的存储位置。

## 创建路线和控制器

在这一步中，我们需要为用户列表创建一个路由。因此，打开您的`routes/web.php`文件，并添加以下路线:

```
Route::get('users', 'UserController@index'); 
```

Enter fullscreen mode Exit fullscreen mode

然后，您应该创建一个名为`UserController`的控制器: