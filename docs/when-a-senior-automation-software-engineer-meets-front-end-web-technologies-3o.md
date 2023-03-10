# 当一个高级自动化软件工程师遇到前端 web 技术时

> 原文：<https://dev.to/absinthetized/when-a-senior-automation-software-engineer-meets-front-end-web-technologies-3o>

#### TL；博士:

自动化软件工程师应该远离 web 前端:-)但是新手可以用这个作为现代前端开发的轻量级入门。

#### 上下文

我在自动化领域做了十多年的软件工程师，主要是机器视觉应用。我在代数、机器视觉、Matlab 方面有很强的技能，很好的 c++知识和你可以放入自动化软件的后端东西。又名:*当我看到一个 CSS 时，我害怕地飞走了*

[![css float joke](img/2854d15bc520cb89adff193eefc9c686.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--laNP6nAg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2t8v3o.jpg)

最近几天，一些潜在客户对 web 前端自动化应用程序表现出兴趣。他们想要一个 web SPA(即使他们不知道 SPA 是什么)，这样他们就可以用不同的设备管理他们的机器。

因此，我开始了我的旅程，试图找到一个适合我的需求。结果是一次组装之旅，我努力构建我的 dev env 并找到适合我需要的工具箱，最重要的是，我的技能水平！

#### 我都要，现在就要！

所以作为一名高级自动化软件工程师，我的几个 GUI 都是用经典工具开发的。不幸的是。NET 框架和 Windows(我更喜欢 QT 和 Linux ),我的普通 GUI 被命名为“人机界面”,如下所示:

[![great GUI](img/8aa6542a7bda3f755baaf85a0c588498.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DTzkjnpO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ADfLKHreOsvXedQcrgFjM7Q.png)

是的，谢谢:这里没有虚假的谦逊。在我坐在电脑前的 10 多年里，我获得了超过平均水平的 UX 竞争力。

尽管如此，我已经习惯了图形设计人员(Visual Studio 有人吗？)、XML/XAML、数据绑定和现成的小部件。更不用说带有集成图形调试器的合适的 IDE 了。

所以我开始在 webdev 中寻找同样的工具。我只是想设计一些面板，而不是下一个消费级杀手级应用...应该很容易吧！

#### 我告诉过你我懒吗？

[![lazy boy](img/d28a684b91e5660ba6188cc40bb4d019.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b7SHL5aS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.tvtropes.org/pmwiki/pimg/garfieldlazy_2816.jpeg)

懒惰的第一步:我没有花时间去发现大量的秘密。我已经开始跟上潮流:IDE+调试器必须是 Visual Studio 代码(VSC ),带有几个插件和 Chrome 调试器或 Firef..*否:带镀铬。句号。*

懒惰的第二步:VSC 有哪些集成，他们的网站上有哪些关于前端框架/库/东西的文档？[轻松](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial):

*   有角的
*   某视频剪辑软件
*   反应
*   余烬
*   下一个还有 6 个月寿命的酷酷的东西，在 VSC，下一个

#### 工具链在哪？

真的:看在上帝的份上，给我一个工具链！

这里不是有 javascript 工具链吗？地狱连 c 都有一个(编译器，链接器，make，blabla)。不要等待:您可以使用 node.js 来实现这一点。

NODEJS？新经济发展局。？从 Chromium 复制的服务器端 javascript 解释器？怎么了？

是的。这些年来，当我试图让机器训练一堆该死的神经网络时，一些聪明的家伙发现他们可以使用 javascript 来构建 javascript 工具链。最后 c 编译器都是用 c 写的，那有什么错呢？

所以你的英雄开始安装 node.js 和 npm。和升级的 npm。所有以前的框架/库/东西都有适当的工具链准备好了，并在 npm 包注册表中等待着你:关于 webpack、webpick、babel、gomorra、ES2015、ES3048 等的无脑粉碎课程...一个 linter 也在适当的位置，一切只是一个 *npm 安装离开*(和一个 **创建应用程序名称*)离开！)

**爽！**哦，等等，这是什么 node_modules 文件夹？

[![node_modules](img/699303cd357358c56a13c9c38ef69072.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RiPbQBm1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.redd.it/tfugj4n3l6ez.png)

#### 开始编码吧！

现在一切就绪，我可以愉快地享受我的 IDE，它具有强大的调试功能和良好的调试/发布周期，这要感谢 case 的 cli 所做的巨大搭建，但是...调试器只是忽略了我的断点。事实证明，虽然 VSC 有大量的调试功能，但你必须将它们设置到。/.vscode/launch.js 文件(相对于应用程序文件夹的路径)。每个工具都有它自己的专用页面，30 分钟后。我在网上搜索了一下，找到了 launch.js 文件的正确配置。因此，我开始愉快地编写前端代码，就像我在 2000 年初编写后端代码一样:P

[![debugger my friend](img/c6012bf092e342dd2fbc5ba22b6d8f08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y2I2MvGA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pics.me.me/a-debugger-console-log-got-here-console-log-aaaaaa-console-log-bbbbbb-how-40120497.png)

#### widget set 在哪里？

[![my_widget](img/abf0fbe8dfda04dc463bccdc7538dbbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vb1f-OPk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://memecreator.org/statimg/memes/4050535.jpg)

好的，所以你需要安装另一组节点模块，但是所有以前的框架/库都有大量新的小部件可以使用...自举，材料设计基础，布尔玛...等等，布尔玛，真的吗？！啊，谁在乎，我刚刚安装了我的新 1TB nvm 磁盘用于节点模块托管，只需安装并关闭它！

现在告诉我，那个笑话是什么？啊，是的，JQuery 是如此沉重的一个库，你宁愿避免它...犯罪...你看到部件组的重量了吗？什么？我应该从头开始重新实现吗？真的吗？Naaaa....

#### 从哪里开始？

所以我实际上有 3 种可能性:我已经放弃了 Ember.js，因为整个生态系统似乎在围绕 Vue 旋转，React，Angular。抱歉，恩伯，我欠你一杯啤酒。然后，我试图在我的早期测试中尽可能保持主流，所以我选择了引导和材料设计。抱歉，布尔玛:我太害怕贝吉塔了。最后，我发现自己用了 6 种工具组合:

*   视图+引导程序
*   视图+材质
*   反应+引导
*   反应+材料
*   角度+自举
*   棱角+材质

#### 而我学到了什么？

嗯，来自后端/自动化背景的我评估了作为 whol 的 6 种可能性:这就像几乎使用了 6 种不同的框架，因为每个小部件集库都添加了自己的逻辑。

我发现 **Angular** 绝对是最接近服务器端编程的东西。至少感觉很接近 python/c#和 so: async events，decorators，把你的小部件分成 3 个不同的文件:html，css，logic。
**似乎也是三者中最正式的(许多关于概念和理论的文件)和最“公司化”的:固定的发布周期，有规律的节奏...对于我小小的需求来说，它也显得太大了。但是——正如所料——它的材料设计实现得非常好。**

 **[![Angular meme](img/4f7a412008f5ed2f148fce24e43d8961.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pwZ4nAAc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DOLb5vHU8AApWce.jpg)

**React** 是最接近我口味的纯 js。我在介绍中作弊了:过去我用 uwsgi+Flask+Python 开发了几个 web 应用程序。Bootstrap 用于这种风格，JQuery 是构建应用程序的唯一助手。逻辑是纯粹的 js。
所以当我说“尽可能地接近我的感觉”时，我指的是我的一个小经历。React JSX 也是我在过去几年里尝试过的最神奇的东西:优雅、有效。这似乎是摆脱模板残缺不全的逻辑的最好方法。自从我发现 Python 的那一年以来，我从来没有对新技术感到如此富有成效。
我已经准备好爱上 React，但是我遇到了很多问题，我所有的搜索路径都到了同一个地方:React 状态管理。对于一个简单的 UI 来说，需要学习的东西实在太多了。我也非常讨厌函数式编程。

[![React state](img/51249e5b198170cb507d2ff42492a951.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LAd1mydL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcSm39D-7HMCGSZK27x6wmRdYTmp0A9iE6ov0o_QusNImHQpCkVOLQ)

**Vue** ...一开始我忽略了 vue。这看起来像是一碗有棱角的、有反应的、奇怪的东西(TM)之类的中国汤。的确如此。有史以来最棒的组合。模板中所有那些丑陋的指令，说同一件事的一百万种方法(:，@，v-on，v-bind，v-ery-strange)，它甚至建议你必须使用一个状态管理库，看起来像是 Redux (Vuex？真的吗？).

[![Vue](img/7d68ac05f92067a24782c2584ed6c844.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MgGrtGsK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://secure.meetupstatic.com/photos/event/c/e/c/6/600_471832934.jpeg) 
(还好网络缺乏好的 vue.js 模因...)

但是它也有。vue 文件，其中简单的 html、css 和 js 对于每个组件保持紧密和隔离。你可以把 JSX 也算进去。这是我发现的最实用的解决方案，一天下来，我已经熟悉了在文档中读到的一些东西。

#### 好吧，那么？！

所以我选择了 Vue 作为我在前端世界的第一个入口。还有 Bootstrap(Bootstrap-vue 文档简直太棒了)。不是因为它在某些方面更优秀，而是因为它是最“宽容”的技术，在这个新世界里，它更容易工作，也更有经验。对于习惯于代数和机器视觉的人来说，这是一个非常好的切入点。

#### 好吧，谁在乎？！

哦。我真的不知道...但是你已经读过了，谢谢你的阅读:)
*记住:所有的模因都属于它们的作者。***