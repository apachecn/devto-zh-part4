# 使用 Laravel (5.8) Passport 进行 API 认证

> 原文：<https://dev.to/sandeepbalachandran/api-authentification-with-laravel-5-8-passport-56d7>

嘿抄写员们，

没错。我很久没有给盖茨比上课了。我得做点什么才能回到正轨。

[![lazy](img/b69ab43ed5117e007dd88f0906042519.png)](https://i.giphy.com/media/qd7Wlk9nx8VwI/giphy.gif)

但是等等，我有东西要给`future me`或者`you`看，

你有没有想过如何保护 laravel apis？为什么你会？对吗？你不需要。但我知道。在工作。这就是为什么我一直使用“tymondesigns/jwt-auth”包。

所以今天我想我可以用“护照”来代替智威汤逊。我发现 passport 用的是 jwt。Passport 是一个实现 Oauth2 和 jwt 的包。

哦差点忘了。`Motivation`时间。如果你不想被激励，那就跳过这一步。

> 如果你今天过得不好，想想你今天吃的早餐。(除非你只是因为没吃早餐而过了糟糕的一天。讽刺的是)。你吃早餐，所以即使在一天开始的时候你也不会挨饿。有些人不吃早饭就去上班。不只是因为他们没有时间，而是因为他们负担不起。所以你今天有吃的就很幸运了。

# 主要内容来自这里

## 目录

*   描述
*   通过 composer 安装 Laravel 5.8
*   配置到数据库的连接
*   安装软件包
*   运行迁移
*   安装 Passport
*   Passport 配置
*   创建 API 路线

<center>

# <u>描述</u>

</center>

## 什么是 [Laravel 护照](https://laravel.com/docs/5.8/passport)？(只是谷歌)

API 通常使用令牌来验证用户，并且不维护请求之间的会话状态。Laravel 使用 Laravel Passport 使 API 认证变得轻而易举，它在几分钟内为您的 Laravel 应用程序开发提供了完整的 OAuth2 服务器实现。

开始吧！

<center>

# <u>通过 composer 安装 Laravel 5.8:</u> 

</center>

```
composer create-project --prefer-dist laravel/laravel api-authentification</code> 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>在我们的中配置数据库连接。环境文件:</u>

</center>

```
 DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=api-authentification
DB_USERNAME=root
DB_PASSWORD= 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>安装包</u>

</center>

```
composer require laravel/passport 
```

Enter fullscreen mode Exit fullscreen mode

成功安装软件包后，打开`config/app.php`文件并添加服务提供商。

```
'providers' =>[
Laravel\Passport\PassportServiceProvider::class,
], 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>运行迁移并安装</u>

</center>

在护照服务提供商注册后，我们需要运行迁移命令，当您运行迁移命令表时，将在数据库中设置(您已经知道什么是迁移。对吗？对吗？)

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

下一次安装 passport 时，它将为安全性创建令牌密钥。所以让我们运行下面的命令:

<center>

# <u>安装护照</u>

</center>

```
php artisan passport:install 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>护照配置</u>

</center>

在这一步中，我们必须对三个文件进行配置

*   模型
*   互联网服务商
*   授权配置文件

所以你只需要跟踪文件变化。

在模型中，我们将添加`HasApiTokens`类别的护照，

在`AuthServiceProvider`中，我们将添加`Passport::routes()`，

在`auth.php`中，我们增加了 API auth 配置。

app/User.php

```
 <?php
namespace App;
use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;
class User extends Authenticatable
{
  use HasApiTokens, Notifiable;

protected $fillable = [
'name', 'email', 'password',
];

protected $hidden = [
'password', 'remember_token',
];
} 
```

Enter fullscreen mode Exit fullscreen mode

<center>app/Providers/AuthServiceProvider.php</center>

```
<?php
namespace App\Providers;
use Laravel\Passport\Passport; 
use Illuminate\Support\Facades\Gate; 
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
class AuthServiceProvider extends ServiceProvider 
{ 

    protected $policies = [ 
        'App\Model' => 'App\Policies\ModelPolicy', 
    ];

    public function boot() 
    { 
        $this->registerPolicies(); 
        Passport::routes(); 
    } 
} 
```

Enter fullscreen mode Exit fullscreen mode

config/auth.php

```
 <?php
return [
'guards' => [ 
        'web' => [ 
            'driver' => 'session', 
            'provider' => 'users', 
        ], 
        'api' => [ 
            'driver' => 'passport', 
            'provider' => 'users', 
        ], 
    ], 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>创建 API 路由</u>

</center>

让我们在 api.php 文件上添加一些路线

<center>routes/api.php</center>

```
 <?php

Route::post('login', 'UserController@login');
Route::post('register', 'UserController@register');
Route::group(['middleware' => 'auth:api'], function()
{
   Route::post('details', 'UserController@details');
}); 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>创建控制器</u>

</center>

在最后一步，我们必须创建一个新的控制器和三个 API 方法，

```
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request; 
use App\Http\Controllers\Controller; 
use App\User; 
use Illuminate\Support\Facades\Auth; 
use Validator;
class UserController extends Controller 
{
public $successStatus = 200;

    public function login(){ 
        if(Auth::attempt(['email' => request('email'), 'password' => request('password')])){ 
            $user = Auth::user(); 
            $success['token'] =  $user->createToken('MyApp')-> accessToken; 
            return response()->json(['success' => $success], $this-> successStatus); 
        } 
        else{ 
            return response()->json(['error'=>'Unauthorised'], 401); 
        } 
    }

    public function register(Request $request) 
    { 
        $validator = Validator::make($request->all(), [ 
            'name' => 'required', 
            'email' => 'required|email', 
            'password' => 'required', 
            'c_password' => 'required|same:password', 
        ]);
if ($validator->fails()) { 
            return response()->json(['error'=>$validator->errors()], 401);            
        }
$input = $request->all(); 
        $input['password'] = bcrypt($input['password']); 
        $user = User::create($input); 
        $success['token'] =  $user->createToken('MyApp')-> accessToken; 
        $success['name'] =  $user->name;
return response()->json(['success'=>$success], $this-> successStatus); 
    }

    public function details() 
    { 
        $user = Auth::user(); 
        return response()->json(['success' => $user], $this-> successStatus); 
    } 
} 
```

Enter fullscreen mode Exit fullscreen mode

<center>Register response</center>

![register](img/430c68363586be6d64c4833ca0d4b9ad.png)

<center>Login Response</center>

![login](img/248d3d5df45128e19e1c7125ec653b64.png)

现在，我们将测试详细信息 api，在此 API 中，您必须设置两个标题，如下所示:

headers ' = >[
' Accept ' =>' application/JSON '，
'Authorization' = > 'Bearer '。$accessToken，

]

所以，确保以上标题，否则，你不能得到用户的详细资料。

<center>Fetch Data</center>

![details](img/e623d310b8ba9f1343f393bf75907fbc.png)

我想就是这样了。

[![wohoo](img/03daec7df83df7763f77a7f5987c6bd1.png)](https://i.giphy.com/media/nuQ79LwWfmXeM/giphy.gif)

这是一个很长的帖子。所以你可能会在这里发现错误。在某些时候，如果你发现自己有什么东西，请自己保存。