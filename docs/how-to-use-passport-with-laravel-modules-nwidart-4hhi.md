# 如何使用带有 Laravel 模块的 Passport(nwi dart)

> 原文：<https://dev.to/santhoshj/how-to-use-passport-with-laravel-modules-nwidart-4hhi>

让我为刚接触 it 的人介绍一下这两个软件包。

**旅行护照:**

> Laravel 使用 Laravel Passport 使 API 认证变得轻而易举，它在几分钟内为您的 Laravel 应用程序提供了完整的 OAuth2 服务器实现。Passport 建立在 Andy Millington 和 Simon Hamp 维护的 League OAuth2 服务器之上。

注意:我是按照介质文章来配置 Passport - [这里是介质文章，供进一步参考。](https://medium.com/techcompose/create-rest-api-in-laravel-with-authentication-using-passport-133a1678a876)

**Laravel 模块:**

> 是一个 Laravel 包，创建它是为了使用模块管理你的 Laravel 应用程序。一个模块就像一个 Laravel 包，它有一些视图、控制器或模型。这个包在 Laravel 5 中得到支持和测试。
> 
> 这个包是 [pingpong/modules](https://github.com/pingpong-labs/modules) 的重新发布、重新组织和维护版本，不再维护。这个包在 [AsgardCMS](https://asgardcms.com/) 中使用。

在创建企业应用程序时，尽可能将它们开发成模块化的，以使其易于维护，这将非常有用。我正在经历一个场景，其中我必须使用 Laravel 模块实现一个用户模块，并且使用 Passport 进行身份验证。我是这样做的。

**1。开始一个新的 laravel 项目**

```
composer create-project laravel/laravel my-app 
```

为了减少混乱，我将默认迁移移出了这个项目。我们稍后会将它添加回用户模块下的项目中。

```
cd my-app 
```

**2。安装和配置 Laravel 模块:**

```
composer require nwidart/laravel-modules
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" 
```

**3。将模块文件夹添加到 composer.json 中的自动加载数组:**

```
"autoload": {
        "psr-4": {
            "App\\": "app/",
            "Modules\\": "Modules/"
        },
        "classmap": [
            "database/seeds",
            "database/factories"
        ]
    }, 
```

**4。运行自动加载**

```
composer dumpautoload 
```

**5。创建用户模块和模型:**

```
php artisan module:make Users
php artisan module:make-model User Users 
```

**6。在* my-app/Modules/Users/Database/Migrations***下复制迁移

完成 Passport 配置后，我们将设置登录和注册功能。

7 .**。安装和配置 Passport:**

```
composer require laravel/passport 
```

**8。编辑 config/app.php 以添加护照服务提供商**

```
'providers' =>[
Laravel\Passport\PassportServiceProvider::class,
], 
```

**9。运行 migrate and install - Install 将为安全性创建令牌密钥。**

```
php artisan migrate
php artisan passport:install 
```

10。将 app/User.php 移动到模块/用户/实体/User.php

**11。app/Providers/authserviceprovider . PHP**

```
<?php
namespace App\Providers;
use Laravel\Passport\Passport; 
use Illuminate\Support\Facades\Gate; 
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
class AuthServiceProvider extends ServiceProvider 
{ 
    /** 
     * The policy mappings for the application. 
     * 
     * @var array 
     */ 
    protected $policies = [ 
        'App\Model' => 'App\Policies\ModelPolicy', 
    ];
/** 
     * Register any authentication / authorization services. 
     * 
     * @return void 
     */ 
    public function boot() 
    { 
        $this->registerPolicies(); 
        Passport::routes(); 
    } 
} 
```

**12。重要**:在 config/auth.php 中，指定 passport 的 api 认证驱动。此外，在提供者中，让 laravel 知道我们必须使用模块中的用户类作为模型。**不是**默认的 app/User::class。

```
<?php
return ['guards' => [   
    'web' => [   
        'driver' => 'session',   
        'provider' => 'users',   
    ],   
    'api' => [   
        'driver' => 'passport',   
        'provider' => 'users',   
    ],   
],
.
.
.
'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => Modules\Users\Entities\User::class,
        ],

        // 'users' => [
        //     'driver' => 'database',
        //     'table' => 'users',
        // ],
    ], 
```

13。在模块/用户/路由/api.php 中添加 API 路由

```
Route::post('login', 'UsersController@login');
Route::post('register', 'UsersController@register'); 
```

**14。创建用户控制器——当我们创建模型时，Laravel 模块会自己创建它。**

```
<?php

namespace Modules\Users\Http\Controllers;

use Validator;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Modules\Users\Entities\User;
use Illuminate\Routing\Controller;
use Illuminate\Support\Facades\Auth; 

class UsersController extends Controller
{
    public $successStatus = 200;

    /**
    * Handles user logins
    *
    * @return void
    */
    public function login()
    {
        if(Auth::attempt(['email' => request('email'), 'password' => request('password')])){ 
            $user = Auth::user(); 
            $success['token'] =  $user->createToken('my-app')-> accessToken; 
            return response()->json(['success' => $success], $this-> successStatus); 
        } 
        else{ 
            return response()->json(['error'=>'Unauthorised'], 401); 
        } 
    }

    /** 
     * Register api 
     * 
     * @return \Illuminate\Http\Response 
     */ 
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

        /** 
        * details api 
        * 
        * @return \Illuminate\Http\Response 
        */ 
        public function details() 
        { 
            $user = Auth::user(); 
            return response()->json(['success' => $user], $this-> successStatus); 
        } 

    } 
```

15。在命令行中，运行服务器，你将能够使用 Postman 或失眠症对其进行测试。

```
php artisan serve 
```

关于这篇文章，请随时提问。我很乐意帮忙。通过 gmail dot com 联系我- santhoshj。

> 用 [StackEdit](https://stackedit.io/) 写的。