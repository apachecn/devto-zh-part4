# ★改进对 Laravel 假货的断言

> 原文：<https://dev.to/freekmurze/improving-assertions-on-laravel-fakes-401j>

Laravel 有一些很酷的 [fakes](https://laravel.com/docs/master/mocking#event-fake) 可以帮助你测试你的代码。在这篇简短的博文中，我将向你展示一个小技巧，告诉你当一个包含这样一个伪测试失败时，如何获得更好的反馈。

## 如何使用假货

先说一个假货的例子。如果你已经处理过赝品，你可以跳到下一部分。

假设您的应用程序发送了一封邮件。您想要测试邮件是否被正确发送。第一步是设置假的。Laravel 让这变得非常简单。下面是你需要做的:

```
use Illuminate\Support\Facades\Mail;

// inside a PHPUnit test class

/** @test */
public function this_is_your_test() 
{
     Mail::fake();
} 
```

这个`fake`方法将把 IoC 容器中真正的邮件程序(Laravel 已经设置好了)换成一个假的。那个假的不会发邮件。

有了这些，您就可以测试邮件是否已经发送。你必须通过[你的可邮寄类](https://laravel.com/docs/master/mail#writing-mailables)到`assertSent`。

```
use Illuminate\Support\Facades\Mail;

/** @test */
public function this_is_your_test() 
{
     Mail::assertSent(OrderShipped::class);
} 
```

## 改进对假货的断言

这个方法接受一个 callable 作为第二个参数。那个可调用的将接收可邮寄的。在那个 callable 中，你可以使用像`hasTo`、`hasCc`这样的方法来断言 mailable 配置正确。文档包含[如下例](https://laravel.com/docs/master/mocking#mail-fake) :

```
// Assert a message was sent to the given users...
 Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
     return $mail->hasTo($user->email) &&
            $mail->hasCc('...') &&
            $mail->hasBcc('...');
 }); 
```

现在，只有当可调用函数返回`true`时，测试才通过。不错！

但是如果由于某种原因 callable 没有返回`false`呢？失败的测试将输出一条消息，类似于下面这条:

```
The expected [OrderShipped::class] mailable was not sent. 
```

这里的问题是不清楚`to`、`cc`或`bcc`是否不正确。让我们改进这一点。

可能您正在 PHPUnit 测试中测试邮件。让我们使用 PHPUnit 断言，而不是在 callable 中只有一个`return`语句。

```
Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
   $this->assertTrue($mail->hasTo($user->email));
   $this->assertTrue($mail->hasCc('...'));
   $this->assertTrue($mail->hasBcc('...'));

   return true;
}); 
```

当然，这需要输入更多的代码。但是有了这些断言，失败的测试现在还会报告失败断言的行号，所以您可以立即知道是`to`、`cc`还是`bcc`没有包含预期的地址。

您可以选择将自定义消息作为第二个参数传递给`assertTrue`。当断言失败时，将显示该消息。

```
Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
   $this->assertTrue($mail->hasTo($user->email), 'Unexpected to');
   $this->assertTrue($mail->hasCc('...'), 'Unexpected cc');
   $this->assertTrue($mail->hasBcc('...'), 'Unexpected bcc');

   return true;
}); 
```

## 总之

Laravel 的[赝品](https://laravel.com/docs/master/mocking)太棒了。在这篇文章中，我们只使用了邮件特定的一个。但是对于事件、通知、队列，...这些 fakes 上的大多数 assert 方法都接受一个 callable 作为第二个参数。下面是[从返回布尔值到使用断言的一个真实重构](https://github.com/spatie/laravel-stripe-webhooks/commit/60bd1b154ef3ebb7668d408debb9e40bb41f8d64)。

您可以让一个失败的测试更具可读性，不仅仅是在您传递的调用中返回一个布尔值，而是在它们内部使用 PHPUnit 断言方法。

祝测试愉快！