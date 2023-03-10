# 餐饮服务教会了我什么软件架构:第 2 部分

> 原文：<https://dev.to/dealeron/what-food-service-taught-me-about-software-architecture-part-2-7k9>

## 有状态 vs 无状态

<figure>

[![Woman ordering food in a restaurant](img/f63993d6a04bcd04d3ed8947c5cb1e88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GuDwE9mk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cpb0wdwdpdo9hmavrvkb.jpeg)

<figcaption>Photo by [Fraser Cottrell](https://unsplash.com/@sourfraser) on Unsplash</figcaption>

</figure>

状态*满*应用程序是需要在应用程序内保存信息(或“状态”)的应用程序。

State *less* 应用程序是那些在两次使用之间没有任何持久性的应用程序。

识别餐饮服务中有状态角色的最简单方法是识别履行该角色的员工是否可以在任何时候被替换为相同角色的任何其他员工，而不会对餐厅产生影响。

例如，通常可以在一个班次结束时换下一个服务员，新的服务员将能够继续工作，而不需要了解餐馆的状态信息。

然而，传统的侍者随身携带桌上的信息。“*9 号桌对坚果过敏*”、“*13 号桌买单*”、“*牛油果蛋黄酱卖完了*”。如果被替换，则必须将该信息传输给替换者，然后才能继续正常工作。然而，这可以通过将状态存储在外部源(POS 系统、记事本等)上来最小化。).调酒师也非常有状态，必须记录对话、顾客偏好等。

韩国烧烤店有相当无国籍的服务员。它们通常不会保留太多关于表状态的信息，它们只是对实际表的状态做出反应(这被认为是一个*外部*状态)。这使得在不干扰客户的情况下增加员工数量(向外扩展)变得非常容易。

一般来说，无状态使得应用程序更容易处理。就像上面的例子一样，它使得自动扩展应用程序(将应用程序的更多实例放在负载均衡器后面)来处理高容量时间段变得很容易。

如今，有状态应用程序最常见的用途是在服务器端存储用户会话(而不是在浏览器或前端应用程序中存储用户凭证，这会变得复杂)，或者在应用程序中进行缓存以提高性能。这两者实际上都可以卸载到在无状态应用程序之间共享的外部数据库，但是这增加了其他层次的复杂性。

## 异步设计

<figure>

[![A stack of steaming tortillas.](img/2860090562481a16e7f72dd0e8324c99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d9Oa_ZKx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0pigk9n50gk2ahnrnqox.jpeg)

<figcaption>Photo by [Octavian Catana](https://unsplash.com/@iisus_d_costea) on Unsplash</figcaption>

</figure>

异步设计是设计应用程序和软件，使其能够进行多任务处理。

一个简单的例子，几乎每天都有 15 秒钟的时间在辣椒店里:

*   在邮局为一张桌子打电话订餐
*   把玉米饼扔到玉米饼加热器上
*   设置好咖啡滴斗，用一只手冲泡咖啡，同时用另一只手往杯子里倒水
*   把玉米饼从加热器上拿下来
*   把所有东西都放在托盘上，然后走出去

我不仅能够在处理咖啡和水的同时多任务加热玉米饼，而且在开始时发送食物订单是让厨师开始他们烹饪食物的异步过程的一种方式(我稍后需要重新同步)。

幸运的是，我们人类非常擅长异步。我们将任务委派给其他人，可以用两只手同时做两件不同的事情，可以跟踪几项不同任务的进展，可以适应两项任务变得相互依赖(死锁)。

在不引入危险的情况下，使应用程序异步要困难得多。竞争条件(两个任务的结果不同，取决于哪一个先完成)和死锁(两个任务需要互相等待，导致都没有进展)对大多数开发人员来说几乎是噩梦。

如果设计正确，异步应用程序可以大大加快进程，有时可以帮助分配工作负载，使应用程序不会超负荷工作(想象一下，如果服务器必须处理烹饪食物，而不是让应用程序(厨师)并行工作)。

## 云服务

<figure>

[![Bottle of wine being poured into a decanter](img/2498e3b6509818d98451c217d6712647.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ss0vxbuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qchtod40k7j33ie88g5x.jpeg)

<figcaption>Photo by [Megan Markham](https://unsplash.com/@meganmarkham) on Unsplash</figcaption>

</figure>

云服务基本上是将应用程序的物理服务器外包给第三方来管理。

我去过几个葡萄园，他们有自己的厨房，当然也有自己的饮料，但是他们使用第三方公司来提供服务员。葡萄园可以对员工进行培训，让他们了解对他们的期望(做/不做车牌，处理特定区域等。)，但他们自己并不直接支付员工工资，也从来不需要担心管理员工的问题。

另一个很好的例子是餐饮公司。你不需要管理员工或告诉他们如何烹饪东西，你只需要告诉公司你需要什么食物和你可能需要多少服务员，公司会负责剩下的事情。

从开发人员的角度来看，这一切都归结为一个问题:我们是更愿意让一台计算机放在办公室的角落里托管我们的应用程序，还是让像微软或亚马逊这样的公司自己管理物理计算机？后一种选择的好处通常是，微软和亚马逊有大量的团队专门负责大规模地维护这些服务器，确保它们的持续可用性和安全性。这些服务通常还提供像无服务器应用程序这样的功能，否则这是不可能的。

坏处是微软和亚马逊喜欢钱。放在办公室角落里的电脑不需要钱(尽管电力公司、互联网提供商和管理那台电脑的 IT/devo PS/系统团队确实需要钱)。

## 无服务器应用

<figure>

[![Waiter speaking to a table outside of a restaurant](img/32d598cf487d4935fcc4ec8fdd7ab59d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CprZfcra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bfe42wb2w47mic7t1dpr.jpeg)

<figcaption>Photo by [Henrique Felix](https://unsplash.com/@henriquefelix) on Unsplash</figcaption>

</figure>

我怀疑这种实现是否真的存在于食品服务领域，因为人们不是这样工作的。

无服务器应用几乎是微服务和云服务的逻辑极端。

想象一下，如果你有一家餐馆，你付钱给一家第三方公司，让它随时为你提供你需要的所有服务员。这些服务员只在他们需要的时候工作，并且在他们开始/停止工作的时候打卡。

因此，如果你一整天都没有一个顾客，你就欠第三方公司 0 美元。如果早上有 100 名顾客，晚上没有，你只需支付公司早上提供的 4 或 5 名服务员的费用，而不需要支付他们晚上的任何费用。

根据职位的不同，这些临时员工可能有足够的准备时间(穿上制服、打卡等)。)甚至在不忙的时候，让它们留在身边会变得更加可取。

无服务器的目标是让每个员工都这样做，并给每个员工分配非常非常具体的任务。你甚至可以把服务员分成“餐桌迎宾员”、“点餐员”、“结账员”，并减少你需要支付给每个员工的金额，以匹配他们正在执行的非常精细的任务。在任何给定的时间点，你都不会有比你需要承担的当前工作量更多的员工。

自然，人们不会那样工作，但是应用程序可以。大多数云服务器为您的应用程序提供了没有专用物理服务器的能力，而是在需要时在任意位置运行。当设计正确时，这可以使云托管几乎免费，因为你不会不断地吃掉云服务的资源，只是每天很短的时间。

然而，这些无服务器应用程序在设计时往往需要考虑无服务器。就像上面的例子一样，您需要确保应用程序的启动开销非常低。一般来说，您还必须将应用程序设计为专注于解决一个非常精细的任务，以最小化运行时间/成本。

## 关闭思绪

<figure>

[![Chocolate Cake](img/57b1e46caacdf2157e54c34cb41a6493.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2tlzAoFW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lrwdma9vnrwm56j4sssx.jpeg)

<figcaption>Photo by [Toa Heftiba](https://unsplash.com/@heftiba) on Unsplash</figcaption>

</figure>

我经常想知道这里的相似之处是否存在，因为食品服务和软件架构都在试图解决同一个问题:不同的、通常是复杂的服务网络之间的流线型交互。如果是这样的话，一定还有其他行业有类似的相似之处。

<figure>

我还想知道，如果有人完全重新设计整个用户体验和用来迎合这种体验的角色，一家餐馆会是什么样子。我们会以巨石而告终吗？我们是否会以托管非常专业的员工的“云服务”告终，这些员工只在需要他们的时候才在需要他们的地方工作？“人性化”因素实际上对需要特定角色的重要性有多大影响(例如:如果我们发现服务员对系统没有价值，我们可以解雇他们吗)？

<figcaption>Cover photo by [Moss](https://unsplash.com/@mossphotography) on Unsplash</figcaption>

</figure>