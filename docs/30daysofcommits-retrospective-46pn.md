# # 30 日提交回顾

> 原文：<https://dev.to/antjanus/30daysofcommits-retrospective-46pn>

结束了。我能呼吸了。我真的可以呼吸了。

我没想到 30 天的承诺会是一个如此大的挑战，但 7 月也是我个人生活中最忙的一个月——我搬了几次家，还出差了。

我表现如何？嗯，看看下面的 github 图表吧！

[![github timeline for 2019 with July highlighted](img/9045514a8ad24b74bb41b941213eb11e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zkitxHnJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/08/github-timeline-2019-1.png)

嗯，它不是超级明显的，但是粉色的矩形部分表示七月，除了黑色的正方形仍然是六月。有明显的差距，我不认为我曾经真正做到了 30 天的提交！

发生了什么事？我完成了什么？我还会这样做吗？

## 🥅的目标

我的目标简单明了，并在我关于这个主题的[介绍文章](https://dev.to/antjanus/i-m-starting-30-days-of-commits-in-july-who-d-like-to-join-me-n5o)中陈述:

> 在七月的某一天，一定要做一个开源项目

## 👬👭社区

我开放了我的 dev discord 服务器，让人们加入我的旅程，我们有相当多的人定期分享进展！它开始时很忙，随着时间的推移逐渐减弱，但是和其他人一起这样做是有帮助的。

我认为我们都很好地相互支持。

我习惯于做这些“一天一件事”的挑战——从 NaNoWriMo(写作挑战)到 NaNoEdMo(编辑挑战)以及介于两者之间的任何事情——尤其是我自己编的东西。每次，社区都可能是这些挑战中最令人鼓舞的方面之一。

让人们共同面对挑战，团结奋斗，庆祝彼此的胜利

## 😤那么我完成了什么？

我的目标是开发我的照片管理工具`Skok`。Skok 是捷克语中的`jump`(仅供参考，我是捷克人！Ahojte vsychni！).这是一个应用程序上的一个游戏，它启发了我制作它:Timehop(明白吗？明白了吗？).

无论如何，文字游戏放在一边，我想更接近这个应用程序的完整版本，我想我做到了，只是不是以我期望的方式。

### 🧪测试，测试，更多测试

我对这个应用程序的一个大目标是为后端设置测试，后端会做一些繁重的工作:对照片进行索引、重复数据删除，并根据照片数据库进行查询。

我*有没有*完成这个。通过努力工作和坚持不懈，我通过 Jest 设置测试。不仅如此，我还写了大量的测试，涵盖了所有脆弱的、难以手工测试的后端部分。

最后，这项工作最好的部分是每当我添加一个新的特性(比如缩略图生成！)，我已经做好了从前端独立构建它的一切准备。

### 🏗重构前端

去年年底，我开始在 React 中构建应用前端，今年，我在 React 上获得了更多的经验——我们正从 Angular 转向它。所以我也花了很大一部分“提交”时间来移动东西，让它们在我脑子里变得更有意义。

这可能是一个陷阱，因为重构(尤其是 React)非常有趣。很容易陷入一种模式，在这种模式中，你试图找出如何最好地组织组件和文件。

但希望这是我最后一次像这样进行大规模重构。

### 错误修正！

所以，在我写测试之前，我一直认为我的应用运行得很顺利...发现了大量的漏洞。我把它们修好了，对此我很高兴。:)

这意味着我的应用程序更加稳定了。

## 怎么样？为什么会有差距？

老实说，这很无聊，到了我不知道是否还想再做一次的地步。很长一段时间以来，我已经放慢了做兼职项目的速度——主要是因为工作占据了我大部分的发展精力，也因为我在工作中做我想做的事情。我正在编写 React 代码，我正在编写非常棒的代码，而且总体来说我很享受。

### 反面

所以在一天结束的时候，当我超级累的时候，我通常不喜欢做兼职项目。

偶尔，我会这样做，但通常是在几天的过程中快速冲刺，快速构建一些东西。

> 经验教训:连续 30 天在副业项目上编程，回报极其递减。

我会从周末休假中受益，或者相反，在一周中休息几天，在周末集中精力做一些兼职项目。

> 正如一周工作超过 40 个小时是有害的一样，即使你很累，不想做，也可以做兼职。

### 上升的方面

好的一面是，这是一场苦差事。这种折磨最终让我熬过了开发中的棘手部分，当我只想编程时，我发现很难处理这些部分——我完成了测试。我是*，所以*很高兴我有测试。我不是 TDD 教条的狂热信徒，但是拥有后端测试使得独立于前端开发后端复杂逻辑变得更加容易。

与传统的服务器工作(专注于 CRUD)不同，我一直在开发一个处理文件系统的应用程序，它必须创建缩略图，必须在文件夹中爬行。我真的需要进行可靠的测试，并且仅仅使用应用程序很难发现哪里有 bug——我做到了，我在我认为工作顺利的地方发现了 bug。

> 另外，我将带着一款为更大冲刺做好准备的产品离开。

## 下一次冲刺

我计划在年底再做一次`Shippening`活动。如果你不跟着我，你可能不知道，但去年，我是组织一个松散的活动称为 Shippening 的一部分。圣诞节和新年之间为期 3 天的产品冲刺/黑客马拉松。虽然我不庆祝圣诞节，但它对我来说也是一个“死亡地带”。在这段时间里没有大的节日，工作通常会慢下来甚至停止，等等。

我希望在那里见到你！我非常高兴能为这种黑客马拉松设置一个应用程序！:)