# PyCon AU 2019 亮点

> 原文：<https://dev.to/englishcraig/highlights-from-pycon-au-2019-3joc>

我最近参加了 PyCon Australia，这是我在任何地方举办的第一次 Python 大会，也是我第二次真正的技术大会，但我可以自信地说，尽管样本量很小，PyCon AU 2019 是一次伟大的大会。以下是上周末的一些个人亮点。

## 第 1 天:专家跟踪日

我和我的同事在 T 恤衫订购截止日期后买了票，所以我们没有买到衬衫，在去可爱的达令港旁边的会议中心的路上，一只海鸥俯冲轰炸了我，并为它的麻烦得到了一大块我的肉桂角色(我很快就把剩下的塞进了我的嘴里，并发誓如果我们再遇到那只海鸥，就要对它进行圣经级别的报复:我现在基本上就像海鸥的疾速追杀一样)。我的 PyCon 周末没有一个好的开始。

### 卡梅洛特和神剑为 PDF 数据

虽然我目前还没有它们的任何用例，但我对 [Vinayak Mehta 关于从 PDF 文件中提取数据表的](https://2019.pycon-au.org/talks/extracting-tabular-data-from-pdfs-with-camelot-excalibur) [Camelot](https://github.com/camelot-dev/camelot) 和 [Excalibur](https://github.com/camelot-dev/excalibur) 包的演讲印象深刻。它可以为没有被分隔成单元格的表格使用间距，甚至有一个 GUI 来手动分隔复杂文档的行和列，这种方式真的很酷。我从未处理过 PDF 格式的政府报告，但是我曾经编写过脚本，用于将格式糟糕的政府发布的电子表格转换成类似可旋转表格的东西，并且我对这种工具非常尊重(有一个 GUI *和*一个 CLI！)和灵活性(可以定义字符过滤器、列数等等！)是他和其他贡献者已经构建到这些包中的。这几乎让我想从 pdf 中获取数据。差不多了。

### Unity 3D 环境下进行强化学习

在另一个信息的例子中——我不会在可预见的未来使用——但非常酷——尽管如此， [Paris Buttfield-Addison 谈到了](https://2019.pycon-au.org/talks/building-designing-teaching-and-training-simulation-environments-for-machine-learning)在 Unity 3D 中建立虚拟环境，用于训练强化学习模型，然后可以用来指导机器人或无人驾驶汽车。我听说过 Unity 3D，知道它为我玩的一些游戏提供了动力，知道我妻子曾经用过它，但仅此而已。因此，看到 Paris 通过在 Unity GUI 工具中拖放一些元素来快速模拟具有真实物理交互的对象和环境是很有趣的。然后，他演示了如何将 Unity 与 [Unity 机器学习代理工具包](https://github.com/Unity-Technologies/ml-agents)(用于将 TensorFlow 模型插入到 Unity 中)结合起来，具体方法是:遍历虚拟赛道的创建、应用强化学习的架构、强化学习模型的代码，然后播放他的推土机在他的虚拟 python 主题赛道上倾斜的视频，甚至没有摩擦障碍物，尽管众所周知这样的活动*是*赛车。

我以前在当地的 ML/AI 会议上见过使用虚拟环境来训练机器人的 AI，但当时这看起来像某种数学黑魔法；Unity 友好的用户界面和 Paris 谦逊的演讲风格相结合，使这一切看起来如此容易，似乎有一天我也可以创造一辆无人驾驶汽车。

### 威胁造型死星

我对安全一无所知，也不觉得这个话题特别有趣，但我狂热的《星球大战》迷妻子坚持要我去听这个演讲。所以，部分是为了我的婚姻，部分是被这个演讲的自负所吸引，我去听了马里奥·阿雷亚斯关于威胁建模的演讲。由于我目前的角色是一名基本的后端开发人员，我不太可能将我学到的技术付诸实践，但纯粹为了娱乐价值，Mario 的演讲值得一看。此外，如果您对在会议上发言感兴趣，请注意演讲的描述是如何撰写的，以使人们即使对威胁建模不感兴趣也想参加，以及演讲本身(从幻灯片到演讲)如何既有知识性又有趣。

## 第二天:主会议第一天(别想太多)

### 构建 Python 微服务的经验

在过去的一年里，我真的很喜欢 DevOps 和微服务，而[理查德·琼斯的演讲](https://2019.pycon-au.org/talks/lessons-learned-building-python-microservices)是 PyCon 上的两个演讲之一，从中我获得了关于概念、技术和工具的大量信息，我可以在不久的将来在项目中实施这些信息。听到他和他的团队在拼接使用不同框架和语言的微服务时所面临的挑战，以及他们所做的设计决策强化了我在其他地方听到的一些东西，并让我意识到一些新的可能性。特别是，他们将后端分成多层服务(例如，一层连接到前端，另一层连接到数据库)非常有趣。此外，我非常渴望尝试 Richard 提到的一些工具。我以前用过一点 [`cookiecutter`](https://github.com/cookiecutter/cookiecutter) ，但是从来没有创建过自己的，用它给所有微服务一个一致的、类似框架的目录结构听起来是个好主意。 [`pact.io`](https://docs.pact.io/) 对于我最近在跟踪有效请求和响应体方面遇到的一些问题来说，听起来像是天赐之物。一个解决方案是变得不那么粗心大意，但是拥有一个工具来执行服务之间的契约听起来比完全改变我的个性更合理。

### 足够先进的测试(深入探讨)

这是另一个让我思考的演讲，哇，我得试试这个东西，就像昨天一样。[扎克·哈特菲尔德-多兹的演讲](https://2019.pycon-au.org/talks/sufficiently-advanced-testing)，作为一个深入的人，给他时间来涵盖一些先进的测试概念，然后进入包 [`hypothesis`](https://github.com/HypothesisWorks/hypothesis) 的细节，它实现了其中的许多概念。我已经在使用 [`faker`](https://github.com/joke2k/faker) 为我的大多数测试设备生成随机值，但是在这次演讲中，Zac 进一步向我介绍了模糊测试的概念，具体来说，就是使用算法生成的随机性来探测你的代码在不中断的情况下可以处理的极限。理论上，这将创建您从未想到的输入和情况的组合，有希望在它们出现在生产中之前发现边缘情况错误。在我的日常工作中，我在一个巨大的 Rails 平台上工作，这个平台有很多很多相互连接的部分，我们富有想象力的用户创造了一个绝对不可思议的环境，破坏了代码库的某个遥远的角落，而这个角落自从四年前的空白 git 提交以来就没有改变过，这让我真正体会到了这种技术的潜在价值。

Zac 随后展示了他是维护者的`hypothesis`，以及我们这些普通人在自己的项目中实现模糊测试的方法，而不需要理解所有那些花哨的算法。我还没有真正深入到文档中，但是这个包看起来非常直观，有选项将随机生成的输入限制在给定的约束内，这有助于让您的测试通过，但也明确了您对应用程序中可以发生什么和不可以发生什么的假设。而且，众所周知，显性比隐性好。似乎还有一种机制可以保存失败的测试运行的输入值，这对于减少不可靠测试的痛苦大有帮助。

### 当软件和法律是一回事的时候

另一个例子是一个完整的谈话和演讲者， [Brenda Wallace 的谈话](https://2019.pycon-au.org/talks/when-software-and-law-are-the-same-thing)很有趣，很有娱乐性，Brenda 是一个令人信服的演讲者，轻松地将幽默和严肃的问题结合在一起。如果你认为将业务需求转化为代码很难，你有没有尝试过将几十年前的法律逻辑编码，这些法律多年来一直被这个政党和那个部长零敲碎打地修改？事实证明，不同的人对“孩子”的定义和*四岁半的确切年龄有不同的看法。这个项目背后的想法是我从来没有想到过的，它的目标(民主化对法律的理解和简化人们与政府官僚机构的互动)是值得称赞的。我自己最近获得了澳大利亚的永久居留权(更不用说与四个不同国家的移民部门打过交道)，我可以保证，即使你试图正确填写所有表格，并前往所有正确办公室的所有正确服务台，你也只是猜测了一半时间，因为政府的指示从来没有像一个老好人`if` / `else`那样清晰明确。*

## 第三天:主会议第二天

到了星期天，我已经筋疲力尽了。无论是整整两天的谈话、学习和社交，还是我前一天晚上喝的啤酒，还是我前一天晚上喝的威士忌，谁能说得准呢？不管怎样，我一整天都在猛灌咖啡因，我错过了比前几天更多的演讲，只是为了让自己从关注事物中解脱出来。

### 新 COBOL

对我来说， [Benno Rice 关于 COBOL 和我们如何谈论不同技术的演讲](https://2019.pycon-au.org/talks/the-new-cobol)的主要魅力在于他对编程语言的历史、它们的根源、主要用途以及编码者(通常有偏见的)对它们的理解的调查。在抑扬格五音步方面，我比得墨忒耳律接受了更多的正规训练，所以这对我来说是新的信息。像任何好的演讲一样，这个演讲激发了我想在有时间的时候阅读更多关于这个主题的文章。然而，像 COBOL、Lisp、FP 和 OOP 这样的简单历史可能会有点枯燥，所以 Benno 能够将他的历史课与我们如何谈论各种语言的更广泛的观点联系起来，更重要的是，与他们一起工作的人增加了直接的相关性，使其真正引人入胜。

## 加成:闪电对话

我不会进入具体的闪电谈话(虽然有许多好的，一些信息丰富，一些有趣，一些只是简单的怪异，一些以上)，但我只想说，整个练习对我来说绝对是会议的一个亮点。他们在周六(T0)和周日(T3)分别接近尾声(T1)和(T2)的时候，正好是下午茶中的咖啡因逐渐消退、晚餐的饥饿感逐渐增强的时候，来激励每一个出席的人，这样我们就可以充满活力地结束这一天。当只有五分钟来讲述一个人的材料时，并不是所有的演讲都会按计划进行，一些演讲者不得不被打断，但在如此短的时间内听到如此多不同的主题，技巧和技术的持续，快速的节奏和智慧的鞭笞意味着即使是混乱，意想不到的，也有助于我的享受。如果你有机会参加未来的 PyCon AU，你一定要参加闪电讲座，我强烈推荐观看今年的视频，因为那里有一些非常好的东西。