# 打扫房间教会了我编程。

> 原文：<https://dev.to/kristijanfistrek/what-cleaning-my-room-taught-me-about-programming-3ajn>

**免责声明**

我仍在学习如何组织我的项目。我绝不是专家，我的建议不能全信。然而，我注意到我以前的设置存在某些问题，我已经对其进行了调整，并立即看到了改进。
我想分享我的故事，希望能帮助到你们中的一些人。
如果你看到了不好的东西，请在下面留下你的建议

### **前言**

我们可能都听说过这句名言:去打扫你的房间吧！“”
可能我们所有人，有时候在生活中，都回答过:
，*我现在不行！我喜欢做一些非常非常重要的事情！*“”。

作为一个自称“*创意*”的人，我用尽全力为自己的创意乱局辩护。
我已经非常详细地解释了我椅子上的 20 层 t 恤是如何帮助我做日常决策的，我的桌子是如何不凌乱的，它被完美地组织在经过深思熟虑的部分中，加快了我寻找东西的过程。

但是多年以后，关于创造性混乱的残酷事实击中了我的头。有人告诉我，创意混乱，归根结底，仍然是混乱。

[![](img/a6c20ab342812d02ba7bcc88baa4ce2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WwF_-BX4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nprch23l62jh6j4ibf8k.png)

我:，，*他们怎么敢，那些可怜的傻瓜永远也不会理解我进行这些创造性程序的天文重要性和技术复杂性，这些创造性程序使我的效率和生产力不亚于上帝！*“”
我谦卑地心想。

当我的视线再次落在创意设计的 20 层 t 恤休闲椅上时，从我的后脑勺传来一个微弱的声音:也许他们是对的...'。

不幸的是，他们**是**对的。

我泪眼汪汪地咕哝着:，，*再见，20 层/高姿态/独一无二/定制的躺椅，简直就是上帝...再见。*“”。

### **目录**

1.  干净工作空间的幻觉
2.  现在发生了什么？
3.  例子
4.  结束语

### **1。整洁工作空间的幻觉**

不要误解我的意思，创造性的混乱是一件美好的事情，直到除了你之外的其他人需要它。我仍然记得当我试着在 20 分钟的电话中引导我的兄弟，从埋藏在我房间里的特定文件中读出一些数字。

所有事物都有屈服于熵的趋势，如果这些事物得不到重视，那就更是如此。

我有幸经历了软件项目的这种命运，大部分是我自己的。

在一个项目的开始，无论是一个更大的软件应用还是一个更小的网站，代码迟早会堆积起来。随着你的代码一点点地被添加到你的应用程序中，你不会有事情变得更乱的感觉，就像青蛙不会感觉到水稍微变热，直到它被活活煮熟。太暗了，克里斯蒂昂...太暗了。

但是我如何事先知道如何组织我的项目呢？
我不想被活活煮熟！

别担心，年轻的学徒。我在这里告诉你...我不知道。
我自己也在学习这方面的东西，这要看情况...超级难...我甚至不能...我是说，从哪里开始？

不，我只是在开玩笑...嗯，不完全是。

你的项目的组织和详细程度肯定取决于你使用的技术，应用程序的健壮性，以及有多少人在使用它。不幸的是，在这种情况下，没有一个适合所有人的。

但是，在我参与的整个项目中，我注意到的一致性是团队在建立架构、规模和定义每个开发人员可以遵循的必要步骤方面的协作效果，以便编写更好、一致和干净的代码，同时保持文件结构简洁和整洁。

如果我可以用几个步骤来总结，那就是:

1.  先说话，后编码
2.  清楚地定义架构和必要的工具
3.  根据预定义的体系结构，写下确保一致性和干净环境的规则(如果有必要)
4.  不要崩溃和燃烧

### **2。现在发生了什么？**

考虑到我前面提到的步骤，让我们来看一看。

最近我参加了一个项目，很快我会在这里发布更多的内容，这是一个统一 Nasa API 的项目。这是我的故事。

1) *先说话，后打码*

通过谈论这个项目，你把自己放在了一个不同的位置。最好是处于将会使用该应用程序的人的位置，因为我们作为开发人员经常陷入创建该应用程序的人的思维定式中。

你发现了可以解决某些问题的新角度，并希望开始考虑适合用户的设计，而不是你想看到的设计。

考虑到这一点，我写下了我的申请要点，以及我可能面临的关键问题。当然，你不可能涵盖这一部分的每一个细节，但是为你的项目建立一个合适的基础是很重要的，在这个基础上你可以流畅地构建每一个未来的版本。

2) *明确定义架构和必要的工具*

架构的设计应该让我们能够高效流畅地从 API 中获取结果，将其映射到我们预定义的模型，对数据进行功能性排序，并将其提供给前端。沟通需要尽可能的快速、流畅和干净。

在谈话中，我们清楚地一致同意在后端使用 Java Spring，并使用 **Angular 7** 作为我们的主要前端工具(提前触发评论)。尽管它有时可能很健壮，而且它肯定更多地是作为处理大型企业应用程序的工具，但它完美地满足了我们所有人的需求。

只是提醒一下，还没有写一行代码。

3) *根据预定义的架构*，写下确保一致性和干净环境的规则(如果有必要的话)

随即，清晰简洁的 **Git** 流程说明被写了下来。
源代码控制是一件重要的事情，一个高质量的 **Git** 流真的可以扭转局面。

除此之外，还就我们的文件夹应该如何构建以及命名约定达成了一致。

我们的应用程序文件夹示例如下:

[![](img/faf7007c2cd7c15dfadc8f2b7233651b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZEqOsZHb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zcl8dbr08jkle1wx7ypm.jpg)

*现阶段略有不同*

因此，每个人都能够很容易地在项目中确定自己的方向，并在应用程序中运用自己的方式。

每个人都被告知并参与了应用程序的命名约定和逻辑流程，这意味着我们都遵循了分层规则，包括我们的服务应该做什么，我们的组件应该看起来如何- *或者在 Angular 的情况下，它们应该有多蠢* -最后但同样重要的是，我们的语法应该看起来如何。

我们还使用了像*pretty*这样的工具来保持我们的类型脚本/Javascript 语法一致+像*Husky*T4】e2e 测试工具这样的工具来确保我们的代码在某种程度上是防弹的——我将在以后的帖子中更多地涉及这个主题。

4) *不要撞车和燃烧*

说真的，我们很容易被所有的程序和工作流程所困扰，并被试图让一切尽善尽美的过程所困扰。预测所有事情几乎是不可能的，重要的是你要记住错误会出现在你的项目中，你不可避免地要回去修正一些事情。

不要对此有太多压力。知道你正在尽你最大的努力，并坚持下去。

### **3。示例**

如果您对该主题更感兴趣，请查看以下链接了解更多详情:

*   [https://thehelloworldprogram . com/web-development/creating-files-folder-structure-web-pages/](https://thehelloworldprogram.com/web-development/creating-files-folder-structure-web-pages/)
*   [https://developer . Mozilla . org/en-US/docs/Learn/Getting _ started _ with _ the _ web/Dealing _ with _ files](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/Dealing_with_files)
*   [https://software engineering . stack exchange . com/questions/176803/how-to-organize-my-sites-file-system-approperty](https://softwareengineering.stackexchange.com/questions/176803/how-to-organize-my-sites-file-system-properly)
*   [https://onextrapixel . com/building-we B- apps-file-and-directory-structures/](https://onextrapixel.com/building-web-apps-file-and-directory-structures/)
*   [https://stack overflow . com/questions/24199004/best-practice-to-organize-JavaScript-library-CSS-folder-structure](https://stackoverflow.com/questions/24199004/best-practice-to-organize-javascript-library-css-folder-structure)

我把这个原则应用到了我最小的项目中。
例如，我的网络作品集仅由 HTML、SCSS 和普通 Javascript 组成，其结构如下:

[![](img/0f79f9650488a06b11f16c03b1f56ec4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3RyyhdFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zanuw6bjm2lfqchrmcju.jpg)

现在，如果任何人需要进入我的房间，打个比方说，需要对房间中我的*服务*部分做一些调整，他或她将能够毫不费力地这样做。

### **结束语**

**你的房间和你的代码有什么联系？我还是不明白。**

我话很多。我来总结一下。

当我开始检查我的生活环境时，突然就像星星排列一样，我获得了更大的洞察力，所有的东西都在我的手中，我知道每个小东西在哪里。最后，我不必花太多时间寻找琐碎的东西，最重要的是，如果有人需要我房间里的任何东西，它都非常容易找到，而且井井有条。

依我拙见，同样的原则应该应用于我们的软件项目。

迟早有人会继承你的代码。确保它代表了你的编码技能和组织技能。保持你的房间干净。