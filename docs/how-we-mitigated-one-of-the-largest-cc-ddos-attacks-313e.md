# 我们如何缓解最大的 CC (DDoS)攻击之一

> 原文：<https://dev.to/edowadohu/how-we-mitigated-one-of-the-largest-cc-ddos-attacks-313e>

2019 年第一季度，我们的一个客户向我们提出了一个问题:他们的网站受到了攻击。

无论他们如何试图绕过这个问题，他们的网站总是崩溃。这种在特定客户端上使用的攻击被称为分布式拒绝服务(DDoS)攻击，具体来说就是挑战协作(CC)攻击。

在本案例研究中，我们将更详细地探讨什么是 DDoS 攻击，定义此处被起诉的 DDoS 攻击的具体类型(CC 攻击),并解释我们如何帮助我们的客户缓解该问题。

## 什么是 CC 攻击？

在野外有许多类型的 DDoS 攻击，CC 攻击被归类为 DDoS 应用层攻击。当 CC 攻击发生时，多个 HTTP 请求被同时发送以使服务器过载，因为统一资源标识符(URIs)必须承担复杂的算法或数据库操作以耗尽目标 web 服务器的资源。

[![Imagine multiple missile trucks firing at your website](img/2e13e5b7ddacbff1b67654047be744a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YtB019d4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/0%2ACCpIakredycRvXRd.jpg) 
*想象多辆导弹车向你的网站开火*

攻击者模拟了一个场景，其中大量用户一直在访问页面。因为被访问的页面需要大量的数据操作(消耗许多 CPU 资源)，所以 CPU 使用率保持在 100%的水平，直到正常的访问请求被阻塞。

在此查看所有类型的 DDoS 攻击[。](https://learning.mlytics.com/security/types-of-ddos-attacks/)

有趣的事实: CC 攻击在 2004 年被称为“挑战 Collapsar”攻击，当时一位昵称为 Kiki 的中国黑客发明了一种叫做 Collapsar 的黑客工具，能够用 HTTP 请求突击服务器。

## 我们最近见过的最大的 CC (DDoS)攻击之一

我们的客户尝试了他们能想到的一切来缓解攻击:他们试图通过内部 DDoS 缓解来解决问题，并使用内容交付网络(CDN)服务和提供商，这些服务和提供商声称他们可以帮助缓解 DDoS 攻击。但是每次尝试都失败了，当重新启动时，他们的网站会在几分钟后关闭。

当客户找到我们来帮助解决他们的问题时，我们立即采取行动来减轻攻击。经过调查，我们对这种 DDoS 攻击的巨大规模感到惊讶:每分钟有 3 亿个请求被发送到我们客户的服务器，黑客利用“用户模仿”，这种做法将攻击流量伪装成分布在多个 IP 地址的正常用户流量。

[![Approximately 1.3 Tbps of network attack](img/d6076c709c72c7992a8885bed721361b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IL1SK99h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/0%2AmSObO1cH5XR6E4-R.png) 
*约 1.3 Tbps 的网络攻击*

[![Approximately 300M QPM of application attack](img/e212930ef0729037780ff0a40bd234aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fjV-jhkn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/1%2A0-10IUElKZj_mVWs3Vjlpw.png) 
*约 300 米应用攻击 QPM*

我们能够减轻攻击，让我们客户的网站重新启动并运行，即使在这样一个蛮力 CC 攻击。

## 我们如何减轻攻击

我们使用双管齐下的 DDoS 保护方法来缓解这种特定的 CC 攻击:

1.  **地理限制:**通过保护来自主要用户群所在国家和地区的流量，以及阻止来自俄罗斯、乌克兰和印度等已知“攻击区域”的流量，我们能够将传入流量限制在特定区域。

2.  **启用基于浏览器的质询:**我们的 web 应用防火墙(WAF)允许我们使用基于质询的算法来过滤 CC 攻击机器人。基于全球公共云基础设施，我们可以通过多 CDN 利用该计算能力，根据攻击情况自动调整防御规模。正是这种能力让我们抵挡住了每分钟 3 亿个请求的 CC 攻击。

为了实施这一战略，我们需要一个坚实、强大的基础设施。mlytics 使用[多 CDN](https://mlytics.com/features/multi-cdn/) ，它结合了 2300 多个 pop 的能力，为我们提供了 2,600 Tbps 的容量。这给了我们一个[全球网络，它拥有每秒数兆比特的能力](https://mlytics.com/solutions/ddos-protection/)，可以抵御 DDoS 攻击。

通过将有效的过滤与我们网络的力量相结合，我们能够帮助我们的客户抵御强大的 CC 攻击，并使他们的网站恢复正常运行。