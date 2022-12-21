# 带有 Passport 和 Lighthouse PHP 的 GraphQL Auth

> 原文：<https://dev.to/joselfonseca/graphql-auth-with-passport-and-lighthouse-php-14g5>

用 Lighthouse PHP 和 Laravel 编写一个 GraphQL API 真的很有趣，但是你必须做的事情是添加认证。这是 Laravel 已经用 Passport 完成的一个常见任务，但是如果您想让您的登录和刷新令牌端点成为突变，该怎么办呢？这实际上是一个好主意，因为除了 GraphQL API 之外，您实际上不必记录您的认证机制。你也可以有突变。这就是我写一个名为 Lighthouse GraphQL Passport Auth 的小软件包的灵感所在，它起初看起来是一个很长的名字，说实话，的确如此。但是，让我们深入研究如何使用它，我们担心名称稍后 XD。

## 安装和配置

我们需要做的第一件事是安装软件包，现在请记住，Lighthouse PHP 和 passport 都是必需的，所以我们为什么不一次性安装它呢？在全新的 laravel 应用程序中，请输入:

```
composer require nuwave/lighthouse laravel/passport joselfonseca/lighthouse-graphql-passport-auth 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们从 Laravel Passport 开始配置每个包，因为我们将首先运行迁移。

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/b58402375f33ac5d953ee1fe973e67e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0B0G1O5R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/is0nvim0wa2ibnl82kta.png)

然后我们应该运行 passport 安装命令。

```
php artisan passport:install 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/a8421f03a13c195b539d85098bafa23e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IOdBYHYU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0tq7angibakuy1mmhlk5.png)

然后将 HasApiTokens 特征添加到您的用户模型中

```
 namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该注册 passport 路由，因为我们仍然需要它们能够在内部获得令牌。

```
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

Enter fullscreen mode Exit fullscreen mode

一旦我们有了这个，让我们将 passport 驱动程序添加到 config/auth.php 文件中的 API guard。

```
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

这应该结束了 passport 的配置。

现在让我们配置并安装 Lighthouse PHP 包。因为它已经从 composer 中拉出，所以我们只需要运行以下命令来发布默认模式。

```
php artisan vendor:publish --tag=lighthouse-schema 
```

Enter fullscreen mode Exit fullscreen mode

该命令将在 graphql/schema.graphql 中创建一个具有以下模式的文件

```
"A  datetime  string  with  format  `Y-m-d  H:i:s`,  e.g.  `2018-01-01  13:00:00`."  scalar  DateTime  @scalar(class:  "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\DateTime")  "A  date  string  with  format  `Y-m-d`,  e.g.  `2011-05-23`."  scalar  Date  @scalar(class:  "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\Date")  type  Query  {  users:  [User!]!  @paginate(type:  "paginator"  model:  "App\\User")  user(id:  ID  @eq):  User  @find(model:  "App\\User")  }  type  User  {  id:  ID!  name:  String!  email:  String!  created_at:  DateTime!  updated_at:  DateTime!  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你有这个文件，你应该准备好继续 Lighthouse GraphQL Passport Auth 包。

要获得所需的值，需要打开数据库客户机，从 oauth_clients 表中获取密码 oauth client，获得客户机 id 和密码，并将它们放入。环境文件

```
PASSPORT_CLIENT_ID=2
PASSPORT_CLIENT_SECRET=69YJomGV9plchWIkGD2PyKBBTHfkNA7H83iYGc6j 
```

Enter fullscreen mode Exit fullscreen mode

完成后，发布包配置和默认模式。

```
php artisan vendor:publish --provider="Joselfonseca\LighthouseGraphQLPassport\Providers\LighthouseGraphQLPassportServiceProvider" 
```

Enter fullscreen mode Exit fullscreen mode

这个命令应该发布两个文件，config/light house-graph QL-passport . PHP 和 graphql/auth.graphql 文件。为了方便起见，为了能够更好地控制身份验证模式，让我们通过将模式属性的值更改为已发布文件的路径来更新配置文件以使用已发布的模式，如下所示:

```
'schema' => base_path('graphql/auth.graphql') 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们在需要扩展模式或对解析器进行更改时操作模式。

现在让我们从 auth 模式中删除用户类型，因为在之前导出的默认模式中已经有了它。转到 graphql/auth/graphql 文件并删除以下类型

```
type  User  {  id:  ID!  name:  String!  email:  String!  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们这样做了，我们需要向模式添加一个默认的突变，这样 auth 就可以扩展它，或者我们可以简单地将 auth 突变移动到主模式文件中的突变类型。所以在 graphql/auth.graphql 文件中查找这段代码，并将其移动到 graphql/schema.graphql 文件

```
extend  type  Mutation  {  login(input:  LoginInput  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Login@resolve")  refreshToken(input:  RefreshTokenInput  @spread):  RefreshTokenPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\RefreshToken@resolve")  logout:  LogoutResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Logout@resolve")  forgotPassword(input:  ForgotPasswordInput!  @spread):  ForgotPasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\ForgotPassword@resolve")  updateForgottenPassword(input:  NewPasswordWithCodeInput  @spread):  ForgotPasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\ResetPassword@resolve")  register(input:  RegisterInput  @spread):  RegisterResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Register@resolve")  socialLogin(input:  SocialLoginInput!  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\SocialLogin@resolve")  verifyEmail(input:  VerifyEmailInput!  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\VerifyEmail@resolve")  updatePassword(input:  UpdatePassword!  @spread):  UpdatePasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\UpdatePassword@resolve")  @guard(with:  ["api"])  } 
```

Enter fullscreen mode Exit fullscreen mode

然后去掉扩展字

```
type  Mutation  {  login(input:  LoginInput  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Login@resolve")  refreshToken(input:  RefreshTokenInput  @spread):  RefreshTokenPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\RefreshToken@resolve")  logout:  LogoutResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Logout@resolve")  forgotPassword(input:  ForgotPasswordInput!  @spread):  ForgotPasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\ForgotPassword@resolve")  updateForgottenPassword(input:  NewPasswordWithCodeInput  @spread):  ForgotPasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\ResetPassword@resolve")  register(input:  RegisterInput  @spread):  RegisterResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Register@resolve")  socialLogin(input:  SocialLoginInput!  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\SocialLogin@resolve")  verifyEmail(input:  VerifyEmailInput!  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\VerifyEmail@resolve")  updatePassword(input:  UpdatePassword!  @spread):  UpdatePasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\UpdatePassword@resolve")  @guard(with:  ["api"])  } 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该有这样的模式文件

*graph QL/auth . graph QL*T2】

```
input  LoginInput  {  username:  String!  password:  String!  }  input  RefreshTokenInput  {  refresh_token:  String  }  type  User  {  id:  ID!  name:  String!  email:  String!  }  type  AuthPayload  {  access_token:  String  refresh_token:  String  expires_in:  Int  token_type:  String  user:  User  }  type  RefreshTokenPayload  {  access_token:  String!  refresh_token:  String!  expires_in:  Int!  token_type:  String!  }  type  LogoutResponse  {  status:  String!  message:  String  }  type  ForgotPasswordResponse  {  status:  String!  message:  String  }  type  RegisterResponse  {  tokens:  AuthPayload  status:  RegisterStatuses!  }  type  UpdatePasswordResponse  {  status:  String!  message:  String  }  enum  RegisterStatuses  {  MUST_VERIFY_EMAIL  SUCCESS  }  input  ForgotPasswordInput  {  email:  String!  @rules(apply:  ["required",  "email"])  }  input  NewPasswordWithCodeInput  {  email:  String!  @rules(apply:  ["required",  "email"])  token:  String!  @rules(apply:  ["required",  "string"])  password:  String!  @rules(apply:  ["required",  "confirmed",  "min:8"])  password_confirmation:  String!  }  input  RegisterInput  {  name:  String!  @rules(apply:  ["required",  "string"])  email:  String!  @rules(apply:  ["required",  "email",  "unique:users,email"])  password:  String!  @rules(apply:  ["required",  "confirmed",  "min:8"])  password_confirmation:  String!  }  input  SocialLoginInput  {  provider:  String!  @rules(apply:  ["required"])  token:  String!  @rules(apply:  ["required"])  }  input  VerifyEmailInput  {  token:  String!  }  input  UpdatePassword  {  old_password:  String!  password:  String!  @rules(apply:  ["required",  "confirmed",  "min:8"])  password_confirmation:  String!  } 
```

Enter fullscreen mode Exit fullscreen mode

*graph QL/schema . graph QL*T2】

```
"A  datetime  string  with  format  `Y-m-d  H:i:s`,  e.g.  `2018-01-01  13:00:00`."  scalar  DateTime  @scalar(class:  "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\DateTime")  "A  date  string  with  format  `Y-m-d`,  e.g.  `2011-05-23`."  scalar  Date  @scalar(class:  "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\Date")  type  Query  {  users:  [User!]!  @paginate(type:  "paginator"  model:  "App\\User")  user(id:  ID  @eq):  User  @find(model:  "App\\User")  }  type  Mutation  {  login(input:  LoginInput  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Login@resolve")  refreshToken(input:  RefreshTokenInput  @spread):  RefreshTokenPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\RefreshToken@resolve")  logout:  LogoutResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Logout@resolve")  forgotPassword(input:  ForgotPasswordInput!  @spread):  ForgotPasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\ForgotPassword@resolve")  updateForgottenPassword(input:  NewPasswordWithCodeInput  @spread):  ForgotPasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\ResetPassword@resolve")  register(input:  RegisterInput  @spread):  RegisterResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\Register@resolve")  socialLogin(input:  SocialLoginInput!  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\SocialLogin@resolve")  verifyEmail(input:  VerifyEmailInput!  @spread):  AuthPayload!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\VerifyEmail@resolve")  updatePassword(input:  UpdatePassword!  @spread):  UpdatePasswordResponse!  @field(resolver:  "Joselfonseca\\LighthouseGraphQLPassport\\GraphQL\\Mutations\\UpdatePassword@resolve")  @guard(with:  ["api"])  }  type  User  {  id:  ID!  name:  String!  email:  String!  created_at:  DateTime!  updated_at:  DateTime!  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要安装 GraphQL playground，我们将使用它向我们的 GraphQL 服务器发送查询和变化，为此运行下面的命令

```
composer require mll-lab/laravel-graphql-playground 
```

Enter fullscreen mode Exit fullscreen mode

这是准备帐篷了！

## 检测 auth 突变

让我们从运行 PHP 开发服务器
开始

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

这将在我们的本地主机中打开端口 8000，以便我们运行项目，让我们导航到[http://127 . 0 . 0 . 1:8000/graph QL-playground](http://127.0.0.1:8000/graphql-playground)，您应该会看到这样的操场

[![](img/0c6174e4f006fa0da8ec37ae97716da6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNstPYHV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4l3u8drtbdkzsecj93i.png)

现在让我们使用一个简单的控制台命令创建一个用户，这样我们就可以使用我们的 graphql API 登录了。在 routes/console.php 文件中输入

```
Artisan::command('user', function () {
    \App\User::create([
        'name' => 'Jose Fonseca',
        'email' => 'myemail@email.com',
        'password' => bcrypt('123456789qq')
    ]);
})->describe('Create sample user'); 
```

Enter fullscreen mode Exit fullscreen mode

然后在控制台运行命令

```
php artisan user 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个要测试的用户，让我们在 GraphQL 操场上运行下面的变异

```
mutation  {  login(input:  {  username:  "myemail@email.com",  password:  "123456789qq"  })  {  access_token  refresh_token  expires_in  token_type  user  {  id  email  name  created_at  updated_at  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这应该会给您以下响应

```
{  "data":  {  "login":  {  "access_token":  "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6Ijk3NDE3MTI1YjBmNjQyZDNkZDljMDkwOGJiNTM2NDQ5YTYwZWU5ZTQ0ZTBkYzEzY2E5M2FhNGFjODI5ZWRiNzkwMWZhZmY5NzVhZTc5MjRiIn0.eyJhdWQiOiIyIiwianRpIjoiOTc0MTcxMjViMGY2NDJkM2RkOWMwOTA4YmI1MzY0NDlhNjBlZTllNDRlMGRjMTNjYTkzYWE0YWM4MjllZGI3OTAxZmFmZjk3NWFlNzkyNGIiLCJpYXQiOjE1NjE0MDkyMDMsIm5iZiI6MTU2MTQwOTIwMywiZXhwIjoxNTkzMDMxNjAzLCJzdWIiOiIxIiwic2NvcGVzIjpbXX0.CZOpXN0mFHXAQexpA4-FPMOcV-bdZRJD7zUK7guzethg9KUMrALhzgwfQZJv3w74Gnj54aAbdswHBwZEt9DREyEer7Ht0c8108amOllPqb-ydLET1RL1oYCE9H9vvUK0ZafLp09pMrXKot6UcViGOy97KF7YilAvaFfyGxSOmTTZyn0noe9F2ztIOPd3u9XuPuTR5yL-NqHufTTtyJkdQ2xPo03bF4tRfpMXQ5prnIJi4rxmBkpASwMwVraL4lVSZg_9STWMxXWWFdvmXydkNUtQSAftQyHmwMy33OOTxRtFRDN_1Y9wW7U9okVRM-gindkx0o_EB7ekcP1mvHc2PwxwWPMfFxezex98wYX3jTo8y7CN4vDrgUdXqrKkFc6JzwBgn8q_f7c5SzbzxR824h6ujFzSgMaUk_8zKtHX_qgDqaqPVzTebazQ0Pu9PNoYcCkQi5bNldCGuuaMsMxz3H-CWstR4_pAj9_jeKdvC5MA0OkQ30b3RlSmhSqb65LfZEU-y3wG62FKHD49JxBOpPh_Ga8SOQvfOCIL3SzURX9uOvSgcprQqLBYkhJNJC0gobAFgrKHbDhrBVvGH5U4BbIPVX-gnhR44aoyIf8sXFJQkPJJ7-p8HrCqDqjahrlDXsf4DRZGaVJJFhX2VcWAkCcQA2yJ1LSS1XpNU5oL-x4",  "refresh_token":  "def502004531ba2a174546464239e59c3d07332f8aec44bf95db59ba578bcb58c1986dd494d887c79282daf7379d2959b35816e07245ad237e5fa5e6b3ea1ff19ca0058aa82abc4d0abad427eb045b894c7591e4e248d684ea0c6772011ac1723d3d63805e0e148ec0875277072e39d9ecafcbe55d29b28607dc3ea9f85bdc0ca88aadca41b97e8f061b9b326dc922708f6826aee7e2e3296891e923fd9b5386b4c99d46e9832cb8e191c63bb31394a3dc92a8fc1b907925e9c1888d8b0c4d8c30ea330785dd429623e93979670856fdb5bf80c6c8cac08dc7d040d796e535aa082e241dd38b4ab7a4b0055d554cd85888533c76af594bed20dddcded751bfecd0ffdb741169f204c732cef0e2c49c26ec81de9dd8099b5ab1a481283f70c9685a11ca7018c259688601359722aee0f527c95b8b48062be81d414ce3e10a334d8b5c4f64c25b248c0b76bc40596b99580e38310e7d3a4e82c910276645101d821f",  "expires_in":  31622400,  "token_type":  "Bearer",  "user":  {  "id":  "1",  "email":  "myemail@email.com",  "name":  "Jose Fonseca",  "created_at":  "2019-06-24 20:43:53",  "updated_at":  "2019-06-24 20:43:53"  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您现在可以在 GraphQL API 中使用这个令牌了！

## 测试受保护的查询

让我们修改用户查询，要求使用 passport 进行身份验证，为此，打开 graphql/schema.graphql 文件，用下面的
更新用户查询

```
users:  [User!]!  @guard(with:  ["api"])  @paginate(type:  "paginator"  model:  "App\\User") 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试运行用户查询

```
{  users(count:  20){  data  {  id  email  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这应该会给你一个类似下面的认证错误

```
{  "errors":  [  {  "debugMessage":  "Unauthenticated.",  "message":  "Internal server error",  "extensions":  {  "category":  "internal"  },  "locations":  [  {  "line":  2,  "column":  3  }  ],  "path":  [  "users"  ],  "trace":  [  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

这意味着查询需要在请求中包含访问令牌。为此，在 GraphQL Playground 中添加带有我们之前生成的令牌的授权头

[![](img/3aa60839a62e9ef54798570a2eec2964.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tLakI4SS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9k9pj0rrmtqpvjrsntpv.png)

重新运行查询，您应该会得到很好的结果！

[![](img/83e260064f04a90d0e47ce5a40a9b716.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_tsg61u---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2lekmp2riex92m21vpsy.png)

如您所见，查询受到了保护，现在可以在 GraphQL API 中使用身份验证。

现在开始尝试其他可用的变化，比如刷新令牌或重置密码。

如果你喜欢这个小软件包，请在 Github 中启动它，如果遇到任何问题，请告诉我。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ joselfonseca ](https://github.com/joselfonseca) / [灯塔-graph QL-护照-授权](https://github.com/joselfonseca/lighthouse-graphql-passport-auth)

### 添加 GraphQL 突变以从 https://lighthouse-php.com/的 passport 获取令牌

<article class="markdown-body entry-content container-lg" itemprop="text">

# Lighthouse GraphQL 护照认证(拉勒维尔^7.0 /灯塔^4.0)

[![Total Downloads](img/780ef05c7f57d3824161458395ff94c2.png)](https://packagist.org/packages/joselfonseca/lighthouse-graphql-passport-auth)[![StyleCI](img/a07a31cfe315736063bce1bd6c3d499a.png)](https://styleci.io/repos/166881918)[![License](img/d5af76ce59814eedca9136546295f533.png)](https://packagist.org/packages/laravel/framework)

使用 Lighthouse PHP 实现 Laravel Passport 的 GraphQL 突变。

## 辅导的

安装使用可以看[本教程](https://lighthouse-php-auth.com/tutorials/getting-started/)。

## 证明文件

在[文档网站](https://lighthouse-php-auth.com/)中找到详细的文档

## 更改日志

请看发布页面[https://github . com/joselfonseca/light house-graph QL-passport-auth/releases](https://github.com/joselfonseca/lighthouse-graphql-passport-auth/releases)

## 试验

若要运行此包中的测试，请导航到项目的根文件夹并运行

```
    composer install
```

Enter fullscreen mode Exit fullscreen mode

然后

```
    vendor/bin/phpunit
```

Enter fullscreen mode Exit fullscreen mode

## 贡献的

详见[投稿](https://github.com/joselfonseca/lighthouse-graphql-passport-authCONTRIBUTING.md)。

## 安全性

如果您发现任何与安全相关的问题，请发送电子邮件至 ditecnologia dot com，而不是使用问题跟踪器。

## 信用

*   何塞·路易斯·丰塞卡
*   [所有贡献者](https://github.com/joselfonseca/lighthouse-graphql-passport-auth../../contributors)

## 许可证

麻省理工学院许可证(MIT)。更多信息请参见[许可文件](https://github.com/joselfonseca/lighthouse-graphql-passport-authlicense.md)。

</article>

[View on GitHub](https://github.com/joselfonseca/lighthouse-graphql-passport-auth)

编码快乐！