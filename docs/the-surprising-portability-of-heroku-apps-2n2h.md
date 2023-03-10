# Heroku 应用程序令人惊讶的可移植性

> 原文：<https://dev.to/heroku/the-surprising-portability-of-heroku-apps-2n2h>

当我每周写关于 Heroku 的文章时，我已经在 dev.to、Twitter 和 Reddit 上积累了大量的追随者。我的工作所在的这些社区是由互惠形成的:我们互相学习。通常，人们会在评论中提供有用的见解，提出我以前没有想到过的观点，或者以同样的方式。最近，我学到了三件主要的事情:

*   我对发展“场景”的看法是难以置信的以美国为中心。我写了每个人如何使用 MacBook，而其他国家的开发者(这是大多数开发者)很少看到他们在使用。
*   r/programming 上的人可能是混蛋！
*   关于 Heroku 有很多误解！

我知道我已经写了一整篇文章来解决关于 Heroku 的常见误解，但是 6 个月后，是时候再次讨论这个话题了，而且这一次是最经常出现的。

[!["did you miss me terribly?"](img/33c6eab531e4843657aefbf1b8f6dde1.png)](https://i.giphy.com/media/3o7WIq5u3jif09GK1a/giphy.gif)

## “如果你在 Heroku 上部署你的应用，你将永远与 Heroku 绑在一起”

这里的担心，即使没有提到这个术语，是可怕的“供应商锁定”虽然我会说锁定不是 Heroku 的问题，但我承认锁定是一件可怕的事情！从房间大小的大型机时代开始，就有公司发现自己与单一供应商的计算服务密不可分。如果 20 世纪 60 年代的企业选择 IBM，他们通常会发现他们的账单每年都在增加。如果他们选择了 IBM 以外的公司，他们的供应商通常会突然倒闭，没有为他们庞大的计算机提供服务的计划，剩下的一堆程序不能在其他人的机器上运行。

供应商锁定非常可怕，这也正是*为什么*你应该使用 Heroku 的原因。

## Heroku 上的应用程序比你在其他任何地方运行的应用程序都更容易移植，甚至是你自己的服务器

Heroku 不仅没有使用任何“秘方”——没有专有的 SDK，没有不具备导出能力的晦涩难懂的存储系统——而且 Heroku 还迫使你创建一个随时随地都可以使用的应用程序。

[![child being dragged on a suitcase](img/32d05d330c40f0b866e969097745f46a.png)](https://i.giphy.com/media/3oEdv8kjL7XxMyJFbW/giphy.gif)

开发人员普遍认为最容易移植的格式是自托管应用。即使你正在为你的机器使用主机服务，你也可以很容易地转移到另一台主机上，对吗？在这里，当我们深入研究大多数操作结构的糟糕设计时，问题就出现了。

## 我们来谈谈 12 因素应用

[12 因素应用](https://12factor.net/)专为易于扩展和移动的应用而设计。设计文档[由 Heroku](https://www.google.com/url?q=https://dev.to/heroku/twelve-factor-apps-a-retrospective-and-look-forward-4j4f&sa=D&ust=1565637858238000&usg=AFQjCNH7rvFOKBfs4x206lxLVi9A2J4VHw) 编写，并在业内其他部门得到广泛应用。通过这些因素，你也可以看到事情是如何出错的。

尝试移动应用程序时产生摩擦的一个重要原因是事实上的依赖性或配置:

*   "哦，是的，你必须复制新的遗迹配置文件"
*   "其中一个过程需要 imagemagick，所以在重新映像后安装它"
*   “我们用旧版本的[X dependency]设置服务器，因为如果下载最新版本，会有兼容性问题”

这些问题都是糟糕的设计给操作人员带来压力的例子，他们需要记住特殊的设置，实施变通办法，并且通常*手动做*我们的工具应该自动做的事情。

## 如果好的设计是隐含的呢？

虽然我觉得所有的 12 个因素都是一个伟大的设计目标，但考虑 Heroku 与这些目标的关系会带来一个惊喜:当你使用 Heroku 时，你会自动满足这些需求。

[明确说出你的依赖关系](https://12factor.net/dependencies)？因为 Heroku Dynos 需要一个依赖项列表(例如，节点应用程序的 package.json ),并且不允许您偷偷进入机器并安装组件，所以没有明确声明依赖项的工作 Heroku 应用程序是不可能的。

同样的道理也适用于所有代码都受版本控制的要求。Heroku 的更新是通过版本控制来实现的。

## Heroku 的很多魔力来自于好的设计

[![cackling witch](img/f90a0eaec46bb62455909ba024c5ad2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vOcZyALp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://giphygifs.s3.amazonaws.com/media/6Z3yLZzfZ0Kpa/giphy.gif)

为什么使用 Heroku 可以轻松扩展您的服务？强制的 12 因素思维真的很有帮助。由于对人工调整没有任何复杂的要求，所以旋转大量的 dynos 来接受额外的请求不会太痛苦。

Heroku 是一个很棒的平台，它有许多自托管平台、*和*所没有的工具[，如果你需要离开，你的应用程序已经准备好旅行——它已经准备好旅行，只需要你定制一个 procfile 来适应它的新家。](https://dev.to/mskog/heroku-vs-self-hosted-paas-2bk1)