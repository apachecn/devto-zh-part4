# 现代 Web 开发太复杂了吗？

> 原文：<https://dev.to/ohryan/is-modern-web-development-too-complex-5c40>

作为一个已经在这个行业呆了几乎很长时间的人，现代 web 开发的轨迹令人担忧。通常当我退后一步时，“现代”工具似乎过于抽象、相互依赖和复杂。当我说“现代工具”时，我指的是一系列旨在改进开发过程的技术:依赖管理(NPM、composer 等)、框架(react、laravel 等)和一般的“devo PS”(docker、AWS soup 等)。加入当今任何流行的解决方案。

或者换一种说法…

> ![unknown tweet media content](img/62731c4d4a272e849fb0744e9cb277e0.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1142800692232564736/pu/vid/272x480/re70ueiT7cZ_lbEB.mp4?tag=10" type="video/mp4"></video>![Jared Palmer profile image](img/4494cedaed8afc7138447ef794a3552a.png)贾里德·帕尔默[@ jaredpalmer](https://dev.to/jaredpalmer)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)现代 web 开发14:24PM-23 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1142800704580591617)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1142800704580591617)

这并不是说这些工具一定有什么问题。我更担心的是，他们正在伤害我们解决问题的**方法**。

* * *

本周早些时候，几个月前的一篇 Hacker Noon 文章《用 Factorio 理解卡夫卡》进入了我的工作间隙。

这篇文章是我最关心的思考方式的典型例子。首先，将一个 web 开发问题+解决方案与[Factorio](https://www.reddit.com/r/factorio/)——迄今为止最复杂、最困难的 RTS 之一——相比较，这本身就很能说明问题。

作者从设置大多数开发人员应该熟悉的问题开始。

> 假设我们有三个微服务。一个是开采铁矿石，一个是将铁矿石冶炼成铁板，一个是用这些铁板生产铁齿轮。我们可以用同步 HTTP 调用来链接这些服务。每当我们的采矿钻机有新的铁矿石时，它都会对冶炼炉进行 POST 调用，然后冶炼炉会向工厂发送 POST。

因此，我们有 3 个以线性方式相互依赖的服务。如果其中任何一个失败了，整个系统都会崩溃。似乎是零容错，这可能是不好的。

进入卡夫卡…

> 有了 Kafka，您可以以一种容错且持久的方式存储记录流。在卡夫卡的术语中，这些流被称为*主题*。
> 
> 对于服务之间的异步主题，消息或*记录*，在峰值负载期间以及出现故障时被缓冲。

干净利落。

但是坚持住。现在我们有**四个**故障点，而不是**三个！**事实上，我们现在已经引入了**单点故障**。如果卡夫卡层失败了，整个系统就会失败。

为什么我们应该相信 Kafka 而不是我们建立的 3 个微服务？有没有办法让单个微服务更容错？

为什么不两者都要？这篇文章的作者看起来像一个可靠的开发者，他知道自己在做什么。也许潜在的假设是，我们的微服务已经尽可能地容错，我们应该添加 Kafka 作为额外的容错层。

我们在堆栈中引入了第四种复杂且专业的技术。现在我们的团队需要一位卡夫卡专家…

* * *

这篇文章并不是对阿帕奇·卡夫卡的分析或批判。

它旨在为现代 web 开发人员解决问题的方式提供一个范例。我们倾向于构建或实现复杂的系统，在问题之上提供一个抽象层，而没有充分解决根本问题。

我担心的是，我们在处理过去的问题时，会倾向于实施当前的解决方案。

我很担心我们正在培养一代网络开发人员，他们在纸牌屋的基础上建造纸牌屋，解决不完全理解的问题，却没有恰当地解决这些问题。

现代网络开发太复杂了吗？最早出现在 [ohryan.ca](https://ohryan.ca) 上。