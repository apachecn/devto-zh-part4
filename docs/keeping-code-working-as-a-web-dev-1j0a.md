# 减少接触你无法控制的东西...当你可以的时候

> 原文：<https://dev.to/jkvyff/keeping-code-working-as-a-web-dev-1j0a>

我只想问一个问题，为什么我们写的东西可以完美的表演，但仍然是垃圾。

系统设计中有一个概念叫高可用性。经常用来提供对系统按预期执行的水平的期望的简写是“五个 9”或 99.999%的正常运行时间，大约。许多领域都以系统可用性为目标，甚至更高(医院、航空设备和卫星软件)。

哇，多么美好的梦想，但是 WebDev 呢？我希望能够编写代码并在这些级别上执行，但是我在分布式硬件(服务器、路由器)和分布式软件(包、链接、库)中工作。让我们从托管我们站点的数据中心开始。

<figure>

[![](img/7a8d71ad250e0b3b631649682c72dfaf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KvUWcGyM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2iv602sn7k22jlrhe3pl.png)

<figcaption>https://hostingfacts.com/average-hosting-uptime-study/</figcaption>

</figure>

哎呀，最高的平均分只有四个 9，而我们甚至还没有写任何代码。

至少谷歌处理他们自己的主机，并且已经解决了这个问题。

> ![G Suite profile image](img/66e4a33d86f7275894b154432a00a259.png)G 套件@ G Suite![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)谷歌日历目前正在经历服务中断。敬请关注更新或关注此处:[goo.gl/NOZTZ](https://t.co/2SGW3X1cQn)2019 年 6 月 18 日下午 14:39[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1140992327290429441)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1140992327290429441)109[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1140992327290429441)209

..哦不

> ![unknown tweet media content](img/d4937fb087a1f152da912192eb519871.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D9WfflxX4AAn2tM.mp4" type="video/mp4"></video>![Chip Wolf profile image](img/753c1482558a7306cc25d480bc839170.png)Chip Wolf@ Chip Wolf uk![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ gsuite](https://twitter.com/gsuite)14:43PM-18 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1140993457504378881)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1140993457504378881)

看起来 G-Cal 停机了三个多小时，这是今年的第三个 9。

作为 web 开发人员，我们习惯于依赖如此多的依赖项，以至于我们甚至无法正确估计有多少行代码正在运行。

几年前，一名开发人员凯西·穆拉托里试图计算出一个典型的 web 堆栈需要引入和使用多少代码，结果止步于 5600 万行。当然，人们不会使用通过这个堆栈引入的所有函数和方法，但是它确实使得一些问题很难或者不可能实际测试。

<figure>

[![](img/37fb8fc8d8efecd65a26593007b9c13a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U6tnPNaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h20ztmlrsrepi4fa9nap.png)

<figcaption>https://www.youtube.com/watch?v=kZRE7HIO3vk</figcaption>

</figure>

我的观点不是我们不应该使用这些技术。但是只要意识到你带来了什么，如果你不理解它在更深层次上是如何运行的，试着限制你带来项目的代码，因为作为一个 web 开发人员，假设你的代码是完美的，你已经达到了四个 9 或者更差。

资源

*   [https://en.wikipedia.org/wiki/High_availability](https://en.wikipedia.org/wiki/High_availability)
*   [https://hostingfacts.com/average-hosting-uptime-study/](https://hostingfacts.com/average-hosting-uptime-study/)