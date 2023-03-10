# 凯文·鲍尔访谈录

> 原文：<https://dev.to/codetips/an-interview-with-kevin-ball-5dip>

[![An interview with Kevin Ball](img/b60b46a427a0caf854e7c2527aec1e8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UrdcjvHY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/08/Webp.net-compress-image.jpg)

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。

* * *

在这次采访中，我们采访了凯文·鲍尔( [@kbal11](https://twitter.com/kbal11) ),他是 [ZenDev](https://zendev.com/) 的创始人和 [Friday Frontend](https://zendev.com/friday-frontend.html) 时事通讯的创建者，讲述了他在这个行业的经历。

> 你能介绍一下自己吗(姓名、在科技行业的工作年限、最喜欢的语言等)以及你为什么同意接受这次采访？

当然可以。我是凯文·鲍尔(经常被称为 KBall)，我从 2004 年开始从事科技行业，到现在已经 15 年了。就语言选择而言，我非常务实，根据项目/客户来改变我使用的语言。

现在，和我的主要客户一起，我正在用 [golang](https://www.codetips.co.uk/tag/go/) ，一些 [python](https://www.codetips.co.uk/tag/python/) ，和一些 [JavaScript](https://www.codetips.co.uk/tag/javascript/) 编写大量代码。也就是说，我最喜欢的纯趣味编程语言是 Ruby。

我同意接受这次采访有几个原因。在科技行业工作对我来说很棒，让我有可能过上美好的生活&拥有各种各样的机会，帮助别人获得这些机会对我来说很重要。我也喜欢教学，做大量的写作和演讲，并发现我最好的想法/那些最终与其他人产生共鸣的想法通常来自于其他人问的问题，所以这是一个被问一些问题的机会，这些问题可能会变成内容的伟大想法。:)

> 谢谢你的精彩介绍，也谢谢你同意接受这次采访！在这个行业干了 15 年，你会学到很多知识，看到很多变化。过去 15 年最大的变化是什么，你现在知道的哪一件事是你希望能告诉 15 年前的自己的？

我认为最大的变化是云计算的可用性和开源软件的巨大爆炸，这使得推出和扩展软件变得无限容易。举一个简短的例子，我记得我在一个数据库分片项目上工作了几个月，因为我们的一个关键表即将超出大容量*内存(64GB ram，也许 512GB 磁盘？)的服务器上。我们需要在多台机器上分割这个表，但是它有 3 种不同的查找模式，所以这样做并不简单。为此，我们不得不在我们的 colo 购买和安装新的服务器。如今，我可以简单地分配一个云 SQL 服务器，通过 web 界面将其扩展到 400 多 GB 的 RAM 和 30 TB 的磁盘...但是如果点击一个按钮还不够的话，我可以建立一个弹性搜索实例集群，几乎可以无限扩展。所有这些都不需要接触任何一个真正的硬件。

就我希望告诉自己的事情而言，我会建议自己的一件事是更担心深入而不是广泛，尤其是在早期。科技生态系统中的东西比你所能学到的要多得多，如果你总是跳来跳去，很难积累足够的专业知识来产生重大影响。我很早就这样做了，只是想尝试一切。我看到很多人现在都在这样做，他们才刚刚起步，他们想学习 React、Vue、Node.js、数据库和其他各种东西。

相反，我建议(尤其是在早期):选择一些东西，并成为这方面的专家。要想在职业生涯中取得进步，你需要在你知道如何完成工作的领域建立一个专长。软件的伟大之处在于有许多共同的概念，所以深入研究可以帮助你以后走得更远。如果你花时间去掌握 React，理解好的组件设计，以及路由是如何工作的，以及所有这些东西，当你决定以后继续前进时，这些知识将会移植到 Vue 或 Angular 或 Svelte 或其他任何东西。如果您了解数据管理以及数据如何从一个 API 流经客户端应用程序的不同部分，以及是什么使 API 更容易和更难使用，这些知识将在您以后扩展到服务器和构建这些 API 时对您有所帮助。但是如果你试图一次学完所有的东西，你最终会变得笨拙，而且要花更长的时间去学习那些更深层次的概念。

> 云计算改变了一切。我认为自己很幸运，因为当我进入开发世界时，它已经是一件“事情”，但是你认为这阻碍了今天的软件开发人员吗？我们可以用一个按钮来启动和关闭服务器，但是这里面有负面的东西吗？比如说，现在有经验的开发人员不知道如何安装操作系统吗？这是件坏事吗？正在学习的开发者应该为学习硬件方面而烦恼吗？

我不这么认为，我认为它让我们在抽象和生产力上更上一层楼。只要抽象不会泄露，你就不需要学习它们。

当像 C 这样的编程语言第一次被引入的时候，人们不再需要学习汇编，有很多人担心这会导致低质量的开发人员，但我认为这反而会导致人们在更高的抽象层次上自然地思考。今天，我想没有人会反对你应该从汇编语言开始你的编程经验，同样的道理也适用于操作系统安装等。如果你对这些东西感兴趣，那很好，但是我不认为只要你能忽略它就有什么坏处。

> 我认为，尝试一下子学会所有的东西是每个开发人员都会有的，所以这是一个很好的选择！对于使用 CodeTips 或任何媒体来学习如何编程的开发人员来说，他们可能会被当今所有的选择弄得不知所措。有如此多的语言可供选择，而且每隔一周就有新的 web 框架出现，你会给刚开始学习的人什么建议呢？

这在很大程度上取决于你的个人情况和兴趣。去年，我写了一个由三部分组成的决策框架，名为“三个月”,强调了三个考虑因素:动机、动力和金钱。本质上，当决定学什么时，注意什么能让你兴奋，什么能让你为未来的学习做好准备，什么能让你学习它或者让你很快从中获得收入。

如果你在前端领域，对于大多数人来说，动力和金钱都支持在 React 上深入发展，React 有大量的工作和自由职业机会，核心概念与前端开发的更广泛趋势非常一致，所以你也为未来的任何变化做好了准备。也就是说，如果它不能让你兴奋，你就不会学得很好，所以看看其他选择。我喜欢 Vue.js，并且发现对于很多人来说学习 React 更容易，尤其是如果你来自一个不太传统的编程或者更视觉化的背景。如果你已经找到了一份工作，围绕那里正在使用的东西(或者他们感兴趣的东西)寻找机会，这样你就可以通过学习获得报酬。

试图追随“本周风味”是非常诱人的，但是，特别是当你开始时，这是一条不知所措的道路。我大力提倡实用主义和复合增长。关注那些能给你未来动力的事情，关注那些你可以马上或很快获得学习报酬的事情，这样你就不必在有报酬的“不学习”和无报酬的学习之间分配时间。

> 对于阅读这篇采访的人，你还有什么想补充的吗？

我认为重要的是强调软件可以是一个伟大的职业，即使你不想把每分钟都花在编码上。你确实需要比大多数职业道路投入更多的时间来保持最新和持续学习，但是关于必须一直对编码和代码“超级热情”的宣传是虚假的。你可以精心打造一个工作与生活的平衡，用它来支撑你的生活，即使这不是你的生活。我认识一些人，他们使用自由网络开发来支持他们作为专业音乐家、演员和旅行者的热情。

除此之外，如果人们对前端感兴趣，他们可能会喜欢我的[简讯](https://zendev.com/friday-frontend.html)，如果他们有问题，我的 [Twitter](https://twitter.com/kbal11) DMs 是开放的，或者我的电子邮件很容易在我的[网站](https://zendev.com/)上找到。

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。