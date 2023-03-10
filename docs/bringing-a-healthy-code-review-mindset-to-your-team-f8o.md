# 给你的团队带来健康的代码审查心态

> 原文：<https://dev.to/a_sandrina_p/bringing-a-healthy-code-review-mindset-to-your-team-f8o>

花点时间回忆一下你最后一次合作进行代码评审的时间。您的团队是否克服了反馈阻力并管理了时间预期？培养健康的心态是与同事建立信任和分享知识的关键。

代码评审是开发过程中的一个时刻，在这个过程中，您(作为开发人员)和您的同事一起工作，在最近的一段代码发布之前寻找其中的 bug。在这样的时刻，你既可以是代码作者，也可以是评审者之一。

当进行代码审查时，您可能不确定您在寻找什么。另一方面，当提交代码评审时，您可能不知道等待什么。双方缺乏同理心和错误的期望会引发不幸的情况，并加快进程，直到以双方都不愉快的经历结束。

在本文中，我将分享如何通过在代码评审期间改变你的思维方式来改变这一结果:

*   [作为一个团队](#as-a-team)
*   [作为作者](#as-an-author)
*   [作为评审者](#as-a-reviewer)

## 👩‍💻👨‍💻作为团队

### 培养协作文化

在我们开始之前，理解[为什么代码需要被评审](https://sophiebits.com/2018/12/25/why-review-code.html)的价值是很重要的。知识共享和团队凝聚力对每个人都有好处，但是，如果心态不好，代码评审可能会耗费大量时间，并带来不愉快的结果。

团队的态度和行为应该接受非评判性协作的价值，以**学习和分享**为共同目标——不管其他人的经历如何。

### 在您的评估中包括代码评审

完整的代码审查需要时间。如果一个变更花了一个星期才完成，不要期望代码评审花不到一天的时间。事情不是这样的。不要试图催促代码评审，也不要把它看作瓶颈。

代码评审和编写实际代码一样重要。作为一个团队，记得**在你的工作流程**中包含代码评审，并设定代码评审可能需要多长时间的期望值，这样每个人都会同步并对他们的工作充满信心。

### 借助指南和自动化节省时间

保证一致贡献的有效方法是在项目中集成一个[拉请求模板](https://github.com/stevemao/github-issue-templates)。这将有助于作者提交一份完整描述的健康 PR。公关描述应该解释改变的目的，背后的原因，以及如何重现。截屏和参考链接(Git 问题、设计文件等等)是不言自明的提交的最后润色。

这样做可以防止评审人员过早地询问更多细节。另一种让代码评审看起来不那么吹毛求疵的方法是在评审员介入之前使用 [linters](https://github.com/collections/clean-code-linters) 来发现代码格式和代码质量问题。每当你在评审过程中看到一个重复的评论时，寻找减少它的方法(使用更好的指导方针或代码自动化)。

### 留校生

任何人都可以进行代码审查，每个人都必须接受代码审查，无论其资历如何。感激地接受任何反馈，作为学习和分享知识的机会。**将任何反馈视为公开讨论，而非防御性反应。**正如瑞安·霍利迪所说:

> “业余爱好者是防御性的。专业人士发现学习(甚至偶尔被展示)是令人愉快的；他们喜欢接受挑战和谦卑，并把教育作为一个持续不断的过程。（...)"
> 
> ——瑞安·哈乐黛，自我是敌人

保持谦逊，因为一旦你不再是学生，你的知识就会变得脆弱。

### 选择评审人员的艺术

在我看来，挑选评审员是团队中有效和健康的代码评审的最重要的决定之一。

假设您的同事正在提交代码评审，并决定标记“每个人”,因为不知不觉中，她/他可能希望加快过程，尽可能交付最好的代码，或者确保每个人都知道代码更改。每个审阅者都会收到一个通知，然后打开 PR 链接，看到许多人被标记为审阅者。“其他人会做”的想法会在他们的脑海中弹出，导致忽略代码审查并关闭链接。

由于还没有人开始评估，你的同事会提醒每位评估者开始评估，也就是给他们施加压力。一旦评审员开始做，评审过程将会持续很长时间，因为每个人都有自己的观点，达成一致是一场噩梦。与此同时，无论谁决定不评审代码，都会收到大量带有评审意见的电子邮件通知，从而影响他们的工作效率。

这是我看到的比我希望的更多的事情:拉请求，一群人被标记为评审者，讽刺的是，以非生产性的代码评审结束。

在选择评审人员时有一些常见的有效流程:一个可能的流程是挑选两到三个熟悉代码的同事，请他们担任评审人员。Gitlab 团队解释说，另一种方法是建立一个[链式审查流程](https://about.gitlab.com/2017/03/17/demo-mastering-code-review-with-gitlab/)，在这个流程中，作者挑选一个审查者，这个审查者挑选另一个审查者，直到至少有两个审查者同意代码。不管你选择哪个流程，**都要避免有太多的评审**。能够信任同事对代码的判断是进行有效和健康的代码评审的关键。

### 有同理心

发现需要改进的代码片段只是成功的代码审查的一部分。同样重要的是，通过对同事表现出同理心，以健康的方式传达反馈。

写评论之前，记得设身处地为别人着想。写的时候很容易被人误解，所以发之前先把自己的话审核一下。即使对话开始变得不愉快，也不要让它影响你——永远保持尊重。对别人说好话从来都不是一个坏决定。

### 懂得妥协

当讨论没有很快解决时，打个电话或者聊聊天。一起分析它是否是一个值得阻碍当前变更请求的主题，或者它是否可以在另一个中被解决。

灵活而务实知道如何平衡效率(交付)和效果(质量)。这是作为一个团队做出的妥协。在这些时候，我喜欢把代码评审看作是一个迭代，而不是最终的解决方案。下一轮总有进步的空间。

### 亲自审核代码

以结对编程的方式将作者和评审者聚集在一起是非常有效的。就个人而言，当代码审查涉及复杂的变更或者有大量知识共享的机会时，我更喜欢这种方法。尽管这是一个离线的代码审查，但是在网上留下关于讨论的评论是一个好习惯，特别是当会议后需要进行修改的时候。这也有助于让其他在线审阅者了解最新情况。

### 从代码评审结果中学习

当一个代码评审经历了很多变化，花费了太长时间或者已经进行了太多的讨论，把你的团队召集起来，分析原因以及可以采取的措施。当代码审查很复杂时，**将未来的任务分割成更小的任务**使得每个代码审查更容易。

当经验差距很大时，采用结对编程是一种具有令人难以置信的结果的策略——不仅对于知识共享，而且对于离线协作和交流。无论结果如何，总是以一个健康的、充满活力的、有明确期望的团队为目标。

## 📝作为一名作家

作为一名作者，在进行代码评审时，最主要的关注点之一是将评审者第一次阅读代码时的惊讶最小化。这是可预测的和顺利的代码审查的第一步。这是你可以做到的。

### 建立早期沟通

在编写太多代码之前，和你未来的评审人员谈谈并不是一个坏主意。无论是内部的还是外部的贡献，你都可以在开发之初一起做一个细化或者一点结对编程来讨论解决方案。

作为第一步，寻求帮助并没有错。事实上，在审查之外开展合作是防止早期错误并确保达成初步协议的第一个重要步骤。与此同时，您的评审人员知道未来要进行的代码评审。

### 遵循准则

当提交待审查的拉取请求时，记得添加描述并遵循指导原则。这将节省评审人员花时间去理解新代码的上下文。即使你的评论者已经知道它是关于什么的，你也可以借此机会提高你的写作和沟通技巧。

### 成为你的第一个评价者

在不同的上下文中查看您自己的代码可以让您找到在代码编辑器中可能会遗漏的内容。在询问你的同事之前，对你自己的代码做一个代码检查。要有一个评审者的心态，真正的检查每一行代码。

就我个人而言，**我喜欢注释我自己的代码评审**来更好地指导我的评审人员。这里的目标是防止与可能缺乏关注相关的评论，并确保您遵循了投稿指南。目的是提交一份代码评审，就像你希望收到一份那样。

### 保持耐心

在提交代码评审后，不要直接跳到一条新的私人信息上，要求你的评审员“看一下，只需要几分钟”并间接地渴望那个竖起大拇指的表情符号。试图催促你的同事做他们的工作不是一种健康的心态。相反，**信任你同事的工作流程**，因为他们信任你提交一份好的代码评审。同时，等他们有空的时候给你回电话。不要把你的审稿人看做瓶颈。记住要有耐心，因为困难的事情需要时间。

### 做一个倾听者

一旦代码评审被提交，评论就会出现，问题会被提出，修改会被提出。这里的黄金法则是不要将任何反馈视为人身攻击。请记住，任何代码都可以从外部视角中受益。

不要把你的评论者当成敌人。相反，利用这一时刻放下你的自我，接受你犯的错误，并虚心向你的同事学习，这样下次你就能做得更好。

## 👀作为评论者

### 提前计划

当你被要求做一个评论者时，不要马上打断事情。这是我经常看到的常见错误。审查代码需要你的全部注意力，每次你切换代码上下文，你都在通过浪费时间重新调整你的焦点来降低你的生产力。相反，通过分配一天中的时间来做代码审查，提前计划。

就我个人而言，我更喜欢在早上或午饭后，在选择任何其他任务之前，先做代码评审。这对我来说是最好的，因为我的大脑仍然是新鲜的，没有以前的代码上下文可以切换。除此之外，一旦我完成了审查，我可以专注于我自己的任务，而作者可以根据反馈重新评估代码。

### 予以支持

当一个拉式请求没有遵循贡献指南时，要给予支持——尤其是对新来者。把那个时刻作为引导作者提高他/她的贡献的机会。同时，试着理解为什么作者一开始没有遵循这些指导方针。也许还有你以前没有意识到的改进空间。

### 检查分支并运行它

审查代码时，让它在您自己的计算机上运行——尤其是当它涉及用户界面时。这个习惯将帮助您更好地理解新代码，并发现如果您只是在浏览器中使用默认的 diff-tool，将您的审查范围限制在更改的代码(没有代码编辑器中的完整上下文)，您可能会错过的东西。

### 问前假设

当你不明白某件事时，不要害怕说出来并提出问题。问的时候记得先看周围的代码，避免臆断。

大多数问题都属于这两类:

1.  **“如何”提问**
    当你不明白某个东西是如何工作的，或者它是做什么的时候，和作者一起评估代码是否足够清晰。不要把简单的代码误认为无知。难读的代码和你不知道的代码是有区别的。对学习和使用一种新的语言特性持开放态度，即使你还没有深入了解它。但是，只有当它简化了代码库时才使用它。

2.  **“为什么”提问**
    当你不理解“为什么”时，不要害怕建议对代码进行评论，尤其是如果它是一个[边缘案例或一个 bug 修复](https://twitter.com/sophiebits/status/1063180141872898048)。在解释它做什么的时候，代码应该是不言自明的。注释是对解释某种方法背后的原因的补充。当谈到可维护性时，解释上下文是非常有价值的，它将使某人免于删除被认为无用的代码块。(就我个人而言，我喜欢对代码进行评论，直到我对以后忘记它感到安全为止。)

### 建设性的批评

一旦你发现一段你认为应该改进的代码，一定要记住要认可作者为项目所做的努力，并以一种容易接受和透明的方式表达自己。

#### 公开讨论。

避免将您的反馈形式化为命令或指责，例如“您应该…”或“您忘记了…”。以公开讨论的方式表达您的反馈，而不是强制要求。记得评论代码，而不是作者。如果注释与代码无关，那么它就不应该属于代码评审。就像之前说的，要支持。使用被动语态、用复数说话、表达问题或建议都是强调与作者感同身受的好方法。

> 而不是“从这里提取这个方法…”更喜欢“这个方法应该提取…”或者“你觉得提取这个方法怎么样…”

#### 明确。

反馈很容易被误解，尤其是在表达意见而不是分享事实或指导方针时。永远记得在你的评论中马上澄清这一点。

> 不清楚:“这个代码应该是……”
> 
> 观点:“我相信这个代码应该是…”
> 
> 事实:“根据[我们的指导原则]，该代码应该是…”。

#### 说明原因。

对你来说显而易见的，对其他人来说不一定。解释你的反馈背后的动机永远不会太多，所以作者不仅知道如何改变一些东西，还知道从中有什么好处。

> 观点:“我相信这段代码可能是…”
> 
> 解释道:“我相信这段代码可能是(…)，因为这将提高可读性并简化单元测试。”

#### 提供例子。

当分享作者不熟悉的代码特性或模式时，用参考文献作为指导来补充你的建议。如果可能的话，不要局限于 MDN 文档，而是分享一个片段或者一个适用于当前代码场景的工作示例。当写一个例子太复杂时，支持一下，亲自或通过视频电话提供帮助。

> 除了说“使用过滤器将有助于我们[激励]”之类的话，还可以说，“在这种情况下，它可能是类似于:[代码片段]。可以查看【Finder.js 的例子】。如有任何疑问，请随时联系我。”

#### 讲道理。

如果同样的错误犯了很多次，最好只留下一条评论，并记住作者也在其他地方评论它。添加多余的评论只会制造噪音，可能会适得其反。

### 保持焦点

避免提出与当前代码无关的代码更改。在建议改变之前，问问你自己在那个时刻是否真的有必要。这种类型的反馈在重构中尤其常见。这是我们作为一个团队需要在效率和效果之间做出的众多权衡之一。

说到重构，就个人而言，我倾向于**喜欢小而有效的改进。**这些更容易审查，并且在用目标分支更新分支时，代码冲突的可能性更小。

### 设定期望值

如果你留下一个未完成的代码评审，让作者知道，这样时间期望就在控制之中。最后，也要让作者知道你是否同意新的代码，或者你是否愿意以后再重新检查一遍。

在批准代码评审之前，问问你自己你是否对将来接触代码的可能性感到放心。如果是，这表明你做了一次成功的代码审查！

### 学会拒绝代码审查

虽然没有人承认，但有时你不得不拒绝代码审查。当你决定接受一个代码评审，但是试图仓促行事的时候，项目的质量和你的团队的心态都会受到损害。

当你接受审查某人的 else 代码时，那个人信任你的能力——这是一种承诺。如果你没有时间做评审，就对你的同事说不。我是真心的；不要让你的同事等待一个永远不会完成的代码审查。记得沟通并保持明确的期望。对你的团队诚实，更好的是对自己诚实。无论你选择什么，健康地做，并且正确地做。

### 结论

如果有足够的时间和经验，进行代码评审将会教给你的不仅仅是技术知识。你将学会给予和接受他人的反馈，以及在做决定时投入更多的思考。

每一次代码审查都是一次社区和个人成长的机会。即使在代码审查之外，也要记住培养健康的心态，直到你和你的同事自然而然地拥有它的那一天。因为分享让我们变得更好！

* * *

这篇文章[最初写于 Smashing 杂志](https://www.smashingmagazine.com/2019/06/bringing-healthy-code-review-mindset/)。