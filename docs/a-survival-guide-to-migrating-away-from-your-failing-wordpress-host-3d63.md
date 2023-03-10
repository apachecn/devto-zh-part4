# 从失败的 WordPress 主机中迁移出来的生存指南

> 原文：<https://dev.to/pagely/a-survival-guide-to-migrating-away-from-your-failing-wordpress-host-3d63>

并非所有主机都是平等的。说到托管 WordPress，这个事实对我们来说越来越清楚了，几乎每天都有人打电话来推销业务关键的 WordPress 网站，因为他们当前的主机让他们失望了。这是一个经常发生的对话，所以我们在这里讨论它，希望我们能接触到更多有这种经历的人，并提供一个行动计划(或者至少是一线希望)。并非一切都没了。

你现在的主人可能会因为很多原因而不再为你工作。例如，当你的网站成长时，如果你一直使用的共享虚拟主机不再工作了，那么激动人心的时刻就来了。或者可能你的主机缺乏 WordPress 的微调，或者缺乏[主机栈](https://pagely.com/tech/)，所以你的网站没有在你选择的 CMS 上以最大容量运行。也可能是你的主机缺乏识别问题和帮助你优化的支持知识；他们只是试图无耻地追加销售。比较基础？也许托管公司本身的基础设施正在发生变化，或者你只是不分享相同的道德和伦理。

不管是什么原因，它们都令人沮丧。好消息是什么？都是可以避免的。迁移主机似乎是一项艰巨的任务，甚至可能是你应该避免的事情，但是好处大于额外的工作。这是你的生存指南，让你在日常生活中以最少的摩擦完成这件事。

## 为什么你目前的网络主机可能会失败

为了制定一个行动计划，你首先需要了解你目前的情况有哪些是行不通的。这里有几个场景可以让你的大脑思考。您应该在这里仔细考虑，因为您掌握的细节越多，您就越有可能为您的迁移选择另一个提供商。[![failing host](img/19bf1bbdacf97391a2f0998383bdfdbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0JWo6Q71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pagely.com/wp-content/uploads/2019/07/LI_256_259.png)

1.  你的主机没有提供真正的托管 WordPress 服务。[补丁和更新](https://pagely.com/kb/en/handle-security-patches/)在没有适当测试和 QA 的情况下被应用。
2.  您的主机没有提供深入的 DevOps 知识。它们无法帮助您解决性能瓶颈问题。
3.  您的主机向您销售了一台运行过时硬件的物理服务器，您无法再扩展或满足需求。
4.  你的主机在共享容器主机上卖给你，而共享 VPS 跟不上你的站点和服务器上其他客户的需求。

像这样的问题不能被忽视。网站的安全性、性能和可伸缩性是使用顶级托管 WordPress 主机的基本好处。如果宿主自己在这些领域中的任何一个都成为了障碍，那么无疑是时候继续迁移了。

## 迁移到新主机之前需要准备什么

这份清单将有助于您在决定新主机之前了解基本情况。将这些发现作为你参加的任何会议的谈话要点，你越快了解新主人满足你在这些方面需求的能力，你就越能更好地了解最适合你的人。

[![prepare for migrating](img/79cd56c1c5a5937905eb773e35616bd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3BvjXeOQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pagely.com/wp-content/uploads/2019/07/LI_256_285.png)

*   了解你有多少个 WordPress 应用程序，它们是单个 WordPress 安装还是[多站点安装](https://pagely.com/kb/en/how-do-i-set-up-multisite-with-pagely/)。
*   你所有的 WordPress 网站消耗多少磁盘空间？
*   您的数据库消耗了多少磁盘空间？
*   通过 PHPMyAdmin 或命令行 SSH 访问来迁移数据库。
*   你在 WordPress 目录之外有任何自定义代码吗？

准备好这些问题的答案，并在任何销售拜访中提出来。另一条线上的代表应该已经准备好后续问题或解决方案，并且完全熟悉你正在陈述的每一点。如果他们在任何一个讨论点出现失误，那就是一个危险信号。

## 当你遇到困难时，问一个新的网络主持人的三件事

如果你的主人在最糟糕的时候让你失望了怎么办？也许您正在进行一场挑战您的服务器的活动，或者您的更新已经过时并使您的访问者面临安全漏洞。这是一个压力很大的情况，如果你在这样一个微妙的时刻选择这样做，你需要确保你做的是正确的。

我们的高层建议很简单。首先，你要确保你进入的是更可靠的*而不是*可承受的。第二，如果没时间自己灭火，找个团队帮忙。当外包对你的业务至关重要时，外包并不可耻(或浪费资源)。

在灭火和更换主机的过程中，你需要问自己几个问题:

[![ask your host](img/8541a75efc10930fc93a1a78f42efbcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lsi_jV4r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pagely.com/wp-content/uploads/2019/07/LI_256_284.png)

1.  新的网络主机是否提供[白手套迁移和入职服务](https://pagely.com/support/migration-request/)？
2.  他们提供可靠的基础设施和托管服务器吗？
3.  是否会有支持人员与我一起找出性能或安全性方面的差距？

准备好这些问题，这样一旦出现问题，你就可以立即寻求答案。十有八九，如果发生这种情况，你会分心，寻找你现在的位置和解决问题之间的最短距离。当你心不在焉的时候，这些问题可以帮助你做出最好的决定。

## 记住:你的主机失败不是你的错

如果你对你的 WordPress 站点了如指掌，但它还是崩溃了，这不是你的错。简单明了。事实很糟糕，但这是一个可悲的事实，也是一个常见的例子，说明当像托管 WordPress 主机这样的行业发展到企业看到了剥削用户的机会时会发生什么。

大多数大型网络主机使用华而不实的功能和营销让你迷上了低价，只是为了以后追加销售。能说会道的销售代表和高设计销售宣传材料并不一定能转化为出色的托管服务(当然，除非你是在和出色的销售代表交谈。我们都是能说会道的人，我们的托管服务[和入职服务](https://pagely.com/process/)非常出色，因为它既灵活又可定制)。

[![find the right host](img/6daa180836d51dd2fcf31a0675b51d86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DGB3akcB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pagely.com/wp-content/uploads/2019/07/LI_256_261.png) 大多数 web 主机不会透露你没有专用硬件，它们正在与其他客户共享资源，这对于任何寻求扩展和优化性能的用户来说绝对是错误的解决方案。你不能被另一个客户的资源使用所拖累，这是你不应该愿意承担的风险。

归根结底，[托管 WordPress Hosting](https://pagely.com/blog/managed-wordpress-hosting/) 对于不同的主机来说意味着不同的东西。他们中的大多数只是让 WordPress 自动为你更新，而不是你的插件，他们缺乏知识[真正优化](https://pagely.com/blog/optimizing-wordpress-for-speed/)和保护你的网站。对于刚刚起步的小规模博客和企业来说，这可能行得通。给你的？不会的。

如果我们留给你一件事，只有一件事，不要害怕迁移主机，如果你遇到的问题与您目前的供应商。你事业的成功不能等待。

我们很高兴与您一起深入探讨这些问题。[点击这里](https://pagely.com/contact/)联系我们，或者在评论中给我们留下问题。

### 需要帮助评估你的 WordPress 托管基础设施吗？我们提供一份免费的 WordPress 准备清单，你可以在这里申请。

有主持恐怖故事的吗？请在评论中告诉我...我想全部听完！