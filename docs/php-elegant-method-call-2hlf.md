# PHP -优雅的方法调用

> 原文：<https://dev.to/suddenlyrust/php-elegant-method-call-2hlf>

我经常发现自己处于这样一种情况:在一个类中，我只有一个公共方法，并且想要调用它。假设我们有一个向特定用户发送邮件的类

```
class SendMail
{
    public function handle(User $user)
    {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以调用如下方法

```
(new SendMail)->handle($this->user); 
```

Enter fullscreen mode Exit fullscreen mode

但是我不喜欢启动一个新对象的噪音。或许有更好的解决方案吗？🤔

如果我们包含一个小小的 PHP-Magic ✨，并添加一个静态函数，为我们创建对象并调用方法，会怎么样？像这样

```
class SendMail
{
    public function handle(User $user)
    {
        //
    }

    public static function execute(User $user)
    {
        (new static)->handle($user);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了新的静态方法，我们现在可以这样调用它

```
SendMail::execute($this->user); 
```

Enter fullscreen mode Exit fullscreen mode

好哇🙌这个在我眼里看起来干净多了。但是为什么我们现在要这么麻烦呢？🙄

```
(new SendMail)->handle($this->user);
// vs
SendMail::execute($this->user); 
```

Enter fullscreen mode Exit fullscreen mode

这两行做了同样的事情，受过训练的读者现在会问为什么不直接执行静态方法中的代码，而不是创建一个新的对象。根据功能的不同，没有必要这样做。然而，根据我的经验，大多数函数并不像我们的例子那样简单。

我引起你的兴趣了吗？试一试吧。也许你也喜欢它😉

编辑:
感谢大家的反馈👌dev.to 真的很棒😁

我在本文中举了一个非常简单的例子，并在下面的评论[https://dev.to/suddenlyrust/comment/e58i](https://dev.to/suddenlyrust/comment/e58i)中进一步解释了何时使用这种模式