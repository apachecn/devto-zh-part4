# 如何升级 omnipay-stripe 以支持 Laravel 应用中的 3D 安全支付

> 原文：<https://dev.to/daniel_werner/how-to-upgrade-omnipay-stipe-to-support-3d-secure-payments-in-laravel-application-3ck3>

## 背景

从 2019 年 9 月 14 日开始，新法规将要求欧洲卡支付采用强客户身份认证(SCA)。这将需要不同的支付工作流程，因为用户需要在输入卡的详细信息后验证支付。你可以在这里找到更多关于 SCA 条带支持的信息。如果你使用 [laravel-omnipay](https://github.com/barryvdh/laravel-omnipay) 软件包进行条纹支付——在撰写本文时——你需要将软件包升级到开发版本。最新版本不包括 PaymentIntents 工作流。

在本文中，我将描述我如何设法升级 stripe 支付以支持 SCA 的步骤和挑战。

## 将 Stripe.js 升级到 v3

我的第一步是将 stripe.js 升级为 stripe 元素。我不确定这对于整合 stripe 的支付意图是否绝对必要，但如果我们无论如何都需要做一些改变，最好使用最新的版本。v2 和 v3 的基本区别在于，v2 的字段是手动创建的，而 v3 只需要一个容器，它会自动创建卡片详细信息的输入。这里就不赘述了，因为 Stripe 有一个很棒的迁移指南，我也跟着看了。请在这里找到[。](https://stripe.com/docs/stripe-js/elements/migrating)

## 将 thehpleague/omnipay-stripe 包升级到 dev 版本

在软件包的下一个版本发布之前，我们需要使用软件包的开发版本。将以下内容添加到应用程序中的**composer.json**:

"**omnipay/stripe ":" 3.1 . x-dev**"它是包的 composer . JSON 中" dev-master "的分支别名

为了防止将来可能出现的破坏性变化，我在 composer.json 中使用了最新的提交散列，如下所示:

```
"omnipay/stripe": "3.1.x-dev#37df2a791e8feab45543125f4c5f22d5d305096d" 
```

Enter fullscreen mode Exit fullscreen mode

完成后，使用 composer update 更新软件包:

```
composer update omnipay/stripe 
```

Enter fullscreen mode Exit fullscreen mode

## 使用支付意向网关

现在您已经安装了最新版本，您可以使用 PaymentIntents 网关。将网关添加到 **config/omnipay.php** :
中的网关部分

```
 'Stripe_PaymentIntents' => array(
            'ApiKey' => env('StripeApiKey', ''),
        ), 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 Facade 来访问网关，您应该使用新网关的名称作为参数，如下所示:

```
Omnipay::gateway('Stripe\_PaymentIntents'); 
```

Enter fullscreen mode Exit fullscreen mode

我已经遵循了软件包的自述文件[中描述的步骤。这里我只总结主要的步骤。

在采购方式的参数数组中添加“**确认= >真**”，以避免需要人工确认付款意向:](https://github.com/thephpleague/omnipay-stripe#stripe-payment-intents) 

```
$response = $gateway->purchase([
     'amount'                   => '10.00',
     'currency'                 => 'USD',
     'description'              => 'This is a test purchase transaction.',
     'token'                    => $token,
     'returnUrl'                => $completePaymentUrl,
     'confirm'                  => true,
 ])->send(); 
```

Enter fullscreen mode Exit fullscreen mode

正如文档所建议的，可以使用**令牌**来代替**支付方法**，所以我也使用了它。

当购买请求发出时，我们可以得到 3 种类型的响应:

*   **成功**:不需要 3DS 认证，支付成功
*   **重定向** : 3DS 认证是必要的，我们需要重定向用户来认证交易
*   **Fail** :出错了，我们需要处理错误

```
if ($response->isSuccessful()) {    // Pop open that champagne bottle, because the payment is complete.} else if($response->isRedirect()) {    $response->redirect();} else {    // The payment has failed. Use $response->getMessage() to figure out why and return to step (1).} 
```

Enter fullscreen mode Exit fullscreen mode

如果需要重定向，我们会将用户重定向到 3DS 身份验证页面。一旦认证完成(或未完成)，用户将被重定向到 **completePaymentUrl** 。发生这种情况时，我们会确认支付意向，并检查支付是否成功完成。

```
if ($response->isSuccessful()) {
    // Pop open that champagne bottle, because the payment is complete.
} else if($response->isRedirect()) {
    $response->redirect();
} else {
    // The payment has failed. Use $response->getMessage() to figure out why and return to step (1).
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

截止日期就要到了，这现在是开发者们的热门话题，我希望我的博客帖子能帮助你按时完成过渡。

帖子[如何升级 omnipay-stipe 以支持 Laravel 应用](https://42coders.com/how-to-upgrade-omnipay-stipe-to-support-3d-secure-payments-in-laravel-application/)中的 3D 安全支付最早出现在 [42 Coders](https://42coders.com) 上。