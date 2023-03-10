# Laravel 中的单一方法控制器

> 原文：<https://dev.to/massivebrains/single-method-controllers-in-laravel-4gf6>

我们都同意方法和函数应该只做一件事。我们称之为*单一责任原则*。

然而，方法应该遵循 SRP 的事实并不意味着我们应该有一个包含许多(几乎)独立方法的控制器，这会导致一个非常非结构化的业务逻辑。

拥有一个单一方法控制器是维护*单一责任原则*的一个极好的方式。例如，看看下面的控制器

```
namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\Plan;

class PlansController extends Controller
{
    public function index()
    {
        //Logic to Get All Plans
    }

    public function create(Request $request)
    {
        //Logic to create new Plan
    }

    public function inviteUser(Request $request)
    {
        //Logic to invite user to plan
    }
} 
```

首先想到的是`inviteUser`方法不属于这个控制器。我们应该有类似`InviteUserController`的东西。这绝对是我们应该做的。

从这个控制器的名字来看，它应该只做一件事——邀请用户参加一个特定的计划。

所以我们可以简单地使用类似
的东西

```
namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class InviteUserController extends Controller
{

    public function inviteUser(Request $request)
    {
        //Logic to invite user to plan
    }
} 
```

我们应该做的对吗？-那如果能做得更好呢？-介绍魔法方法`__invoke`:)

PHP 有几个神奇的方法。魔术方法以两个下划线开始命名，当特定事件发生时会自动调用。

当对象作为函数被调用时，`__invoke()`方法被调用。当你声明它的时候，你说它应该期待哪些参数。这里有一个非常简单的例子:

```
class Human {

  public function __invoke() {

    echo "Sophisticated creature";
  }
} 
```

我们可以实例化一个人类对象，然后像函数一样使用它:

```
$victor = new Human();
$victor(); // Sophisticated creature 
```

有了这样的理解，我们可以简单地将`InviteUserController`的`inviteUser`方法重命名为`__invoke`，如下图

```
namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class InviteUserController extends Controller
{

    public function __invoke(Request $request)
    {
        //Logic to invite user to plan
    }
} 
```

在 routes 文件中，我们不再使用字符串文字约定来映射到这个控制器的路由，而是添加类似于
的类

```
use App\Http\Controllers\Api;

Route::get('/invite', InviteUserController::class) 
```

让我知道你对单一方法控制器的想法！

信用:[洛娜佳](https://lornajane.net/posts/2012/phps-magic-__invoke-method-and-the-callable-typehint)

*PS:这是我第一篇关于开发者的博客文章*