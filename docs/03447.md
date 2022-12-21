# 捕捉 Laravel 雄辩异常:简单的错误消息

> 原文：<https://dev.to/adam_crampton/catching-laravel-eloquent-exceptions-easy-peasy-error-messages-1mpn>

# 问题

我最近发现自己处于这样一种情况，我有一个代码库，它会从某些有说服力的查询中抛出异常——但是是以一种*某种*合法的方式。

从本质上来说，该场景是某些用户帐户与某些模型没有关系，但是对于控制器完成其工作是至关重要的。

# 解

这种事情通常应该通过在入口点(用户创建的验证)修复问题来处理，但是在这个特定的场景中，向用户返回一个错误来让他们知道需要人工干预是一个更好的主意。

因此，为了解决这个问题，我需要捕捉异常，生成人类可读的消息，并将其发送回前端。这个功能不应该使控制器混乱，并且应该易于维护。

以下是我一步一步的方法:

## 1。树立一种品质

因为我们可能希望这个功能可以通过应用程序在全球范围内使用，所以让我们设置一个特征——这对这类事情非常有用。

```
 namespace App\Traits;

Trait GlobalUtility
{
// Code
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2。定义错误

在 trait 中，我们设置了一组错误消息，每个消息都有一个有意义的键名。添加了一条默认消息，以确保总是返回一些内容。

```
namespace App\Traits;

Trait GlobalUtility
{
    protected $customExceptionErrors = [
        'generic' => 'Sorry, there was a problem with this page. Please contact your Sales Account Manager for help.',
        'noDealerRelationship' => 'Sorry, your user account does not appear to be connected to a Dealer. Please contact your Sales Account Manager for help.'
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 3。返回逻辑

返回方法是一个简单的检查，它:

*   接受一个命名的异常参数，该参数与`$customExceptionErrors`数组中的键对齐
*   获取错误消息
*   如果找不到错误字符串或泛型版本(或者没有传递参数)，则返回错误字符串或泛型版本

```
namespace App\Traits;

Trait GlobalUtility
{
    protected $customExceptionErrors = [
        'generic' => 'Sorry, there was a problem with this page. Please contact your Sales Account Manager for help',
        'noDealerRelationship' => 'Sorry, your user account does not appear to be connected to a Dealer. Please contact your Sales Account Manager for help.'
    ];

    /**
     * Returns a custom error message for the exception.
     *
     * @param string $exceptionName
     * @return string
     */
    public function getExceptionError($exceptionName = 'generic')
    {
        return $this->customExceptionErrors[$exceptionName] ?: $this->customExceptionErrors['generic'];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4。向控制器添加特征和异常类

接下来，trait 和 exception 类被添加到控制器中，在那里进行有说服力的查询。

在名称空间声明之后，我们添加:

```
use App\Traits\GlobalUtility;
use Illuminate\Database\Eloquent\ModelNotFoundException; 
```

Enter fullscreen mode Exit fullscreen mode

还有班内:

```
 use GlobalUtility; 
```

Enter fullscreen mode Exit fullscreen mode

## 5。更新查询

现在，我们必须确保查询利用了“orFail”方法，如`firstOrFail`或`findOrFail`，然后将它放入 try & catch 块。

```
try 
{
    $dealer = $user->worksFor()->firstOrFail();
}
catch (ModelNotFoundException $e)
{

} 
```

Enter fullscreen mode Exit fullscreen mode

## 6。配置重定向

在 catch 语句中，我们通过调用 trait 方法来添加重定向，以捕获适当的错误。我们将把它设置为`customError`，它将是一个稍后通过会话可用的值。

```
try 
{
    $dealer = $user->worksFor()->firstOrFail();
}
catch (ModelNotFoundException $e)
{
    return redirect()->route('orders.history')->with([
        'customError' => $this->getExceptionError('noDealerRelationship')
    ]);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。向刀片文件添加检查和提取

最后，我们需要询问应用程序`customError`值是否存在于会话中，如果存在，则显示。

```
{{-- Show custom defined errors (caught from exceptions) if they exist --}}
@if (Session::has('customError'))
    <div class="alert alert-danger">
        <strong>{{ Session::get('customError') }}</strong>
    </div>
@endif 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！希望有人觉得这有用:)