# 餐饮服务教会了我什么软件架构:第 1 部分

> 原文：<https://dev.to/dealeron/what-food-service-taught-me-about-software-architecture-3a77>

我妻子和我曾经一起在食品服务部门工作(Chili's 和 Gordon Biersch)。当我们在餐馆时，我们喜欢数每个服务员有多少张桌子，有多少员工和有多少顾客，经理数等等。

最近，我开始能够在我们对不同餐馆的观察和常见的应用程序设计/工具之间建立联系。其中一些最终成为疯狂的思想实验，比如“云服务的食品服务等价物是什么？”。人们常常会惊讶地发现有如此多的相似之处。

这里有一些这样的思想实验:

## 单体 vs 微服务

<figure>

[![Overhead view of a large rock casting a shadow on a beach.](img/5347e1f60b54d7e60e6e9c8ee9c0cd48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hW06i-hc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/byux8i26rj4hhchx8atm.jpeg)

<figcaption>Photo by [Dan Meyers](https://unsplash.com/@dmey503) on Unsplash</figcaption>

</figure>

整体式应用程序是为在一个应用程序中处理公司需要的一切而构建的。微服务是较小的应用程序，旨在专注于非常具体的任务。

餐饮业中最常见的例子是咖啡店。大多数咖啡店的员工被期望能够处理任何需要他们完成的任务，并且经常在不同的任务之间切换(一会儿处理收银台，一会儿煮咖啡，一会儿清理盘子/垃圾)。

这些员工一般都有大致相同的资源成本(时薪)，所以即使你只是需要一个新员工帮忙清理垃圾，你也要支付一个员工的标准化成本。虽然这样的团队通常更容易管理，因为每个员工之间没有太多的偏差(个性之外)。

食品服务中的微服务在坐式餐馆中更为常见。员工接受培训，并被分配特定的角色:服务员、送餐员、服务员、厨师、洗碗工、招待等。

这些角色的主要好处是很容易扩展单个任务。如果未摆放的餐盘是性能的瓶颈，您可以在不影响任何其他角色(服务员、主人等)的情况下，在地板上摆放更多的餐盘。).由于每个角色的预期工作更加细化，培训也变得更加简单。你不需要教一个洗碗工如何优雅地处理一张抱怨寿司是生的桌子。

现在的趋势是尝试并瞄准基于单片的微服务。将应用程序设计成只专注于它们的特定任务，可以让您在不太担心影响其他应用程序的功能的情况下进行更改，并且通常更适合让多个开发团队彼此独立地工作。

也就是说，就像在上面的场景中一样，monoliths 在需要支持的功能集非常有限的小公司中有非常强的优势。降低的复杂性可以节省许多开发时间/会议，这对小公司和初创公司相当重要。

## 缓存

<figure>

[![Someone searching through a crate of vinyl records](img/17484c0feaf94856406ac55f6d8d10e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lZCGaMdz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e1wx87vye9sf2q8rbzyz.jpeg)

<figcaption>Photo by [Leigh Cooper](https://unsplash.com/@leigh_cooper) on Unsplash</figcaption>

</figure>

缓存本质上是跟踪一个问题的答案，以防这个问题再次被问到。

这一点在餐饮服务中相当微妙，但却很有影响力。当咖啡店里的顾客要脱脂牛奶，而咖啡师去拿牛奶时，他们可能会发现商店已经关门了。对于一些咖啡师来说，这可能发生在他们在菜单上写上“脱脂奶用完”之前。许多 POS 系统还允许经理不让员工敲入他们没有的某个项目，因此问题永远不会反馈给厨师。

现在，如果有人出去买脱脂牛奶，但没有把菜单上的信息清除掉，人们仍然会认为他们没有脱脂牛奶了。缓存对该问题有一个旧的、现在不正确的答案。

缓存到处都在使用。您的浏览器为该页面上的 Javascript 和 CSS 做了这件事，以减轻承载 Javascript 和 CSS 的服务器的压力，并改善浏览器中的页面加载时间。一些应用程序将经常访问的资源保存在内存缓存中，以缩短加载时间。最大的问题是如何通知缓存值已经更改(如果用于托管该页面的 Javascript 发生更改，您的浏览器可能在 30 分钟内都不会知道，直到它再次检查)。

## 推 vs 拉

<figure>

[![A beverage being dispensed into a glass mug](img/02ef0f4a2f9895f03d2b678f6b8b2d6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KSbDbrpl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5t6e3zqsimtxap11596m.jpeg)

<figcaption>Photo by [Louis Hansel](https://unsplash.com/@louishansel) on Unsplash</figcaption>

</figure>

推模型是一种每个应用程序告诉另一个应用程序信息的模型。

拉模型是一个应用程序需要向另一个应用程序请求信息的模型。

这其实是餐饮服务界很有意思的一个。大多数坐下来吃饭的餐馆遵循拉动模式。服务员应该检查顾客，问他们是否准备好了或者需要什么。因此，服务员通常会花费大量资源(在这种情况下，时间是服务员最重要的资源)检查桌子，确保他们没有任何新的信息提供给服务员。一个好的服务员之所以优秀，很大程度上是因为知道如何调整这些检查，因为做得太频繁会对顾客和服务员都造成负担，而做得不够会使服务员错过重要的新信息(饮料需要重新斟满)。

我见过的最传统的推送模型实现之一是 diners。在餐馆的大部分时间里，服务员不会花太多时间检查桌子的状况。相反，他们确保在客户需要向他们提供信息(用技术术语来说，发送命令)的情况下，他们能够可靠地联系/出现。

此外，大多数用餐者让顾客提前付款，而不是在餐桌上付款。这实质上是服务员向顾客发出“买单”的命令。此时，服务员不需要花费任何资源(时间)来检查顾客，除非顾客向他们要求什么(发送另一个命令)。然后，顾客在他们的时间将支票带到前台(实质上是将他们自己的“支付账单”命令发送到前台)。这种账单支付方式的区别通常是服务员可以一次拿 6-10 张桌子(就餐者经常这样做)和被限制在 4-6 张桌子(大多数 2-3 星级餐厅都这样做)之间的区别。

最近，顾客点餐时也有使用桌边平板电脑的趋势。这是顾客能够将命令推给服务员(或者有时直接推给厨房)的另一个例子，而服务员不需要拉动/检查它们。

从应用程序的角度来看，您通常希望确保命令被推送，而不是信息被拉取/轮询。这将避免来自计划同步作业的最新信息的延迟，并且通常可以避免由缓慢查询导致的瓶颈。

然而，在许多情况下，你无法避免获取信息或进行检查。

## 负载均衡

<figure>

[![Coffee being poured into a stack of mugs](img/88c2c8907b92bf0c1488b5c949c9fc57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TnZbKAST--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9gsxt6o45bs6rwex5naq.jpeg)

<figcaption>Photo by [Nathan Dumlao](https://unsplash.com/@nate_dumlao) on Unsplash</figcaption>

</figure>

负载平衡是指当你有许多应用程序做同样的工作时，有一个应用程序坐在它们前面分配工作。

主持人往往是餐厅的负载平衡器。通常有许多服务员在楼层工作，如果顾客可以自由决定坐在哪里，他们可能最终都坐在同一个服务员区，导致一个服务员负担过重，而其他服务员无事可做。或者更糟的是，他们可以去一个区域，那里的服务员因为任何原因都不在(休息，封闭区域，等等)。).主人的工作是知道有多少服务员在轮班，他们可能有多紧张。

这实际上是对负载平衡器实际工作方式的直接翻译。你看的大多数网站都设置在负载均衡器后面。您可能会遇到数百台服务器，但负载均衡器会跟踪哪些服务器当前处于活动状态，哪些服务器可能负载过重(或有错误)，并将流量定向到它认为合适的服务器。

## 更来了！

这是一个正在进行的思想实验，我已经为未来的文章准备了更多的比较(希望在下周左右)。敬请关注更多内容！