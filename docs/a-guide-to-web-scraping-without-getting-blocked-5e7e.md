# 网页抓取指南

> 原文：<https://dev.to/scrapingbee/a-guide-to-web-scraping-without-getting-blocked-5e7e>

Web 抓取或爬行是通过下载和解析 HTML 代码来提取您想要的数据，从而从第三方网站获取数据。

但是你应该为此使用一个 API！

不是每个网站都提供 API，API 也不总是暴露你需要的每一条信息。所以提取网站数据往往是唯一的解决方案。

web 抓取有许多使用案例:

*   电子商务价格监测
*   新闻聚合
*   线索挖掘
*   SEO(搜索引擎结果页面监控)
*   银行账户汇总(美国造币厂，欧洲银行)
*   还包括许多个人和研究人员，他们需要建立一个否则无法获得的数据集。

那么，问题是什么呢？

主要问题是大部分网站都不想被刮。他们只想用真正的网络浏览器向真正的用户提供内容(除了谷歌，他们都想被谷歌刮走)。

所以，当你刮的时候，你必须小心不要被认为是机器人，基本上做两件事:使用人类的工具&有人类的行为。这篇文章将引导你了解所有你可以用来保护自己的东西，以及所有网站用来屏蔽你的工具。

# 模拟人体工具即:无头铬合金

## 为什么使用无头浏览？

当你打开浏览器，进入一个网页时，几乎总是意味着你在向一个 HTTP 服务器请求一些内容。从 HTTP 服务器获取内容最简单的方法之一是使用经典的命令行工具，比如 [cURL](https://en.wikipedia.org/wiki/CURL) 。

事情是，如果你只是做一个:卷曲[www.google.com](http://www.google.com)，谷歌有很多方法知道你不是一个人，只要看看标题的例子。头是伴随每个到达服务器的 HTTP 请求的小块信息，其中一块信息精确地描述了发出请求的客户机，我说的是“用户代理”头。仅仅通过查看“用户代理”标题，Google 现在就知道你在使用 cURL。如果你想了解更多关于标题的信息，维基百科的[页面](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)很棒，为了做一些实验，只要[到这里](http://www.httpbin.org/headers?json)，这是一个简单显示你请求的标题信息的网页。

用 cURL 修改标题真的很容易，复制合法浏览器的用户代理标题就可以做到。在现实世界中，您需要设置不止一个 header，但更普遍的是，用 cURL 或任何库人工创建一个 HTTP 请求并不困难，它会使这个请求看起来完全像是用浏览器发出的请求。每个人都知道这一点，所以，要知道你是否在使用一个真正的浏览器网站，就要检查一件 cURL 和 library 不能做的事情:JS 执行。

## 你会说 JS 吗？

这个概念非常简单，网站在其网页中嵌入一小段 JS，一旦执行，就会“解锁”网页。如果你用的是真正的浏览器，那么你不会注意到区别，但是如果你不是，你会收到一个 HTML 页面，里面有一些晦涩的 JS。

<figure>

[![](img/3af9841a6d66bcc3e8cf06ca1a4469d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7GCqCBX9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9jxuds0b3pa770dej6t8.png)

<figcaption>(an actual example of such a snippet)</figcaption>

</figure>

但是，这个解决方案也不是完全可靠的，主要是因为自从 nodeJS 以来，在浏览器之外执行 JS 变得非常容易。但是再一次，网络进化了，有其他的技巧来决定你是否在使用真正的浏览器。

## 无头浏览

试图在有节点的一侧执行 snippet JS 确实很困难，而且一点也不健壮。而且更重要的是，一旦网站有了更复杂的检查系统或者是大的单页应用 cURL，用 node 伪 JS 执行就变得没用了。所以看起来像一个真正的浏览器的最好方法是实际使用一个。

无头浏览器的行为将“完全”像一个真正的浏览器，除了您可以很容易地以编程方式使用它们。使用最多的是 Chrome Headless，这是一个 Chrome 选项，具有 Chrome 的行为，而没有包装它的所有 UI。

使用 Headless Chrome 最简单的方法是调用驱动程序，将其所有功能封装到一个简单的 API 中， [Selenium](https://github.com/SeleniumHQ/selenium) 和[木偶师](https://github.com/GoogleChrome/puppeteer)是两个最著名的解决方案。

然而，这还不够，因为网站现在有工具可以检测无头浏览器。这场军备竞赛已经持续了很长时间。

## 指纹识别

每个人，主要是前端开发人员，都知道每个浏览器的行为是如何不同的。有时可能是关于渲染 CSS，有时是 JS，有时只是内部属性。这些差异大部分是众所周知的，现在可以检测出一个浏览器是否真的是它所伪装的那个人。这意味着网站在问自己“所有的浏览器属性和行为都与我所知道的这个浏览器发送的用户代理相匹配吗？”。

这就是为什么在那些想把自己当成真正的浏览器的抓取者和那些想把无头网站与其他网站区分开来的网站之间会有一场无休止的军备竞赛。

然而，在这场军备竞赛中，网页抓取工具往往有很大的优势，原因如下。

[![](img/5cd51eae51993cfa55d101993933e573.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hKLM-IZV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6augn0y6fqtlqd08ratm.png)

大多数时候，当 Javascript 代码试图检测它是否以无头模式运行时，它就是一个试图逃避行为指纹的恶意软件。这意味着 JS 在扫描环境中表现良好，而在真正的浏览器中表现不佳。这就是为什么 Chrome 无头模式背后的[团队试图使其与真实用户的网络浏览器无法区分，以阻止恶意软件这样做。这就是为什么在这场军备竞赛中，网页抓取工具可以从这种努力中获利。](https://transparencyreport.google.com/safe-browsing/overview)

另一件要知道的事情是，并行运行 20 cURL 是微不足道的，Chrome Headless 虽然对于小用例来说相对容易使用，但在大规模应用时却很棘手。主要是因为它使用大量的 RAM，所以管理它的 20 多个实例是一个挑战。

如果你想了解更多关于浏览器指纹识别的知识，我建议你看一看 Antoine Vastel 的博客，这是一个完全致力于这个主题的博客。

为了理解如何假装你正在使用一个真实的浏览器，你需要知道的大概就是这些了。现在让我们来看看你是如何表现得像一个真正的人。

# 模拟人类行为，即:代理、验证码求解和请求模式

## 代理自己

使用真实浏览器的人很少会每秒从同一个网站请求 20 个页面，所以如果你想从同一个网站请求很多页面，你必须欺骗这个网站，让它认为所有这些请求来自世界上不同的地方，即不同的 IP 地址。换句话说，你需要使用代理。

代理现在不是很贵:每个 IP 大约 1 美元。然而，如果你需要在同一个网站上每天处理超过 10k 个请求，成本会很快上升，需要数百个地址。需要考虑的一件事是，需要不断地监控代理 IP，以便丢弃不再工作的 IP 并替换它。

市场上有几种代理解决方案，下面是使用最多的: [Luminati Network](https://luminati.io/) 、[炽焰 SEO](https://blazingseollc.com/) 和 [SmartProxy](https://smartproxy.com/) 。

还有很多免费的代理列表，我不推荐使用它们，因为它们通常很慢，不可靠，而且提供这些列表的网站并不总是透明的

代理被定位。那些免费的代理名单大部分时间都是公开的，因此，他们的 IP 会被 most 网站自动禁止。代理质量非常重要，众所周知，反爬行服务维护代理 IP 的内部列表，因此来自这些 IP 的每个流量也将被阻止。小心选择一个好的信誉代理。这就是为什么我建议使用付费代理网络或建立自己的网络

要构建您的，您可以看看 [scrapoxy](https://scrapoxy.io/) ，这是一个很棒的开源 API，允许您在不同的云提供商之上构建代理 API。Scrapoxy 将通过在各种云提供商(AWS、OVH、数字海洋)上创建实例来创建代理池。然后你就可以配置你的客户端，让它使用 Scrapoxy URL 作为主代理，Scrapoxy 会自动在代理池中分配一个代理。Scrapoxy 很容易定制，以满足您的需求(速率限制，黑名单...)但是安装起来可能有点繁琐。

你也可以使用 TOR 网络，也就是洋葱路由器。这是一个全球性的计算机网络，旨在通过许多不同的服务器路由流量，以隐藏其来源。TOR 的使用使得网络监控/流量分析变得非常困难。TOR 的使用有很多用例，比如隐私、言论自由、独裁政权中的记者，当然还有非法活动。在网络抓取的背景下，TOR 可以隐藏你的 IP 地址，并且每 10 分钟改变你的机器人的 IP 地址。TOR 出口节点的 IP 地址是公共的。一些网站使用一个简单的规则来阻止 TOR 流量:如果服务器收到来自 TOR 公共出口节点之一的请求，它将阻止它。这就是为什么在许多

与传统的代理相比，TOR 不会帮助你。值得注意的是，由于多重路由的原因，通过 TOR 的流量本来就要慢得多。

## 验证码

但有时代理是不够的，一些网站系统地要求你确认你是一个有所谓验证码的人。大多数时候验证码只显示给可疑的 IP，所以切换代理将在这些情况下工作。对于其他情况，你需要使用验证码解析服务(想到了[2 验证码](https://2captcha.com/)和[死亡验证码](https://www.deathbycaptcha.com/user/login))。

你必须知道，虽然一些验证码可以通过光学字符识别(OCR)自动解决，但最近的一个验证码必须手动解决。

<figure>

[![](img/8965264d29406823a8a2616a8e5b0ef8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gif1XNpL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fq70vlkx0ud047kn9us7.png)

<figcaption>Old captcha, breakable programatically</figcaption>

</figure>

<figure>

[![](img/7e3d2592d3ba316f7dc372dac34110d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yKi6fA01--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/68acxo4r89rla6u385qm.png)

<figcaption>Google ReCaptcha V2</figcaption>

</figure>

这意味着，如果你使用上述服务，在 API 调用的另一边，你将有数百人以低至每小时 20 码的价格解析验证码。

但是话说回来，即使你解决了 CAPCHAs 或者一看到代理就换代理，网站仍然可以检测到你的小抓取工作。

## 请求模式

网站用来检测抓取的最后一个高级工具是模式识别。因此，如果你打算为 URL[www.example.com/product/](http://www.example.com/product/)废弃从 1 到 10 000 的每个 id，尽量不要按顺序和恒定的请求速率来做。例如，您可以维护一组从 1 到 10 000 的整数，并在这个集合中随机选择一个整数，然后抓取您的产品。

这是一个简单的例子，有些网站也对每个端点的浏览器指纹做统计。这意味着，如果您不更改您的无头浏览器中的一些参数并以单个端点为目标，他们可能会阻止您。

网站也倾向于监控流量的来源，所以如果你想抓取巴西的网站，尽量不要用越南的代理。

但是从经验来看，我能告诉你的是，速度是“请求模式识别”中最重要的因素，所以你刮得越慢，被发现的机会就越少。

# 结论

我希望这篇概述能帮助你更好地理解网络抓取，并且你在阅读这篇文章时学到了一些东西。

我在这篇文章中谈到的一切都是我用来构建 [ScrapingBee](https://www.scrapingbee.com) 的东西，这是最简单的网络抓取 API。如果您不想浪费太多时间来设置一切，请不要犹豫测试我们的解决方案，首批 1k API 调用由我们负责:)。

不要犹豫，在评论中告诉我你想知道的关于刮擦的事情，我会在我的下一篇文章中谈论它。
快乐刮痧😎