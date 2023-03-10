# 质量第一的 UI 开发

> 原文：<https://dev.to/jperasmus/quality-first-ui-development-1b5n>

<figure>[![](img/51e898358d56f58e8d9e4c39904249c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AIcr7GCL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5tk-YnLnjSIIqrgPhJaDeQ.jpeg) 

<figcaption>照片由[凯兰·奥尔德沃斯](https://unsplash.com/@kyran12?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/focus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

本着与[移动优先](https://en.ryte.com/wiki/Mobile_First)和[线下优先](http://offlinefirst.org/)相同的精神，我想谈谈质量优先。

> 它仅仅意味着:从质量开始，不要事后才想到。

如果我们说我们是专业开发人员，我们生产的系统的质量是我们的责任。

这适用于你吗？问自己这两个问题:

1.  你做的大部分工作都围绕着软件开发吗？不是*编码*，而是*开发*
2.  有人付钱给你做这件事吗？

如果你对这两个问题的回答都是肯定的，那么恭喜你，你是专业人士了！🎉

> 你可以放开冒名顶替综合症了，你成功了！

没有人什么都知道。差远了。我们都还在掌握这门手艺的旅途中。大家都是从零知识，零经验开始的。

既然我们已经确立了质量是我们的责任，那就让我们来谈谈吧。

我们将从两个角度来看质量:UX 和 DX(用户体验和开发者体验)

在这篇文章中，他们没有纠结于正确的定义，而是从最终用户和开发者的角度来看质量。

我认为好的 DX 对生产好的 UX 大有帮助。如果你不同意，请在 [Twitter](https://twitter.com/jpunk11) 上告诉我。

### UX

我们都同意 UX 至关重要。如果用户有糟糕的体验，他们会离开，没有用户，我们的建设有什么意义？

为了产生良好的用户体验，我们需要了解我们的用户以及他们如何使用我们的产品。

我们需要问一些关于用户的问题:

*   他们是谁？
*   他们来自哪里？
*   他们使用哪些浏览器？
*   他们有残疾吗？
*   他们的母语是英语吗？
*   他们使用的是什么类型的设备？
*   他们的互联网连接有多稳定？
*   他们通常在一天中的什么时候使用该产品？

你明白了。大多数这些你可能已经想到了，但是如果你没有，现在是开始锻炼这些移情肌肉的理想时机。

### DX

开发人员体验关注的是开发过程中开发人员的生活质量。很多 DX 归结于工具。这些工具包括我们选择使用的框架、我们的 IDE、浏览器开发工具、插件、CI/CD 流程等等。

如果你是团队领导，你有责任让你的团队取得胜利。通过充分设置构建过程、林挺、测试和部署，让团队成员很难生产出质量差的产品。

### 可达性

可访问性有时是一个奇怪的话题，人们会对此直言不讳并加以辩护。不需要那样。

当你听到无障碍时，大多数人想到的是盲人，但其实还有更多。它是关于真实生活中的真实人物，以及我们作为开发者如何迎合这些人，我们的用户。它是关于尽可能多的人可以访问网络。没有其他平台能做到这一点。

根据[世界银行集团](https://www.worldbank.org/en/topic/disability)的数据，10 亿人，即世界人口的 15%，都有某种形式的残疾，其中超过 1 亿人患有严重残疾。

客观地说:IE11 目前在全球的使用率为 2.75%，我们正在建立相关网站。如果我们在乎这一小部分人，为何我们不照顾那 15%的残疾人士呢？我并不是说全部 15%的残疾人都使用网络，但好消息是这些用户不使用 IE，这是肯定的。🤗

> 我们不应该因为可访问性而额外收费。这是我们作为专业人员应该具备的素质的一部分。

#### 有些事情要考虑:

*   你的应用可以只用键盘导航吗？
*   你的应用程序对有视力障碍的人使用的屏幕阅读器友好吗？
*   你的应用是否认为用户可以仅根据颜色来区分选项？
*   你的用户界面元素之间是否有足够的对比(文本和背景颜色，等等)。)?
*   用户喜欢减少运动吗？
*   你所有的视频都提供字幕吗？

#### 我们能做什么？

*   设置像 ESlint 这样的工具，让我们意识到代码中明显的可访问性问题
*   学习正确的 HTML 语义
*   了解 ARIA
*   使用浏览器和 CLI 工具测试可访问性问题
*   使用屏幕阅读器
*   仅使用键盘测试应用导航
*   当你注意到 UX 时大声说出来，那会更好。不要做一个只做给你的事情的开发者。用你美丽的大脑；记住，你是专业人士。

### 安全

我们并非都在为中情局工作，但我们可以制定一些简单的原则来极大地提高我们应用程序的安全性。

#### 要考虑的事情

*   你所有的页面都在 HTTPS 吗？你的应用程序没有理由不再使用它。大多数主机提供商免费提供 SSL 证书。不仅如此，许多 web API 和功能，如[服务人员](https://developers.google.com/web/fundamentals/primers/service-workers/#you_need_https)只适用于 HTTPS 的网站。
*   使用像 [HSTS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security) 和 [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 这样的 HTTP 头来限制你的站点在普通 HTTP 上运行或者在你的页面上加载任何其他资源。有了这些基本的东西，您几乎可以消除大多数技术安全问题。
*   你的应用程序接受用户输入吗？如果是这样，确保你消毒一切。一个很好的例子是使用 innerHTML 将 HTML 写入 DOM。如果你正在编写的 HTML 字符串来自用户，他们可以毫不费力地将脚本注入你的应用程序。做最坏的打算。一个开发者稍微玩世不恭是合适的。
*   大多数时候，我们的安全计划中最大的缺口是我们自己，也就是相关的人类。谈到安全性，一个可靠的原则是“[最小特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege)”。它仅仅意味着提供完成任务所需的最低权限，仅此而已。例如，如果用户只能添加新条目，就不要给他们删除权限。在创业公司，当快速迭代时，这对你的内部系统来说可能是不必要的繁文缛节，但请考虑一下，并做出最适合你的团队的决定。

### 表现

从用户和开发者的角度来看，性能可能是我们所能看到的高质量软件的最关键的方面之一。许多研究报告了当应用程序加载时间减少几秒钟时，公司的财务收益或损失。因此，我们应该尽可能地优化性能。

我们也可以从交付的角度来看性能:一个特性的开发需要多长时间。换句话说，效率。功能开发是一个越快质量越好的领域。另一方面，更慢也不一定意味着更好的质量。工具可以帮助我们。

让我们面对它；我们不擅长重复性的任务。当你快速移动时，你不可能记得检查所有的东西。事先设置好你的工具来捕捉明显的错误，当事情从缝隙中溜走时，为下一次更新你的工具。我在这里非常随意地使用“工具”这个术语。这些工具可以是代码棉条、单元测试、拼写检查、发布清单等。

我个人不认为计算机科学学位是成为顶级开发人员的必要条件。我想说的一点是，每个人都应该理解 [Big-O 符号](https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/)。我不是在谈论沉重的计算机科学理论，而是作为一种思考您刚刚编写的代码及其性能的方式。理解时间复杂度和空间复杂度的区别。一个函数可以有 O(1)的时间复杂度，但是在空间复杂度方面非常大，以至于用户的设备耗尽了内存。在这种情况下，O(1)函数毫无用处。也就是说，Big-O 是需要注意的事情——如果你在考虑它，你已经赢了。

### 沟通

关于这一点我不打算多说，因为我们经常听到，交流在我们生活的大部分领域都是必不可少的。与其引用另一个陈词滥调，不如让我们谈谈沟通在我们作为开发人员的日常工作中很重要的一两个地方:

#### 代码评审

很少有沟通工具像代码评审一样，在我们生产的质量上产生如此大的差异。我想鼓励你注意你是如何在代码评审中交流的。作为创建拉请求的人:你清楚你的代码变更的意图吗？使用提交消息、PR 描述或截图前后。这些东西对给你的评论者一些非常需要的背景大有帮助。

从审查者的角度来看，你是否“倾听”(注意)了提议的代码变更？当你给出反馈时，你仅仅是对喜欢的代码风格吹毛求疵，还是你的目标是增加价值？要记住的一件好事是“[彻底坦率](https://www.radicalcandor.com/)”的概念。归结起来就是:说话要直截了当，但要从关心人和产品的角度出发。不同意和质疑代码实现是健康的，但是以卑鄙和傲慢的方式这样做是不酷的。幸运的是， [Responsive](https://www.goresponsive.com/) 的每个人都非常友好，所以这很少成为问题。🙂

#### 任务记录器

当你写任务描述或评论时，作为一个人，想想将会阅读它的人。如果那个人不是另一个开发人员，不要使用你所有的技术术语。如果你在写东西，而对方不理解你，你在交流吗？

> 永远以简单明了为目标。

### 测试

我首先承认测试并不总是最有趣的事情。没有人拍关于测试的黑客电影；只是没那么性感。

对于不同类型的测试，有多少个得力的开发人员，就有多少种观点，所以下面是我对 UI 测试的看法:

*   并非所有类型的测试都有用
*   TDD 对于纯粹的表示组件来说不太适用。为此，在事后添加测试要容易得多。
*   你的测试应该尽可能地反映用户如何与用户界面互动— [tweet inspiration](https://twitter.com/kentcdodds/status/977018512689455106?lang=en)
*   有时测试不值得花时间投资
*   有时候测试会救你的命
*   单元测试对开发人员有益，对利益相关者无益——利益相关者不应该关心代码覆盖率。开发人员应该编写足够多的单元测试，让他们有信心推出高质量的代码。
*   单元测试最适合基于纯逻辑的功能
*   端到端和集成测试比单元测试有更高的 ROI。花时间设置自动化测试来覆盖应用程序中的关键旅程比追求单元测试的 100%测试覆盖率要好。
*   测试应该作为 CI 流程的一部分运行，这样向主分支添加代码就需要通过测试。
*   测试应该是你的应用程序中的一级代码，并与你的应用程序代码一起更新。

我们现在知道我们想把质量放在第一位，但是我们实际上如何做到这一点呢？我们提到了许多有用的工具，但是我想在这篇文章的剩余部分谈谈一个把质量放在前面和中心的特殊工具: [Cypress.io](https://www.cypress.io/)

### 柏树

Cypress.io 是一个开源的前端测试工具，为现代网络而构建。

#### 为什么是柏树？

Cypress 最常被比作 Selenium，但基于其架构，它是根本不同的。我知道，你们中的一些人有过使用工具的经历，比如黑猩猩和量角器，它们在引擎盖下使用硒。Chimp 用的是 WebDriver，也就是 Selenium。

我承认在 CHEP 写 BDD 测试可能是我在这个项目中最不喜欢的部分。不要误解我，自动化 UI 测试的想法和其他开发人员一样让我兴奋，但是我大部分时间都在调试古怪的测试。它似乎从来没有做我想要的。这导致对该工具的信心为零。如果它不能让我们对应用程序的质量有信心，那么做还有什么意义呢？

在 Selenium 和其他类似工具通过从浏览器外部发送命令与您的应用程序交互的地方，Cypress 采用了一种完全不同的方法，直接在您的浏览器中工作。

#### 这为什么伟大？

柏树可以做硒根本做不到的事情。比如模拟网络请求，在本地存储中设置项目，以及直接与应用程序的状态进行交互。Selenium 只能对你的应用程序的状态做出有根据的猜测；Cypress 非常清楚你的应用程序中发生了什么。

> 不再剥落！不再痛苦！

#### 入门

使用 Cypress 运行起来非常容易，所以我不会在这篇文章中讨论它。从 NPM 安装它，然后运行它。他们的[文档](https://docs.cypress.io/guides/overview/why-cypress.html#In-a-nutshell)非常优秀——我鼓励你去看一看。

第一次运行 cypress open 命令时，它会自行安装 cypress，然后打开接口。这个 UI 是一个电子应用程序，像 Cypress 的其他东西一样，它是 100%用 JavaScript 编写的。

您可以使用 UI Electron app 运行 Cypress，也可以使用 cypress run 命令运行 headless。headless 选项对于将 Cypress 集成到您的 CI 管道中很有用。

#### 黑仔特色

*   时间旅行

实时重新加载

*   可调试性

由于 Cypress 与你的应用程序运行在相同的浏览器环境中，你可以很容易地用熟悉的工具调试它。将调试器直接放在代码或控制台中，尽情记录。我应该提到，因为 Selenium 使用与浏览器开发工具相同的调试协议，所以在它运行时不可能打开开发工具吗？我们这么长时间以来一直对此无动于衷，这太疯狂了。

最重要的是，Cypress 会给出有意义的错误消息。

*   自动等待
*   间谍、存根和时钟
*   网络流量控制
*   一致的结果
*   截图和视频

到目前为止，我对硒一直比较苛刻。公平地说，硒是一种体面的产品，很好地服务了它的时代。过去，在 JavaScript 框架时代之前，web 应用要简单得多。每个用户操作几乎都会导致页面刷新，这意味着客户端几乎不需要处理任何状态。今天的应用程序非常复杂，我们需要新的工具来帮助我们理解这一切。柏树就是这样一种工具。

### 点链接:

*   [安装和设置](https://docs.cypress.io/guides/getting-started/installing-cypress.html)
*   [写作测试](https://docs.cypress.io/guides/getting-started/writing-your-first-test.html)
*   [运行测试](https://docs.cypress.io/guides/core-concepts/test-runner.html)
*   [调试测试](https://docs.cypress.io/guides/guides/debugging.html)

*最初发布于*[*https://jperasmus . me*](https://jperasmus.me/posts/quality-first-ui-development)*。*