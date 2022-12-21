# 使用 Ionic / Cordova 购买应用程序

> 原文：<https://dev.to/bewizyu/in-app-purchase-with-ionic-cordova-3fjc>

文章最初刊登在[培养基](https://medium.com/bewizyu/in-app-purchase-with-ionic-cordova-18960239f989)上。

## 简介

在应用购买(或集成购买)中，有机会销售原生移动应用(智能手机和平板电脑)中的附加内容。如果你的产品符合下面的应用内购买类别，那么你可以，甚至必须在应用内购买中使用。

[![A typical example of IAP](img/f024cba4089f1663240b602c23c865f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OU6LmW3l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3044/1%2AA7DWo3FqQqnAuJiXNVQrGg.png)*IAP 的典型例子*

## 用 IAP 可以卖什么？

原则上，只要属于以下几类，你想卖什么都可以:

### 消耗品

用户可以购买不同类型的消耗品，如游戏中的生命或宝石，以通过应用程序推进他们的进展。可消耗的应用内购买被使用一次，被耗尽，并且可以再次购买。

### 非消耗品

用户可以在应用程序中购买非消耗性的高级功能。非消耗品购买一次，不会过期，例如照片应用程序中的附加滤镜。

### 自动续费套餐

用户可以购买服务或定期更新的内容，如每月访问云存储或每周订阅杂志。

### 非续费订阅

用户可以在有限的时间内购买对服务或内容的访问，例如流媒体内容的季度通行证。这种类型的订阅不会自动续订，因此用户每次都需要续订。

你可以在这里找到更多的信息

## 我们的 Bewiz Spin 应用程序

我们想创建一个移动应用程序，用户可以在其中购买积分，我们将称之为 **Bewiz。有了这些惊喜，他可以转动轮盘来赢取礼物🎁(1 贝威兹= > 1 旋)**

[![Photo by [Navneet Shanu ](https://www.pexels.com/@navneet-shanu-202773?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)from [Pexels](https://www.pexels.com/photo/brown-carriage-wheel-672630/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)](img/e3cb9bb40b0d74511416e04ecff025e9.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--JfOj5beH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/9216/1%2AF8D_fSLWaMv0vszCw9Ytbg.jpeg) *照片由[navenet Shanu](https://www.pexels.com/@navneet-shanu-202773?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)发自[Pexels](https://www.pexels.com/photo/brown-carriage-wheel-672630/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)T8】*

对于我们的情况，我们将需要使用可消耗的产品，因为它被用来购买曾经用来旋转轮子的信用。

假设我们有一组建议的产品:

[![](img/83ecd5a22398945c025f6bf00cee214c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OfqhZO5i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2358/1%2AN2ah_00DHtC_Spp_yapd0Q.png)

**10** 贝威兹为**10**， **50** 贝威兹为**40**和 **100** 贝威兹为**75**

让我们看看如何将这一切落实到位…😀

## 我需要设置什么 IAP

### Ionic / Cordova 移动应用

由于我们将开发一个 ionic 移动应用程序，您可以按照以下步骤启动您的项目:
[**免费移动应用程序开发:开始使用 Ionic 应用程序**
*使用我们现成的应用程序模板之一创建一个 Ionic 应用程序，或者使用空白模板重新开始。查看……*ionicframework.com](https://ionicframework.com/getting-started#cli)的市场

### 服务器端应用

出于安全原因，购买验证应该在服务器端进行。

每次购买后，商店都会返回一个收据数据，该数据应该在服务器端进行验证(IOS 使用 base64 字符串，Android 使用 Json 格式)。

对于我们的 Bewiz Spin，我们将使用 PHP 服务器端收据验证，使用这个开源库:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿波拉特](https://github.com/aporat) / [商店-收据-验证器](https://github.com/aporat/store-receipt-validator)

### 苹果 iTunes，Google Play 和亚马逊应用商店的 PHP 收据验证器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 商店收据验证器

[![Latest Stable Version](img/9bef05d8c59f768b1ee350a543f409aa.png)](https://packagist.org/packages/aporat/store-receipt-validator)[![Composer Downloads](img/f150b301dcb9582bf325741e168b30f7.png)](https://packagist.org/packages/aporat/store-receipt-validator)[![Build Status](img/309d3f38dbe262f2502af6ba6c7360f9.png)](https://github.com/aporat/store-receipt-validator/actions)[![Code Coverage](img/47af4dc7305e0d67ec9ec8132ecb1c52.png)](https://scrutinizer-ci.com/g/aporat/store-receipt-validator/?branch=master)[![Scrutinizer Code Quality](img/538a377ba46c5521830eea978021e4af.png)](https://scrutinizer-ci.com/g/aporat/store-receipt-validator/?branch=master)[![StyleCI](img/12046d777aa0c084b4b6f37e2532d32b.png)](https://github.styleci.io/repos/14928361)[![License](img/d2cc1c7792ae93f18d5eb1c6f0035243.png)](https://packagist.org/packages/aporat/store-receipt-validator)

苹果 iTunes，Google Play 和亚马逊应用商店的 PHP 收据验证器

## 要求

*   PHP >= 7.2

## 装置

`composer require aporat/store-receipt-validator`

## 快速示例

### 苹果公司开发的热门软件（苹果公司开发的与 iPod 搭配的在电脑上使用的音乐播放器和音乐管理器）

```
use ReceiptValidator\iTunes\Validator as iTunesValidator
$validator = new iTunesValidator(iTunesValidator::ENDPOINT_PRODUCTION); // Or iTunesValidator::ENDPOINT_SANDBOX if sandbox testing
$receiptBase64Data = 'ewoJInNpZ25hdHVyZSIgPSAiQXBNVUJDODZBbHpOaWtWNVl0clpBTWlKUWJLOEVkZVhrNjNrV0JBWHpsQzhkWEd1anE0N1puSVlLb0ZFMW9OL0ZTOGNYbEZmcDlZWHQ5aU1CZEwyNTBsUlJtaU5HYnloaXRyeVlWQVFvcmkzMlc5YVIwVDhML2FZVkJkZlcrT3kvUXlQWkVtb05LeGhudDJXTlNVRG9VaFo4Wis0cFA3MHBlNWtVUWxiZElWaEFBQURWekNDQTFNd2dnSTdvQU1DQVFJQ0NHVVVrVTNaV0FTMU1BMEdDU3FHU0liM0RRRUJCUVVBTUg4eEN6QUpCZ05WQkFZVEFsVlRNUk13RVFZRFZRUUtEQXBCY0hCc1pTQkpibU11TVNZd0pBWURWUVFMREIxQmNIQnNaU0JEWlhKMGFXWnBZMkYwYVc5dUlFRjFkR2h2Y21sMGVURXpNREVHQTFVRUF3d3FRWEJ3YkdVZ2FWUjFibVZ6SUZOMGIzSmxJRU5sY25ScFptbGpZWFJwYjI0Z1FYVjBhRzl5YVhSNU1CNFhEVEE1TURZeE5USXlNRFUxTmxvWERURTBNRFl4TkRJeU1EVTFObG93WkRFak1DRUdBMVVFQXd3YVVIVnlZMmhoYzJWU1pXTmxhWEIwUTJWeWRHbG1hV05oZEdVeEd6QVpCZ05WQkFzTUVrRndjR3hsSUdsVWRXNWxjeUJUZEc5eVpURVRNQkVHQTFVRUNnd0tRWEJ3YkdVZ1NXNWpMakVMTUFrR0ExVUVCaE1DVlZNd2daOHdEUVlKS29aSWh2Y05BUUVCQlFBRGdZMEFNSUdKQW9HQkFNclJqRjJjdDRJclNkaVRDaGFJMGc4cHd2L2NtSHM4cC9Sd1YvcnQvOTFYS1ZoTmw0WElCaW1LalFRTmZnSHNEczZ5anUrK0RyS0pFN3VLc3BoTWRkS1lmRkU1ckdYc0FkQkVqQndSSXhleFRldngzSExFRkdBdDFtb0t4NTA5ZGh4dGlJZERnSnYyWWFWczQ5QjB1SnZOZHk2U01xTk5MSHNETHpEUzlvWkhBZ01CQUFHamNqQndNQXdHQTFVZEV3RUIvd1FDTUFBd0h3WURWUjBqQkJnd0ZvQVVOaDNvNHAyQzBnRVl0VEpyRHRkREM1RllRem93RGdZRFZSMFBBUUgvQkFRREFnZUFNQjBHQTFVZERnUVdCQlNwZzRQeUdVakZQaEpYQ0JUTXphTittVjhrOVRBUUJnb3Foa2lHOTJOa0JnVUJCQUlGQURBTkJna3Foa2lHOXcwQkFRVUZBQU9DQVFFQUVhU2JQanRtTjRDL0lCM1FFcEszMlJ4YWNDRFhkVlhBZVZSZVM1RmFaeGMrdDg4cFFQOTNCaUF4dmRXLzNlVFNNR1k1RmJlQVlMM2V0cVA1Z204d3JGb2pYMGlreVZSU3RRKy9BUTBLRWp0cUIwN2tMczlRVWU4Y3pSOFVHZmRNMUV1bVYvVWd2RGQ0TndOWXhMUU1nNFdUUWZna1FRVnk4R1had1ZIZ2JFL1VDNlk3MDUzcEdYQms1MU5QTTN3b3hoZDNnU1JMdlhqK2xvSHNTdGNURXFlOXBCRHBtRzUrc2s0dHcrR0szR01lRU41LytlMVFUOW5wL0tsMW5qK2FCdzdDMHhzeTBiRm5hQWQxY1NTNnhkb3J5L0NVdk02Z3RLc21uT09kcVRlc2JwMGJzOHNuNldxczBDOWRnY3hSSHVPTVoydG04bnBMVW03YXJnT1N6UT09IjsKCSJwdXJjaGFzZS1pbmZvIiA9ICJld29KSW05eWFXZHBibUZzTFhCMWNtTm9ZWE5sTFdSaGRHVXRjSE4wSWlBOUlDSXlNREV5TFRBMExUTXdJREE0T2pBMU9qVTFJRUZ0WlhKcFkyRXZURzl6WDBGdVoyVnNaWE1pT3dvSkltOXlhV2RwYm1Gc0xYUnlZVzV6WVdOMGFXOXVMV2xrSWlBOUlDSXhNREF3TURBd01EUTJNVGM0T0RFM0lqc0tDU0ppZG5KeklpQTlJQ0l5TURFeU1EUXlOeUk3Q2draWRISmhibk5oWTNScGIyNHRhV1FpSUQwZ0lqRXdNREF3TURBd05EWXhOemc0TVRjaU93b0pJbkYxWVc1MGFYUjVJaUE5SUNJeElqc0tDU0p2Y21sbmFXNWhiQzF3ZFhKamFHRnpaUzFrWVhSbExXMXpJaUE5SUNJeE16TTFOems0TXpVMU9EWTRJanNLQ1NKd2NtOWtkV04wTFdsa0lpQTlJQ0pqYjIwdWJXbHVaRzF2WW1Gd2NDNWtiM2R1Ykc5aFpDSTdDZ2tpYVhSbGJTMXBaQ0lnUFNBaU5USXhNVEk1T0RFeUlqc0tDU0ppYVdRaUlEMGdJbU52YlM1dGFXNWtiVzlpWVhCd0xrMXBibVJOYjJJaU93b0pJbkIxY21Ob1lYTmxMV1JoZEdVdGJYTWlJRDBnSWpFek16VTNPVGd6TlRVNE5qZ2lPd29KSW5CMWNtTm9ZWE5sTFdSaGRHVWlJRDBnSWpJd01USXRNRFF0TXpBZ01UVTZNRFU2TlRVZ1JYUmpMMGROVkNJN0Nna2ljSFZ5WTJoaGMyVXRaR0YwWlMxd2MzUWlJRDBnSWpJd01USXRNRFF0TXpBZ01EZzZNRFU2TlRVZ1FXMWxjbWxqWVM5TWIzTmZRVzVuWld4bGN5STdDZ2tpYjNKcFoybHVZV3d0Y0hWeVkyaGhjMlV0WkdGMFpTSWdQU0FpTWpBeE1pMHdOQzB6TUNBeE5Ub3dOVG8xTlNCRmRHTXZSMDFVSWpzS2ZRPT0iOwoJImVudmlyb25tZW50IiA9ICJTYW5kYm94IjsKCSJwb2QiID0gIjEwMCI7Cgkic2lnbmluZy1zdGF0dXMiID0gIjAiOwp9'
try {
  $response = $validator->setReceiptData($receiptBase64Data)->validate()
  // $sharedSecret = '1234...'; // Generated in iTunes Connect's In-App Purchase menu
  // $response = $validator->setSharedSecret($sharedSecret)->setReceiptData($receiptBase64Data)->validate(); // use setSharedSecret() if for recurring subscriptions
} catch (Exception $e) {
  echo 'got error = ' . $e->getMessage() . PHP_EOL;
}

if ($response->isValid()) {
  echo 'Receipt is valid.' . PHP_EOL;
  echo 'Receipt data = ' . print_r($response->getReceipt()) . PHP_EOL;

  foreach ($response->getPurchases() as $purchase
```

…</article>

[View on GitHub](https://github.com/aporat/store-receipt-validator)

### 在 app 购买 Cordova 插件

目前，Github 上有一个主要的开源 cordova 插件，由 ionic 社区提供支持。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ j3k0 ](https://github.com/j3k0) / [科尔多瓦-外挂-购买](https://github.com/j3k0/cordova-plugin-purchase)

### 在 iOS、Android 和 Windows 上为 Cordova 进行应用内购买

<article class="markdown-body entry-content container-lg" itemprop="text">

# 科尔多瓦购买插件

> 科尔多瓦的应用内购买

[![Build Status](img/8703347d56a05a02e2c5d4d291bb969b.png)](https://travis-ci.org/j3k0/cordova-plugin-purchase)

* * *

需要专业的帮助和支持？[联系我](https://raw.githubusercontent.com/j3k0/cordova-plugin-purchase/master/mailto:hoelt@fovea.cc)。

我花了大量的空闲时间来开发和维护这个 Cordova 插件，以及我的其他开源软件。为了帮助确保这个插件保持更新，新的功能被添加，错误修复被快速实现，请捐赠几美元(或者多一点，如果你可以伸展)，因为这将帮助我负担得起专门的时间来维护它。如果你在一个为你赚钱的应用程序中使用这个插件，如果你制作这个应用程序是有报酬的，如果你要求新功能或优先漏洞修复，请考虑捐赠。谢谢大家！

*   [帕特里翁](https://www.patreon.com/join/2219243?)
*   [PayPal](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=7A4826SH6RJSE&source=url)

## 摘要

这个插件允许从 **Cordova、PhoneGap 和 Ionic** 应用程序中购买。

它让你可以用一个代码库在许多平台上处理应用内购买。

### 特征

| ios | 机器人 |
| --- | --- |

…</article>

[View on GitHub](https://github.com/j3k0/cordova-plugin-purchase)

还有 ionic 应用的包装器:
[**在 app 购买 2 - Ionic 文档**
*Ionic 是面向 web 开发者的 App 平台。用一个共享代码库构建令人惊叹的移动、网络和桌面应用…*ionicframework.com](https://ionicframework.com/docs/native/in-app-purchase-2)

将所有这些依赖项添加到您的 **package.json** :