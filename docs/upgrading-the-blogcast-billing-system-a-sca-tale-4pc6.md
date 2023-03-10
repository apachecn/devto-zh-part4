# 升级博客广播计费系统:SCA 故事

> 原文：<https://dev.to/m1guelpf/upgrading-the-blogcast-billing-system-a-sca-tale-4pc6>

*如果你不知道的话，[欧盟新的 PSD2 法规将要求从 9 月 14 日](https://stripe.com/payments/strong-customer-authentication)开始对许多在线支付进行严格的客户身份验证(SCA)。这是使用新的 [Laravel Cashier](https://github.com/laravel/cashier) 版本(v10)升级 [Laravel](https://laravel.com) 应用支持 SCA 的故事。*

Blogcast 没有遵循传统的*软件即服务* (SaaS)计费模式。它不再每月收取固定费用，而是在用户每次转换文章时收取固定费用。在升级之前，Blogcast 在注册时要求支付方式，然后在每次创建新文章时从服务器收取费用。

不幸的是，这个系统不再适用于 SCA，因为它可能需要持卡人随时进行额外的身份验证，而这在服务器收费时并不总是可行的。此更新还阻止单次收费(与订阅无关的收费)使用存储的支付方式，使服务器收费再次成为不可能。*请记住，这些限制是由于 Stripe 在其平台中实施法律的方式造成的，而不是法律明确规定的。*

由于我目前的实现被认为是不可能的，我不得不绞尽脑汁想出一个新的。我不想改变我的基于使用的计费模式，而是希望使用某种类似订阅的实现方式，因为这是 Cashier 和 Stripe 的优化目标。幸运的是，Stripe 提供了的计量使用订阅，即允许你按单位计费的订阅。我创建了一个新的订阅，并将计费周期设置为天而不是月。这样，用户将在一天结束时而不是按月计费，使这个系统与旧系统相似。

现在唯一剩下的事情就是更新后端代码来记录使用情况，而不是直接向用户收费。幸运的是，我可以重用其他应用程序中的一些代码，所以这只是几分钟的事情，而不是几个小时。下面是增加特定用户使用记录的代码:

```
\Stripe\UsageRecord::create([
    'quantity' => 2, // number of units to increment
    'timestamp' => now()->timestamp, // when was this charge made?
    'action' => 'increment', // if you skip this it'll override the units instead of adding them
    'subscription_item' => $user->subscription('recurring')->asStripeSubscription()->items->data[0]['id'], // id of the user's subscription, you can get it using Stripe's API
]); 
```

Enter fullscreen mode Exit fullscreen mode

现在，Stripe 将自己处理非会话计费，如果用户需要额外的身份验证，就向他们发送电子邮件。旧系统的另一个优势是每天只收费一次，而不是每篇文章一次，这使得用户在他们的银行账户上显示费用时更容易识别。

尽管看起来很简单，但要让一切正常工作，需要做很多改变，从更新前端来处理新的 [Stripe 元素](https://stripe.com/payments/elements)方法，到更新所有的自动化测试。

<figure>[![Upgrading the Blogcast billing system: A SCA Tale](img/af688875fcc5b301c2d10608c0f75934.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U_6xPjOt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/08/Image-2019-08-14-at-8.33.15-PM.png) 

<figcaption>最终拉动请求，实现了本变更</figcaption>

</figure>

在 9 月 14 日立法通过之前还有一些，但是如果你的应用程序处理在线支付，你一定要开始研究它。说服人们为你的应用付费已经很难了，人们最不希望看到的就是糟糕的付费流程阻碍了你的应用。