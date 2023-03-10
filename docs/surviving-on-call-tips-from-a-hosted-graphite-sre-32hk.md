# 随叫随到:来自石墨 SRE 的提示

> 原文：<https://dev.to/bbhnn/surviving-on-call-tips-from-a-hosted-graphite-sre-32hk>

*最初发布在[主持的石墨博客](https://www.hostedgraphite.com/blog/surviving-on-call-tips-from-a-hosted-graphite-sre)上。*

随叫随到是痛苦的，任何不这么说的人都是在试图向你推销某种东西。也就是说，有很多方法可以让随叫随到成为更好的体验，尽管这是不可避免的。自 2016 年以来，我一直是 Graphite 主持人[的 SRE，所以我也做了相当多的随叫随到工作。已经有很多关于公司如何让随叫随到成为团队更好的体验的文章，幸运的是，我们在随叫随到后得到了强制性的一天休息，灵活的工作时间和远程友好的办公室大大减少了随叫随到的体验。这些显然是特定于组织的，但就这篇文章的目的而言，我想根据我所学到的，把重点放在你可以做的事情上，让你的个人体验变得更好。](https://www.hostedgraphite.com/)

# 通知卫生

‍

**“保持良好的卫生习惯很重要
至少如果你想和我的团队一起跑步的话”**
*德尔时髦的人类(如果你必须这么做)*
‍

通知卫生是我们大多数人都非常不擅长的事情——在工作内外。清理你的通知对于随叫随到来说是一个非常有用的策略，因为你很有可能会回复寻呼机，并比平时花更多的时间看屏幕。如果你使用 [Pagerduty](https://www.pagerduty.com/) ，我建议你为你所在的地区购买一张相关的 [Pagerduty V-Card](https://support.pagerduty.com/docs/notification-phone-numbers) 。接下来，您需要设置“请勿打扰”:对于我来说，这是一个亲密的家庭，我的队友的电话号码以及所有与寻呼机工作 V-Card 相关的号码。在那之后，我关闭了所有其他的推送通知，并把我的手机弹出到勿扰模式。这种设置确保我收到的声音通知要么来自近亲，要么来自电话报警、传呼机值班通知或紧急工作呼叫。我们在托管的 Graphite 上使用 [Slack](https://slack.com/intl/en-ie/) ，所以我也启用了工作时间之间的 Slack 通知，并且通常允许从早上 7 点到晚上 9 点的所有电话处理像医生预约、包裹递送或者最重要的外卖递送之类的事情。

这可能看起来需要很多努力，尤其是如果你使用手机的时候(你会被一大堆通知淹没)。如果这对你有用，那很酷，但是我在值班期间的目标是减少我主动考虑被传呼的时间，除非传呼机正在对我大喊大叫。

额外提示:当你设置寻呼机工作通知通道时，确保在第一次通知前留出一两分钟的延迟时间。这意味着，如果你在工作时看到一个页面实时出现，你可以在它破坏你的手机之前通过网络应用程序确认它。

注意:这将完全取决于您所要求的服务的响应时间。如果这是一个非常高优先级的服务，需要几秒钟的响应时间，那么这个策略是行不通的。但是，如果您的服务具有足够的弹性，并允许更长的事件响应时间，这种策略可以使您的事件管理更加有效，因为您能够正确处理问题，而不会经常关机。

在这种情况下，我建议让你的初始通知成为直接发送到笔记本电脑的推送通知，并结合发送到手机的通知——这样你就可以在手机关机前获得即时反馈，因为你直接看着屏幕，可以在那里确认。

# 个人护理

‍

**“我知道你过得很艰难
我来劫持你(劫持你)，我会爱你，而
我在尽情享受夜晚”**
*【卡莉·瑞·吉普森】(尽情享受夜晚)*

不管你喜不喜欢，大多数随叫随到的轮班都会让你疲惫不堪。例外情况是，当完全什么都没有发生时，我们渴望通过进行适当的事件跟踪、事后检查，并专注于基础设施和软件改进，以减少随叫随到的情况。也就是说，当凌晨 3 点面对数据中心的故障路由器时，人和老鼠的最佳计划都毫无意义。

所以你在凌晨 3 点被叫醒，你解决了你的问题，但是现在你醒了，不能再睡了。诱惑是起床，努力工作，早点完成工作，然后试着睡一会儿。然而，这种方式是危险的，因为它很容易陷入熬夜的模式，并错过了大部分的前半天。优先安排有规律的睡眠至关重要，努力坚持合理的作息也是如此。我是一个夜猫子，因为我们在 Hosted Graphite 有灵活的工作时间，我的工作时间通常是上午 11 点到晚上 7 点，我通常在凌晨 2 点左右入睡。所以即使我在早上 8 点被传呼，我可以起床开始新的一天，我也会一直睡下去。

当我第一次开始做随叫随到的工作时，我并没有这样做，而是努力去做...就一次。你可能不会认为多睡一两个小时会有什么不同，但事实确实如此。我记得那天我犯了很多小错误，感觉我的大脑在以四分之一的速度工作(对于认识我的人来说，这肯定说明了一些问题)。

繁忙的一周也会影响到你个人生活的其他方面——如果你发现自己工作到深夜，那么你会忍不住想在回家的路上点些食物或者吃个披萨。我发现，如果我在待命前不做一些准备，我最终会在一周内大部分时间吃垃圾食品，感觉比不做准备时更糟糕。当与潜在的睡眠障碍相结合时，不吃早餐以便在床上多呆一会儿的诱惑是另一个决定，这可能会让你的值班周变得扫兴。

为了避免这种情况，在去值班之前，我会买一盒某种谷物/早餐棒和任何我想吃的水果，并给自己定下规则，不吃早餐我是不会离开家的。如果我尽可能简单地吃一顿像样的早餐，就没有理由不吃。我像往常一样吃工作午餐，晚餐时会做两倍于平时的量，以防第二天我被传呼。至于那些充满极端事件的几周，任何你可以设置并在烹饪完成前忘记的事情都是理想的，这意味着即使你正在处理许多事件，你仍然会吃得很好。

最后，也可能是最重要的一点是，要评估自己的心理健康状况。你需要做任何能让你放松的事情，无论是电子游戏、安静地看书还是去健身房。不管是什么，你需要腾出时间来做一些不去想工作的事情，这样你就可以充电了。你可能一周随叫随到，但呼机不是你的生命。

记住有一个团队在你身后支持你，永远是件好事。在 Hosted Graphite，公司想要休息好、快乐的工程师，因为休息好、快乐的工程师犯的错误更少，工作做得更好，团队更强大。
‍

# 外出走动随叫随到

‍

**“如果你今天走进树林
，你一定会大吃一惊”**
*【泰迪熊的野餐】*

有一种观点认为，当你在做随叫随到的工作时，你需要呆在家里，与你的手机和笔记本电脑绑在一起，弯腰驼背，期待着下一个通知，这样你就可以迅速采取行动。事实并非如此。虽然我通常不建议去看电影或在高级法国餐厅度过一个夜晚，但你可以继续你的日常生活——只要你准备好几样东西:

## 良好的 4G/LTE 连接和可用作热点的手机

如果你是随叫随到，你应该有这个。在 Hosted Graphite，我们的电话账单由办公室支付，我们可以选择使用自己的设备或公司提供的电话。无论您的公司采取何种方式，您都需要随时访问互联网，以获得通知交付和实际事件响应时间。

## 你的笔记本电脑

显然，您需要一种方法来响应事件，并且您可能没有太多的选择。如果你足够幸运，能够挑选出用于随叫随到的设备，我会推荐 13 英寸的 macbook air 用于大多数 SRE 类型的工作，它严重偏向于在远程服务器上工作。它重量轻，便于携带。出色的续航能力也是一大加分项。对于 windows 倾斜(或者如果你只是想运行一些没有 faff 的 linux)我也听说了 Thinkpad X 系列非常好的事情。

## 一个电源组和各种充电器

你不想被抓到一个没电的手机或笔记本电脑，所以一个强大的电源是值得的，既可以安心，也可以在你被抓到没有插头接入的地方，如公共汽车或火车时，给你可能有的任何设备充电。

## 一个结实的背包，可以装下所有随叫随到的相关物品

这里的理想是有足够大的东西来舒适地容纳上述所有东西，但又不会大到让你到处拖着一个巨大的背包。当我随叫随到时，我倾向于使用一个简单的皮革斜挎包来上下班，而在其他地方，我使用[这个 MATEIN 背包(亚马逊英国)](https://www.amazon.co.uk/gp/product/B076LQ8WT8/ref=oh_aui_search_detailpage?ie=UTF8&psc=1)。它可以容纳我需要的一切，当所有空间都没有被使用时，它可以折叠得很薄。它的侧面还有一个方便的 usb 端口，所以你可以插入你的电源，而不需要拿出来。

你可能会有其他特定的需求，比如用于身份验证的 yubikey，或者用于 vpn 访问的刷卡器或其他类似的东西，所以我试图在这里只关注最基本的需求。最重要的是尽你最大的努力忘记传呼机，直到它离开你的生活。

# 终注

如果你从这篇文章中只学到一件事，那就是你需要把自己的幸福放在第一位，一旦你做到了这一点，随叫随到的其他方面就会变得更容易。我们居住的这些移动肉袋充其量是脆弱的，这既是随叫随到人员的责任，也是公司领导层的责任，以确保定期随叫随到的人员获得他们成功所需的资源，以及他们保持休息良好、健康和快乐所需的时间。

戴夫·芬内尔，SRE，在主办的石墨。