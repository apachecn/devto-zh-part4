# 让我们用 Laravel 建立一个超级简单的推荐系统

> 原文：<https://dev.to/simioluwatomi/let-s-build-a-super-simple-referral-system-with-laravel-1o3h>

不久前，我被要求构建一个推荐系统，作为 Laravel web 应用程序特性集的一部分。推荐系统对我来说并不陌生，但这是我第一次建立这样的系统。推荐系统用于鼓励应用程序的用户邀请其他用户，并在人们使用其推荐链接注册该应用程序时获得奖励。

我真的想让事情变得超级简单，但是我在网上读到的大多数关于推荐系统的东西要么太复杂了，要么对我的用例来说太复杂了。经过一些修修补补，我想出了这么简单的东西，我很惊讶它的工作。我们将一起建立一个推荐系统，并检查我们在这个过程中必须做出的一些决定和权衡。

首先，创建一个新的 Laravel 项目。我给我的取名`simple-referral`。让 composer 或 laravel 安装程序施展它的魔法，你应该有一个准系统的 Laravel 应用程序在你的指尖。配置应用数据库，运行`php artisan make:auth`，让我们开始破解。

推荐流看起来像这样

1.  用户单击一个链接，将他们带到应用程序注册页面。
2.  当他们注册时，他们的注册应该链接到他们在注册中使用的推荐链接的用户。
3.  推荐链接用于注册的用户应该得到通知，有人使用他们的推荐链接注册。
4.  新用户应该有自己的推荐链接。

我们可以用这个场景来模拟我们雄辩的关系，如下所示

*   一个用户可以被另一个用户推荐。
*   一个用户可以引用多个用户。

本质上，我们有一种`One-To-Many`关系。打开`create_users_table`迁移并进行如下更改。

```
$table->unsignedBigInteger('referrer_id'); 
```

但是等等！并不是所有的用户都会有一个 referrer，所以让我们来制作 referrer 列`nullable`。同时，让我们也给`users`表添加一个`foreign_key`约束。`create_users_table`迁移现在应该是这样的。

```
 $table->bigIncrements('id');
    $table->unsignedBigInteger('referrer_id')->nullable();
    $table->foreign('referrer_id')->references('id')->on('users');
    $table->string('name');    
    $table->string('email')->unique();            
    $table->string('password');
    $table->rememberToken();
    $table->timestamps(); 
```

迁移数据库，并在`User.php`中进行如下更改。以防您不知道，您只是设置了一个自我引用表。

```
 /**
 * The attributes that are mass assignable.
 *
 * @var array
 */
protected $fillable = [
    'referrer_id', 'name', 'email', 'password',
];

/**
 * A user has a referrer.
 *
 * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
 */
public function referrer()
{
    return $this->belongsTo(User::class, 'referrer_id', 'id');
}

/**
 * A user has many referrals.
 *
 * @return \Illuminate\Database\Eloquent\Relations\HasMany
 */
public function referrals()
{
    return $this->hasMany(User::class, 'referrer_id', 'id');
} 
```

#### 现在让我们考虑一下推荐链接，以及我们希望我们的推荐链接是什么样子。

对于我正在构建的应用程序，业务需求之一是用户有一个容易记住的推荐链接。久而久之，我明白了，容易记住的网址总是很短的。这是有例外的，但正如我所说的，他们是例外。经过一番反复，我们同意推荐链接应该是这样的`https://simple-referral/register?ref=username`。这将要求用户有一个`unique`，`unchanging`用户名。

为了满足这个业务需求，将`create_users_table`迁移修改为

```
//...
$table->string('username')->unique(); 
```

然后打开`User.php`并进行以下更改

```
protected $fillable = [
    // ...
    'username',
];

/**
 * The accessors to append to the model's array form.
 *
 * @var array
 */
protected $appends = ['referral_link'];

/**
 * Get the user's referral link.
 *
 * @return string
 */
public function getReferralLinkAttribute()
{
    return $this->referral_link = route('register', ['ref' => $this->username]);
} 
```

我们没有将用户的引用链接存储在数据库中，而是使用一个访问器来计算它。然后，我们将属性添加到用户模型的数组或 JSON 表单中。要阅读更多关于访问器和雄辩序列化的内容，点击[这里](https://laravel.com/docs/5.8/eloquent-mutators#defining-an-accessor)和[这里](https://laravel.com/docs/5.8/eloquent-serialization#appending-values-to-json)。

您对推荐链接的要求可能不同，这里有另一个选项可供您考虑。在这种情况下，编辑`create_users_table`像这样迁移

```
 //...
 $table->string('referral_token')->unique(); 
```

然后打开`User.php`并

*   将`referral_token`添加到可填写字段。
*   将`referral_link`追加到模型的数组或 JSON 表单中。
*   使用用户的`referral_token`而不是`username`来计算引用链接属性。

这种方法将允许用户拥有可编辑的用户名，但是您必须考虑如何为您的用户生成唯一的引用令牌。我让你自己去想。

用户模型已经准备好了，数据库结构支持我们的转诊系统；让我们继续我们的控制器！

打开`App\Http\Controllers\Auth\RegisterController.php`。控制器使用`Illuminate\Foundation\Auth\RegistersUsers` trait 来执行它的大部分功能，这意味着我们可以覆盖控制器中 trait 的方法。

```
/**
 * Show the application registration form.
 *
 * @return \Illuminate\Http\Response
 */
public function showRegistrationForm(Request $request)
{
    if ($request->has('ref')) {
        session(['referrer' => $request->query('ref')]);
    }

    return view('auth.register');
}

/**
 * Get a validator for an incoming registration request.
 *
 * @param  array  $data
 * @return \Illuminate\Contracts\Validation\Validator
 */
protected function validator(array $data)
{
    return Validator::make($data, [
        'name' => ['required', 'string', 'max:255'],
        'username' => ['required', 'string', 'unique:users', 'alpha_dash', 'min:3', 'max:30'],
        'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
        'password' => ['required', 'string', 'min:8', 'confirmed'],
    ]);
}

/**
 * Create a new user instance after a valid registration.
 *
 * @param array $data
 *
 * @return \App\Models\User
 */
protected function create(array $data)
{
    $referrer = User::whereUsername(session()->pull('referrer'))->first();

    return User::create([
        'name'        => $data['name'],
        'username'    => $data['username'],
        'email'       => $data['email'],
        'referrer_id' => $referrer ? $referrer->id : null,
        'password'    => Hash::make($data['password']),
    ]);
} 
```

看起来很多，但我们只做了三件事

*   如果注册路由包含一个`ref`查询参数，则设置一个名为“referrer”的会话变量。
*   创建用户前验证表单域。
*   创建用户时从会话中检索 referrer，然后将其 id 设置为正在创建的用户的 referrer_id。

为了完善我们的简单推荐系统，当新用户使用他们的推荐链接注册时，用户应该得到通知。为了尽可能灵活和强大，我将使用一个`Notification`而不是一个`Mailable`。运行`php artisan make:notification nameOfTheNotification`生成通知。我将命名我的`ReferralBonus`。我将让您决定通过什么渠道发送通知。

像这样打开`RegisterController.php`并覆盖`registered`方法

```
/**
 * The user has been registered.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  mixed  $user
 * @return mixed
 */
protected function registered(Request $request, $user)
{
    if ($user->referrer !== null) {
        Notification::send($user->referrer, new ReferrerBonus($user));
    }

    return redirect($this->redirectPath());
} 
```

在这里，如果新注册的用户有推荐人，我们会向其发送通知。然后我们重定向到在`RegisterController`上定义的重定向路径。**注意:不要忘记导入所有必需的类。**

现在让我们来处理一下我们的前端。打开`register.blade.php`并为用户名添加一个表单输入字段。

```
//...
<div class="form-group row">
    <label for="name" class="col-md-4 col-form-label text-md-right">{{ __('Username') }}</label>

    <div class="col-md-6">
        <input id="username" type="text" class="form-control @error('name') is-invalid @enderror" name="username" value="{{ old('username') }}" required autocomplete="username">

        @error('username')
            <span class="invalid-feedback" role="alert">
                <strong>{{ $message }}</strong>
            </span>
        @enderror
    </div>
</div> 
```

现在，让我们修改`home.blade.php`来显示一些用户信息

```
<div class="card-body">
    @if (session('status'))
        <div class="alert alert-success" role="alert">
            {{ session('status') }}
        </div>
    @endif

    You are logged in!

    <ul class="list-group mt-3">
        <li class="list-group-item">Username: {{ Auth::user()->username }}</li>
        <li class="list-group-item">Email: {{ Auth::user()->email }}</li>
        <li class="list-group-item">Referral link: {{ Auth::user()->referral_link }}</li>
        <li class="list-group-item">Referrer: {{ Auth::user()->referrer->name ?? 'Not Specified' }}</li>
        <li class="list-group-item">Refferal count: {{ count(Auth::user()->referrals)  ?? '0' }}</li>
    </ul>
</div> 
```

我们的简单转诊系统现在已经完成。如果您在学习过程中遇到困难，您可以浏览我在 Github 上为本教程创建的存储库

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ simioluwatomi ](https://github.com/simioluwatomi) / [简单转诊](https://github.com/simioluwatomi/simple-referral)

### 用 Laravel 构建的一个超级简单的推荐系统

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Image of User Details](img/103506d37729c82c5371adec309b16e3.png)](https://raw.githubusercontent.com/simioluwatomi/simple-referral/master/README.MD/User-Details.png)

这个项目演示了一个用 Laravel 构建的超级简单的推荐系统。要阅读我为此写的教程，点击[这里](https://dev.to/simioluwatomi/let-s-build-a-super-simple-referral-system-with-laravel-1o3h)。

运行项目

*   将其克隆到您的计算机上。
*   运行`composer install`安装应用程序依赖项。
*   将`.env.example`复制到`.env`。
*   运行`php artisan key:generate`生成应用密钥。
*   将数据库凭证添加到`.env`。
*   运行`php artisan migrate`来迁移数据库。
*   为了运行包含的测试，为在`config/database.php`到`.env`中定义的`testing`数据库连接添加数据库凭证。

</article>

[View on GitHub](https://github.com/simioluwatomi/simple-referral)