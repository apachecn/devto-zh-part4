# 对于数据科学来说，Python 和 R 之间的真正区别(TM)

> 原文：<https://dev.to/daveparr/the-real-difference-tm-between-python-and-r-for-data-science-280i>

## 蝙蝠侠大战超人

数据科学有两种语言的问题。r 和 python 都是数据科学的语言。这导致了两种语言之间一些非常抽象、通用、有时甚至荒谬的定义。最糟糕的大概是这个形象:

[![RBat vs pyman](img/a2b9f539b9aceaa6baf9804e7b6a97cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ReZqX08z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Ce8VP0FWIAI0ad2%3Fformat%3Djpg%26name%3Dsmall)

数据科学是一个非常宽泛的术语，有时在业务中对它的理解非常不同，甚至很差，以至于给出肤浅答案的点击式标题比比皆是。我已经追踪到了这张图片的来源，这张图片也在推特上流传过一次(2016 年在 T2，2016 年在 T3，在 T4，2018 年在 T5)。说句公道话，这篇文章的作者很有趣，我希望随着电影炒作周期的增长，出版 SEO 的奖金能让他获得一些额外的书籍销售(他链接的书籍实际上*真的*值得一读)。

我第一次看到它是在 instagram 的一篇帖子上(现已删除)，标题是“如何通过任何数据科学面试”，是一个专门宣传其数据科学招聘服务的账户。我并不反对任何形式的流行文化/编程流血(我已经做了足够多的口袋妖怪编程演讲，我骑的任何马都必须少一些 Rapidash，多一些 Ponyta)，但在这一点上，试图进入这个行业的人被告知这是对采访者有价值的信息，那么显然在需求和知识之间存在差距。

这种差异以及为工作选择“正确”工具的决定实际上是由内部和外部因素共同决定的。这两种语言本身在数据接收、处理、分析、建模和预测方面做得一样好。两者都可以部署在内部或云上。语法上的微小差异和部署工具上的主要差异都成为决定性因素。

## 内部:语法和生态系统

任何编程语言都只不过是语法。函数式的、面向对象的、命令式的等等，以不同的组合，用特定的约定和模式。奇怪的是，根据维基百科的说法， [R](https://en.wikipedia.org/wiki/R_(programming_language)) 和 [Python](https://en.wikipedia.org/wiki/Python_(programming_language)) 都是“多范例”。这意味着可以根据手头的问题使用多种不同的风格。你可以用面向对象的方式或者函数式的方式来编写 python。r 通常被认为是函数式的，尽管它允许用方法和副作用构造类和对象，所以它不是一种“纯函数式”语言。

### 然而，*人*实际上是如何为数据科学编写代码的呢？

在 R 中，数据科学是*一级应用*(甚至可能是*唯一应用*？).毫无疑问，现在大多数 R 项目都是以这种方式编写的。由于 RStudio 给了它全面的公司支持，并资助了一个工具生态系统，我写的大多数 R 都是用这种风格写的，我也看过别人写的，我也教过人们用这种风格写。Pipes `%>%`带非标准参数评估的链式函数调用、Rmarkdown 文档、ggplot 可视化和闪亮的应用程序是 R data scientists 的首选解决方案。Tidyverse 本身就是受*函数启发的*， [purrr 明确地陈述了这一点](https://purrr.tidyverse.org/articles/other-langs.html)而 [Hadley Wickham 则直接指出“R 在本质上是一种函数式编程(FP)语言。”](http://adv-r.had.co.nz/Functional-programming.html)

Python 是无处不在的*。Python 中的数据科学并不是 Python 存在的唯一理由。 [scipy](https://www.scipy.org/) 是用于数据科学的 python 工具链。numpy 和 pandas 是最好的伙伴，matplotlib 是图形的领跑者。尽管 scikit learn、seaborn 和 jupyter 笔记本也非常受欢迎，但这些工具的集合远没有 scipy 生态系统那么紧密。因此，这些工具需要更多的工作来塑造成一个完整的产品。面向对象编程也更加流行，[甚至对于](https://towardsdatascience.com/a-data-scientist-should-know-at-least-this-much-python-oop-d63f37eaac4d) [数据科学](https://towardsdatascience.com/object-oriented-programming-for-data-scientists-build-your-ml-estimator-7da416751f64)。这意味着您将更直接地调用对象，并且更频繁地使用`myObject.method`显式地提取对象上的方法。*

 *### 这对我意味着什么？

你是否已经理解了函数式编程或者面向对象编程，或者一个比另一个更有意义？很好，用那个。您是否已经有了由某些工具组成的生态系统？很好，用你已经有的。
你是否已经有了需要共事的同事？太好了，用他们是什么。
你是否已经有了一个支持一种语言而不是另一种语言的网络？很好，不要让生活变得不必要的艰难。

## 外部:应用和部署

如果你没有简单的答案呢？你正在开始一个新项目，或者你是这个行业中唯一的程序员？那么这个选择就变得更难做对，但是经验越多就越清晰。当你知道两个工具*可以完成*同样的工作，你必须选择哪一个做*更容易*。我发现我会平等地使用 python 和 R，现在我已经在这两个领域专业地工作了一段时间，然而，我使用的*定义得非常清楚。*

### 何时使用 R

r 清晰而健壮地封装了统计和数学概念。根据您从哪里获得依赖项，您可以非常清楚地了解库可能的“正确性”。大致来说 [ROpenSci](https://ropensci.org/) 比 [CRAN](https://cran.r-project.org/) 更严谨稳定，后者比 github 更好。Tidyverse pipes、NSE、purrr 提供了一个紧密耦合的工作环境，其中语法一致、简洁且易于重构。ggplot 和 RMarkdown 从一开始就为业务提供了可消费的输出，其中代码可以与叙述和报告紧密耦合，但也可以无限定制，以产生一眼就能通过营销的东西。许多学术出版物依赖 R 和 RMarkdown 进行研究和出版。如果你需要交互式输出，shiny 是一个简单的应用程序框架，与这个生态系统中的其他东西紧密相连。

r 代表**分析师**，他们需要*确定*底层数据处理*立即*，尽可能快地产生并迭代*报告*输出*并编写*最少代码*以获得最大回报。哈德利上周也说过同样的话。*

> 我认为 R Markdown 是对 R 的惊人贡献。...当你在做数据分析时，打字速度实际上是一个瓶颈。

### 何时使用 Python

Python 已经比 R 多使用了几吨，因为数据科学不是 T2 唯一的工作。数据工程师大量使用它，后端甚至前端开发人员在各种项目中都使用它，不管它是否是数据密集型的。在许多 linux 发行版(包括 Mac)中，当你打开操作系统时，它实际上是作为你的机器的一部分安装的。这也可以说是*云的母语*语言。AWS lambda 函数[支持开箱](https://aws.amazon.com/lambda/features/)，微软[最近复制了它们](https://www.theregister.co.uk/2019/08/20/microsoft_azure_functions/)。这非常有趣，因为微软在 2015 年收购了最大的 R 咨询公司之一，但是 R 在微软的生态系统中还不是一个本地支持的语言。它肯定比 AWS 得到了更多的 R 支持( [PowerBI](https://docs.microsoft.com/en-us/power-bi/desktop-r-visuals) 和[许多其他的](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Understanding-your-R-strategy-options-on-the-Azure-AI-Platform/ba-p/735626?WT.mc_id=Revolutions-blog-davidsmi&WT.mc_id=Revolutions-blog-davidsmi))，但已经采取措施[潜在地削减 MRAN](https://blog.revolutionanalytics.com/2019/05/cran-snapshots-and-you.html) 。所有这些意味着 python 被大多数开发人员使用，python 被大多数大型云提供商支持，Python 可能已经内置于您正在开发的任何东西中。

Python 面向需要在*传统软件环境*中*更传统的开发*工作流程中**部署软件的**开发者，其中*一流的云支持*至关重要，与*现有代码*的集成是重中之重。

## 结论

r[不一定容易部署到软件开发中](https://resources.rstudio.com/rstudio-conf-2019/it-depends-a-dialog-about-dependencies)(<——不严格相关，但与问题相关的精彩谈话)，尽管它肯定是可能的(我已经做过)，但你必须投入更多的工作才能得到大致相似的结果。在许多情况下，这种交换可能不值得。

Python 不一定适合 BI 和学术工作流，虽然它肯定是可能的(我也做过)，但你必须投入更多的工作才能得到大致相似的结果。在许多情况下，这种交换可能不值得。

超人，蝙蝠侠，侦探工作，聪明，狡猾，工具的使用，大脑比肌肉多，肌肉力量，超级力量，优雅，范围广，肌肉比大脑多都不是有意义的微分器。*