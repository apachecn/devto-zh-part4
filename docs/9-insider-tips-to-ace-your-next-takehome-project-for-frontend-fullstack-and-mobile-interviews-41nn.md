# 在前台、全栈和移动面试中提高你的带回家项目的 9 个技巧

> 原文：<https://dev.to/gergelyorosz/9-insider-tips-to-ace-your-next-takehome-project-for-frontend-fullstack-and-mobile-interviews-41nn>

在与招聘人员或招聘经理通话后，带回家的项目通常是前端/移动/全栈招聘流程的第一步。当我面试不同的公司时，我有相当多的带回家的经历。在桌子的另一边，我回顾了几十个网络和移动外卖项目。作为一名招聘经理，我也收集了一些，我团队中的工程师每年都会审查数百份这样的文件。

**这篇文章分享了在完成这些挑战中的一个**时需要注意的建议，提到了我从“另一边”提交外卖中学到的很多东西。

## 典型的外卖项目

带回家的目的是让你面试的公司了解你如何解决现实世界的问题。这些挑战的创造者试图限制范围，以便有足够经验的人可以在几个小时内完成。同时，他们希望看到开发者在实践中使用日常概念。概念，如通过网络获取东西，操作用户界面和处理边缘情况。有时甚至是测试和文档。

这是一个普通的外卖店的样子:

*   **使用/不使用$specificTechnology 构建一个 web/移动应用程序**。
*   使用 APIxzy.com/api,，这是一个典型的可以搜索一些术语的 API。
*   构建一个漂亮的 UI ，用户可以在其中输入搜索词，然后显示可视化结果。
*   **注意**诸如$specificTechnology 的良好使用、代码分离、可读性、可维护性和错误处理。
*   常见的附加功能可能是练习的一部分，包括分页、延迟加载和响应式布局。这些通常被标记为额外的东西。
*   **奖励积分**用于测试、文档和其他展示您超越自我的东西。

要查看一些具体的例子，请看科技公司使用的这些带回家的测试:

*   [Skyscanner fullstack 带回家测试](https://github.com/Skyscanner/full-stack-recruitment-test)
*   [只吃外卖测试(前端，数据工程师& DevOps)](https://github.com/justeat/JustEat.RecruitmentTest)
*   [Shoppad 外卖测试](https://github.com/shoppad/takehome-test)

## 我的外卖会被如何评价？

在我们开始学习如何完成一个伟大的任务之前，先了解一下谁会看你的作品以及为什么会看。

几乎在所有情况下，在该公司工作的开发人员都会检查您的提交内容。以下是他们第一眼看到的内容:

*   它到底有没有用？他们能按照您提供的设置说明获取您的代码并让它运行吗？
*   **工作正常吗？**一旦它启动并运行，开发人员会用一批*边缘案例对它进行压力测试。别忘了:评审人员对项目非常熟悉。他们有足够的时间去了解棘手的案例——所以他们会看到你的解决方案涵盖了多少案例。如果 takehome 是一个基于搜索的项目，他们会尝试什么都不输入，像`'`或`&`这样的特殊字符不会返回任何结果，或者可能会返回无效数据。*
*   给我看看代码。他们会仔细检查代码本身，阅读它并弄清楚它的可读性和整洁程度。
*   **视觉效果**。尤其是当它是一个前端或者移动位置的时候，UI 是不是看起来很好看，感觉很愉悦？有反应吗？有没有一个漂亮的“额外的触摸”添加到评论者的脸上，让他露出笑容？

**更资深的候选人。**假设这一切看起来都没问题，对于更有经验的候选人，评审人员可能会更深入地检查:

*   **遵循框架/语言最佳实践**。即使没有定义框架，开发人员也可能会检查框架和语言的使用是否符合行业标准。这可以从简单的事情如`JavaScript`中的`camelCase`变量命名一直到在移动项目中正确使用`Rx`库。
*   **阶层结构**。定义了什么类？班级是否遵循[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)，有明确的目的？
*   **代码架构**。代码是如何构建的？业务层和表示层是否清晰分离？在整个项目中是否使用了任何设计模式，如 [MVC、MVVM、MVP](https://medium.com/@ankit.sinhal/mvc-mvp-and-mvvm-design-pattern-6e169567bbad) 模式或类似的模式？它是以一种易于扩展的方式构建的吗？目录/模块被很好的分开了吗？
*   **测试**。业务逻辑是用单元测试来测试的吗？测试通过了吗？测试写得清楚吗？通过让[每个测试有一个断言](https://dev.to/donut87/one-assert-per-test---what-is-that-about-4l75)，测试是单一目的的吗？
*   **文档**。安装说明清楚吗？文档是否总结了已经构建和没有构建的内容？有关于改进或扩展点的注释吗？

...也就这样了！当我审查提交的内容时，我会花 5-20 分钟在每一条上，花较少的时间在那些效果好且清晰的内容上。我会花更多的时间在那些不确定的事情上。我观察到其他评审者为每次提交分配了相似的时间。

## 写外卖的小技巧

现在我们知道人们会看什么了，该去工作了。以下是我对成功清除这一阶段的建议。

### 技巧 1:知道第一次会花更长的时间

在你开始编码之前，要知道这一点:你的第一次带回家要比你(或创造者)设想的花费更多的时间来完成。没有好的经验法则。然而，在我第一次带回家的时候，我被告知要花 4 个小时。我轻而易举地在上面花了 12 个小时。候选人也经常告诉我，他们超出了建议的时间范围。

这是为什么呢？归结起来有三个主要原因。首先，您需要做大量的准备工作，从运行“hello world”到测试和文档。第二，人们不想提交质量差的作品。即使你确切地知道如何做事，高质量的工作也需要时间。最后，人们经常用这个练习来尝试新的东西，这需要额外的时间。而这是一件了不起的事情！这让我想到了下一个技巧。

### 技巧 2:把带回家的东西当作学习练习，尝试更好的、新的做事方法

听着，你已经在外卖上花了很多时间了。不妨让你的时间物有所值，在途中学习一些新的东西。这样做，它也变得有趣多了，不再只是一件琐事。

试着使用一种比你平时更好的方法。这可以从使用新的设计模式或者用最近流行的方法设计项目开始。

使用各种框架，这些框架完成繁重的工作，留给你的代码很少。大多数外卖员明确要求不要这么做。这是因为如果你这样做，评论者只能评价第三方库有多棒，你使用它有多聪明。他们不会知道如果没有那个特定的库你会过得有多好。

### 提示#3:快速原型化，然后很好地构建

如果外卖是你以前没有建立过几十次的东西，你可以通过建立一个快速的原型来取得更好的进展。获取数据并将其显示在屏幕上的版本。有时候，仅仅是获取数据源的 API 键就要花费一段时间。一旦你有了功能性的东西，开始一个新的项目就容易多了，比如 UI 和业务逻辑是分开的。

### 技巧 4:在你编码之前，列出一个边缘案例的列表

我看到评论者拒绝工作的家庭作业的一个最常见的原因是草率的工作。大多数情况下，这意味着不处理非常基本的边缘情况。就像在没有输入的情况下进行搜索一样，什么都不会发生。或者插入特殊字符时应用程序中断。或者对于 web 项目来说，调整窗口大小会弄乱 UI。

这些问题大部分都来自作业中没有定义的“边缘案例”。当大多数开发人员知道涵盖什么样的边缘情况时，他们会做得非常好。所以拿出你的边缘案例清单吧！花五分钟思考可能的怪异案例，并列出清单。然后，作为完成任务的一部分，确保涵盖这些内容。这也是为什么这项工作会比你预期的时间更长的原因。

### 技巧 5:你越想获得有经验的职位，你需要覆盖的范围就越广

带回家的练习是面试过程中的一个“二元”门。在这一步之后，你要么进入下一轮——通常是现场——要么这个过程就此停止。公司招聘的职位越高，他们对质量的期望就越高。除了可读的功能代码，他们还期望遵循最佳实践，并关注架构和测试。

我见过那些专注于“X 小时”建议的有经验的候选人被拒绝，提交了一个质量较低的解决方案。如果他们再投入一两个小时，结果可能是肯定的:事实上，这对双方来说都是一种浪费。

### 小贴士#6:要求更多时间没关系

如果你听从了我的建议，并且这是你做的第一份外卖，你可能在外卖上花的时间比你想象的要多。你也可能会用完别人告诉你的任意时间。或者，出现了其他的事情，阻止了你继续这个项目。

这里有一个鲜为人知的秘密:95%的招聘经理不在乎你花多长时间完成这项工作。。问题是，招聘人员，也就是你正在与之交谈的人，有时并不知道这一点。大多数时候他们会这样做，但他们的目标是让人们快速到达潜在的现场。这就是为什么他们传达一个紧张的最后期限。说*“你有 X 天的时间来完成这件事”*是一个激励人们把它发送过来的非常有效的方法。

作为一名招聘经理，我从来没有对任何延迟提交的人说“不”，假设他们就此进行了沟通，而不是就这样消失了。当评估外卖时，评论者不看——通常甚至不知道——这个人做了多长时间。

我绝不是建议你花尽可能多的时间想出一个完美的外卖。我只是说，如果你的生活中出现了其他事情，或者如果你决定花更多的时间去做，不要有压力。

### 提示#7:添加(基本)文档

这是我见过的 80%的提交中的一个失误。在解决方案上花费数小时后，很少有人花 10 分钟输入基本文档，这有助于提供上下文并帮助他们发光。剩下的 20%是这样做的:

*   **启动应用程序**或设置应用程序的注意事项，供审核者使用。
*   **总结建造了什么和(故意)没有建造什么**。当评审者知道在额外的任务中，分页被实现了，但是延迟加载没有被实现，这就省去了很多吹毛求疵。这有助于唤起人们对你所做的额外工作的注意。你是否投入了额外的创造力来处理一个边缘案例？把它写下来，否则评论者可能会完全跳过它。
*   **设计决策和框架选择的背景**。如果你尝试了一个新的框架，或者为什么你选择了一个特定的架构模式，给出上下文是很好的。对于更有经验的考生来说更是如此。不要低估这种影响:评论者*会*考虑上下文。如果他们明白你尝试了一些新的东西，他们就不太可能变得超级严格。
*   需要改进的地方。你可能没有时间了，本可以做得更多。所以列出这些吧！当评论者看到你知道还遗漏了什么时，会有很大的帮助。如果这是一个投入生产的项目，提到你会做的事情也是很好的。

### 技巧 8:在 Github 上发布你的代码

如果你在找工作已经不是什么秘密了，或者你想在你的 Github 个人资料中添加一些新的东西，请考虑发布你写的代码。假设你有很好的文档，代码写得很好，它可以作为其他雇主未来的另一个数据点。通过发布它，你也可以更容易地与任何人分享，他们将来可能会从你那里寻求指导。

如果你不想公开你在做外卖，你仍然可以发布你的代码而不提及它是外卖。尝试新技术并公开分享总是一个很好的练习。

### 小贴士#9:做更多的家务将会花费*更少的时间*

完成你的第一份外卖，或者很长一段时间内完成一份外卖会让你感到筋疲力尽。但是隧道的尽头有光明。由于大多数外卖都是相似的，你的很多工作在下一次都是可重复使用的。当然，下一个带回家的东西会让你和一个不同的 API 对话，也许会做一些除了搜索之外的事情。但是几乎可以肯定的是，*将会涉及到联网、列表显示、UI 调整、边缘案例处理、测试和文档编制。所有你花了大量心血的事情。*

我希望这个指南能给你一些想法和灵感，让你下次在面试过程中完成一个带回家的任务。**你还有哪些没有在这里列出的额外技巧**可以分享给大家，以应对这个带回家的项目？

* * *

*关于我:*我是一名工程师出身的工程经理，在硅谷&欧洲初创公司和科技公司的交汇处工作。关注我这里和推特上的。我在我的博客“务实的工程师 T4”上写关于软件工程的长篇文章。我还发送一份每月简讯[,里面有关于软件工程和技术领导主题的详细说明。](https://blog.pragmaticengineer.com/newsletter/)