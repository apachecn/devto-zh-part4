# 通过 Powershell 功能自动扩展 Azures SignalR 服务

> 原文：<https://dev.to/mattbrailsford/auto-scaling-azures-signalr-service-via-powershell-function-45j5>

## 简介

我目前正在为一个相当大的自行车比赛项目工作，组织者希望在他们的网站上显示一个现场比赛进度图。为了做到这一点，组织者已经与第三方服务提供商合作，他们可以在比赛前为 pace 车辆配备 GPS 跟踪器，并提供一个 API，我们可以通过该 API 访问车辆的当前位置。

现在，自行车赛事产生了大量的 web 流量，在比赛日可以达到超过 30，000 个并发连接，因此，与其让每个客户端都直接连接到跟踪 API，我们认为更好的方法是使用一组 Azure 服务来卸载网站和第三方 API 的这些需求。为此，我们决定采用以下设置:

*   1 x [Azure SignalR 服务](https://azure.microsoft.com/en-gb/services/signalr-service/) 这将作为我们的消息中心，向所有连接的客户端推送当前汽车位置
*   **1 x 基于定时器的 [Azure 函数](https://azure.microsoft.com/en-gb/services/functions/)** 这连接到第三方 API 服务以检索当前汽车位置并将其推送到 SignalR hub
*   1 个基于 Http 的 Azure 函数它充当 SignalR 协商端点，用于将 web 客户端连接到 SignalR hub
*   **1 x 地图网页**这是网站上的地图页面，显示一个[地图框](https://www.mapbox.com/)地图，并有一个基本的 SignalR javascript 客户端，该客户端连接并监听 SignalR 服务以获取汽车位置信息，并相应地更新地图标记

我不打算在这里深入研究各个部分的代码，也许我会在另一篇文章中进一步研究它们，但在一天之内，我们就有了一个可行的解决方案，所有的组件都按照我们的希望连接起来，最终形成了一个在地图上自动移动的地图标记(不幸的是，我还不能展示它，因为这个项目还没有上线，但我相信你可以在地图上画一个标记😁).

然而，我们最大的问题出现在我们开始计划网站预期的流量水平时。

## 问题

我们遇到的问题是，由于活动的性质以及比赛只持续几个小时的事实，我们预计网站上的流量不会全天保持一致。这一天开始时基本上没有连接，但当比赛开始时，它可能会非常快地上升到 30k+大关，然后在比赛结束后突然下降。除此之外，这是一个多种族事件，一周内都有比赛，有时每天都有多场比赛，这意味着该网站的流量水平将有点像过山车。

如果你在项目开始时和我想的一样，你可能会想“这不就是 Azure 要做的吗，去掉所有的扩展和资源管理，这样你就不用担心了？”。好吧，当谈到信号服务，似乎不是这样。

现在，从某种意义上来说，它确实提供了扩展，但不幸的是，它完全是手动的，因此需要我们通过上下滑动滑块来按需扩展资源。

[![Azure SignalR Scaling Slider](img/a8b3f2e3d0ee0a8fe72ccf9341433e3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RYXuZyKQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zw7bw16masr9qumhswgj.png)

不用说，这对我们来说是行不通的，所以我们必须找到另一个更自动化的解决方案。

## 解

幸运的是，很多 Azure 服务都可以使用各种 API 来提供和更改，SignalR 服务也不例外。不幸的是，看起来以前没有其他人这样做过，尽管我确实发现有一个人[向微软技术支持部门询问了同样的问题](https://social.msdn.microsoft.com/Forums/en-US/2430f02e-a028-4a93-9050-9a1cc5c56033/how-can-i-autoscale-unit-count-in-azure-signalr-service?forum=windowsazurewebsitespreview)，我联系了他，他很友好地[分享了他们的方法](https://staffordwilliams.com/blog/2019/07/13/auto-scaling-signalr-service-with-logic-apps/)，然而此时我已经开始着手自己的解决方案，他自己也承认，他的解决方案无论如何都不理想。

我们对这个问题的解决方案是创建另一个 Azure 函数，这次使用 [powershell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-6) 和 [Az 管理模块](https://docs.microsoft.com/en-us/powershell/azure/new-azureps-module-az?view=azps-2.4.0)来为我们执行管理任务。该功能是基于定时器的功能，计划每 5 分钟运行一次，并将执行以下任务:

1.  创建 azure 连接，验证链接到我们希望管理的 Azure 帐户的[服务主体](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal),以便为以下操作设置上下文
2.  获取有关 Azure SignalR 服务资源的当前配置的信息
3.  查询 Azures Metrics API，查看自该函数上次运行以来的 5 分钟内的最大连接数
4.  确定我们是否达到/低于当前配置配额的限制，如果是，我们应该扩展到哪个级别
5.  如果我们已经确定我们不在最佳规模上，那么将资源更新到新的规模级别

**旁注:**我在这里让这一切看起来像是事实，就像这是我从一开始就计划好的，但事实是，我实际上对 Azure 函数、Powershell 和所有 Azures APIs 知之甚少，所以这确实花了我几天时间来拼凑，但如果我告诉你每一个步骤，这篇文章会变得有点长😁

## 代码

为了创建我的函数，我按照教程[开始使用 Powershell Azure 函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-powershell)来设置我的文件夹结构和样板文件。下面是您想要修改的关键文件以及执行我们的行动计划所需的代码。