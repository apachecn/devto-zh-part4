# 通过推送通知服务泄露用户数据

> 原文：<https://dev.to/kjgenevieve/compromising-user-data-via-push-notification-services-k6a>

2019 年 9 月 3 日，我看着手机发现一个令人惊讶的通知:

[![Message Image](img/0aeeab65d962662b86bf308d4f3c0988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aH8I3kjI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/kspCJ8P.jpg)

这条消息是从 [UVLens](https://www.uvlens.com/) 发出的，这是一个用户群很小的小众应用。通常，UVLens 会显示您所在地区的当前紫外线指数，并针对您的皮肤类型提供防晒建议。作为一个非常苍白的人，这一直是我决定出门(很少)时是否需要涂防晒霜(是的)和戴帽子(最有可能)的一个非常有用的方法。它从来没有给我发过推送通知，当然也从来没有这么淫秽的。

UVLens 迅速跟进，通过推送通知道歉，并在社交媒体[上解释说，他们的“第三方推送通知服务(已经)受到损害。”](https://www.facebook.com/UVLens/posts/3298768900134988)

这条信息比前者更激起我的好奇心。什么是推送服务，它是如何被破坏的？

### 推送服务

推送服务是一家管理 API 的公司，应用程序使用该 API 自动向用户发送消息。它们提供了简单的方法来根据用户的数据查询用户群，例如上次登录的时间，他们是否通过应用程序进行了购买，或者设备类型(iOS，Android，Chrome 等)。).

我联系了 UVLens 了解他们的服务提供商，他们说他们在发送违规通知时使用了 [OneSignal](https://onesignal.com/) ，但他们后来改用了谷歌运行的 [Firebase 云消息](https://firebase.google.com/docs/cloud-messaging)。据 OneSignal 称，它们被威瑞森、维珍移动和总部等公司使用。

### 推送通知

推送通知本身就是一个话题，但简而言之，它是应用服务器、推送服务和用户之间的三方对话。

*   用户下载您的应用程序并同意接收推送通知。
*   你的应用程序的服务器接收这些信息...
*   将它传递给你的推送服务。
*   推送服务用该用户的唯一 ID 来响应。
*   当您准备好发送消息时，您查询 push 服务的 API 以获得您需要的用户 id，然后将信息传递给 push 服务。
*   推送服务会将消息发送给您指定的用户。

为了让推送通知通过，您通常需要推送服务提供的两个密码:应用程序的 ID 和 API Rest 密钥。

### 推送服务是如何被攻破的

OneSignal 没有就该事件或该事件如何发生发表任何公开声明。然而，他们的 API 文档提供了一些简单的方法来访问应用程序的数据。

首先，用于查询 API 的代码很容易获得，包括快速下载所有用户信息的 CVS 文件的指令。不过，您确实需要两个认证密钥。

然而，在研究这个话题时，我发现许多人实际上都在网上公布了他们的钥匙。通常，他们只给出 REST API 键或应用程序 ID，但也有少数人两者都给出。(一名程序员甚至贴出了他的用户 id 的图片。)假设使用该信息，可以下载应用程序的用户信息 CVS。假设。

当然，还有其他公开密码的方式，但这是一个独特的例子，用户错误导致数据受损。

### 小型应用程序的漏洞

如果这是用来发送流氓通知的方法，那么 UVLens 已经特别容易受到攻击。OneSignal 有一个简单的查询，如果用户少于 10 万，它将下载一个应用程序的所有用户信息。

不可能知道一个 app 有多少下载量，但是我们可以用一个基于评分的大概估计。如果我们假设像 UVLens 这样的免费应用程序每 100 次下载就会获得一次评级，那么它们的总用户数可能约为 88，000 人。

| 平台 | 复习 | 预计下载量 |
| --- | --- | --- |
| 苹果商店 | One hundred and seventy | Seventeen thousand |
| 游戏商店 | Seven hundred and thirteen | Seventy-one thousand three hundred |
| **总计** | Eight hundred and eighty-three | **88300** |

有可能他们所有用户的信息都是用终端中的一个命令下载的。

UVLens 表示，他们的数据和服务器没有受到损害，用户的信息是安全的。对他们来说幸运的是，用户输入的最多的个人信息是他们的肤色和邮政编码，所以即使这些数据被公布，也不太可能有深远的影响。

* * *

## 查阅资料来源

*   [一个信号文件](https://documentation.onesignal.com/docs/product-overview)
*   [估算应用下载量](https://stackoverflow.com/questions/4756717/find-the-number-of-downloads-for-a-particular-app-in-apple-appstore)
*   [推送的工作原理](https://developers.google.com/web/fundamentals/push-notifications/how-push-works)