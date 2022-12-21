# iOS 应用的最佳后端

> 原文：<https://dev.to/leogdion/best-backend-for-your-ios-app-53g9>

为你的 iOS 应用选择最佳后端可能是一个挑战？这些选项非常强大，并且依赖于许多问题，例如——您需要云服务吗？你应该运行自己的服务器吗？你应该使用 [Cloudkit](https://developer.apple.com/icloud/cloudkit/) 、 [Firebase](https://firebase.google.com) 还是其他选项？我们深入探讨了这些问题以及你在为你的 iOS 应用寻找后端和云服务时应该考虑的其他事情。

首先，重要的是要指出，对于 iOS(或任何移动)应用来说，云服务并不是在所有情况下都是必要的。那么，没有它们你能做什么？实际上，很多。

> 因此，要考虑的一件事是，即使不做云服务，你也可以做很多事情。iOS 应用程序的特点是，你可以将数据存储在 iCloud 之类的东西中，或者在很多情况下，人们真的不需要云来存储他们的数据。他们可以直接存储在手机上。[授权应用程序。节目—第 8 集—云和后端服务](https://share.transistor.fm/s/ffcb9fc1)

[![](img/2151e0012705842622490d94f73ab4df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fa4Ubzzk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2AI3ayW-_JURbTvDWSg0JmOg.png)

## 最好的后端是没有后端？

是不是你的 iOS 应用最好的后端，没有后端？在许多情况下，你不需要云服务来存储，因为你可以直接使用手机本身。当你构建一个应用程序时，你应该考虑你是否真的需要存储在云中的东西，或者你是否能够使用手动机制来备份数据？如果你能创建一种机制，将数据以文件的形式保存到云存储中，这将为你节省大量的时间和金钱。

在处理私人或敏感数据的情况下，坚持使用手机是个好主意。在处理第三方应用程序时尤其如此，比如 Twitter、T2、YouTube 或其他 API，在这些应用程序中，你并不是真的在存储数据，而是存储在第三方的结构中。您可以将该 API 用作您数据库。

例如，在一个为医生和护士构建的接受职业继续教育的应用程序中，所有数据基本上都是使用[核心数据](https://developer.apple.com/documentation/coredata)存储的，这是一个 [SQLite](https://www.sqlite.org/index.html) 数据库抽象层。这些都存储在本地，因为他们的时间和预算不允许做其他事情。有一个手动备份过程，该过程会运行并创建数据的 zip 文件，然后将其保存在设备上。如果他们需要恢复它，他们可以在云中分类，然后恢复到他们的手机上。

 [# 应用的云和后端服务](/empowerappsshow/cloud-and-backend-services-for-apps)  [## 授权应用程序。显示](/empowerappsshow) ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png) ![EmpowerApps.Show](img/c9b4a08a2c6dc76abe7feb1ef4a8225f.png)

<audio id="audio" data-episode="cloud-and-backend-services-for-apps" data-podcast="empowerappsshow"><source src="https://dts.podtrac.com/redirect.mp3/media.transistor.fm/b0afd7df.mp3" type="audio/mpeg"> Your browser does not support the audio element.</audio>

[![Cloud and Backend Services For Apps](img/a613c19a7a3b704f56d9bbf50902fa04.png) ![animated volume bars](img/b4c6951c950232cdfef32ac750ed6d92.png) ](/empowerappsshow/cloud-and-backend-services-for-apps)  ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png)     ![volume](img/64c7607b77476eb3d924254262a5bca4.png)   <input type="range" name="points" id="volumeslider" value="50" min="0" max="100" data-show-value="true">    ![mute](img/53eef5aa80ab0610ec1ea90b95d54171.png)  1x  initializing... × 

[![](img/e3f3718099a6fa3f572a508e8c9841cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dbOYNAiE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2Avy6KWuNftrYDMhqj9Qng-g.jpeg)

## 为您的最佳后端选择云服务

如果你在多个设备之间存储数据或同步，iOS 应用程序的最佳后端会非常有用。例如，所有苹果设备(手机、iPads、电脑)上可用的笔记应用程序结合使用 [iCloud](https://www.icloud.com) 和 [CloudKit](https://developer.apple.com/icloud/cloudkit/) 在后端存储信息。当用户在 Mac 或 iPhone 上打开笔记时，它们都是可用的，因为它们存储在云中。

如果您已经决定您确实需要后端存储，那么在选择正确的途径时，您应该考虑哪些事情？让我们来看看。

> 要考虑的一件事是时间表和预算，特别是这个应用程序的目的是什么？这只是一个简单的 MVP 还是一个长期的应用程序，当我第一次开始时需要很好的架构？[* *授权应用程序。节目—第 8 集—云和后端服务](https://share.transistor.fm/s/ffcb9fc1) **

## 你的应用目的:MVP Vs. Enterprise

看看你正在设计的应用程序。是 MVP 还是更健壮的企业 app？

MVP(最可行的产品)是一个简单的应用程序，它只是证明它是你的客户真正想要的东西。这是您需要快速推出的东西，并且很可能是您认为您的客户会想要的基本功能的基本版本。

或者，应用程序是否需要从一开始就设计良好，就像财富 500 强公司的企业应用程序一样？如果你走这条路，你会想要更强大和可定制的东西，它应该是你想长期使用的东西。

如果你想获得一个 MVP，你的 iOS 应用程序最好的后台是简单、容易、便宜，并且能完成工作的东西。它应该是能被你的观众很容易测试的东西，这样你就能看到它是否能满足你的用户的需求。

当您编写 Swift of Objective-C 代码与后端对话时，您还需要确保您的 API 通信有一些健康的抽象。这样，如果你以后决定使用不同的服务，你将能够很容易地改变，你的用户不会在前端看到任何不同。

## 支持的设备

你只支持苹果设备吗？你也会支持网络吗？将会支持 Android。将支持 Windows 和 Linux 桌面。你打算支持物联网吗？这些都是在决定使用什么后端服务时要考虑的问题。

如果你只支持苹果设备，你可能会考虑 [CloudKit](https://developer.apple.com/icloud/cloudkit/) ，这是一个选项，我们将在下面更详细地讨论。

## 复杂与简单查询

有些任务，比如关系数据，最好通过某种 SQL 数据库来执行。然而，其他人可能更适合使用文档存储类型的数据库。最重要的是，您应该考虑是否需要聚合数据或者是否需要创建过滤器。换句话说，您是否需要以多种不同的方式查看您的数据来组合您的数据集。

相反，许多简单的基于文档的云存储选项更便宜。因此，如果你愿意将一个好的查询的大量复杂性转移到像你的 iPhone 这样的设备上，像 [Firebase](https://firebase.google.com) 这样的基于文档的存储可能是一个不错的选择。另外，您可以考虑缓存复杂的数据以备后用，而不是每次都查询数据。记住，一个简单的查询只需要查找具有特定 ID 的数据。

你需要问问自己，你是否愿意将一个好的查询的很多复杂性转移到像你的 iPhone 这样的设备上，或者将这些信息保存在服务器上是否更重要。iOS 应用的最佳后端可能不是最容易查询的，但却是最便宜、最容易维护的。

## 对外交流

除了存储，你还应该问自己是否需要任何外部通信或外部计划的工作。如果用户做了一些创建数据的事情，然后你需要能够访问这些数据，即使他们关闭了手机，比如电子邮件时事通讯，运输订单，这可能会发生。

## 你团队的专长

这是一个很大的因素，因为如果他们的专业知识是 is.net，你可能最好使用一个带有 SQL Server 和 C#的 Windows 后端。例如，如果你已经在 Azure 上获得了不错的价格，就使用它，因为这是你的团队所擅长的。如果你的团队精通 Swift，你可以考虑用 [Vapor](http://vapor.codes/) 作为你的后端。点击这里查看[我们对 Vapor 的评论，了解更多细节。](https://brightdigit.com/blog/2019/03/19/vapor-swift-backend-review/)

## 用户界面

我们还建议进行一些以用户为中心的设计，以提供用户需要的数据类型。之后，你可以回到你需要如何储存它。例如，你可以从构建应用程序的用户界面开始，从本质上伪造数据，以确保数据是客户想要的，然后从那里返回。注意你的用户界面，因为这是用户将要看到的东西。

这些因素，再加上您的预算和花费的时间，都是考虑后端存储时要考虑的因素。

 [# 应用的云和后端服务](/empowerappsshow/cloud-and-backend-services-for-apps)  [## 授权应用程序。显示](/empowerappsshow) ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png) ![EmpowerApps.Show](img/c9b4a08a2c6dc76abe7feb1ef4a8225f.png)

<audio id="audio" data-episode="cloud-and-backend-services-for-apps" data-podcast="empowerappsshow"><source src="https://dts.podtrac.com/redirect.mp3/media.transistor.fm/b0afd7df.mp3" type="audio/mpeg"> Your browser does not support the audio element.</audio>

[![Cloud and Backend Services For Apps](img/a613c19a7a3b704f56d9bbf50902fa04.png) ![animated volume bars](img/b4c6951c950232cdfef32ac750ed6d92.png) ](/empowerappsshow/cloud-and-backend-services-for-apps)  ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png)     ![volume](img/64c7607b77476eb3d924254262a5bca4.png)   <input type="range" name="points" id="volumeslider" value="50" min="0" max="100" data-show-value="true">    ![mute](img/53eef5aa80ab0610ec1ea90b95d54171.png)  1x  initializing... × 

## 选项

一旦你问了自己所有必要的问题，你会想开始寻找适合你的需求和预算的选择。如果你觉得你需要后端支持，你需要为你的应用和你的用户决定最好的选择。这里有一些选项可以考虑。

> …拥有自己的主机的一些优势是，你可以定制一切细节，我的意思是，你基本上有一个完整的机器，你可以用它来工作，这也是缺点，因为你必须确保一切都设置正确，就像许多其他服务一样。他们几乎可以照顾一切。你所需要做的就是编写你的应用程序代码，并获得你的数据库设置。[授权应用程序。节目—第 8 集—云和后端服务](https://share.transistor.fm/s/ffcb9fc1)

## 为你的后端托管服务器

最可定制和最可靠的选择是托管您自己的服务器。你可以购买自己的服务器，并将其托管在一个有虚拟机的站点，如 [AWS EC2](https://aws.amazon.com/ec2/) 、 [Linode](https://www.linode.com/?r=97e09acbd5d304d87dadef749491d245e71c74e7) 或 [DigitalOcean](https://m.do.co/c/255904441a32) 。Linode 是一个价格合理的选择，每月 5 美元。然而，由于成本和维护的需要，您可能不需要一个完整的服务器。

Kubernetes 归谷歌所有，但它是开源的，甚至 AWS、Linode 和 Digital Ocean 也托管 Kubernetes 实例。这是一个很受欢迎的选择，尤其是在开发领域。

如果你是从零开始，想玩点什么，Heroku 是个不错的选择。他们的免费层包括一个 PostgreSQL 数据库。

说到 PostgreSQL，您需要建立自己的数据库，以及其他服务器软件。以下是一些可供考虑的具体选项:

### 数据库

*   [PostgreSQL](https://www.postgresql.org/)
*   [MySQL](https://www.mysql.com/) 的实现
*   [马里亚布](https://mariadb.org/)
*   [MongoDB](https://www.mongodb.com/)
*   [SQL 服务器](https://www.microsoft.com/en-us/sql-server/default.aspx)

您需要一些东西来提供内容，无论是图像、JSON 还是 HTML:

### 服务器软件

*   [IIS](https://www.iis.net/)
*   [Nginx](https://www.nginx.com/)
*   [阿帕奇](https://httpd.apache.org/)

还要考虑一些其他服务，例如:

*   [Cloudflare](https://www.cloudflare.com/) 用于监控、缓存和 CDN
*   [用于监控和安全的新遗迹](https://newrelic.com/)
*   [服务主管](http://supervisord.org/)

如果你正在使用一个简单的 MVP 或者只为苹果发布，我们不建议你自己托管。如果您有简单的需求，就用最简单的解决方案，然后根据您自己需求的增长计划从那里扩展。

## CloudKit 作为你的后端

一个真正实惠的选择是苹果的后端存储系统，CloudKit。[如果你坚持使用 iOS，CloudKit](https://developer.apple.com/icloud/cloudkit/) 使用起来相当简单。它自带推送通知，完全是苹果原生的。如果你正在运行 Mac 应用程序或 iPad 应用程序，或者使用 Apple Watch 或 Apple TV，它真的很容易使用。它还包括一个用于 web 的 JavaScript 组件。如果你只是针对苹果设备，答案是肯定的。当使用 [CloudKit](https://developer.apple.com/icloud/cloudkit/) 时，如果数据不太复杂，你可以把一些东西卸载到 iPhone 上。

如果您绝对需要运行外部备份或外部 cron 作业，并且无法在 iPhone 或 iPad 上运行，那么您可能需要考虑另一个选项。

## Firebase 作为你的后台

另一个受欢迎的选择是 [Firebase](https://firebase.google.com) 。 [Firebase](https://firebase.google.com) 是最受欢迎的移动后端服务，不仅因为价格，还因为它可以在众多设备上使用。但是请注意，从表面上看，它似乎真的很有吸引力，但是当我们向可能扩大规模的项目推荐它时，我们会犹豫不决。如果你想学习如何在网络浏览器中进行实时数据库集成，并且不想引入需要创建大量额外代码来访问数据的应用程序， [Firebase](https://firebase.google.com) 是一个很好的方法。

但是，虽然它很受欢迎，但经过一些研究后，似乎没有多少企业级公司依赖它。我们建议在非常非常简单的情况下使用它，在这种情况下，您的数据结构不会随着时间的推移而改变，数据通常是平面的，并且您不必执行大量复杂的查询。在你进入它之前，真的要研究一下它的局限性，因为它可能不太适合你正在寻找的东西。

 [# 应用的云和后端服务](/empowerappsshow/cloud-and-backend-services-for-apps)  [## 授权应用程序。显示](/empowerappsshow) ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png) ![EmpowerApps.Show](img/c9b4a08a2c6dc76abe7feb1ef4a8225f.png)

<audio id="audio" data-episode="cloud-and-backend-services-for-apps" data-podcast="empowerappsshow"><source src="https://dts.podtrac.com/redirect.mp3/media.transistor.fm/b0afd7df.mp3" type="audio/mpeg"> Your browser does not support the audio element.</audio>

[![Cloud and Backend Services For Apps](img/a613c19a7a3b704f56d9bbf50902fa04.png) ![animated volume bars](img/b4c6951c950232cdfef32ac750ed6d92.png) ](/empowerappsshow/cloud-and-backend-services-for-apps)  ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png)     ![volume](img/64c7607b77476eb3d924254262a5bca4.png)   <input type="range" name="points" id="volumeslider" value="50" min="0" max="100" data-show-value="true">    ![mute](img/53eef5aa80ab0610ec1ea90b95d54171.png)  1x  initializing... × 

## 最终提示

我们知道我们给你留下了很多信息，所以作为最后的提示，我们想添加这条建议——当考虑任何存储解决方案时，查看是否有任何大公司在使用它。你的 iOS 应用程序最好的后端需要像岩石一样坚固、稳定，并得到很多支持，看看那些大家伙在做什么就知道了。你可以肯定的是，有很多钱可以花的公司会花时间研究和审查这些解决方案，这样你就可以考虑他们正在使用的任何东西，比研究不足的选择更安全一点。

通常他们会选择三大巨头之一:AWS、Azure 和 IBM。或者，他们会决定举办自己的。

无论你选择什么，记住，如果你需要改变，早点做。不要等到太晚的时候再做。如果您觉得您的数据不太适合您最终的目标，请尽快做出更改，因为您不想因为不断尝试让您的数据适合特定空间而筋疲力尽。

想要更多信息吗？点击这里查看[全部对话](https://share.transistor.fm/e/ffcb9fc1)和[订阅我们的时事通讯，了解最新消息。](https://brightdigit.com/subscribe/)

*最初发布于 2019 年 7 月 30 日[https://brightdigit.com](https://brightdigit.com/blog/2019/07/30/best-backend-for-your-ios-app/)。*